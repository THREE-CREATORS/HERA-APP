<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>HERA Pro System</title>
    <script defer src="https://cdn.jsdelivr.net/npm/face-api.js@0.22.2/dist/face-api.min.js"></script>
    <style>
        /* ... (mantiene tus estilos anteriores) ... */
        #login-cam { width: 100%; max-width: 300px; border: 3px solid var(--silver); border-radius: 15px; }
    </style>
</head>
<body>
    <div id="login-face" class="section" style="display:block;">
        <h2>Reconocimiento Facial</h2>
        <video id="login-cam" autoplay playsinline></video>
        <p id="status">Cargando modelos...</p>
    </div>

    <script>
        const video = document.getElementById('login-cam');

        // 1. Cargar modelos de IA al iniciar
        Promise.all([
            faceapi.nets.tinyFaceDetector.loadFromUri('https://raw.githubusercontent.com/justadudewhohacks/face-api.js/master/weights'),
            faceapi.nets.faceLandmark68Net.loadFromUri('https://raw.githubusercontent.com/justadudewhohacks/face-api.js/master/weights')
        ]).then(startVideo);

        function startVideo() {
            navigator.mediaDevices.getUserMedia({ video: true })
                .then(stream => video.srcObject = stream)
                .catch(err => console.error(err));
        }

        // 2. Procesar rostro en tiempo real
        video.addEventListener('play', () => {
            const status = document.getElementById('status');
            setInterval(async () => {
                const detections = await faceapi.detectAllFaces(video, new faceapi.TinyFaceDetectorOptions());
                if (detections.length > 0) {
                    status.innerText = "¡Rostro detectado! Verificando identidad...";
                    // Aquí compararías los datos con tu base de datos de usuarios
                    // Si coincide: mostrar el botón "EMPIEZA A DESCUBRIR EL PASADO"
                }
            }, 100);
        });
    </script>
</body>
</html>
