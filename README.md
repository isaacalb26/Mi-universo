# Mi-universo<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Mi Universo</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }
    body {
      background-color: #05020a;
      color: #ffffff;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      overflow: hidden;
      height: 100vh;
      width: 100vw;
    }

    /* Modal de Inicio */
    #modal-inicio {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      background: rgba(20, 10, 30, 0.85);
      border: 1px solid rgba(255, 105, 180, 0.3);
      padding: 30px;
      border-radius: 20px;
      text-align: center;
      box-shadow: 0 0 30px rgba(236, 72, 153, 0.4);
      backdrop-filter: blur(10px);
      z-index: 10;
      width: 90%;
      max-width: 380px;
    }

    #modal-inicio h1 {
      font-size: 1.4rem;
      margin-bottom: 20px;
      color: #ffb7c5;
    }

    #corazon-icon {
      font-size: 40px;
      color: #ff3366;
      margin-bottom: 15px;
      animation: beat 1.2s infinite;
    }

    @keyframes beat {
      0%, 100% { transform: scale(1); }
      50% { transform: scale(1.2); }
    }

    button {
      background: linear-gradient(135deg, #e91e63, #9c27b0);
      color: white;
      border: none;
      padding: 12px 24px;
      font-size: 0.9rem;
      font-weight: bold;
      border-radius: 25px;
      cursor: pointer;
      box-shadow: 0 4px 15px rgba(233, 30, 99, 0.4);
      transition: all 0.3s ease;
    }

    button:hover {
      transform: scale(1.05);
      box-shadow: 0 6px 20px rgba(233, 30, 99, 0.6);
    }

    /* Barra de carga */
    #loader {
      display: none;
      margin-top: 15px;
    }

    #progress-bar {
      width: 100%;
      height: 8px;
      background: rgba(255, 255, 255, 0.1);
      border-radius: 4px;
      overflow: hidden;
      margin-top: 10px;
    }

    #progress {
      width: 0%;
      height: 100%;
      background: linear-gradient(90deg, #ff007f, #7f00ff);
      transition: width 0.1s linear;
    }

    /* Título principal */
    #titulo-galaxia {
      position: absolute;
      bottom: 30px;
      left: 50%;
      transform: translateX(-50%);
      text-align: center;
      z-index: 5;
      pointer-events: none;
      opacity: 0;
      transition: opacity 2s ease;
    }

    #titulo-galaxia h2 {
      font-size: 1.8rem;
      letter-spacing: 4px;
      text-shadow: 0 0 10px #ff69b4;
    }

    #titulo-galaxia p {
      font-size: 0.8rem;
      letter-spacing: 2px;
      color: #aaa;
    }

    canvas {
      display: block;
    }
  </style>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
