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
        .btn { padding: 25px; border-radius: 20px; border: none; cursor: pointer; font-weight: bold; margin: 10px; width: 90%; max-width: 400px; font-size: 1.2rem; background: var(--accent-yellow); color: var(--text-black); }
        .btn-small { padding: 15px 30px; width: auto; }
        .btn-success { background: #28a745; color: white; }
        .btn-danger { background: #ff4d4d; color: white; }
        .btn-stop { background: var(--danger) !important; color: white !important; }
        .row { display: flex; gap: 20px; justify-content: center; margin-top: 20px; }
        input { padding: 15px; margin: 10px; border-radius: 10px; border: none; width: 80%; max-width: 300px; }
        video, #foto-capturada { background: #000; border-radius: 20px; border: 4px solid var(--accent-yellow); width: 90%; max-width: 400px; margin-bottom: 20px; display: block; }
        #foto-capturada { display: none; }
        .visor { background: #000; padding: 30px; border-radius: 20px; margin: 20px auto; width: 80%; border: 3px solid var(--accent-yellow); color: #fff; font-size: 1.2rem; min-height: 150px; text-align: left; }
    </style>
</head>
<body>

    <div id="splash" class="section" style="display: flex;">
        <h1 style="font-size: 4rem;">HERA</h1>
        <button class="btn" onclick="iniciarProceso('login')">INICIAR SESIÓN</button>
        <button class="btn btn-small" onclick="iniciarProceso('register')">REGÍSTRATE</button>
        <button class="btn btn-small" onclick="accesoInvitado()">INGRESAR COMO INVITADO</button>
    </div>

    <div id="camera-view" class="section">
        <h2 id="cam-title">Cámara</h2>
        <video id="video" autoplay playsinline></video>
        <img id="foto-capturada" alt="Foto capturada">
        <div id="form-registro" style="display:none;">
            <input type="text" id="nombre" placeholder="Nombre">
        </div>
        <div class="row">
            <button class="btn btn-small" id="btn-accion" onclick="procesarAccion()">PROCESAR</button>
            <button class="btn btn-small btn-success" id="btn-guardar" onclick="guardarUsuario()" style="display:none;">GUARDAR</button>
            <button class="btn btn-small btn-danger" onclick="volver()">VOLVER</button>
        </div>
    </div>

    <div id="dashboard" class="section">
        <h1>Panel de Control</h1>
        <button id="btn-conectar" class="btn btn-small" onclick="conectarNube()">CONECTAR A NUBE</button>
        <div class="row">
            <button class="btn" onclick="abrirVisor('traducciones')">TRADUCCIÓN</button>
            <button class="btn" onclick="abrirVisor('piezas')">PIEZAS</button>
        </div>
        <button id="btn-tarea" class="btn" onclick="toggleTarea()">INICIAR TAREA</button>
        <button class="btn btn-danger" onclick="volver()">SALIR</button>
    </div>

    <div id="visor-datos" class="section">
        <h2 id="titulo-visor">Información</h2>
        <div class="visor" id="contenido-visor">Cargando...</div>
        <button class="btn" onclick="showSection('dashboard')">VOLVER AL PANEL</button>
    </div>

    <script>
        let modo = "", tareaActiva = false;
        let baseDeDatos = ["Eliana", "Admin"];
        const IP_ROBOT = "http://192.168.4.1";
        const IP_NUBE = "http://TU_IP_DE_LA_NUBE_AQUI"; 
        const video = document.getElementById('video');
        const foto = document.getElementById('foto-capturada');
        const canvas = document.createElement('canvas');

        function hablar(t) { window.speechSynthesis.cancel(); const m = new SpeechSynthesisUtterance(t); m.lang = 'es-VE'; window.speechSynthesis.speak(m); }
        
        function showSection(id) { document.querySelectorAll('.section').forEach(s => s.style.display = 'none'); document.getElementById(id).style.display = 'flex'; }

        function iniciarProceso(m) {
            modo = m; showSection('camera-view');
            foto.style.display = 'none'; video.style.display = 'block';
            const isReg = (modo === 'register');
            document.getElementById('form-registro').style.display = isReg ? 'block' : 'none';
            document.getElementById('btn-guardar').style.display = 'none';
            document.getElementById('btn-accion').innerText = isReg ? "CAPTURAR" : "RECONOCER";
            navigator.mediaDevices.getUserMedia({ video: true }).then(s => video.srcObject = s);
        }

        function procesarAccion() {
            if (modo === 'register') {
                canvas.width = video.videoWidth;
                canvas.height = video.videoHeight;
                canvas.getContext('2d').drawImage(video, 0, 0);
                foto.src = canvas.toDataURL('image/png');
                video.style.display = 'none';
                foto.style.display = 'block';
                document.getElementById('btn-guardar').style.display = 'block';
                hablar("Foto capturada. Presione guardar.");
            } else {
                hablar("Analizando...");
                setTimeout(() => {
                    if(baseDeDatos.includes("Eliana")) { hablar("Bienvenida, Eliana."); showSection('dashboard'); }
                    else { hablar("Acceso denegado."); }
                }, 2000);
            }
        }

        function accesoInvitado() { hablar("Acceso invitado."); showSection('dashboard'); }

        function guardarUsuario() { 
            baseDeDatos.push(document.getElementById('nombre').value); 
            hablar("Usuario registrado."); 
            volver(); 
        }

        async function toggleTarea() {
            const btn = document.getElementById('btn-tarea');
            tareaActiva = !tareaActiva;
            if (tareaActiva) {
                btn.innerText = "TERMINAR TAREA"; btn.classList.add('btn-stop');
                hablar("Iniciando motores y sistemas.");
                await fetch(IP_ROBOT + '/tarea/iniciar');
            } else {
                btn.innerText = "INICIAR TAREA"; btn.classList.remove('btn-stop');
                hablar("Tarea finalizada.");
                await fetch(IP_ROBOT + '/tarea/detener');
            }
        }

        async function conectarNube() { try { await fetch(IP_NUBE + '/status'); hablar("Conectado a nube."); } catch(e) { alert("Error nube"); } }

        async function abrirVisor(tipo) {
            showSection('visor-datos');
            document.getElementById('titulo-visor').innerText = tipo.toUpperCase();
            try { const res = await fetch(IP_NUBE + '/' + tipo); document.getElementById('contenido-visor').innerText = await res.text(); } 
            catch(e) { document.getElementById('contenido-visor').innerText = "Error de conexión."; }
        }

        function volver() { showSection('splash'); }
    </script>
</body>
</html>
