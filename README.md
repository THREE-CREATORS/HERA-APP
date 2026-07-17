<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HERA System Pro</title>
    <style>
        :root { --amber-gold: #d9a441; --bg-dark: #14100c; --text: #f5ead2; }
        body { font-family: 'Segoe UI', serif; background: linear-gradient(160deg, #14100c, #1c1712); color: var(--text); margin: 0; display: flex; flex-direction: column; align-items: center; min-height: 100vh; }
        .panel { width: 90%; max-width: 700px; background: rgba(0,0,0,0.6); border: 1px solid #453410; border-radius: 1rem; padding: 30px; margin: 20px 0; display: none; text-align: center; }
        .btn-gold { background: var(--amber-gold); color: #2a1c08; padding: 15px 30px; border-radius: 0.75rem; font-weight: 700; border: none; cursor: pointer; margin: 10px; font-size: 1rem; }
        .btn-ghost { background: rgba(60, 40, 20, 0.6); color: var(--text); border: 1px solid var(--amber-gold); padding: 15px 30px; border-radius: 0.75rem; cursor: pointer; }
        .visor { background: #000; padding: 20px; border-radius: 10px; min-height: 150px; font-family: 'Courier New', monospace; color: #00ff41; border: 1px solid #333; text-align: left; margin: 15px 0; }
        input { padding: 15px; width: 70%; border-radius: 5px; border: 1px solid var(--amber-gold); background: #222; color: white; margin-bottom: 20px; font-size: 1rem; }
    </style>
</head>
<body>

    <header style="width: 100%; border-bottom: 1px solid #453410; padding: 20px; text-align: center;">
        <h1 style="color: var(--amber-gold); margin: 0;">HERA</h1>
        <p style="font-size: 0.8rem; letter-spacing: 2px;">SISTEMA DE ASISTENCIA PROFESIONAL</p>
    </header>

    <!-- PAGINA 1: LOGIN Y REGISTRO -->
    <div id="login-panel" class="panel" style="display: block;">
        <h2>Identificación de Usuario</h2>
        <input type="text" id="user-input" placeholder="Ingrese su nombre de usuario">
        <br>
        <button class="btn-gold" onclick="accederSistema()">INGRESAR AL SISTEMA</button>
    </div>

    <!-- PAGINA 2: BIENVENIDA -->
    <div id="welcome-panel" class="panel">
        <h2 id="welcome-text"></h2>
        <button class="btn-gold" onclick="showSection('dashboard')">CONTINUAR AL PANEL</button>
    </div>

    <!-- DASHBOARD -->
    <div id="dashboard" class="panel">
        <h2>Panel de Control Principal</h2>
        <button class="btn-gold" onclick="showSection('manuscript-panel')">TRADUCCIÓN</button>
        <button class="btn-gold" onclick="showSection('cloud-panel')">RESULTADOS NUBE</button>
        <button class="btn-ghost" onclick="location.reload()">CERRAR SESIÓN</button>
    </div>

    <div id="manuscript-panel" class="panel">
        <h2>Traducción</h2>
        <div class="visor" id="manuscript-result">Esperando datos...</div>
        <button class="btn-gold" onclick="leerTexto('Iniciando proceso de traducción de manuscritos antiguos')">PROCESAR</button>
        <button class="btn-ghost" onclick="showSection('dashboard')">VOLVER</button>
    </div>

    <!-- PAGINA 4: RESULTADOS NUBE -->
    <div id="cloud-panel" class="panel">
        <h2>Resultados de Servidor en Nube</h2>
        <div class="visor" id="cloud-result">Cargando datos remotos...</div>
        <button class="btn-ghost" onclick="showSection('dashboard')">VOLVER AL PANEL</button>
    </div>

    <script>
        // Función de Voz Profesional Femenina
        function hablar(texto) {
            window.speechSynthesis.cancel();
            const voz = new SpeechSynthesisUtterance(texto);
            voz.lang = 'es-ES';
            voz.rate = 0.9;
            voz.pitch = 1.2;
            const voces = window.speechSynthesis.getVoices();
            voz.voice = voces.find(v => v.name.includes('Google') || v.name.includes('Microsoft')) || voces[0];
            window.speechSynthesis.speak(voz);
        }

        function showSection(id) {
            document.querySelectorAll('.panel').forEach(p => p.style.display = 'none');
            document.getElementById(id).style.display = 'block';
        }

        function accederSistema() {
            let nombre = document.getElementById('user-input').value;
            if (!nombre) { alert("Debe ingresar un nombre."); return; }
            
            let registrados = JSON.parse(localStorage.getItem('usuarios')) || [];
            if (!registrados.includes(nombre)) {
                registrados.push(nombre);
                localStorage.setItem('usuarios', JSON.stringify(registrados));
                hablar("Usuario no reconocido. Registrando nuevo investigador en la base de datos. Bienvenido, " + nombre);
            } else {
                hablar("Acceso verificado. Bienvenida de nuevo al sistema HERA, " + nombre);
            }
            
            document.getElementById('welcome-text').innerText = "Bienvenido al sistema, " + nombre;
            showSection('welcome-panel');
        }

        // Simulador de datos de la Nube (Página 4)
        function cargarDatosNube() {
            const data = "--- INFORME DE NUBE ---\nID_STATUS: SECURE_SYNC\nVAL_A: 45.88\nVAL_B: 12.04\nESTADO: CONEXIÓN ESTABLE\nRESULTADO: OPTIMIZADO";
            document.getElementById('cloud-result').innerText = data;
            hablar("Se han cargado correctamente los resultados de la red. El sistema operativo se encuentra en niveles óptimos.");
        }

        // Ejecutar carga automática al entrar a la página 4
        const observer = new MutationObserver((mutations) => {
            if(document.getElementById('cloud-panel').style.display === 'block') {
                cargarDatosNube();
            }
        });
        observer.observe(document.getElementById('cloud-panel'), { attributes: true });

        function leerTexto(t) { hablar(t); }
    </script>
</body>
</html>
