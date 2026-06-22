<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HERA Pro System - Auth</title>
    <style>
        :root { --nav-blue: #001f3f; --accent-yellow: #ffcc00; --text-black: #000000; --danger: #ff4d4d; }
        body { margin: 0; background: var(--nav-blue); color: white; font-family: 'Segoe UI', sans-serif; display: flex; flex-direction: column; align-items: center; min-height: 100vh; }
        .section { padding: 40px 20px; display: none; text-align: center; width: 100%; flex-direction: column; align-items: center; }
        #splash { display: flex; }
        .btn { padding: 30px; border-radius: 25px; border: none; cursor: pointer; font-weight: bold; margin: 10px; width: 90%; max-width: 400px; font-size: 1.2rem; background: var(--accent-yellow); color: var(--text-black); }
        .btn-small { padding: 15px 30px; width: auto; font-size: 1rem; }
        .btn-danger { background: var(--danger); color: white; }
        .row { display: flex; gap: 20px; justify-content: center; margin-top: 20px; }
        input { padding: 15px; margin: 10px; border-radius: 10px; border: none; width: 80%; max-width: 300px; }
        video { background: #000; border-radius: 20px; border: 4px solid var(--accent-yellow); width: 90%; max-width: 400px; }
        .user-list { width: 90%; max-width: 600px; background: #002d5e; padding: 20px; border-radius: 15px; margin-top: 20px; }
    </style>
</head>
<body>

    <div id="splash" class="section" style="display: flex;">
        <h1 style="font-size: 3rem;">HERA Pro System</h1>
        <button class="btn" onclick="showSection('camera-view', 'login')">INICIAR SESIÓN</button>
        <button class="btn btn-small" onclick="showSection('camera-view', 'register')">REGÍSTRATE</button>
        <button class="btn btn-small" onclick="showSection('lista-usuarios')">VER USUARIOS</button>
    </div>

    <div id="camera-view" class="section">
        <h2 id="cam-title">Cámara</h2>
        <video id="video" autoplay playsinline></video>
        <div id="form-registro" style="display:none;">
            <input type="text" id="nombre" placeholder="Nombre">
            <input type="text" id="apellido" placeholder="Apellido">
        </div>
        <div class="row">
            <button class="btn btn-small" onclick="capturar()">CAPTURAR</button>
            <button class="btn btn-small btn-danger" onclick="showSection('splash')">VOLVER</button>
        </div>
    </div>

    <div id="lista-usuarios" class="section">
        <h2>Usuarios Registrados</h2>
        <div id="contenedor-usuarios" class="user-list">Cargando...</div>
        <button class="btn btn-small" onclick="showSection('splash')">VOLVER</button>
    </div>

    <script>
        let modo = "";
        const IP_ROBOT = "http://192.168.4.1";

        function showSection(id, m = "") {
            modo = m;
            document.querySelectorAll('.section').forEach(s => s.style.display = 'none');
            document.getElementById(id).style.display = 'flex';
            if(id === 'camera-view') {
                document.getElementById('cam-title').innerText = modo === 'login' ? "Autenticación Facial" : "Registro de Usuario";
                document.getElementById('form-registro').style.display = modo === 'register' ? 'block' : 'none';
                navigator.mediaDevices.getUserMedia({ video: true }).then(stream => { document.getElementById('video').srcObject = stream; });
            }
        }

        function capturar() {
            const video = document.getElementById('video');
            const canvas = document.createElement('canvas');
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            canvas.getContext('2d').drawImage(video, 0, 0);
            const data = canvas.toDataURL('image/jpeg');
            
            if(modo === 'login') {
                alert("Verificando identidad...");
            } else {
                alert("Usuario " + document.getElementById('nombre').value + " registrado.");
            }
        }
    </script>
</body>
</html>