</head>
<body>

  <div id="modal-inicio">
    <div id="corazon-icon">❤️</div>
    <h1 id="texto-bienvenida">Feliz Día de la Novia, Mi Amor</h1>
    <button id="btn-iniciar" onclick="iniciarCarga()">CLICK AQUÍ MI AMOR</button>
    
    <div id="loader">
      <p id="status-text" style="font-size: 0.85rem; color: #ddd;">Cargando estrellas y recuerdos...</p>
      <div id="progress-bar">
        <div id="progress"></div>
      </div>
      <p id="porcentaje" style="font-size: 0.8rem; margin-top: 5px;">0%</p>
    </div>
  </div>

  <div id="titulo-galaxia">
    <h2>MI UNIVERSO</h2>
    <p>EXPLORA NUESTRA GALAXIA</p>
  </div>

  <script>
    // Configuración de palabras flotantes
    const palabras = [
      "Amor eterno", "Te Amo", "Para siempre", "Mi paraíso",
      "Pasión", "Constelación", "Tú y yo", "Luz",
      "Destino", "Sueños", "Magia", "Siempre juntos",
      "Alma", "Ternura", "Felicidad", "Mi vida"
    ];

    let scene, camera, renderer, galaxyParticles;

    function iniciarCarga() {
      document.getElementById('btn-iniciar').style.display = 'none';
      document.getElementById('loader').style.display = 'block';

      let porcentaje = 0;
      const progress = document.getElementById('progress');
      const porcentajeTexto = document.getElementById('porcentaje');

      const interval = setInterval(() => {
        porcentaje += 2;
        progress.style.width = porcentaje + '%';
        porcentajeTexto.innerText = porcentaje + '%';

        if (porcentaje >= 100) {
          clearInterval(interval);
          setTimeout(() => {
            document.getElementById('modal-inicio').style.display = 'none';
            document.getElementById('titulo-galaxia').style.style = 'block';
            document.getElementById('titulo-galaxia').style.opacity = '1';
            initUniverse();
          }, 400);
        }
      }, 50);
    }

    function initUniverse() {
      // Escena, cámara y renderizador
      scene = new THREE.Scene();
      camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
      camera.position.z = 20;
      camera.position.y = 8;
      camera.lookAt(0, 0, 0);

      renderer = new THREE.WebGLRenderer({ antialias: true });
      renderer.setSize(window.innerWidth, window.innerHeight);
      renderer.setPixelRatio(window.devicePixelRatio);
      document.body.appendChild(renderer.domElement);

      // Crear espiral de galaxia (Partículas)
      const particleCount = 6000;
      const geometry = new THREE.BufferGeometry();
      const positions = new Float32Array(particleCount * 3);
      const colors = new Float32Array(particleCount * 3);

      const colorInside = new THREE.Color('#ff007f');
      const colorOutside = new THREE.Color('#7f00ff');

      for (let i = 0; i < particleCount; i++) {
        // Coordenadas en espiral
        const radius = Math.random() * 12 + 0.5;
        const spinAngle = radius * 2.5;
        const branchAngle = ((i % 3) * 2 * Math.PI) / 3;

        const randomX = (Math.random() - 0.5) * 0.5 * radius;
        const randomY = (Math.random() - 0.5) * 0.5 * radius;
        const randomZ = (Math.random() - 0.5) * 0.5 * radius;

        positions[i * 3] = Math.cos(spinAngle + branchAngle) * radius + randomX;
        positions[i * 3 + 1] = randomY;
        positions[i * 3 + 2] = Math.sin(spinAngle + branchAngle) * radius + randomZ;

        // Mezcla de colores
        const mixedColor = colorInside.clone().lerp(colorOutside, radius / 12);
        colors[i * 3] = mixedColor.r;
        colors[i * 3 + 1] = mixedColor.g;
        colors[i * 3 + 2] = mixedColor.b;
      }

      geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
      geometry.setAttribute('color', new THREE.BufferAttribute(colors, 3));

      const material = new THREE.PointsMaterial({
        size: 0.08,
        vertexColors: true,
        transparent: true,
        opacity: 0.8
      });

      galaxyParticles = new THREE.Points(geometry, material);
      scene.add(galaxyParticles);

      // Crear etiquetas de texto en el espacio
      palabras.forEach((texto, i) => {
        const textCanvas = document.createElement('canvas');
        const ctx = textCanvas.getContext('2d');
        textCanvas.width = 256;
        textCanvas.height = 64;

        ctx.font = 'Bold 20px Arial';
        ctx.fillStyle = '#ffffff';
        ctx.shadowColor = '#ff69b4';
        ctx.shadowBlur = 8;
        ctx.textAlign = 'center';
        ctx.fillText(texto, 128, 40);

        const texture = new THREE.CanvasTexture(textCanvas);
        const spriteMaterial = new THREE.SpriteMaterial({ map: texture, transparent: true });
        const sprite = new THREE.Sprite(spriteMaterial);

        const angle = (i / palabras.length) * Math.PI * 2;
        const radius = 4 + Math.random() * 8;
        sprite.position.set(
          Math.cos(angle) * radius,
          (Math.random() - 0.5) * 4,
          Math.sin(angle) * radius
        );
        sprite.scale.set(4, 1, 1);
        scene.add(sprite);
      });

      // Animación
      function animate() {
        requestAnimationFrame(animate);
        galaxyParticles.rotation.y += 0.002;
        scene.rotation.y += 0.001;
        renderer.render(scene, camera);
      }
      animate();

      // Ajuste al cambiar tamaño de pantalla
      window.addEventListener('resize', () => {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
      });
    }
  </script>
</body>
</html>
