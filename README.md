<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>HERA Pro System</title>
    <style>
        :root { --nav-blue: #001f3f; --accent-yellow: #ffcc00; --text-black: #000000; --danger: #ff4d4d; }
        body { margin: 0; background: var(--nav-blue); color: white; font-family: 'Segoe UI', sans-serif; height: 100vh; display: flex; flex-direction: column; align-items: center; justify-content: center; overflow: hidden; }
        
        .section { padding: 20px; display: none; text-align: center; width: 100%; }
        
        /* Ojos Robóticos */
        #splash { display: flex; flex-direction: column; align-items: center; cursor: pointer; }
        .eyes { font-size: 10rem; transition: transform 3s ease-in-out; }
        .closed { transform: scaleY(0.1); }
        .open { transform: scaleY(1); }

        .btn { padding: 20px; border-radius: 15px; border: none; cursor: pointer; font-weight: bold; margin: 10px 0; width: 85%; max-width: 400px; color: var(--text-black); background: var(--accent-yellow); }
        .btn-small { padding: 8px 16px; width: auto; font-size: 0.9rem; }
        .btn-danger { background: var(--danger); color: white; }
    </style>
</head>
<body>

    <div id="splash" onclick="abrirOjos()">
        <div id="eyes" class="eyes closed">O_O</div>
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
        <button class="btn" onclick="abrirVisor('traducciones')">TRADUCCIÓN</button>
        <button class="btn" onclick="abrirVisor('piezas')">PIEZAS</button>
        <button class="btn" onclick="activarMotores()">EMPEZAR LECTURAS</button>
        <button class="btn btn-danger" onclick="showSection('home')">SALIR</button>
    </div>

    <script>
        const IP_ROBOT = "http://192.168.4.1";

        function hablar(texto) {
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(texto);
            msg.lang = 'es-ES';
            window.speechSynthesis.speak(msg);
        }

        function abrirOjos() {
            const ojos = document.getElementById('eyes');
            ojos.classList.remove('closed');
            ojos.classList.add('open');
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

        // Funciones de acción (conectar, visor, motores) iguales a la versión anterior...
        async function conectarRobot() {
            hablar("Intentando conectar con el sistema.");
            try {
                const response = await fetch(IP_ROBOT + '/status');
                if(response.ok) {
                    document.getElementById('btn-conectar').innerText = "CONECTADO";
                    hablar("Conexión exitosa.");
                }
            } catch (e) { hablar("No se pudo conectar."); }
        }
    </script>
</body>
</html>
