<script>
  import { onMount, onDestroy, afterUpdate } from "svelte";
  import { browser } from "$app/environment";

  export let backgroundColor = "transparent";
  export let shapeColor = "#9A86D9"; 
  export let autoRotate = true;
  export let morphSpeed = 0.005;
  export let scale = 1.0; 
  export let highResolution = true; 
  export let superSample = 2.0; 
  export let performanceMode = true;
  export let ultraHD = false; // New parameter to enable even higher resolution
  export let lineColor = "#FFFFFF";  // New parameter for stroke color
  export let backgroundSvg = ''; // URL to SVG instead of SVG content
  
  // Canvas setup - smaller base size for more control
  let canvas;
  let ctx;
  let width = 300; 
  let height = 300; 
  let depth = 600;
  let isInitialized = false;
  let isRunning = false;
  
  // Performance monitoring variables
  let frameCount = 0;
  let lastFrameTime = 0;
  let frameTimes = [];
  let lowPerformanceMode = false;
  
  // Animation state
  let animationFrame;
  let angle = 0;
  let currentShape = 0;
  let nextShape = 1;
  let morphProgress = 0;
  let rotation = { x: 0, y: 0, z: 0 };
  
  // Light settings
  const lightDirection = { x: 0.5, y: -0.5, z: -1 };
  const ambientLight = 0.65; // Increased from 0.6 to 0.8 for brighter ambient light
  
  // Error tracking
  let errorCount = 0;
  const MAX_ERRORS = 5;
  let lastErrorTime = 0;
  
  // Shape transition tracking
  let lastTransitionTime = 0;
  let shapesInitialized = false;
  
  // Available shapes - ensure stable and consistent data
  const shapes = [
    { name: "cube", vertices: [], generate: generateCube, duration: 3000 },
    { name: "sphere", vertices: [], generate: generateSphere, duration: 3000 },
    { name: "octahedron", vertices: [], generate: generateOctahedron, duration: 3000 },
    { name: "icosahedron", vertices: [], generate: generateIcosahedron, duration: 3000 },
    { name: "pyramid", vertices: [], generate: generatePyramid, duration: 3000 },
    { name: "triangularPrism", vertices: [], generate: generateTriangularPrism, duration: 3000 },
    { name: "diamond", vertices: [], generate: generateDiamond, duration: 3000 }
  ];
  
  // Generate vertices for each shape
  function initShapes() {
    try {
      const size = 120;
      
      shapes.forEach(shape => {
        if (shape.name === "cube") {
          shape.vertices = generateCube(size);
        } else if (shape.name === "sphere") {
          shape.vertices = generateSphere(size / 2);
        } else if (shape.name === "octahedron") {
          shape.vertices = generateOctahedron(size);
        } else if (shape.name === "icosahedron") {
          shape.vertices = generateIcosahedron(size);
        } else if (shape.name === "pyramid") {
          shape.vertices = generatePyramid(size);
        } else if (shape.name === "triangularPrism") {
          shape.vertices = generateTriangularPrism(size);
        } else if (shape.name === "diamond") {
          shape.vertices = generateDiamond(size);
        }
      });
      
      if (shapes.some(shape => !shape.vertices || shape.vertices.length === 0)) {
        throw new Error("One or more shapes have no vertices");
      }
      
      isInitialized = true;
      shapesInitialized = true;
      console.log("All shapes initialized successfully");
    } catch (error) {
      console.error("Error initializing shapes:", error);
      isInitialized = false;
      shapesInitialized = false;
      setTimeout(initShapes, 500);
    }
  }
  
  // Generate cube vertices
  function generateCube(size) {
    const half = size / 2;
    const vertices = [];
    
    addQuad(vertices, 
      [-half, -half, half], [half, -half, half], 
      [half, half, half], [-half, half, half], 
      [0, 0, 1]
    );
    
    addQuad(vertices, 
      [half, -half, -half], [-half, -half, -half], 
      [-half, half, -half], [half, half, -half], 
      [0, 0, -1]
    );
    
    addQuad(vertices, 
      [-half, half, half], [half, half, half], 
      [half, half, -half], [-half, half, -half], 
      [0, 1, 0]
    );
    
    addQuad(vertices, 
      [-half, -half, -half], [half, -half, -half], 
      [half, -half, half], [-half, -half, half], 
      [0, -1, 0]
    );
    
    addQuad(vertices, 
      [half, -half, half], [half, -half, -half], 
      [half, half, -half], [half, half, half], 
      [1, 0, 0]
    );
    
    addQuad(vertices, 
      [-half, -half, -half], [-half, -half, half], 
      [-half, half, half], [-half, half, -half], 
      [-1, 0, 0]
    );
    
    return vertices;
  }
  
  // Generate sphere vertices - optimize segments based on performance mode
  function generateSphere(radius, segments = performanceMode ? 12 : 16) {
    const vertices = [];
    
    for (let lat = 0; lat < segments; lat++) {
      const theta = (lat * Math.PI) / segments;
      const sinTheta = Math.sin(theta);
      const cosTheta = Math.cos(theta);
      
      for (let lon = 0; lon < segments; lon++) {
        const phi = (lon * 2 * Math.PI) / segments;
        const sinPhi = Math.sin(phi);
        const cosPhi = Math.cos(phi);
        
        const x = cosPhi * sinTheta;
        const y = cosTheta;
        const z = sinPhi * sinTheta;
        
        const nextLat = (lat + 1) % segments;
        const nextLon = (lon + 1) % segments;
        
        const theta2 = (nextLat * Math.PI) / segments;
        const sinTheta2 = Math.sin(theta2);
        const cosTheta2 = Math.cos(theta2);
        
        vertices.push({
          x: radius * x, y: radius * y, z: radius * z,
          nx: x, ny: y, nz: z
        });
        
        vertices.push({
          x: radius * cosPhi * sinTheta2, 
          y: radius * cosTheta2, 
          z: radius * sinPhi * sinTheta2,
          nx: cosPhi * sinTheta2, 
          ny: cosTheta2, 
          nz: sinPhi * sinTheta2
        });
        
        vertices.push({
          x: radius * Math.sin((nextLon * 2 * Math.PI) / segments) * sinTheta, 
          y: radius * cosTheta, 
          z: radius * Math.cos((nextLon * 2 * Math.PI) / segments) * sinTheta,
          nx: Math.sin((nextLon * 2 * Math.PI) / segments) * sinTheta, 
          ny: cosTheta, 
          nz: Math.cos((nextLon * 2 * Math.PI) / segments) * sinTheta
        });
        
        vertices.push({
          x: radius * Math.sin((nextLon * 2 * Math.PI) / segments) * sinTheta, 
          y: radius * cosTheta, 
          z: radius * Math.cos((nextLon * 2 * Math.PI) / segments) * sinTheta,
          nx: Math.sin((nextLon * 2 * Math.PI) / segments) * sinTheta, 
          ny: cosTheta, 
          nz: Math.cos((nextLon * 2 * Math.PI) / segments) * sinTheta
        });
        
        vertices.push({
          x: radius * cosPhi * sinTheta2, 
          y: radius * cosTheta2, 
          z: radius * sinPhi * sinTheta2,
          nx: cosPhi * sinTheta2, 
          ny: cosTheta2, 
          nz: sinPhi * sinTheta2
        });
        
        vertices.push({
          x: radius * Math.sin((nextLon * 2 * Math.PI) / segments) * sinTheta2, 
          y: radius * cosTheta2, 
          z: radius * Math.cos((nextLon * 2 * Math.PI) / segments) * sinTheta2,
          nx: Math.sin((nextLon * 2 * Math.PI) / segments) * sinTheta2, 
          ny: cosTheta2, 
          nz: Math.cos((nextLon * 2 * Math.PI) / segments) * sinTheta2
        });
      }
    }
    
    return vertices;
  }
  
  // Generate pyramid vertices
  function generatePyramid(size) {
    const vertices = [];
    const half = size / 2;
    const height = size * 0.8;
    
    // Base vertices
    const baseVertices = [
      [-half, -half / 2, -half / 2],
      [half, -half / 2, -half / 2],
      [half, -half / 2, half / 2],
      [-half, -half / 2, half / 2]
    ];

    // Apex
    const apex = [0, height / 2, 0];

    // Add triangular faces
    for (let i = 0; i < 4; i++) {
      const v1 = baseVertices[i];
      const v2 = baseVertices[(i + 1) % 4];
      const normal = calculateNormal(v1, v2, apex);
      addTriangle(vertices, v1, v2, apex, normal);
    }

    // Add base
    addQuad(vertices,
      baseVertices[0], baseVertices[1],
      baseVertices[2], baseVertices[3],
      [0, -1, 0]
    );

    return vertices;
  }

  // Generate triangular prism vertices
  function generateTriangularPrism(size) {
    const vertices = [];
    const half = size / 2;
    const height = size * 0.8;
    
    // Front face vertices
    const frontVertices = [
      [-half, -height / 2, half / 2],
      [half, -height / 2, half / 2],
      [0, height / 2, half / 2]
    ];

    // Back face vertices
    const backVertices = [
      [-half, -height / 2, -half / 2],
      [half, -height / 2, -half / 2],
      [0, height / 2, -half / 2]
    ];

    // Add front and back faces
    addTriangle(vertices, ...frontVertices, [0, 0, 1]);
    addTriangle(vertices, ...backVertices, [0, 0, -1]);

    // Add side faces
    for (let i = 0; i < 3; i++) {
      const v1 = frontVertices[i];
      const v2 = frontVertices[(i + 1) % 3];
      const v3 = backVertices[i];
      const v4 = backVertices[(i + 1) % 3];
      
      const normal = calculateNormal(v1, v2, v3);
      addQuad(vertices, v1, v2, v4, v3, normal);
    }

    return vertices;
  }

  // Generate diamond vertices
  function generateDiamond(size) {
    const vertices = [];
    const half = size / 2;
    const height = size * 0.7;
    
    // Middle vertices
    const middleVertices = [
      [-half / 2, 0, -half / 2],
      [half / 2, 0, -half / 2],
      [half / 2, 0, half / 2],
      [-half / 2, 0, half / 2]
    ];

    // Top and bottom points
    const top = [0, height / 2, 0];
    const bottom = [0, -height / 2, 0];

    // Add top faces
    for (let i = 0; i < 4; i++) {
      const v1 = middleVertices[i];
      const v2 = middleVertices[(i + 1) % 4];
      const normal = calculateNormal(v1, v2, top);
      addTriangle(vertices, v1, v2, top, normal);
    }

    // Add bottom faces
    for (let i = 0; i < 4; i++) {
      const v1 = middleVertices[i];
      const v2 = middleVertices[(i + 1) % 4];
      const normal = calculateNormal(v1, v2, bottom);
      addTriangle(vertices, v2, v1, bottom, normal);
    }

    return vertices;
  }

  // Generate torus vertices
  function generateTorus(radius, tubeRadius, radialSegments = 16, tubularSegments = 80) {
    const vertices = [];
    
    for (let i = 0; i < radialSegments; i++) {
      for (let j = 0; j < tubularSegments; j++) {
        const u = (i / radialSegments) * Math.PI * 2;
        const v = (j / tubularSegments) * Math.PI * 2;
        
        const centerX = radius * Math.cos(u);
        const centerY = radius * Math.sin(u);
        const centerZ = 0;
        
        const x = (radius + tubeRadius * Math.cos(v)) * Math.cos(u);
        const y = (radius + tubeRadius * Math.cos(v)) * Math.sin(u);
        const z = tubeRadius * Math.sin(v);
        
        const nx = Math.cos(u) * Math.cos(v);
        const ny = Math.sin(u) * Math.cos(v);
        const nz = Math.sin(v);
        
        const nextI = (i + 1) % radialSegments;
        const nextJ = (j + 1) % tubularSegments;
        
        const u2 = (nextI / radialSegments) * Math.PI * 2;
        const v2 = (nextJ / tubularSegments) * Math.PI * 2;
        
        vertices.push({ x, y, z, nx, ny, nz });
        
        vertices.push({
          x: (radius + tubeRadius * Math.cos(v)) * Math.cos(u2),
          y: (radius + tubeRadius * Math.cos(v)) * Math.sin(u2),
          z: tubeRadius * Math.sin(v),
          nx: Math.cos(u2) * Math.cos(v),
          ny: Math.sin(u2) * Math.cos(v),
          nz: Math.sin(v)
        });
        
        vertices.push({
          x: (radius + tubeRadius * Math.cos(v2)) * Math.cos(u),
          y: (radius + tubeRadius * Math.cos(v2)) * Math.sin(u),
          z: tubeRadius * Math.sin(v2),
          nx: Math.cos(u) * Math.cos(v2),
          ny: Math.sin(u) * Math.cos(v2),
          nz: Math.sin(v2)
        });
        
        vertices.push({
          x: (radius + tubeRadius * Math.cos(v2)) * Math.cos(u),
          y: (radius + tubeRadius * Math.cos(v2)) * Math.sin(u),
          z: tubeRadius * Math.sin(v2),
          nx: Math.cos(u) * Math.cos(v2),
          ny: Math.sin(u) * Math.cos(v2),
          nz: Math.sin(v2)
        });
        
        vertices.push({
          x: (radius + tubeRadius * Math.cos(v)) * Math.cos(u2),
          y: (radius + tubeRadius * Math.cos(v)) * Math.sin(u2),
          z: tubeRadius * Math.sin(v),
          nx: Math.cos(u2) * Math.cos(v),
          ny: Math.sin(u2) * Math.cos(v),
          nz: Math.sin(v)
        });
        
        vertices.push({
          x: (radius + tubeRadius * Math.cos(v2)) * Math.cos(u2),
          y: (radius + tubeRadius * Math.cos(v2)) * Math.sin(u2),
          z: tubeRadius * Math.sin(v2),
          nx: Math.cos(u2) * Math.cos(v2),
          ny: Math.sin(u2) * Math.cos(v2),
          nz: Math.sin(v2)
        });
      }
    }
    
    return vertices;
  }
  
  // Generate octahedron vertices
  function generateOctahedron(size) {
    const vertices = [];
    const s = size / 2;
    
    const v = [
      [0, s, 0],   // top
      [0, -s, 0],  // bottom
      [s, 0, 0],   // right
      [-s, 0, 0],  // left
      [0, 0, s],   // front
      [0, 0, -s]   // back
    ];
    
    addTriangle(vertices, v[0], v[2], v[4], normalizeVector([1, 1, 1]));
    addTriangle(vertices, v[0], v[4], v[3], normalizeVector([-1, 1, 1]));
    addTriangle(vertices, v[0], v[3], v[5], normalizeVector([-1, 1, -1]));
    addTriangle(vertices, v[0], v[5], v[2], normalizeVector([1, 1, -1]));
    
    addTriangle(vertices, v[1], v[4], v[2], normalizeVector([1, -1, 1]));
    addTriangle(vertices, v[1], v[3], v[4], normalizeVector([-1, -1, 1]));
    addTriangle(vertices, v[1], v[5], v[3], normalizeVector([-1, -1, -1]));
    addTriangle(vertices, v[1], v[2], v[5], normalizeVector([1, -1, -1]));
    
    return vertices;
  }
  
  // Generate icosahedron vertices
  function generateIcosahedron(size) {
    const vertices = [];
    const phi = (1 + Math.sqrt(5)) / 2;
    const scale = size / 2;
    
    const v = [
      [-1, phi, 0], [1, phi, 0], [-1, -phi, 0], [1, -phi, 0],
      [0, -1, phi], [0, 1, phi], [0, -1, -phi], [0, 1, -phi],
      [phi, 0, -1], [phi, 0, 1], [-phi, 0, -1], [-phi, 0, 1]
    ].map(p => p.map(coord => coord * scale));
    
    const faces = [
      [0, 11, 5], [0, 5, 1], [0, 1, 7], [0, 7, 10], [0, 10, 11],
      [1, 5, 9], [5, 11, 4], [11, 10, 2], [10, 7, 6], [7, 1, 8],
      [3, 9, 4], [3, 4, 2], [3, 2, 6], [3, 6, 8], [3, 8, 9],
      [4, 9, 5], [2, 4, 11], [6, 2, 10], [8, 6, 7], [9, 8, 1]
    ];
    
    for (const face of faces) {
      const v1 = v[face[0]];
      const v2 = v[face[1]];
      const v3 = v[face[2]];
      
      const normal = calculateNormal(v1, v2, v3);
      
      vertices.push({
        x: v1[0], y: v1[1], z: v1[2],
        nx: normal[0], ny: normal[1], nz: normal[2]
      });
      vertices.push({
        x: v2[0], y: v2[1], z: v2[2],
        nx: normal[0], ny: normal[1], nz: normal[2]
      });
      vertices.push({
        x: v3[0], y: v3[1], z: v3[2],
        nx: normal[0], ny: normal[1], nz: normal[2]
      });
    }
    
    return vertices;
  }
  
  // Helper function to add a quad
  function addQuad(vertices, p1, p2, p3, p4, normal) {
    vertices.push({
      x: p1[0], y: p1[1], z: p1[2],
      nx: normal[0], ny: normal[1], nz: normal[2]
    });
    vertices.push({
      x: p2[0], y: p2[1], z: p2[2],
      nx: normal[0], ny: normal[1], nz: normal[2]
    });
    vertices.push({
      x: p3[0], y: p3[1], z: p3[2],
      nx: normal[0], ny: normal[1], nz: normal[2]
    });
    
    vertices.push({
      x: p1[0], y: p1[1], z: p1[2],
      nx: normal[0], ny: normal[1], nz: normal[2]
    });
    vertices.push({
      x: p3[0], y: p3[1], z: p3[2],
      nx: normal[0], ny: normal[1], nz: normal[2]
    });
    vertices.push({
      x: p4[0], y: p4[1], z: p4[2],
      nx: normal[0], ny: normal[1], nz: normal[2]
    });
  }
  
  // Helper function to add a triangle
  function addTriangle(vertices, p1, p2, p3, normal) {
    if (!normal) {
      normal = calculateNormal(p1, p2, p3);
    }
    
    vertices.push({
      x: p1[0], y: p1[1], z: p1[2],
      nx: normal[0], ny: normal[1], nz: normal[2]
    });
    vertices.push({
      x: p2[0], y: p2[1], z: p2[2],
      nx: normal[0], ny: normal[1], nz: normal[2]
    });
    vertices.push({
      x: p3[0], y: p3[1], z: p3[2],
      nx: normal[0], ny: normal[1], nz: normal[2]
    });
  }
  
  // Calculate normal for a triangle
  function calculateNormal(p1, p2, p3) {
    const v1 = [p2[0] - p1[0], p2[1] - p1[1], p2[2] - p1[2]];
    const v2 = [p3[0] - p1[0], p3[1] - p1[1], p3[2] - p1[2]];
    
    const normal = [
      v1[1] * v2[2] - v1[2] * v2[1],
      v1[2] * v2[0] - v1[0] * v2[2],
      v1[0] * v2[1] - v1[1] * v2[0]
    ];
    
    return normalizeVector(normal);
  }
  
  // Normalize a vector
  function normalizeVector(v) {
    const length = Math.sqrt(v[0] * v[0] + v[1] * v[1] + v[2] * v[2]);
    return [v[0] / length, v[1] / length, v[2] / length];
  }
  
  // Normalize vector object
  function normalize(vector) {
    const length = Math.sqrt(vector.x * vector.x + vector.y * vector.y + vector.z * vector.z);
    if (length === 0) {
      return { x: 0, y: 0, z: 0 };
    }
    return {
      x: vector.x / length,
      y: vector.y / length,
      z: vector.z / length
    };
  }
  
  // Calculate dot product
  function dotProduct(v1, v2) {
    return v1.x * v2.x + v1.y * v2.y + v1.z * v2.z;
  }
  
  // Interpolate between two shapes - optimized
  function interpolateShapes(shape1, shape2, t) {
    try {
      if (!shape1 || !shape1.length || !shape2 || !shape2.length) {
        console.warn("Empty shape encountered in interpolation");
        
        if (shape1 && shape1.length) return [...shape1];
        if (shape2 && shape2.length) return [...shape2];
        
        throw new Error("Both shapes are empty");
      }
      
      const maxVertices = lowPerformanceMode ? 200 : 1000;
      const maxLength = Math.min(Math.max(shape1.length, shape2.length), maxVertices);
      const result = [];
      
      const stride = lowPerformanceMode ? 3 : 1;
      
      for (let i = 0; i < maxLength; i += stride) {
        const v1 = shape1[i % shape1.length];
        const v2 = shape2[i % shape2.length];
        
        if (!v1 || !v2) continue;
        
        const ease = t < 0.5 ? 4 * t * t * t : 1 - Math.pow(-2 * t + 2, 3) / 2;
        
        result.push({
          x: v1.x + (v2.x - v1.x) * ease,
          y: v1.y + (v2.y - v1.y) * ease,
          z: v1.z + (v2.z - v1.z) * ease,
          nx: v1.nx + (v2.nx - v1.nx) * ease,
          ny: v1.ny + (v2.ny - v1.ny) * ease,
          nz: v1.nz + (v2.nz - v1.nz) * ease
        });
      }
      
      return result;
    } catch (error) {
      console.error("Error in interpolation:", error);
      errorCount++;
      
      if (errorCount > 2) {
        console.warn("Reinitializing shapes due to interpolation errors");
        initShapes();
        errorCount = 0;
      }
      
      return shape1 && shape1.length ? [...shape1] : (shape2 && shape2.length ? [...shape2] : []);
    }
  }
  
  // Transform and project 3D points to 2D - optimized
  function transformPoints(points) {
    try {
      const radX = (rotation.x * Math.PI) / 180;
      const radY = (rotation.y * Math.PI) / 180;
      const radZ = (rotation.z * Math.PI) / 180;
      
      const cosX = Math.cos(radX);
      const sinX = Math.sin(radX);
      const cosY = Math.cos(radY);
      const sinY = Math.sin(radY);
      const cosZ = Math.cos(radZ);
      const sinZ = Math.sin(radZ);
      
      const transformed = [];
      
      const stride = lowPerformanceMode ? 3 : 1;
      
      for (let i = 0; i < points.length; i += 3 * stride) {
        if (i + 2 >= points.length) continue;
        
        const triangle = [];
        
        for (let j = 0; j < 3; j++) {
          const p = points[i + j];
          
          let y1 = p.y * cosX - p.z * sinX;
          let z1 = p.y * sinX + p.z * cosX;
          
          let x2 = p.x * cosY + z1 * sinY;
          let z2 = -p.x * sinY + z1 * cosY;
          
          let x3 = x2 * cosZ - y1 * sinZ;
          let y3 = x2 * sinZ + y1 * cosZ;
          
          const denominator = depth + z2;
          const scaleFactor = denominator !== 0 ? depth / denominator : 1;
          const x = width / 2 + x3 * scaleFactor;
          const y = height / 2 + y3 * scaleFactor;
          
          let nx, ny, nz;
          
          if (!lowPerformanceMode) {
            let nx1 = p.nx * cosX - p.nz * sinX;
            let nz1 = p.nx * sinX + p.nz * cosX;
            
            let nx2 = p.nx * cosY + nz1 * sinY;
            let nz2 = -p.nx * sinY + nz1 * cosY;
            
            nx = nx2 * cosZ - nx1 * sinZ;
            ny = nx2 * sinZ + nx1 * cosZ;
            nz = nz2;
          } else {
            nx = p.nx;
            ny = p.ny;
            nz = p.nz;
          }
          
          triangle.push({
            x, y, z: z2,
            nx, ny, nz
          });
        }
        
        if (triangle.length === 3) {
          transformed.push(triangle);
        }
      }
      
      if (!lowPerformanceMode) {
        transformed.sort((a, b) => {
          const z1 = (a[0].z + a[1].z + a[2].z) / 3;
          const z2 = (b[0].z + b[1].z + b[2].z) / 3;
          return z2 - z1;
        });
      }
      
      return transformed;
    } catch (error) {
      console.error("Error transforming points:", error);
      return [];
    }
  }
  
  // Update opacity control with smoother transitions
  let shapeOpacity = 0.9; // Reduced initial opacity
  let opacityDirection = -1;
  let opacitySpeed = 0.003; // Slower opacity changes
  let opacityMin = 0.6; // Higher minimum opacity
  let opacityMax = 0.9; // Lower maximum opacity

  function updateOpacity() {
    shapeOpacity += opacityDirection * opacitySpeed;
    
    if (shapeOpacity <= opacityMin) {
      shapeOpacity = opacityMin;
      opacityDirection = 1;
    } else if (shapeOpacity >= opacityMax) {
      shapeOpacity = opacityMax;
      opacityDirection = -1;
    }
  }
  
  // Render the current state - with performance optimizations
  function render() {
    try {
      if (!ctx || !isInitialized) return;
      
      const frameStartTime = performance.now();
      
      ctx.clearRect(0, 0, width, height);
      
      if (backgroundColor !== "transparent") {
        ctx.fillStyle = backgroundColor;
        ctx.fillRect(0, 0, width, height);
      }
      
      if (!shapes || shapes.length < 2) {
        console.error("Shapes array is invalid");
        initShapes();
        return;
      }
      
      if (currentShape < 0 || currentShape >= shapes.length || 
          nextShape < 0 || nextShape >= shapes.length) {
        console.error("Invalid shape indices", currentShape, nextShape);
        currentShape = 0;
        nextShape = 1;
      }
      
      const currentVertices = shapes[currentShape].vertices;
      const nextVertices = shapes[nextShape].vertices;
      
      if (!currentVertices || !currentVertices.length || 
          !nextVertices || !nextVertices.length) {
        console.warn("Missing vertices, reinitializing shapes");
        initShapes();
        return;
      }
      
      const interpolated = interpolateShapes(currentVertices, nextVertices, morphProgress);
      if (!interpolated || !interpolated.length) {
        console.warn("Interpolation failed to produce vertices");
        return;
      }
      
      const transformed = transformPoints(interpolated);
      if (!transformed || transformed.length === 0) {
        console.warn("Transformation failed to produce points");
        return;
      }
      
      updateOpacity();
      
      for (const triangle of transformed) {
        if (!triangle || triangle.length !== 3) continue;
        
        let lightIntensity = ambientLight;
        
        if (!lowPerformanceMode) {
          const normal = {
            x: (triangle[0].nx + triangle[1].nx + triangle[2].nx) / 3,
            y: (triangle[0].ny + triangle[1].ny + triangle[2].ny) / 3,
            z: (triangle[0].nz + triangle[1].nz + triangle[2].nz) / 3
          };
          
          const normalizedLight = normalize(lightDirection);
          lightIntensity = dotProduct(normal, normalizedLight);
          lightIntensity = Math.max(0.2, -lightIntensity); // Added minimum light level
          lightIntensity = ambientLight + (1.2 - ambientLight) * lightIntensity; // Increased multiplier
        }
        
        let r = 232, g = 224, b = 255; // Default to lighter purple (#E8E0FF)
        try {
          if (shapeColor.startsWith('#') && shapeColor.length >= 7) {
            r = parseInt(shapeColor.slice(1, 3), 16);
            g = parseInt(shapeColor.slice(3, 5), 16);
            b = parseInt(shapeColor.slice(5, 7), 16);
          }
        } catch (e) {
          console.warn("Invalid color format, using default");
        }
        
        // Increase brightness boost to 30%
        const brightnessBoost = 1.3;
        const shadedR = Math.min(255, Math.floor(r * (lightIntensity * brightnessBoost)));
        const shadedG = Math.min(255, Math.floor(g * (lightIntensity * brightnessBoost)));
        const shadedB = Math.min(255, Math.floor(b * (lightIntensity * brightnessBoost)));
        
        // Add a dynamic alpha based on shapeOpacity and lightIntensity
        const dynamicAlpha = shapeOpacity * (0.7 + (lightIntensity * 0.3));
        const shadedColor = `rgba(${shadedR}, ${shadedG}, ${shadedB}, ${dynamicAlpha})`;
        
        ctx.beginPath();
        ctx.moveTo(triangle[0].x, triangle[0].y);
        ctx.lineTo(triangle[1].x, triangle[1].y);
        ctx.lineTo(triangle[2].x, triangle[2].y);
        ctx.closePath();
        
        ctx.fillStyle = shadedColor;
        ctx.fill();
        
        if (!lowPerformanceMode) {
          // Add smoother line opacity transitions
          const lineOpacity = Math.min(0.4, dynamicAlpha * 0.5);
          ctx.strokeStyle = `${lineColor}${Math.floor(lineOpacity * 255).toString(16).padStart(2, '0')}`;
          ctx.lineWidth = ultraHD ? 0.15 : (highResolution ? 0.2 : 0.4);
          ctx.lineJoin = 'round';
          ctx.stroke();
        }
      }
      
      const frameTime = performance.now() - frameStartTime;
      frameTimes.push(frameTime);
      
      if (frameTimes.length > 30) {
        frameTimes.shift();
      }
      
      if (frameTimes.length >= 10) {
        const avgFrameTime = frameTimes.reduce((a, b) => a + b, 0) / frameTimes.length;
        
        if (avgFrameTime > 25 && !lowPerformanceMode) {
          console.log("Enabling low performance mode - avg frame time:", avgFrameTime.toFixed(2), "ms");
          lowPerformanceMode = true;
        } 
        else if (avgFrameTime < 10 && lowPerformanceMode) {
          console.log("Disabling low performance mode - avg frame time:", avgFrameTime.toFixed(2), "ms");
          lowPerformanceMode = false;
        }
      }
      
      errorCount = 0;
    } catch (error) {
      const now = Date.now();
      if (now - lastErrorTime > 1000) {
        errorCount++;
        lastErrorTime = now;
        console.error("Error rendering:", error);
        
        if (errorCount > MAX_ERRORS) {
          console.warn("Too many errors, reinitializing animation...");
          restartAnimation();
        }
      }
    }
  }
  
  // Animation loop with improved timing
  function animate(timestamp) {
    if (!isRunning) return;
    
    try {
      const elapsed = timestamp - lastFrameTime;
      
      if (performanceMode && elapsed < 16) {
        animationFrame = requestAnimationFrame(animate);
        return;
      }
      
      lastFrameTime = timestamp;
      
      morphProgress += morphSpeed;
      
      if (morphProgress >= 1) {
        morphProgress = 0;
        const now = Date.now();
        
        lastTransitionTime = now;
        
        currentShape = nextShape;
        nextShape = (nextShape + 1) % shapes.length;
        
        if (currentShape >= shapes.length) currentShape = 0;
        if (nextShape >= shapes.length) nextShape = 0;
      }
      
      if (autoRotate) {
        rotation.y += lowPerformanceMode ? 0.8 : 0.4;
        rotation.x = 12 * Math.sin(rotation.y * Math.PI / 180);
      }
      
      render();
      
      animationFrame = requestAnimationFrame(animate);
    } catch (error) {
      console.error("Error in animation loop:", error);
      restartAnimation();
    }
  }
  
  function initializeCanvas() {
    if (!canvas || !browser) return;
    
    try {
      ctx = canvas.getContext('2d', { 
        alpha: true, 
        antialias: !performanceMode, 
        desynchronized: true,
        willReadFrequently: false
      });
      
      if (!ctx) {
        throw new Error("Failed to get canvas context");
      }
      
      const devicePixelRatio = window.devicePixelRatio || 1;
      
      // Calculate a higher resolution multiplier with ultraHD option
      const maxMultiplier = ultraHD ? 
        Math.min(devicePixelRatio * superSample * 1.5, 6) : // Ultra HD mode
        performanceMode ? 
          Math.min(devicePixelRatio, 2) : 
          Math.min(devicePixelRatio * superSample, 4);
      
      const resolutionMultiplier = highResolution ? maxMultiplier : devicePixelRatio;
      
      // Set canvas dimensions with enhanced resolution
      canvas.width = width * resolutionMultiplier;
      canvas.height = height * resolutionMultiplier;
      
      const container = canvas.parentElement;
      if (container) {
        const containerStyle = window.getComputedStyle(container);
        const containerWidth = parseFloat(containerStyle.width);
        const containerHeight = parseFloat(containerStyle.height);
        
        const calculatedScale = Math.max(
          containerWidth / width,
          containerHeight / height
        ) * scale;
        
        canvas.style.width = `${width * calculatedScale}px`;
        canvas.style.height = `${height * calculatedScale}px`;
        
        canvas.style.position = "absolute";
        canvas.style.left = "50%";
        canvas.style.top = "50%";
        canvas.style.transform = "translate(-50%, -50%)";
      } else {
        canvas.style.width = `${width * scale}px`;
        canvas.style.height = `${height * scale}px`;
      }
      
      // Scale context according to the resolution multiplier
      ctx.scale(resolutionMultiplier, resolutionMultiplier);
      
      // Configure image smoothing based on quality settings
      ctx.imageSmoothingEnabled = !performanceMode || !lowPerformanceMode;
      if ((!performanceMode && !lowPerformanceMode) || ultraHD) {
        ctx.imageSmoothingQuality = 'high';
      }
      
      if (!isInitialized) {
        initShapes();
      }
      
      const handleResize = () => {
        if (!canvas || !ctx) return;
        
        const newDevicePixelRatio = window.devicePixelRatio || 1;
        const newResolutionMultiplier = ultraHD ?
          Math.min(newDevicePixelRatio * superSample * 1.5, 6) :
          highResolution ? 
            Math.min(newDevicePixelRatio * superSample, 6) : newDevicePixelRatio;
        
        canvas.width = width * newResolutionMultiplier;
        canvas.height = height * newResolutionMultiplier;
        
        const container = canvas.parentElement;
        if (container) {
          const containerStyle = window.getComputedStyle(container);
          const containerWidth = parseFloat(containerStyle.width);
          const containerHeight = parseFloat(containerStyle.height);
          
          const calculatedScale = Math.max(
            containerWidth / width,
            containerHeight / height
          ) * scale;
          
          canvas.style.width = `${width * calculatedScale}px`;
          canvas.style.height = `${height * calculatedScale}px`;
        } else {
          canvas.style.width = `${width * scale}px`;
          canvas.style.height = `${height * scale}px`;
        }
        
        ctx.scale(newResolutionMultiplier, newResolutionMultiplier);
        ctx.imageSmoothingEnabled = true;
        ctx.imageSmoothingQuality = 'high';
      };
      
      window.addEventListener('resize', handleResize);
      
      return () => {
        window.removeEventListener('resize', handleResize);
      };
    } catch (error) {
      console.error("Error initializing canvas:", error);
      setTimeout(initializeCanvas, 500);
    }
  }
  
  function startAnimation() {
    if (isRunning) return;
    isRunning = true;
    animationFrame = requestAnimationFrame(animate);
  }
  
  function stopAnimation() {
    isRunning = false;
    if (animationFrame) {
      cancelAnimationFrame(animationFrame);
      animationFrame = null;
    }
  }
  
  function restartAnimation() {
    stopAnimation();
    
    morphProgress = 0;
    currentShape = 0;
    nextShape = 1;
    errorCount = 0;
    
    setTimeout(() => {
      if (!shapesInitialized) {
        initShapes();
      }
      
      startAnimation();
    }, 200);
  }
  
  let lastVisibilityChange = 0;
  function handleVisibilityChange() {
    const now = Date.now();
    if (now - lastVisibilityChange < 1000) {
      return;
    }
    lastVisibilityChange = now;
    
    if (document.hidden) {
      stopAnimation();
    } else {
      frameTimes = [];
      lowPerformanceMode = false;
      startAnimation();
    }
  }
  
  onMount(() => {
    if (browser) {
      const cleanup = initializeCanvas();
      
      initShapes();
      
      startAnimation();
      
      document.addEventListener('visibilitychange', handleVisibilityChange);
      
      const watchdogInterval = setInterval(() => {
        const now = Date.now();
        
        if (!isRunning && !document.hidden) {
          console.log("Animation watchdog: restarting stopped animation");
          restartAnimation();
        }
        
        if (isRunning && lastTransitionTime > 0 && now - lastTransitionTime > 15000) {
          console.log("Animation watchdog: restarting stalled shape transition");
          restartAnimation();
        }
      }, 5000);
      
      return () => {
        stopAnimation();
        if (cleanup) cleanup();
        document.removeEventListener('visibilitychange', handleVisibilityChange);
        clearInterval(watchdogInterval);
      };
    }
  });
  
  afterUpdate(() => {
    if (browser && !isRunning && !document.hidden) {
      startAnimation();
    }
  });
  
  onDestroy(() => {
    stopAnimation();
  });
</script>

<div class="animation-container" style="background-color: {backgroundColor}">
  <canvas bind:this={canvas} width={width} height={height} class="animation-canvas"></canvas>
  
  {#if backgroundSvg}
    <div class="background-svg">
      <img src={backgroundSvg} alt="" aria-hidden="true" />
    </div>
  {/if}

  {#if !isInitialized}
    <div class="loading">Loading shapes...</div>
  {/if}
</div>

<style>
  .animation-container {
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
    position: relative;
  }

  .animation-canvas {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 1;
    filter: drop-shadow(0 0 8px rgba(154, 134, 217, 0.15));
  }

  .background-svg {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 2;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
    pointer-events: none;
  }

  .background-svg img {
    width: auto;
    height: auto;
    max-width: 50%;
    max-height: 50%;
    object-fit: contain;
  }

  .loading {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    color: #9A86D9;
    font-size: 1.2rem;
    z-index: 0;
  }
</style>
