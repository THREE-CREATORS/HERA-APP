<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>HERA Pro System</title>
    <style>
        :root { --nav-blue: #001f3f; --accent-yellow: #ffcc00; --text-black: #000000; --danger: #ff4d4d; }
        body { margin: 0; background: var(--nav-blue); color: white; font-family: 'Segoe UI', sans-serif; min-height: 100vh; display: flex; flex-direction: column; align-items: center; }
        
        .section { padding: 20px; display: none; text-align: center; width: 100%; }
        
        /* Ojos Robóticos SVG Mejorados */
        #splash { display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100vh; cursor: pointer; }
        .eye-container { display: flex; gap: 40px; margin-bottom: 30px; }
        .eye { width: 120px; height: 120px; background: white; border-radius: 50%; display: flex; align-items: center; justify-content: center; transition: transform 4s ease-out; transform: scaleY(0.1); }
        .pupil { width: 40px; height: 40px; background: var(--nav-blue); border-radius: 50%; }
        .open { transform: scaleY(1); }

        .btn { padding: 20px; border-radius: 15px; border: none; cursor: pointer; font-weight: bold; margin: 10px 0; width: 85%; max-width: 400px; color: var(--text-black); background: var(--accent-yellow); transition: transform 0.1s; }
        .btn-small { padding: 8px 16px; width: auto; font-size: 0.9rem; }
        .btn-danger { background: var(--danger); color: white; }
        .btn:active { transform: scale(0.95); }
    </style>
</head>
<body>

    <div id="splash" onclick="abrirOjos()">
        <div class="eye-container">
            <div id="eye1" class="eye"><div class="pupil"></div></div>
            <div id="eye2" class="eye"><div class="pupil"></div></div>
        </div>
        <p>Toca para despertar a HERA</p>
    </div>

    <div id="home" class="section">
        <h1>HERA</h1>
        <button class="btn" onclick="iniciarIntroduccion()">EMPIEZA A DESCUBRIR CON HERA</button>
    </div>

    <div id="dialogo" class="section">
        <p id="texto-hera" style="font-size: 1.5rem;"></p>
        <div class="row">
            <button class="btn btn-small" onclick="showSection('dashboard')">SI</button>
            <button class="btn btn-small btn-danger" onclick="alert('HERA espera por ti...')">NO</button>
        </div>
    </div>

    <div id="dashboard" class="section">
        <h1>Panel de Control</h1>
        <button id="btn-conectar" class="btn btn-small" onclick="conectarRobot()">CONECTAR</button>
        <button class="btn" onclick="abrirVisor('traducciones')">TRADUCCIÓN DE LENGUAJES</button>
        <button class="btn" onclick="abrirVisor('piezas')">IDENTIFICACIÓN DE PIEZAS</button>
        <button class="btn" onclick="activarMotores()">EMPEZAR LECTURAS</button>
        <button class="btn btn-danger" onclick="showSection('home')">SALIR</button>
    </div>

    <script>
        const IP_ROBOT = "http://192.168.4.1";

        function hablar(texto) {
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(texto);
            // Intenta encontrar voz venezolana, si no, usa la por defecto
            const voices = window.speechSynthesis.getVoices();
            const vVenezolana = voices.find(v => v.lang === 'es-VE');
            msg.voice = vVenezolana || voices.find(v => v.lang === 'es-ES');
            msg.lang = 'es-VE';
            window.speechSynthesis.speak(msg);
        }

        function abrirOjos() {
            document.getElementById('eye1').classList.add('open');
            document.getElementById('eye2').classList.add('open');
            setTimeout(() => {
                hablar("Estoy lista para la acción.");
                setTimeout(() => showSection('home'), 2000);
            }, 1000);
        }

        function iniciarIntroduccion() {
            const texto = "Yo soy Hera, te voy a guiar en todo este gran proceso para descubrir nuestro pasado. ¿ESTAS LISTO PARA LA ACCION?";
            document.getElementById('texto-hera').innerText = texto;
            hablar(texto);
            showSection('dialogo');
        }

        function showSection(id) {
            document.querySelectorAll('.section').forEach(s => s.style.display = 'none');
            document.getElementById(id).style.display = 'block';
        }

        // Funciones auxiliares mantenidas
        async function conectarRobot() {
            try {
                const response = await fetch(IP_ROBOT + '/status');
                if(response.ok) {
                    document.getElementById('btn-conectar').innerText = "CONECTADO";
                    hablar("Conexión establecida.");
                }
            } catch (e) { hablar("No pude conectar."); }
        }
    </script>
</body>
</html>
