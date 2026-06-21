<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HERA Pro System</title>
    <style>
        :root { --nav-blue: #001f3f; --accent-yellow: #ffcc00; --silver: #c0c0c0; --danger: #ff4d4d; }
        body { margin: 0; background: #001f3f; color: white; font-family: 'Segoe UI', sans-serif; height: 100vh; overflow-y: auto; }

        .top-bar { padding: 15px; border-bottom: 2px solid var(--silver); display: flex; justify-content: space-between; }
        .btn { padding: 10px 20px; border-radius: 8px; border: none; cursor: pointer; font-weight: bold; }
        .btn-exit { background: var(--danger); color: white; }
        
        .section { padding: 20px; display: none; }
        #home { display: block; text-align: center; margin-top: 50px; }
        
        .user-card { background: rgba(255,255,255,0.1); padding: 15px; margin: 10px 0; border-radius: 10px; display: flex; justify-content: space-between; align-items: center; border: 1px solid var(--silver); }
        .fab { position: fixed; bottom: 30px; right: 30px; width: 60px; height: 60px; background: var(--accent-yellow); border-radius: 50%; border: none; font-size: 30px; box-shadow: 0 4px 10px rgba(0,0,0,0.5); }

        #camera-preview { width: 100%; max-width: 300px; border: 3px solid var(--silver); border-radius: 15px; margin-top: 10px; }
    </style>
</head>
<body onclick="iniciarVoz()">

    <div class="top-bar">
        <button class="btn" onclick="showSection('user-list')">OPCIONES</button>
        <button class="btn btn-exit" onclick="showSection('home')">SALIR</button>
    </div>

    <div id="home" class="section">
        <h1>SISTEMA HERA</h1>
        <button class="btn" style="background: var(--accent-yellow);" onclick="iniciarSesion()">INICIA SESIÓN</button>
    </div>

    <div id="user-list" class="section">
        <h2>Usuarios Registrados</h2>
        <div id="users-container"></div>
    </div>

    <div id="register-form" class="section">
        <h2>Nuevo Usuario</h2>
        <video id="camera-preview" autoplay playsinline></video>
        <input type="text" id="nombre" placeholder="Nombre" style="display:block; width:90%; padding:10px; margin:10px 0;">
        <button class="btn" style="background:var(--accent-yellow)" onclick="guardarUsuario()">REGISTRAR Y CAPTURAR</button>
    </div>

    <button class="fab" onclick="showSection('register-form')">+</button>

    <script>
        // Lógica de Voz
        let vozIniciada = false;
        function iniciarVoz() {
            if(!vozIniciada) {
                const msg = new SpeechSynthesisUtterance("Bienvenido a Hera. Iniciando sistemas.");
                window.speechSynthesis.speak(msg);
                vozIniciada = true;
            }
        }

        // Navegación
        function showSection(id) {
            document.querySelectorAll('.section').forEach(s => s.style.display = 'none');
            document.getElementById(id).style.display = 'block';
            if(id === 'register-form') activarCamara();
        }

        // Cámara
        async function activarCamara() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: true });
                document.getElementById('camera-preview').srcObject = stream;
            } catch(e) { alert("Error al acceder a la cámara"); }
        }

        // Gestión de usuarios
        function guardarUsuario() {
            const nombre = document.getElementById('nombre').value;
            const container = document.getElementById('users-container');
            const div = document.createElement('div');
            div.className = 'user-card';
            div.innerHTML = `<span>${nombre}</span> <button class="btn btn-exit" onclick="this.parentElement.remove()">Eliminar</button>`;
            container.appendChild(div);
            showSection('user-list');
        }
    </script>
</body>
</html>
