<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>HERA Dashboard</title>
    <style>
        :root { 
            --nav-blue: #001f3f; 
            --accent-yellow: #ffcc00; 
            --text-black: #000000;
            --btn-green: #28a745;
            --btn-white: #ffffff;
        }
        body { margin: 0; background: var(--nav-blue); color: white; font-family: 'Segoe UI', sans-serif; height: 100vh; }
        .section { padding: 40px 20px; display: none; text-align: center; }
        #home { display: flex; flex-direction: column; justify-content: center; align-items: center; height: 100vh; }
        
        .btn { padding: 20px; border-radius: 15px; border: none; cursor: pointer; font-weight: bold; margin: 10px; width: 90%; max-width: 400px; transition: 0.3s; color: var(--text-black); }
        
        /* Estilos específicos */
        .btn-yellow { background: var(--accent-yellow); }
        .btn-green { background: var(--btn-green); color: white; }
        .btn-white { background: var(--btn-white); color: var(--text-black); }
        
        .display-area { background: #000; padding: 20px; border-radius: 10px; margin-top: 20px; min-height: 100px; border: 1px solid var(--accent-yellow); }
    </style>
</head>
<body onclick="iniciarVoz()">

    <div id="home" class="section" style="display:block;">
        <h1>HERA</h1>
        <button class="btn btn-yellow" onclick="showSection('dashboard')">EMPIEZA A DESCUBRIR CON HERA</button>
    </div>

    <div id="dashboard" class="section">
        <h1>Panel de Control</h1>
        
        <button class="btn btn-white" onclick="conectarRobot()">Conectar / Conectado</button>
        <button class="btn btn-yellow" onclick="cargarInfo('traducciones')">Traducción de Lenguajes</button>
        <button class="btn btn-yellow" onclick="cargarInfo('piezas')">Identificación de Piezas</button>
        <button class="btn btn-green" onclick="activarMotores()">Empezar Lecturas</button>
        
        <div id="data-display" class="display-area">Esperando datos...</div>
        <br>
        <button class="btn btn-yellow" style="width: 50%;" onclick="showSection('home')">SALIR</button>
    </div>

    <script>
        const IP_ROBOT = "http://192.168.4.1";

        function conectarRobot() {
            fetch(IP_ROBOT + '/status')
            .then(r => document.getElementById('data-display').innerText = "Estado: Conectado correctamente")
            .catch(e => document.getElementById('data-display').innerText = "Error: HERA no encontrada");
        }

        function cargarInfo(tipo) {
            fetch(IP_ROBOT + '/' + tipo)
            .then(r => r.text())
            .then(data => document.getElementById('data-display').innerText = data)
            .catch(e => document.getElementById('data-display').innerText = "Error cargando datos de " + tipo);
        }

        function activarMotores() {
            fetch(IP_ROBOT + '/motores/activar')
            .then(r => alert("Motores activados. Lectura en proceso."))
            .catch(e => alert("Error al activar motores"));
        }

        function showSection(id) {
            document.querySelectorAll('.section').forEach(s => s.style.display = 'none');
            document.getElementById(id).style.display = 'block';
        }

        function iniciarVoz() {
            if(!localStorage.getItem('bienvenida_hera')) {
                const msg = new SpeechSynthesisUtterance("Bienvenido a Hera, aquí podrás descubrir nuestro pasado.");
                msg.lang = 'es-ES';
                window.speechSynthesis.speak(msg);
                localStorage.setItem('bienvenida_hera', 'true');
            }
        }
    </script>
</body>
</html>
