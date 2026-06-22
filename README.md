<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>HERA Pro System</title>
    <style>
        :root { --nav-blue: #001f3f; --accent-yellow: #ffcc00; --text-black: #000000; --danger: #ff4d4d; }
        body { margin: 0; background: var(--nav-blue); color: white; font-family: 'Segoe UI', sans-serif; height: 100vh; display: flex; flex-direction: column; align-items: center; }
        
        .section { padding: 20px; display: none; text-align: center; width: 100%; }
        
        /* Animación de Ojos */
        #splash { display: flex; justify-content: center; align-items: center; height: 100vh; flex-direction: column; cursor: pointer; }
        .eyes { font-size: 5rem; animation: blink 2s infinite; }
        @keyframes blink { 0%, 90%, 100% { opacity: 1; } 95% { opacity: 0; } }

        .btn { padding: 20px; border-radius: 15px; border: none; cursor: pointer; font-weight: bold; margin: 10px 0; width: 85%; max-width: 400px; color: var(--text-black); background: var(--accent-yellow); transition: transform 0.1s; }
        .btn-small { padding: 10px 20px; width: auto; font-size: 0.8rem; }
        .btn-danger { background: var(--danger); color: white; }
        .btn:active { transform: scale(0.95); }
        .row { display: flex; gap: 10px; justify-content: center; width: 90%; }
        .row .btn { width: 45%; }
        .visor { background: #000; padding: 20px; border-radius: 15px; margin: 20px auto; width: 85%; border: 2px solid var(--accent-yellow); color: #fff; min-height: 150px; }
    </style>
</head>
<body>

    <div id="splash" onclick="despertarHera()" style="display:flex;">
        <div class="eyes">O_O</div>
        <p>Toca para despertar a HERA</p>
    </div>

    <div id="home" class="section">
        <h1>HERA</h1>
        <button id="btn-conectar" class="btn btn-small" onclick="conectarRobot()">CONECTAR</button>
        <button class="btn" onclick="iniciarSesion()">EMPIEZA A DESCUBRIR CON HERA</button>
    </div>

    <div id="dashboard" class="section">
        <h1>Panel de Control</h1>
        <div class="row">
            <button class="btn" onclick="abrirVisor('traducciones')">TRADUCCIÓN</button>
            <button class="btn" onclick="abrirVisor('piezas')">PIEZAS</button>
        </div>
        <button class="btn" style="width:85%" onclick="activarMotores()">EMPEZAR LECTURAS</button>
        <button class="btn btn-danger" style="width:85%" onclick="showSection('home')">SALIR</button>
    </div>

    <div id="visor-datos" class="section">
        <h2 id="titulo-visor">Información</h2>
        <div class="visor" id="contenido-visor">Cargando...</div>
        <button class="btn" onclick="showSection('dashboard'); hablar('Has vuelto al panel principal. ¿Qué deseas hacer ahora?')">VOLVER</button>
    </div>

    <script>
        const IP_ROBOT = "http://192.168.4.1";

        function hablar(texto) {
            const msg = new SpeechSynthesisUtterance(texto);
            msg.lang = 'es-ES';
            window.speechSynthesis.speak(msg);
        }

        // DESPERTAR
        function despertarHera() {
            document.getElementById('splash').style.display = 'none';
            document.getElementById('home').style.display = 'flex';
            hablar("Estoy lista para la acción.");
        }

        // GUIAS DE VOZ POR INTERFAZ
        function iniciarSesion() {
            hablar("Bienvenido a Hera. Presiona los botones del panel para comenzar a descubrir nuestro pasado.");
            showSection('dashboard');
        }

        async function abrirVisor(tipo) {
            hablar("Visualizando información de " + tipo + ". Presiona volver para regresar al panel.");
            document.getElementById('titulo-visor').innerText = tipo.toUpperCase();
            showSection('visor-datos');
            try {
                const response = await fetch(IP_ROBOT + '/' + tipo);
                document.getElementById('contenido-visor').innerText = await response.text();
            } catch (e) { document.getElementById('contenido-visor').innerText = "Error de conexión."; }
        }

        async function conectarRobot() {
            hablar("Intentando conectar con el sistema.");
            const btn = document.getElementById('btn-conectar');
            try {
                const response = await fetch(IP_ROBOT + '/status');
                if(response.ok) {
                    btn.innerText = "CONECTADO";
                    hablar("Conexión establecida con éxito.");
                }
            } catch (error) { hablar("No se pudo conectar. Verifica el robot."); }
        }

        async function activarMotores() {
            hablar("Iniciando motores. Por favor, mantente atento a la rutina de lectura.");
            try { await fetch(IP_ROBOT + '/motores/activar'); } 
            catch (e) { alert("Error al activar motores."); }
        }

        function showSection(id) {
            document.querySelectorAll('.section').forEach(s => s.style.display = 'none');
            document.getElementById(id).style.display = 'block';
        }
    </script>
</body>
</html>
