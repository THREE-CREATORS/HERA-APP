<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HERA Pro System</title>
    <style>
        :root { 
            --nav-blue: #001f3f; 
            --accent-yellow: #ffcc00; 
            --text-black: #000000; 
        }
        body { 
            margin: 0; 
            background: var(--nav-blue); 
            color: white; 
            font-family: 'Segoe UI', sans-serif; 
            height: 100vh; 
            display: flex; 
            flex-direction: column; 
            align-items: center; 
        }
        
        .section { padding: 20px; display: none; text-align: center; width: 100%; }
        #home { display: flex; flex-direction: column; justify-content: center; height: 100vh; }
        
        .btn { 
            padding: 20px; 
            border-radius: 15px; 
            border: none; 
            cursor: pointer; 
            font-weight: bold; 
            margin: 10px 0; 
            width: 85%; 
            max-width: 400px; 
            color: var(--text-black); 
            background: var(--accent-yellow); 
        }
        
        .visor { 
            background: #000; 
            padding: 30px; 
            border-radius: 15px; 
            margin: 20px auto; 
            width: 85%; 
            border: 2px solid var(--accent-yellow); 
            color: #fff; 
            font-size: 1.2rem; 
            min-height: 200px; 
        }
    </style>
</head>
<body onclick="iniciarVoz()">

    <div id="home" class="section" style="display:block;">
        <h1>HERA</h1>
        <button class="btn" onclick="showSection('dashboard')">EMPIEZA A DESCUBRIR CON HERA</button>
    </div>

    <div id="dashboard" class="section">
        <h1>Panel de Control</h1>
        <button id="btn-conectar" class="btn" onclick="conectarRobot()">CONECTAR</button>
        <button class="btn" onclick="abrirVisor('traducciones')">TRADUCCIÓN DE LENGUAJES</button>
        <button class="btn" onclick="abrirVisor('piezas')">IDENTIFICACIÓN DE PIEZAS</button>
        <button class="btn" onclick="ejecutarAccion('motores/activar')">EMPEZAR LECTURAS</button>
        <button class="btn" onclick="showSection('home')">SALIR</button>
    </div>

    <div id="visor-datos" class="section">
        <h2 id="titulo-visor">Información</h2>
        <div class="visor" id="contenido-visor">Cargando datos desde la nube...</div>
        <button class="btn" onclick="showSection('dashboard')">VOLVER AL PANEL</button>
    </div>

    <script>
        const IP_ROBOT = "http://192.168.4.1";

        // Función para cambiar a la interfaz de visualización
        async function abrirVisor(tipo) {
            document.getElementById('titulo-visor').innerText = tipo.toUpperCase();
            document.getElementById('contenido-visor').innerText = "Consultando a la nube...";
            showSection('visor-datos');
            
            try {
                const response = await fetch(IP_ROBOT + '/' + tipo);
                const data = await response.text();
                document.getElementById('contenido-visor').innerText = data;
            } catch (error) {
                document.getElementById('contenido-visor').innerText = "Error: No se pudo obtener la información de " + tipo;
            }
        }

        async function conectarRobot() {
            const btn = document.getElementById('btn-conectar');
            try {
                const response = await fetch(IP_ROBOT + '/status');
                if(response.ok) {
                    btn.innerText = "CONECTADO";
                    const msg = new SpeechSynthesisUtterance("Conectado exitosamente.");
                    msg.lang = 'es-ES';
                    window.speechSynthesis.speak(msg);
                }
            } catch (error) { alert("Error de conexión con el robot"); }
        }

        function ejecutarAccion(ruta) {
            fetch(IP_ROBOT + '/' + ruta)
            .then(() => alert("Acción ejecutada correctamente"))
            .catch(() => alert("Error al ejecutar acción"));
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
