<!DOCTYPE html>
<html lang="es-VE">
<head>
    <meta charset="UTF-8">
    <title>HERA Pro System</title>
    <style>
        :root { --nav-blue: #001f3f; --accent-yellow: #ffcc00; --silver: #c0c0c0; --danger: #ff4d4d; }
        body { margin: 0; background: #001f3f; color: white; font-family: 'Segoe UI', sans-serif; height: 100vh; overflow-y: auto; }
        
        .top-bar { padding: 15px; border-bottom: 2px solid var(--silver); display: flex; justify-content: flex-end; }
        .btn { padding: 12px 25px; border-radius: 8px; border: none; cursor: pointer; font-weight: bold; margin: 5px; }
        .btn-exit { background: var(--danger); color: white; }
        .btn-main { background: var(--accent-yellow); color: var(--nav-blue); width: 80%; font-size: 1.1rem; }
        .btn-small { background: transparent; color: var(--silver); border: 1px solid var(--silver); font-size: 0.8rem; padding: 5px 15px; margin-top: 10px; }
        
        .section { padding: 20px; display: none; text-align: center; }
        #home { display: block; margin-top: 50px; }
        
        .user-card { background: rgba(255,255,255,0.1); padding: 15px; margin: 10px; border-radius: 10px; display: flex; align-items: center; border: 1px solid var(--silver); }
        .user-img { width: 50px; height: 50px; border-radius: 50%; background: var(--silver); margin-right: 15px; }
        
        .fab { display: none; position: fixed; bottom: 30px; right: 30px; width: 60px; height: 60px; background: var(--accent-yellow); border-radius: 50%; border: none; font-size: 30px; box-shadow: 0 4px 10px rgba(0,0,0,0.5); }
        #login-cam, #reg-cam { width: 100%; max-width: 300px; border: 3px solid var(--silver); border-radius: 15px; }
    </style>
</head>
<body onclick="iniciarVoz()">

    <div class="top-bar"><button class="btn btn-exit" onclick="showSection('home')">SALIR</button></div>

    <div id="home" class="section">
        <h1>SISTEMA HERA</h1>
        <button class="btn btn-main" onclick="showSection('login-face')">INICIA SESIÓN</button>
        <br>
        <button class="btn btn-small" onclick="showSection('register-form')">Registrar nuevo usuario</button>
    </div>

    <div id="login-face" class="section">
        <h2>Reconocimiento Facial</h2>
        <video id="login-cam" autoplay playsinline></video>
        <p>Escaneando rostro...</p>
        <button class="btn" style="background:var(--silver)" onclick="showSection('user-list')">Ver Usuarios</button>
    </div>

    <div id="user-list" class="section">
        <h2>Usuarios Registrados</h2>
        <div id="users-container"></div>
    </div>

    <div id="register-form" class="section">
        <h2>Nuevo Registro</h2>
        <video id="reg-cam" autoplay playsinline></video>
        <input type="text" id="nombre" placeholder="Nombre y Apellido" style="padding:10px; width:80%; margin:10px 0;">
        <button class="btn btn-main" onclick="guardarUsuario()">GUARDAR</button>
    </div>

    <button id="fab" class="fab" onclick="showSection('register-form')">+</button>

    <script>
        function iniciarVoz() {
            if(window.speechSynthesis.speaking) return;
            const msg = new SpeechSynthesisUtterance("Bienvenida a Hera. Estoy lista para asistirte.");
            msg.lang = 'es-VE';
            msg.pitch = 1.2;
            window.speechSynthesis.speak(msg);
        }

        function showSection(id) {
            document.querySelectorAll('.section').forEach(s => s.style.display = 'none');
            document.getElementById(id).style.display = 'block';
            document.getElementById('fab').style.display = (id === 'user-list') ? 'block' : 'none';
            if(id === 'login-face' || id === 'register-form') activarCamara(id);
        }

        async function activarCamara(id) {
            const video = document.getElementById(id === 'login-face' ? 'login-cam' : 'reg-cam');
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: true });
                video.srcObject = stream;
            } catch(e) { console.error("Cámara no disponible"); }
        }

        function guardarUsuario() {
            const nombre = document.getElementById('nombre').value;
            if(!nombre) return;
            const div = document.createElement('div');
            div.className = 'user-card';
            div.innerHTML = `<div class="user-img"></div><span>${nombre}</span> <button class="btn btn-exit" style="margin-left:auto" onclick="this.parentElement.remove()">X</button>`;
            document.getElementById('users-container').appendChild(div);
            showSection('user-list');
        }
    </script>
</body>
</html>
