<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HERA Pro System</title>
    <style>
        :root { --nav-blue: #001f3f; --accent-yellow: #ffcc00; --text-black: #000000; --danger: #ff4d4d; }
        body { margin: 0; background: var(--nav-blue); color: white; font-family: 'Segoe UI', sans-serif; min-height: 100vh; display: flex; flex-direction: column; align-items: center; }
        .section { padding: 40px 20px; display: none; text-align: center; width: 100%; }
        
        /* Ojos y Boca Robóticos */
        #splash { display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100vh; cursor: pointer; }
        .robot-face { width: 380px; height: 260px; background: #1a2a3a; border: 8px solid #444; border-radius: 40px; display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 20px; transition: transform 4s ease-in-out; transform: scaleY(0.05); margin-bottom: 30px; }
        .eyes-row { display: flex; gap: 60px; }
        .eye-ball { width: 90px; height: 90px; background: #00ffff; border-radius: 50%; box-shadow: 0 0 40px #00ffff; }
        .mouth { width: 120px; height: 20px; background: #00ffff; border-radius: 10px; box-shadow: 0 0 20px #00ffff; }
        .open { transform: scaleY(1); }

        /* Botones optimizados para Tablet */
        .btn { padding: 25px; border-radius: 20px; border: none; cursor: pointer; font-weight: bold; margin: 15px 10px; width: 90%; max-width: 500px; color: var(--text-black); background: var(--accent-yellow); transition: transform 0.2s; font-size: 1.1rem; }
        .btn-small { padding: 15px 30px; width: auto; font-size: 1rem; }
        .btn-danger { background: var(--danger); color: white; }
        .btn:active { transform: scale(0.97); }
        
        /* Contenedor horizontal espaciado */
        .row { display: flex; gap: 30px; justify-content: center; width: 100%; margin-top: 20px; }
        .row .btn { width: 40%; max-width: 220px; }
        
        .visor { background: #000; padding: 40px; border-radius: 20px; margin: 30px auto; width: 85%; border: 3px solid var(--accent-yellow); color: #fff; font-size: 1.4rem; min-height: 200px; }
    </style>
</head>
<body>

    <div id="splash" onclick="abrirOjos()">
        <div id="robot-face" class="robot-face">
            <div class="eyes-row">
                <div class="eye-ball"></div>
                <div class="eye-ball"></div>
            </div>
            <div class="mouth"></div>
        </div>
        <p style="font-size: 1.2rem;">Toca la pantalla para activar a HERA</p>
    </div>

    <div id="home" class="section">
        <h1 style="font-size: 3rem;">HERA</h1>
        <button class="btn" onclick="iniciarIntroduccion()">EMPIEZA A DESCUBRIR CON HERA</button>
    </div>

    <div id="dialogo" class="section">
        <p id="texto-hera" style="font-size: 1.8rem;"></p>
        <div class="row">
            <button class="btn btn-small" onclick="showSection('dashboard'); hablar('Bienvenida al panel. Selecciona una opción para comenzar.')">SÍ</button>
            <button class="btn btn-small btn-danger" onclick="hablar('Estaré esperando aquí.')">NO</button>
        </div>
    </div>

    <div id="dashboard" class="section">
        <h1 style="font-size: 2.5rem;">Panel de Control</h1>
        <button id="btn-conectar" class="btn btn-small" onclick="conectarRobot()">CONECTAR</button>
        <div class="row">
            <button class="btn" onclick="abrirVisor('traducciones')">TRADUCCIÓN</button>
            <button class="btn" onclick="abrirVisor('piezas')">PIEZAS</button>
        </div>
        <button class="btn" style="width:90%" onclick="activarMotores()">EMPEZAR LECTURAS</button>
        <button class="btn btn-danger" style="width:90%" onclick="showSection('home')">SALIR</button>
    </div>

    <div id="visor-datos" class="section">
        <h2 id="titulo-visor">Información</h2>
        <div class="visor" id="contenido-visor">Cargando datos...</div>
        <button class="btn" onclick="showSection('dashboard')">VOLVER AL PANEL</button>
    </div>

    <script>
        const IP_ROBOT = "http://192.168.4.1";

        function hablar(texto) {
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(texto);
            // Configuración para voz de mujer adulta
            msg.lang = 'es-VE';
            msg.rate = 0.85; // Velocidad pausada
            msg.pitch = 0.7; // Pitch bajo para un tono de mujer adulta, más serio
            window.speechSynthesis.speak(msg);
        }

        async function enviarComando(ruta, errorMsg) {
            try {
                const response = await fetch(IP_ROBOT + '/' + ruta);
                return await response.text();
            } catch (e) { alert(errorMsg); return null; }
        }

        function abrirOjos() {
            document.getElementById('robot-face').classList.add('open');
            setTimeout(() => {
                hablar("Estoy lista para la acción.");
                setTimeout(() => { document.getElementById('splash').style.display = 'none'; showSection('home'); }, 2500);
            }, 1000);
        }

        function iniciarIntroduccion() {
            const texto = "Yo soy Hera. Te guiaré en todo este proceso para descubrir nuestro pasado. ¿Estás lista para la acción?";
            document.getElementById('texto-hera').innerText = texto;
            hablar(texto);
            showSection('dialogo');
        }

        function showSection(id) {
            document.querySelectorAll('.section').forEach(s => s.style.display = 'none');
            document.getElementById(id).style.display = 'block';
        }

        async function conectarRobot() {
            document.getElementById('btn-conectar').innerText = "CONECTANDO...";
            const res = await enviarComando('status', "Error al conectar con el robot.");
            if(res) { document.getElementById('btn-conectar').innerText = "CONECTADO"; hablar("Conexión establecida."); }
        }

        async function abrirVisor(tipo) {
            showSection('visor-datos');
            document.getElementById('titulo-visor').innerText = tipo.toUpperCase();
            const data = await enviarComando(tipo, "Error al cargar datos.");
            if(data) document.getElementById('contenido-visor').innerText = data;
        }

        async function activarMotores() {
            hablar("Iniciando rutina de motores.");
            await enviarComando('motores/activar', "Error al activar los motores.");
        }
    </script>
</body>
</html>
