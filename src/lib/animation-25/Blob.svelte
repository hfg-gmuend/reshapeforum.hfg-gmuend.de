<script>
    import { onMount, onDestroy } from "svelte";
    import { browser } from "$app/environment";
  
    export let animationSpeed = 1; // Overall animation speed
    export let mirrorY = false; // When true, mirrors the SVG on the y-axis
  
    // Utility to return a random number within a given range.
    function randomRange(min, max) {
      return Math.random() * (max - min) + min;
    }
  
    // Define all anchor and control points.
    // p0: M command, p1: first L, p2: second L,
    // p3, p4, p5: first cubic bezier (C) command (2 controls + end),
    // p6, p7, p8: second cubic bezier (C) command (2 controls + end),
    // p9: L command, p10: closing L command.
    const points = {
      p0: {
        base: { x: 0.000442982, y: 360 },
        amp: { x: randomRange(-5, 5), y: randomRange(-5, 5) },
        freq: { x: randomRange(0.001, 0.003), y: randomRange(0.001, 0.003) },
        phase: { x: randomRange(0, 2 * Math.PI), y: randomRange(0, 2 * Math.PI) }
      },
      p1: {
        base: { x: 0.000519753, y: -0.000088617 },
        amp: { x: randomRange(-5, 5), y: randomRange(-5, 5) },
        freq: { x: randomRange(0.001, 0.003), y: randomRange(0.001, 0.003) },
        phase: { x: randomRange(0, 2 * Math.PI), y: randomRange(0, 2 * Math.PI) }
      },
      p2: {
        base: { x: 329.462, y: 70.4869 },
        amp: { x: randomRange(-10, 10), y: randomRange(-10, 10) },
        freq: { x: randomRange(0.001, 0.003), y: randomRange(0.001, 0.003) },
        phase: { x: randomRange(0, 2 * Math.PI), y: randomRange(0, 2 * Math.PI) }
      },
      p3: {
        base: { x: 505.345, y: 108.117 },
        amp: { x: randomRange(-10, 10), y: randomRange(-10, 10) },
        freq: { x: randomRange(0.001, 0.003), y: randomRange(0.001, 0.003) },
        phase: { x: randomRange(0, 2 * Math.PI), y: randomRange(0, 2 * Math.PI) }
      },
      p4: {
        base: { x: 631, y: 263.304 },
        amp: { x: randomRange(-1, 1), y: randomRange(-1, 1) },
        freq: { x: randomRange(0.001, 0.003), y: randomRange(0.001, 0.003) },
        phase: { x: randomRange(0, 2 * Math.PI), y: randomRange(0, 2 * Math.PI) }
      },
      p5: {
        base: { x: 631, y: 442.895 },
        amp: { x: randomRange(-1, 1), y: randomRange(-1, 1) },
        freq: { x: randomRange(0.001, 0.003), y: randomRange(0.001, 0.003) },
        phase: { x: randomRange(0, 2 * Math.PI), y: randomRange(0, 2 * Math.PI) }
      },
      p6: {
        base: { x: 631, y: 525.783 },
        amp: { x: randomRange(-10, 10), y: randomRange(-10, 10) },
        freq: { x: randomRange(0.001, 0.003), y: randomRange(0.001, 0.003) },
        phase: { x: randomRange(0, 2 * Math.PI), y: randomRange(0, 2 * Math.PI) }
      },
      p7: {
        base: { x: 573.005, y: 597.408 },
        amp: { x: randomRange(-1, 1), y: randomRange(-1, 1) },
        freq: { x: randomRange(0.001, 0.003), y: randomRange(0.001, 0.003) },
        phase: { x: randomRange(0, 2 * Math.PI), y: randomRange(0, 2 * Math.PI) }
      },
      p8: {
        base: { x: 491.829, y: 614.775 },
        amp: { x: randomRange(-1, 1), y: randomRange(-1, 1) },
        freq: { x: randomRange(0.001, 0.003), y: randomRange(0.001, 0.003) },
        phase: { x: randomRange(0, 2 * Math.PI), y: randomRange(0, 2 * Math.PI) }
      },
      p9: {
        base: { x: 0.0000610352, y: 720 },
        amp: { x: randomRange(-5, 5), y: randomRange(-5, 5) },
        freq: { x: randomRange(0.001, 0.003), y: randomRange(0.001, 0.003) },
        phase: { x: randomRange(0, 2 * Math.PI), y: randomRange(0, 2 * Math.PI) }
      },
      p10: {
        base: { x: 0.000442982, y: 360 },
        amp: { x: randomRange(-5, 5), y: randomRange(-5, 5) },
        freq: { x: randomRange(0.001, 0.003), y: randomRange(0.001, 0.003) },
        phase: { x: randomRange(0, 2 * Math.PI), y: randomRange(0, 2 * Math.PI) }
      }
    };
  
    // Initialize our animated points at their base positions.
    let animatedPoints = {};
    for (const key in points) {
      animatedPoints[key] = { x: points[key].base.x, y: points[key].base.y };
    }
  
    // Build the path "d" attribute reactively.
    // Structure: M p0 L p1 L p2 C p3 p4 p5 C p6 p7 p8 L p9 L p10 Z
    $: pathD = `
      M${animatedPoints.p0.x.toFixed(2)} ${animatedPoints.p0.y.toFixed(2)}
      L${animatedPoints.p1.x.toFixed(2)} ${animatedPoints.p1.y.toFixed(2)}
      L${animatedPoints.p2.x.toFixed(2)} ${animatedPoints.p2.y.toFixed(2)}
      C${animatedPoints.p3.x.toFixed(2)} ${animatedPoints.p3.y.toFixed(2)}
       ${animatedPoints.p4.x.toFixed(2)} ${animatedPoints.p4.y.toFixed(2)}
       ${animatedPoints.p5.x.toFixed(2)} ${animatedPoints.p5.y.toFixed(2)}
      C${animatedPoints.p6.x.toFixed(2)} ${animatedPoints.p6.y.toFixed(2)}
       ${animatedPoints.p7.x.toFixed(2)} ${animatedPoints.p7.y.toFixed(2)}
       ${animatedPoints.p8.x.toFixed(2)} ${animatedPoints.p8.y.toFixed(2)}
      L${animatedPoints.p9.x.toFixed(2)} ${animatedPoints.p9.y.toFixed(2)}
      L${animatedPoints.p10.x.toFixed(2)} ${animatedPoints.p10.y.toFixed(2)}
      Z
    `;
  
    let animationFrame;
  
    // The animation loop updates all points using a sine function for smooth, fluid motion.
    function animate(timestamp) {
      const t = timestamp * animationSpeed;
      for (const key in points) {
        const pt = points[key];
        animatedPoints[key].x = pt.base.x + pt.amp.x * Math.sin(pt.freq.x * t + pt.phase.x);
        animatedPoints[key].y = pt.base.y + pt.amp.y * Math.sin(pt.freq.y * t + pt.phase.y);
      }
      animationFrame = requestAnimationFrame(animate);
    }
  
    onMount(() => {
      if (!browser) return;
      animationFrame = requestAnimationFrame(animate);
    });
  
    onDestroy(() => {
      if (!browser) return;
      cancelAnimationFrame(animationFrame);
    });
  </script>
  
  <svg
    class="w-full"
    width="650"
    height="720"
    viewBox="0 0 650 720"
    fill="none"
    xmlns="http://www.w3.org/2000/svg"
  >
    <!-- Wrap the animated content in a group to conditionally apply mirroring -->
    <g transform={mirrorY ? "translate(650,0) scale(-1,1)" : ""}>
      <path d={pathD} fill="url(#paint0_linear)" />
    </g>
    <defs>
      <linearGradient
        id="paint0_linear"
        x1="0"
        y1="360"
        x2="650"
        y2="360"
        gradientUnits="userSpaceOnUse"
      >
        <stop stop-color="white" />
        <stop offset="1" stop-color="#6D59A2" />
      </linearGradient>
    </defs>
  </svg>
  