<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HERA System</title>
    <style>
        :root { --amber-gold: #d9a441; --bg-dark: #14100c; --text: #f5ead2; }
        body { font-family: 'Georgia', serif; background: linear-gradient(160deg, #14100c, #1c1712); color: var(--text); margin: 0; display: flex; flex-direction: column; align-items: center; min-height: 100vh; }
        
        /* Diseño de Secciones */
        .panel { width: 90%; max-width: 700px; background: rgba(0,0,0,0.3); border: 1px solid #453410; border-radius: 1rem; padding: 20px; margin: 15px 0; display: none; }
        .btn-gold { background: var(--amber-gold); color: #2a1c08; padding: 12px 24px; border-radius: 0.75rem; font-weight: 700; border: none; cursor: pointer; margin: 5px; }
        .btn-ghost { background: rgba(60, 40, 20, 0.6); color: var(--text); border: 1px solid var(--amber-gold); padding: 12px 24px; border-radius: 0.75rem; cursor: pointer; }
        
        .scan-frame { border: 1px solid #5d4a2a; padding: 10px; border-radius: 10px; margin: 10px 0; }
        .visor { background: #000; padding: 15px; border-radius: 10px; min-height: 100px; font-family: monospace; }
        .hidden { display: none; }
    </style>
</head>
<body>
    <header style="width: 100%; border-bottom: 1px solid #453410; padding: 20px; text-align: center;">
        <h1 style="color: var(--amber-gold); margin: 0;">HERA</h1>
        <p style="font-size: 0.8rem; letter-spacing: 2px;">ASISTENTE DE INVESTIGACIÓN</p>
    </header>

    <div id="splash" class="panel" style="display: block;">
        <input type="text" id="login-nombre" placeholder="Nombre de usuario" style="padding: 10px; width: 80%; border-radius: 5px; border: none;">
        <button class="btn-gold" onclick="verificarAcceso()">INGRESAR</button>
    </div>

    <div id="dashboard" class="panel">
        <h2>Panel de Control</h2>
        <button class="btn-gold" onclick="showSection('manuscript-panel')">TRADUCCIÓN</button>
        <button class="btn-gold" onclick="showSection('artifact-panel')">PIEZAS</button>
        <button class="btn-ghost" onclick="location.reload()">SALIR</button>
    </div>

    <div id="manuscript-panel" class="panel">
        <h2>Traducción</h2>
        <div class="scan-frame">Muestra cargada: Escrito Antiguo</div>
        <div class="visor" id="manuscript-result">Esperando escaneo...</div>
        <button class="btn-gold" onclick="extraerDatos('traducciones')">ESCANEAR Y TRADUCIR</button>
        <button class="btn-ghost" onclick="showSection('dashboard')">VOLVER</button>
    </div>

    <div id="artifact-panel" class="panel">
        <h2>Piezas</h2>
        <div class="scan-frame">Muestra cargada: Pieza Arqueológica</div>
        <div class="visor" id="artifact-result">Esperando escaneo...</div>
        <button class="btn-gold" onclick="extraerDatos('piezas')">ESCANEAR PIEZA</button>
        <button class="btn-ghost" onclick="showSection('dashboard')">VOLVER</button>
    </div>

    <script>
        function showSection(id) { 
            document.querySelectorAll('.panel').forEach(p => p.style.display = 'none'); 
            document.getElementById(id).style.display = 'block'; 
        }

        function hablar(t) { const m = new SpeechSynthesisUtterance(t); m.lang = 'es-VE'; window.speechSynthesis.speak(m); }

        function verificarAcceso() {
            let nombre = document.getElementById('login-nombre').value;
            if(nombre) { hablar("Bienvenido investigador."); showSection('dashboard'); }
        }

        async function extraerDatos(tipo) {
            let elemento = tipo === 'traducciones' ? 'manuscript-result' : 'artifact-result';
            document.getElementById(elemento).innerText = "Escaneando...";
            hablar("Procesando " + tipo);
            try { 
                const res = await fetch('/' + tipo); 
                document.getElementById(elemento).innerText = await res.text(); 
            } catch(e) { document.getElementById(elemento).innerText = "Error de conexión."; }
        }
    </script>
</body>
</html>
)rawliteral";
