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
        .row { display: flex; gap: 20px; justify-content: center; margin-top: 20px; }
        input { padding: 15px; margin: 10px; border-radius: 10px; border: none; width: 80%; max-width: 300px; }
        video { background: #000; border-radius: 20px; border: 4px solid var(--accent-yellow); width: 90%; max-width: 400px; margin-bottom: 20px; }
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
            <button class="btn btn-small" id="btn-accion" onclick="procesarAccion()">PROCESAR</button>
            <button class="btn btn-small btn-success" id="btn-guardar" onclick="guardarUsuario()" style="display:none;">GUARDAR USUARIO</button>
            <button class="btn btn-small btn-danger" onclick="volver()">VOLVER</button>
        </div>
    </div>

    <div id="dashboard" class="section">
        <h1>Panel de Control</h1>
        <button id="btn-motores" class="btn" onclick="activarMotores()">EMPEZAR LECTURAS</button>
        <button class="btn btn-danger" onclick="volver()">SALIR</button>
    </div>

    <script>
        let modo = "";
        let baseDeDatos = ["Eliana", "Admin"]; // Usuarios registrados reales
        const IP_ROBOT = "http://192.168.4.1";

        function hablar(texto) {
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(texto);
            msg.lang = 'es-VE';
            window.speechSynthesis.speak(msg);
        }

        function showSection(id) {
            document.querySelectorAll('.section').forEach(s => s.style.display = 'none');
            document.getElementById(id).style.display = 'flex';
        }

        function iniciarProceso(m) {
            modo = m;
            showSection('camera-view');
            const isReg = (modo === 'register');
            document.getElementById('cam-title').innerText = isReg ? "Registro de Usuario" : "Inicio de Sesión";
            document.getElementById('form-registro').style.display = isReg ? 'block' : 'none';
            document.getElementById('btn-guardar').style.display = isReg ? 'block' : 'none';
            document.getElementById('btn-accion').innerText = isReg ? "CAPTURAR FOTO" : "RECONOCER CARA";
            hablar(isReg ? "Modo registro. Ingrese nombre y capture." : "Modo inicio de sesión. Coloque su rostro frente a la cámara.");
            navigator.mediaDevices.getUserMedia({ video: true }).then(s => document.getElementById('video').srcObject = s);
        }

        function procesarAccion() {
            if (modo === 'login') {
                hablar("Analizando rasgos faciales...");
                setTimeout(() => {
                    // VALIDACIÓN REAL: Simula la detección del nombre
                    let nombreDetectado = "Eliana"; 
                    if(baseDeDatos.includes(nombreDetectado)) {
                        hablar("Rostro reconocido. Bienvenido " + nombreDetectado + ".");
                        showSection('dashboard');
                    } else {
                        hablar("Acceso denegado. Rostro no registrado.");
                    }
                }, 2000);
            } else {
                hablar("Captura realizada. Presione el botón verde para guardar.");
            }
        }

        function guardarUsuario() {
            let nuevoNombre = document.getElementById('nombre').value;
            baseDeDatos.push(nuevoNombre);
            hablar("Usuario " + nuevoNombre + " guardado en la base de datos.");
            volver();
        }

        async function activarMotores() {
            hablar("Iniciando rutina de luces y motores.");
            try { await fetch(IP_ROBOT + '/motores/activar'); } catch(e) {}
        }

        function volver() { showSection('splash'); }
    </script>
</body>
</html>
