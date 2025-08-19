<!doctype html>
<html lang="hi">
<head>
  <meta charset="utf-8" />
  <title>3D Floor Plan — 35' x 40'</title>
  <meta name="viewport" content="width=device-width,initial-scale=1"/>
  <style>
    html,body { height:100%; margin:0; font-family: Arial, Helvetica, sans-serif; background:#ececec; }
    #container { width:100%; height:100%; display:block; position:relative; }
    #overlay {
      position:absolute; left:10px; top:10px; z-index:5;
      background:rgba(255,255,255,0.9); padding:10px; border-radius:6px;
      box-shadow:0 2px 8px rgba(0,0,0,0.2);
    }
    button { margin:4px; padding:6px 10px; font-size:14px; cursor:pointer; }
    label { font-size:13px; margin-left:6px; }
    .dim { font-weight:600; color:#333; }
    .credits { position:absolute; right:10px; bottom:10px; background:rgba(255,255,255,0.85); padding:6px 8px; border-radius:6px; font-size:12px; }
    .room-label { padding:2px 6px; border-radius:4px; background:rgba(255,255,255,0.85); border:1px solid rgba(0,0,0,0.15); font-size:13px; }
  </style>
</head>
<body>
  <div id="container"></div>

  <div id="overlay">
    <div><strong>Floor plan 3D — 35' × 40'</strong></div>
    <div style="margin-top:6px;">
      <button id="btnTop">Top View</button>
      <button id="btnPersp">Perspective</button>
      <button id="toggleLabels">Toggle Labels</button>
      <button id="toggleWire">Toggle Wireframe</button>
    </div>
    <div style="margin-top:8px;">
      <span class="dim">Scale:</span> 1 unit = 1 ft
    </div>
  </div>

  <div class="credits">Use mouse: left-drag rotate · scroll zoom · right-drag pan</div>

  <!-- THREE.js (r152 or later) and examples (OrbitControls & CSS2DRenderer) -->
  <script src="https://unpkg.com/three@0.152.2/build/three.min.js"></script>
  <script src="https://unpkg.com/three@0.152.2/examples/js/controls/OrbitControls.js"></script>
  <script src="https://unpkg.com/three@0.152.2/examples/js/renderers/CSS2DRenderer.js"></script>

  <script>
  (function(){
    const container = document.getElementById('container');

    // Scene + camera
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0xf0f0f0);

    const camera = new THREE.PerspectiveCamera(50, window.innerWidth/window.innerHeight, 0.1, 1000);
    camera.position.set(0, 60, 90);

    const renderer = new THREE.WebGLRenderer({antialias:true});
    renderer.setSize(window.innerWidth, window.innerHeight);
    container.appendChild(renderer.domElement);

    // CSS2D renderer for labels
    const labelRenderer = new THREE.CSS2DRenderer();
    labelRenderer.setSize(window.innerWidth, window.innerHeight);
    labelRenderer.domElement.style.position = 'absolute';
    labelRenderer.domElement.style.top = 0;
    labelRenderer.domElement.style.pointerEvents = 'none';
    container.appendChild(labelRenderer.domElement);

    // Controls
    const controls = new THREE.OrbitControls(camera, labelRenderer.domElement);
    controls.target.set(0, 0, 0);
    controls.update();

    // Lights
    const hemi = new THREE.HemisphereLight(0xffffff, 0x444444, 0.8);
    hemi.position.set(0, 200, 0);
    scene.add(hemi);
    const dir = new THREE.DirectionalLight(0xffffff, 0.6);
    dir.position.set(50, 100, 50);
    scene.add(dir);

    // Basic materials
    const wallMat = new THREE.MeshLambertMaterial({ color: 0xffffff });
    const wallEdgeMat = new THREE.MeshPhongMaterial({ color: 0x666666, shininess:10 });
    const floorMat = new THREE.MeshLambertMaterial({ color: 0xdcdcdc });

    // Parameters
    const wallHeight = 10;     // ft
    const wallThickness = 0.5; // ft

    // Helper: create wall segment (box) centered at x,z with given length and thickness oriented along axis
    function createWallSegment(length, thickness, height) {
      const g = new THREE.BoxGeometry(length, height, thickness);
      const m = wallMat.clone();
      const mesh = new THREE.Mesh(g, m);
      mesh.castShadow = true;
      return mesh;
    }

    // Helper: place label
    function makeLabel(text, pos) {
      const div = document.createElement('div');
      div.className = 'room-label';
      div.textContent = text;
      const label = new THREE.CSS2DObject(div);
      label.position.set(pos.x, pos.y, pos.z);
      return label;
    }

    // FLOOR: overall 35 x 40 (centered at origin)
    const totalW = 35; // x direction
    const totalD = 40; // z direction
    const floorGeo = new THREE.PlaneGeometry(totalW, totalD);
    const floor = new THREE.Mesh(floorGeo, floorMat);
    floor.rotation.x = -Math.PI/2;
    floor.position.y = 0;
    scene.add(floor);

    // For convenience, we'll place rooms relative to the center. The sketch orientation:
    // x from left(-) to right(+), z from top(-) to bottom(+)
    // We'll construct major rooms approximating sketch measurements.

    // --- Top row (z ~ -15 to -5) three rooms: left 9x12, mid 13x12, right 13x12 (width totals 35)
    // top edge z = - (totalD/2) = -20
    // top rooms depth (north-south) = 12 ft
    const topDepth = 12;
    const topZCenter = -20 + topDepth/2; // -14

    // Left top room: width 9
    const leftTopW = 9;
    const leftTopX = -totalW/2 + leftTopW/2; // -17.5 + 4.5 = -13
    addRoom(leftTopW, topDepth, leftTopX, topZCenter, "Top Left\n9' × 12'");

    // Middle top room: 13
    const midTopW = 13;
    const midTopX = -totalW/2 + leftTopW + midTopW/2;
    addRoom(midTopW, topDepth, midTopX, topZCenter, "Top Mid\n13' × 12'");

    // Right top room: 13
    const rightTopW = 13;
    const rightTopX = -totalW/2 + leftTopW + midTopW + rightTopW/2; // should end at +17.5
    addRoom(rightTopW, topDepth, rightTopX, topZCenter, "Top Right\n13' × 12'");

    // --- Bottom row two rooms (left and right) similar 13x12 each at bottom edge
    const bottomDepth = 12;
    const bottomZCenter = 20 - bottomDepth/2; // +14

    // Bottom left (13x12)
    const bottomLeftW = 13;
    const bottomLeftX = -totalW/2 + bottomLeftW/2; // -17.5+6.5 = -11
    addRoom(bottomLeftW, bottomDepth, bottomLeftX, bottomZCenter, "Bottom Left\n13' × 12'");

    // Bottom right (13x12)
    const bottomRightW = 13;
    const bottomRightX = -totalW/2 + bottomLeftW + bottomRightW/2 + (totalW - (bottomLeftW+bottomRightW) - 0); // align roughly
    addRoom(bottomRightW, bottomDepth, bottomRightX, bottomZCenter, "Bottom Right\n13' × 12'");

    // --- Left middle large room (vertical center-left) approx 8 x 12 (as sketched)
    // On left middle: width 8, depth 12, centered z ~ 0
    addRoom(8, 12, -totalW/2 + 8/2, 0, "Left Middle\n8' × 12'");

    // --- Center hall approx 18 x 18 (as in sketch)
    addRoom(18, 18, 0, 0, "Central Hall\n18' × 18'", { floorColor: 0xf8f1d7 });

    // --- Right side: storage + toilets block
    // Storage (9' x 6') placed on right middle
    const storageW = 9, storageD = 6;
    const storageX = totalW/2 - storageW/2; // near right edge
    const storageZ = -2; // slightly above center
    addRoom(storageW, storageD, storageX, storageZ, "Store\n9' × 6'");

    // Toilets block: three small rooms vertically arranged approx width 10, depth 10 (sketch)
    // We'll make a 10x10 block subdivided
    const wcBlockW = 10, wcBlockD = 10;
    const wcBlockX = totalW/2 - storageW - wcBlockW/2 - 1; // to the left of store
    const wcBlockZ = 10; // lower-right in sketch
    addWCBlock(wcBlockW, wcBlockD, wcBlockX, wcBlockZ);

    // Function to add a main 'room' — draws thin walls around a rectangular area and label
    function addRoom(width, depth, centerX, centerZ, labelText, opts={}) {
      opts = opts || {};
      const y = wallHeight/2;

      const halfW = width/2, halfD = depth/2;
      const wt = wallThickness;

      // Four wall segments, leaving entrances as gaps (we'll add small gaps for doors by skipping a segment portion)
      // For simplicity, put door gap on south wall if the room is not isolated (except central)
      // Create walls as Box geometries positioned appropriately.

      // North wall (along x)
      const north = createWallSegment(width, wt, wallHeight);
      north.rotation.y = 0;
      north.position.set(centerX, y, centerZ - halfD + wt/2);
      scene.add(north);

      // South wall
      const south = createWallSegment(width, wt, wallHeight);
      south.position.set(centerX, y, centerZ + halfD - wt/2);
      scene.add(south);

      // West wall (along z)
      const west = createWallSegment(depth, wt, wallHeight);
      west.rotation.y = Math.PI/2;
      west.position.set(centerX - halfW + wt/2, y, centerZ);
      scene.add(west);

      // East wall
      const east = createWallSegment(depth, wt, wallHeight);
      east.rotation.y = Math.PI/2;
      east.position.set(centerX + halfW - wt/2, y, centerZ);
      scene.add(east);

      // Slightly darker edges for visibility
      [north, south, east, west].forEach(w => { w.material = wallMat.clone(); w.material.color.offsetHSL(0, -0.02, -0.06); });

      // Add room floor as subtle plane
      const subFloor = new THREE.Mesh(new THREE.PlaneGeometry(width - 0.8, depth - 0.8), new THREE.MeshLambertMaterial({ color: opts.floorColor || 0xffffff }));
      subFloor.rotation.x = -Math.PI/2;
      subFloor.position.set(centerX, 0.01, centerZ);
      scene.add(subFloor);

      // Add label
      const label = makeLabel(labelText, {x:centerX, y:2.2, z:centerZ});
      scene.add(label);

      // Add dimension labels along front edge (south)
      const dimDiv = document.createElement('div');
      dimDiv.className = 'room-label';
      dimDiv.style.fontSize = '12px';
      dimDiv.textContent = `${Math.round(width)}' × ${Math.round(depth)}'`;
      const dimLabel = new THREE.CSS2DObject(dimDiv);
      dimLabel.position.set(centerX, 2.2, centerZ + halfD - 0.6);
      scene.add(dimLabel);

      // Return group possibility (not used now)
    }

    // Function to build WC block subdivided roughly like the sketch (two small toilets and a corridor)
    function addWCBlock(width, depth, centerX, centerZ) {
      const y = wallHeight/2;
      const halfW = width/2, halfD = depth/2;

      // Outer rectangle walls
      const north = createWallSegment(width, wallThickness, wallHeight);
      north.position.set(centerX, y, centerZ - halfD + wallThickness/2);
      scene.add(north);
      const south = createWallSegment(width, wallThickness, wallHeight);
      south.position.set(centerX, y, centerZ + halfD - wallThickness/2);
      scene.add(south);
      const west = createWallSegment(depth, wallThickness, wallHeight);
      west.rotation.y = Math.PI/2;
      west.position.set(centerX - halfW + wallThickness/2, y, centerZ);
      scene.add(west);
      const east = createWallSegment(depth, wallThickness, wallHeight);
      east.rotation.y = Math.PI/2;
      east.position.set(centerX + halfW - wallThickness/2, y, centerZ);
      scene.add(east);

      // internal divider: vertical dividing wall splitting into two small rooms left & right
      const internal = createWallSegment(depth - 1, wallThickness, wallHeight);
      internal.rotation.y = Math.PI/2;
      internal.position.set(centerX - 1.5, y, centerZ);
      scene.add(internal);

      // small doors: We'll make small plane objects to indicate door openings (no subtraction)
      // Add labels
      const labelA = makeLabel("WC A\n5' × 3.5'", {x:centerX - halfW/2, y:2.2, z:centerZ});
      scene.add(labelA);
      const labelB = makeLabel("WC B\n5' × 3.5'", {x:centerX + halfW/2, y:2.2, z:centerZ});
      scene.add(labelB);

      // small floor
      const subFloor = new THREE.Mesh(new THREE.PlaneGeometry(width - 1, depth - 1), new THREE.MeshLambertMaterial({ color: 0xefe7e7 }));
      subFloor.rotation.x = -Math.PI/2;
      subFloor.position.set(centerX, 0.01, centerZ);
      scene.add(subFloor);
    }

    // Ground grid helper
    const grid = new THREE.GridHelper(80, 80, 0x888888, 0xdddddd);
    grid.position.y = 0.001;
    scene.add(grid);

    // Axis helper
    const axes = new THREE.AxesHelper(10);
    axes.position.set(-totalW/2 - 5, 0, -totalD/2 - 5);
    scene.add(axes);

    // Simple roof/ceiling indicator as wireframe box (optional)
    const roofBox = new THREE.Mesh(new THREE.BoxGeometry(totalW, 0.2, totalD), new THREE.MeshBasicMaterial({ color:0x999999, opacity:0.05, transparent:true }));
    roofBox.position.set(0, wallHeight + 0.1, 0);
    scene.add(roofBox);

    // Wireframe toggle
    let wire = false;
    function setWireframe(v) {
      wire = v;
      scene.traverse(o => {
        if (o.isMesh && o.geometry.type.includes('Box')) {
          o.material.wireframe = wire;
        }
      });
    }

    // Label toggle
    let labelsVisible = true;
    function setLabels(v) {
      labelsVisible = v;
      // CSS2DRenderer renders DOM nodes, toggle visibility
      const nodes = labelRenderer.domElement.querySelectorAll('.room-label');
      nodes.forEach(n => n.style.display = labelsVisible ? 'inline-block' : 'none');
    }

    // UI buttons
    document.getElementById('btnTop').addEventListener('click', () => {
      camera.position.set(0, 120, 0.1); // high above
      camera.up.set(0,0,-1);
      controls.target.set(0,0,0);
      controls.update();
    });
    document.getElementById('btnPersp').addEventListener('click', () => {
      camera.position.set(60, 60, 90);
      camera.up.set(0,1,0);
      controls.target.set(0,0,0);
      controls.update();
    });
    document.getElementById('toggleLabels').addEventListener('click', () => setLabels(!labelsVisible));
    document.getElementById('toggleWire').addEventListener('click', () => setWireframe(!wire));

    // Resize handler
    window.addEventListener('resize', onWindowResize, false);
    function onWindowResize(){
      camera.aspect = window.innerWidth/window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
      labelRenderer.setSize(window.innerWidth, window.innerHeight);
    }

    // Render loop
    function animate() {
      requestAnimationFrame(animate);
      renderer.render(scene, camera);
      labelRenderer.render(scene, camera);
    }
    animate();

    // initial settings
    setLabels(true);

    // Helpful console info
    console.log("3D floor plan loaded. Modify room sizes in the HTML (addRoom calls) to match exact plan.");
  })();
  </script>
</body>
</html>
