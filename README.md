<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HERA System</title>
    <style>
        :root { --nav-blue: #001f3f; --accent-yellow: #ffcc00; --text-black: #000000; --danger: #ff4d4d; }
        body { margin: 0; background: var(--nav-blue); color: white; font-family: 'Segoe UI', sans-serif; display: flex; flex-direction: column; align-items: center; min-height: 100vh; }
        .section { padding: 40px 20px; display: none; text-align: center; width: 100%; flex-direction: column; align-items: center; }
        .btn { padding: 25px; border-radius: 20px; border: none; cursor: pointer; font-weight: bold; margin: 10px; width: 90%; max-width: 400px; font-size: 1.2rem; background: var(--accent-yellow); color: var(--text-black); }
        .btn-small { padding: 15px 30px; width: auto; }
        video { background: #000; border-radius: 20px; border: 4px solid var(--accent-yellow); width: 90%; max-width: 400px; }
    </style>
</head>
<body>

    <div id="splash" class="section" style="display: flex;">
        <h1 style="font-size: 4rem;">HERA</h1>
        <button class="btn" onclick="iniciarProceso('login')">INICIAR SESIÓN</button>
        <button class="btn btn-small" onclick="iniciarProceso('register')">REGÍSTRATE</button>
    </div>

    <div id="camera-view" class="section">
        <h2 id="cam-title">Cámara</h2>
        <video id="video" autoplay playsinline></video>
        <div id="form-registro" style="display:none;">
            <input type="text" id="nombre" placeholder="Nombre">
            <input type="text" id="apellido" placeholder="Apellido">
        </div>
        <div class="row">
            <button class="btn btn-small" onclick="capturarYProcesar()">CAPTURAR Y PROCESAR</button>
            <button class="btn btn-small" onclick="volver()">VOLVER</button>
        </div>
    </div>

    <script>
        let modo = "";
        const IP_SERVIDOR_IA = "http://TU_SERVIDOR_PYTHON:5000"; // Aquí va la IP de tu PC/Servidor

        function hablar(texto) {
            window.speechSynthesis.cancel();
            window.speechSynthesis.speak(new SpeechSynthesisUtterance(texto));
        }

        function iniciarProceso(m) {
            modo = m;
            showSection('camera-view');
            hablar(modo === 'login' ? "Modo inicio de sesión. Por favor, mira a la cámara." : "Modo registro. Introduce tus datos y captura.");
            document.getElementById('form-registro').style.display = (modo === 'register') ? 'block' : 'none';
        }

        async function capturarYProcesar() {
            const video = document.getElementById('video');
            const canvas = document.createElement('canvas');
            canvas.width = video.videoWidth; canvas.height = video.videoHeight;
            canvas.getContext('2d').drawImage(video, 0, 0);
            const imageBlob = await new Promise(r => canvas.toBlob(r, 'image/jpeg'));

            const formData = new FormData();
            formData.append('file', imageBlob, 'rostro.jpg');
            if(modo === 'register') {
                formData.append('nombre', document.getElementById('nombre').value);
                formData.append('apellido', document.getElementById('apellido').value);
            }

            // ENVIAR A TU BACKEND (Python/Node.js) PARA EL RECONOCIMIENTO REAL
            fetch(`${IP_SERVIDOR_IA}/${modo}`, { method: 'POST', body: formData })
            .then(res => res.json())
            .then(data => {
                if(data.success) {
                    hablar("Bienvenido " + data.nombre + ". Acceso concedido.");
                    window.location.href = "/dashboard"; // O tu función showSection('dashboard')
                } else {
                    hablar("No se ha reconocido el rostro. Inténtalo de nuevo.");
                }
            });
        }

        function showSection(id) {
            document.querySelectorAll('.section').forEach(s => s.style.display = 'none');
            document.getElementById(id).style.display = 'flex';
            if(id === 'camera-view') navigator.mediaDevices.getUserMedia({ video: true }).then(s => document.getElementById('video').srcObject = s);
        }

        function volver() { showSection('splash'); }
    </script>
</body>
</html>
