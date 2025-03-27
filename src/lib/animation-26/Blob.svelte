<script>
  import { onMount, onDestroy } from "svelte";
  import { browser } from "$app/environment";

  let animationFrame;
  let progress = 0;
  let pathString = "";
  let currentShape = 0;
  let nextShape = 1;
  let perspective = 1200;
  let rotation = { x: 15, y: 15, z: 0 };
  let transitionSpeed = 0.005;
  let lastShapes = new Set();

  let color = "rgba(80, 100, 255, 0.4)";
  let colorChangeInterval = 7000;
  let lastColorChangeTime = 10;

  // Shapes mit fixer Y-Mitte
  const shapes = {
    tallRectangle: createRectangle(225, 495),
    fatCylinder: createCylinder(225, 180),
    slimCylinder: createCylinder(135, 450),
    flatCylinder: createCylinder(270, 67.5),
    circle: createCircle(225),
    diamond: createDiamond(315),
    hexagon: createPolygon(13, 5, 225),
    barrel: createBarrelShape(225, 405),
    squishedCircle: createSquishedCircle(315, 225),
    wonkyHexagon: createWonkyHexagon()
  };

  function updateColor() {
    const r = Math.floor(Math.random() * 256);
    const g = Math.floor(Math.random() * 256);
    const b = Math.floor(Math.random() * 256);
    color = `rgba(${r}, ${g}, ${b}, 0.4)`;
  }

  const yBase = 360; // feste vertikale Mitte im SVG

  function createCircle(radius) {
    return Array.from({ length: 32 }, (_, i) => {
      const angle = (i / 32) * Math.PI * 2;
      return {
        x: 325 + Math.cos(angle) * radius,
        y: yBase + Math.sin(angle) * radius,
        z: 0
      };
    });
  }

  function createRectangle(width, height) {
    const halfW = width / 2, halfH = height / 2;
    return [
      { x: 325 - halfW, y: yBase - halfH, z: 0 },
      { x: 325 + halfW, y: yBase - halfH, z: 0 },
      { x: 325 + halfW, y: yBase + halfH, z: 0 },
      { x: 325 - halfW, y: yBase + halfH, z: 0 }
    ];
  }

  function createDiamond(size) {
    const h = size / 2;
    return [
      { x: 325, y: yBase - h, z: 0 },
      { x: 325 + h, y: yBase, z: 0 },
      { x: 325, y: yBase + h, z: 0 },
      { x: 325 - h, y: yBase, z: 0 }
    ];
  }

  function createCylinder(radius, height) {
    const halfH = height / 2;
    const steps = 32;
    return Array.from({ length: steps }, (_, i) => {
      const angle = (i / steps) * Math.PI * 2;
      return {
        x: 325 + Math.cos(angle) * radius,
        y: yBase + Math.sin(angle) * halfH,
        z: Math.sin(angle) * radius
      };
    });
  }

  function createPolygon(sides, radius) {
    return Array.from({ length: sides }, (_, i) => {
      const angle = (i / sides) * Math.PI * 2;
      return {
        x: 325 + Math.cos(angle) * radius,
        y: yBase + Math.sin(angle) * radius,
        z: 0
      };
    });
  }

  function createBarrelShape(topRadius, height) {
    const segments = 32;
    const curve = 0.3;
    return Array.from({ length: segments }, (_, i) => {
      const angle = (i / segments) * Math.PI * 2;
      const radius = topRadius * (1 + curve * Math.sin(angle));
      return {
        x: 325 + Math.cos(angle) * radius,
        y: yBase + Math.sin(angle) * radius,
        z: 0
      };
    });
  }

  function createSquishedCircle(topW, botW) {
    const points = [];
    const steps = 32;
    for (let i = 0; i < steps; i++) {
      const angle = (i / steps) * Math.PI;
      const w = i < steps / 2 ? topW : botW;
      points.push({
        x: 325 + Math.cos(angle) * w / 2,
        y: yBase + Math.sin(angle) * 80,
        z: 0
      });
    }
    return points;
  }

  function createWonkyHexagon() {
    const base = createPolygon(6, 100);
    return base.map((p, i) => {
      const wobble = i % 2 === 0 ? 1.1 : 0.9;
      return {
        x: 325 + (p.x - 325) * wobble,
        y: yBase + (p.y - yBase) * wobble,
        z: 0
      };
    });
  }

  function rotatePoint(p) {
    const cx = Math.cos(rotation.x * Math.PI / 180);
    const sx = Math.sin(rotation.x * Math.PI / 180);
    const cy = Math.cos(rotation.y * Math.PI / 180);
    const sy = Math.sin(rotation.y * Math.PI / 180);
    const cz = Math.cos(rotation.z * Math.PI / 180);
    const sz = Math.sin(rotation.z * Math.PI / 180);

    let y1 = p.y * cx - p.z * sx;
    let z1 = p.y * sx + p.z * cx;
    let x2 = p.x * cy + z1 * sy;
    let z2 = -p.x * sy + z1 * cy;
    let x3 = x2 * cz - y1 * sz;
    let y3 = x2 * sz + y1 * cz;

    return { x: x3, y: y3, z: z2 };
  }

  function project3DPoint(p) {
    const scale = perspective / (perspective + p.z);
    return {
      x: p.x * scale,
      y: p.y * scale
    };
  }

  function interpolate(start, end, t) {
    const points = [];
    const max = Math.max(start.length, end.length);
    for (let i = 0; i < max; i++) {
      const a = start[i % start.length];
      const b = end[i % end.length];
      const point = {
        x: a.x + (b.x - a.x) * t,
        y: a.y + (b.y - a.y) * t,
        z: a.z + (b.z - a.z) * t
      };
      points.push(project3DPoint(rotatePoint(point)));
    }
    return points;
  }

  function updateShape() {
    const from = shapes[Object.keys(shapes)[currentShape]];
    const to = shapes[Object.keys(shapes)[nextShape]];
    const points = interpolate(from, to, progress);
    pathString = `M ${points.map(p => `${p.x},${p.y}`).join(" L ")} Z`;
  }

  function getRandomShape() {
    const keys = Object.keys(shapes);
    const available = keys.map((_, i) => i).filter(i => !lastShapes.has(i));
    if (available.length === 0) {
      lastShapes.clear();
      return Math.floor(Math.random() * keys.length);
    }
    const selected = available[Math.floor(Math.random() * available.length)];
    lastShapes.add(selected);
    if (lastShapes.size > 5) lastShapes.delete([...lastShapes][0]);
    return selected;
  }

  function checkColorChange() {
    const now = Date.now();
    if (now - lastColorChangeTime >= colorChangeInterval) {
      updateColor();
      lastColorChangeTime = now;
    }
  }

  function animate() {
    progress += transitionSpeed;
    if (progress >= 1) {
      progress = 0;
      currentShape = nextShape;
      nextShape = getRandomShape();
    }
    updateShape();
    checkColorChange();
    animationFrame = requestAnimationFrame(animate);
  }

  onMount(() => {
    if (browser) {
      nextShape = getRandomShape();
      const initial = shapes[Object.keys(shapes)[currentShape]];
      const points = initial.map(p => project3DPoint(rotatePoint(p)));
      pathString = `M ${points.map(p => `${p.x},${p.y}`).join(" L ")} Z`;
      animate();
    }
  });

  onDestroy(() => {
    if (animationFrame) cancelAnimationFrame(animationFrame);
  });
</script>

<div class="blob">
  <svg width="100%" height="100%" viewBox="0 0 650 720">
    <defs>
      <filter id="inset-blur" x="-50%" y="-50%" width="200%" height="200%">
        <feGaussianBlur in="SourceAlpha" stdDeviation="10" result="blur" />
        <feComposite in="SourceAlpha" in2="blur" operator="out" result="inset" />
        <feFlood flood-color={color} result="flood" />
        <feComposite in="flood" in2="inset" operator="in" result="shadow" />
        <feComposite in="shadow" in2="SourceGraphic" operator="over" />
      </filter>
    </defs>
    <path d={pathString} fill="white" filter="url(#inset-blur)" stroke="none" />
  </svg>
</div>

<style>
  svg {
    display: block;
  }
  path {
    transition: all 0.3s ease;
  }
</style>
