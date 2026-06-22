const char index_html[] PROGMEM = R"rawliteral(
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
        
        /* Ojos y Boca Gigantes */
        #splash { display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100vh; cursor: pointer; }
        .robot-face { width: 500px; height: 350px; background: #1a2a3a; border: 12px solid #444; border-radius: 60px; display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 40px; transition: transform 4s ease-in-out; transform: scaleY(0.05); margin-bottom: 50px; }
        .eyes-row { display: flex; gap: 100px; }
        .eye-ball { width: 120px; height: 120px; background: #00ffff; border-radius: 50%; box-shadow: 0 0 60px #00ffff; }
        .mouth { width: 200px; height: 30px; background: #00ffff; border-radius: 15px; box-shadow: 0 0 30px #00ffff; }
        .open { transform: scaleY(1); }

        .btn { padding: 30px; border-radius: 25px; border: none; cursor: pointer; font-weight: bold; margin: 20px 10px; width: 90%; max-width: 600px; color: var(--text-black); background: var(--accent-yellow); font-size: 1.3rem; }
        .btn-small { padding: 20px 40px; width: auto; font-size: 1.1rem; }
        .btn-danger { background: var(--danger); color: white; }
        .row { display: flex; gap: 40px; justify-content: center; width: 100%; margin-top: 30px; }
    </style>
</head>
<body>
    <div id="splash" onclick="abrirOjos()">
        <div id="robot-face" class="robot-face">
            <div class="eyes-row"><div class="eye-ball"></div><div class="eye-ball"></div></div>
            <div class="mouth"></div>
        </div>
        <p style="font-size: 1.5rem;">Toca para despertar a HERA</p>
    </div>

    <div id="home" class="section">
        <h1 style="font-size: 4rem;">HERA</h1>
        <button class="btn" onclick="iniciarIntroduccion()">EMPIEZA A DESCUBRIR CON HERA</button>
    </div>

    <div id="dialogo" class="section">
        <p id="texto-hera" style="font-size: 2rem;"></p>
        <div class="row">
            <button class="btn btn-small" onclick="showSection('dashboard'); hablar('Bienvenido al panel. Seleccione una opción para comenzar.')">SÍ</button>
            <button class="btn btn-small btn-danger" onclick="hablar('Estaré esperando aquí.')">NO</button>
        </div>
    </div>

    <div id="dashboard" class="section">
        <h1 style="font-size: 3rem;">Panel de Control</h1>
        <button id="btn-conectar" class="btn btn-small" onclick="conectarNube()">CONECTAR A NUBE</button>
        <div class="row">
            <button class="btn" onclick="abrirVisor('traducciones')">TRADUCCIÓN</button>
            <button class="btn" onclick="abrirVisor('piezas')">PIEZAS</button>
        </div>
        <button class="btn" style="width:90%" onclick="activarMotores()">EMPEZAR LECTURAS</button>
        <button class="btn btn-danger" style="width:90%" onclick="showSection('home')">SALIR</button>
    </div>

    <script>
        const IP_ROBOT = "http://192.168.4.1";
        const IP_NUBE = "http://TU_IP_DE_LA_NUBE_AQUI"; // CAMBIA ESTO

        function hablar(texto) {
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(texto);
            msg.lang = 'es-VE';
            msg.rate = 1.0; // Velocidad normal para mayor claridad
            msg.pitch = 1.0; // Tono natural, no bajo para evitar efecto nasal
            window.speechSynthesis.speak(msg);
        }

        function abrirOjos() {
            document.getElementById('robot-face').classList.add('open');
            setTimeout(() => {
                hablar("Estoy listo para la acción.");
                setTimeout(() => { document.getElementById('splash').style.display = 'none'; showSection('home'); }, 2000);
            }, 500);
        }

        function iniciarIntroduccion() {
            const texto = "Yo soy Hera. Te guiaré en todo este gran proceso para descubrir nuestro pasado. ¿Estás listo para la acción?";
            document.getElementById('texto-hera').innerText = texto;
            hablar(texto);
            showSection('dialogo');
        }

        async function conectarNube() {
            document.getElementById('btn-conectar').innerText = "CONECTANDO...";
            try {
                const res = await fetch(IP_NUBE + '/status');
                if(res.ok) { document.getElementById('btn-conectar').innerText = "CONECTADO A NUBE"; hablar("Conexión con la nube establecida."); }
            } catch(e) { alert("Error de conexión a la nube"); document.getElementById('btn-conectar').innerText = "REINTENTAR"; }
        }

        function showSection(id) {
            document.querySelectorAll('.section').forEach(s => s.style.display = 'none');
            document.getElementById(id).style.display = 'block';
        }
    </script>
</body>
</html>
)rawliteral";
