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
        #splash { display: flex; }
        .btn { padding: 30px; border-radius: 25px; border: none; cursor: pointer; font-weight: bold; margin: 10px; width: 90%; max-width: 400px; font-size: 1.2rem; background: var(--accent-yellow); color: var(--text-black); }
        .btn-small { padding: 15px 30px; width: auto; font-size: 1rem; }
        .btn-success { background: #28a745; color: white; }
        .btn-danger { background: var(--danger); color: white; }
        .row { display: flex; gap: 20px; justify-content: center; margin-top: 20px; }
        input { padding: 15px; margin: 10px; border-radius: 10px; border: none; width: 80%; max-width: 300px; }
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
            <button class="btn btn-small" id="btn-capturar" onclick="capturar()">CAPTURAR ROSTRO</button>
            <button class="btn btn-small btn-success" id="btn-guardar" onclick="guardarUsuario()" style="display:none;">GUARDAR USUARIO</button>
            <button class="btn btn-small btn-danger" onclick="volver()">VOLVER</button>
        </div>
    </div>

    <script>
        let modo = "";

        // Función de voz de HERA
        function hablar(texto) {
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(texto);
            msg.lang = 'es-VE';
            window.speechSynthesis.speak(msg);
        }

        function iniciarProceso(m) {
            modo = m;
            showSection('camera-view');
            if(modo === 'login') {
                hablar("Modo de inicio de sesión. Por favor, posiciona tu rostro frente a la cámara.");
                document.getElementById('cam-title').innerText = "Inicio de Sesión";
            } else {
                hablar("Modo de registro. Introduce tus datos y captura tu rostro para identificarte.");
                document.getElementById('cam-title').innerText = "Registro de Nuevo Usuario";
                document.getElementById('form-registro').style.display = 'block';
            }
        }

        function showSection(id) {
            document.querySelectorAll('.section').forEach(s => s.style.display = 'none');
            document.getElementById(id).style.display = 'flex';
            if(id === 'camera-view') {
                navigator.mediaDevices.getUserMedia({ video: true }).then(stream => { document.getElementById('video').srcObject = stream; });
            }
        }

        function capturar() {
            hablar("Imagen capturada correctamente.");
            if(modo === 'register') {
                document.getElementById('btn-guardar').style.display = 'block';
            } else {
                alert("Verificando identidad en la nube...");
            }
        }

        function guardarUsuario() {
            const nombre = document.getElementById('nombre').value;
            hablar("Usuario " + nombre + " guardado con éxito en la base de datos de HERA.");
            alert("Usuario guardado.");
            volver();
        }

        function volver() {
            hablar("Volviendo al inicio.");
            showSection('splash');
        }
    </script>
</body>
</html>
