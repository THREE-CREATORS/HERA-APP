<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>HERA Pro System</title>
    <style>
        :root { --nav-blue: #001f3f; --accent-yellow: #ffcc00; --text-black: #000000; --danger: #ff4d4d; }
        body { margin: 0; background: var(--nav-blue); color: white; font-family: 'Segoe UI', sans-serif; min-height: 100vh; display: flex; flex-direction: column; align-items: center; }
        .section { padding: 20px; display: none; text-align: center; width: 100%; }
        
        /* Ojos Robóticos */
        #splash { display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100vh; cursor: pointer; }
        .robot-face { width: 350px; height: 220px; background: #1a2a3a; border: 6px solid #444; border-radius: 30px; display: flex; align-items: center; justify-content: center; gap: 40px; transition: transform 4s ease-in-out; transform: scaleY(0.05); margin-bottom: 20px; }
        .eye-ball { width: 80px; height: 80px; background: #00ffff; border-radius: 50%; box-shadow: 0 0 30px #00ffff; }
        .open { transform: scaleY(1); }

        .btn { padding: 20px; border-radius: 15px; border: none; cursor: pointer; font-weight: bold; margin: 10px 0; width: 85%; max-width: 400px; color: var(--text-black); background: var(--accent-yellow); transition: transform 0.1s; }
        .btn-small { padding: 10px 20px; width: auto; font-size: 0.9rem; }
        .btn-danger { background: var(--danger); color: white; }
        .btn:active { transform: scale(0.95); }
        .row { display: flex; gap: 10px; justify-content: center; width: 90%; }
        .row .btn { width: 45%; }
        .visor { background: #000; padding: 20px; border-radius: 15px; margin: 20px auto; width: 85%; border: 2px solid var(--accent-yellow); color: #fff; min-height: 150px; }
    </style>
</head>
<body>

    <div id="splash" onclick="abrirOjos()">
        <div id="robot-face" class="robot-face"><div class="eye-ball"></div><div class="eye-ball"></div></div>
        <p>Toca para despertar a HERA</p>
    </div>

    <div id="home" class="section">
        <h1>HERA</h1>
        <button class="btn" onclick="iniciarIntroduccion()">EMPIEZA A DESCUBRIR CON HERA</button>
    </div>

    <div id="dialogo" class="section">
        <p id="texto-hera" style="font-size: 1.5rem;"></p>
        <div class="row">
            <button class="btn btn-small" onclick="showSection('dashboard'); hablar('Bienvenida al panel. Selecciona una opción para continuar.')">SI</button>
            <button class="btn btn-small btn-danger" onclick="hablar('Estaré esperando aquí cuando estés lista.')">NO</button>
        </div>
    </div>

    <div id="dashboard" class="section">
        <h1>Panel de Control</h1>
        <button id="btn-conectar" class="btn btn-small" onclick="conectarRobot()">CONECTAR</button>
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
        <button class="btn" onclick="showSection('dashboard')">VOLVER AL PANEL</button>
    </div>

    <script>
        const IP_ROBOT = "http://192.168.4.1";

        // Motor de Voz Seria Femenina
        function hablar(texto) {
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(texto);
            const voices = window.speechSynthesis.getVoices();
            msg.voice = voices.find(v => v.lang === 'es-VE') || voices.find(v => v.lang === 'es-ES');
            msg.rate = 0.9;
            msg.pitch = 0.8;
            window.speechSynthesis.speak(msg);
        }

        // --- NUCLEO DE ACCIONES (INTERFAZ CON EL ROBOT) ---
        async function enviarComando(ruta, mensajeError) {
            try {
                const response = await fetch(IP_ROBOT + '/' + ruta);
                if (!response.ok) throw new Error("Fallo en respuesta");
                return await response.text();
            } catch (e) {
                console.error(e);
                alert(mensajeError);
                return null;
            }
        }

        async function conectarRobot() {
            const btn = document.getElementById('btn-conectar');
            btn.innerText = "CONECTANDO...";
            const res = await enviarComando('status', "Error: No se pudo contactar con HERA.");
            if(res) {
                btn.innerText = "CONECTADO";
                hablar("Conexión establecida exitosamente.");
            } else {
                btn.innerText = "CONECTAR";
            }
        }

        async function abrirVisor(tipo) {
            showSection('visor-datos');
            document.getElementById('titulo-visor').innerText = tipo.toUpperCase();
            document.getElementById('contenido-visor').innerText = "Solicitando datos a la placa...";
            const data = await enviarComando(tipo, "No se pudo obtener información de " + tipo);
            if(data) document.getElementById('contenido-visor').innerText = data;
        }

        async function activarMotores() {
            hablar("Iniciando rutina de motores.");
            const res = await enviarComando('motores/activar', "Error al activar los motores. Revisa la conexión.");
            if(res) alert("Rutina de motores ejecutada.");
        }

        // --- SISTEMA DE NAVEGACION ---
        function abrirOjos() {
            document.getElementById('robot-face').classList.add('open');
            setTimeout(() => {
                hablar("Estoy lista para la acción.");
                setTimeout(() => { document.getElementById('splash').style.display = 'none'; showSection('home'); }, 2000);
            }, 1000);
        }

        function iniciarIntroduccion() {
            const texto = "Yo soy Hera. Te guiaré en este proceso para descubrir nuestro pasado. ¿Estás lista para la acción?";
            document.getElementById('texto-hera').innerText = texto;
            hablar(texto);
            showSection('dialogo');
        }

        function showSection(id) {
            document.querySelectorAll('.section').forEach(s => s.style.display = 'none');
            document.getElementById(id).style.display = 'block';
        }
    </script>
</body>
</html>
