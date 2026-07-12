<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HERA System</title>
    <style>
        :root { --amber-gold: #d9a441; --bg-dark: #14100c; --text-light: #f5ead2; }
        body { margin: 0; background: #14100c; color: var(--text-light); font-family: 'Georgia', serif; display: flex; flex-direction: column; align-items: center; min-height: 100vh; }
        
        /* Estilos HERA */
        .hera-bg { background: radial-gradient(ellipse at 20% 0%, rgba(198,138,63,0.18), transparent 55%), linear-gradient(160deg, #14100c 0%, #1c1712 55%, #100d0a 100%); }
        .btn { padding: 15px 30px; border-radius: 12px; border: 1px solid var(--amber-gold); cursor: pointer; font-weight: bold; margin: 10px; background: rgba(217, 164, 65, 0.2); color: var(--amber-gold); transition: 0.3s; }
        .btn:hover { background: var(--amber-gold); color: #000; }
        .panel { width: 90%; max-width: 500px; background: rgba(0,0,0,0.4); padding: 20px; border-radius: 20px; border: 1px solid #453410; margin: 20px 0; }
        
        #menu-usuarios { display: none; background: #1c1712; padding: 15px; border: 1px solid var(--amber-gold); position: absolute; top: 70px; right: 20px; border-radius: 10px; }
        .visor { background: #000; padding: 20px; border-radius: 15px; border: 1px solid var(--amber-gold); min-height: 150px; margin-top: 10px; }
    </style>
</head>
<body class="hera-bg">
    <header style="padding: 20px; text-align: center;">
        <h1 style="color: var(--amber-gold); font-size: 2rem;">HERA</h1>
        <button class="btn" onclick="toggleMenu()">USUARIOS 👤</button>
    </header>

    <div id="menu-usuarios"><h3>Registrados</h3><div id="lista-nombres"></div></div>

    <div id="splash" class="panel">
        <input type="text" id="login-nombre" placeholder="Usuario" style="width: 100%; padding: 10px; border-radius: 8px;">
        <button class="btn" onclick="verificarAcceso()">INGRESAR</button>
    </div>

    <div id="dashboard" class="panel" style="display:none;">
        <h2>Panel de Control</h2>
        <button class="btn" onclick="abrirVisor('traducciones')">TRADUCCIÓN</button>
        <button class="btn" onclick="abrirVisor('piezas')">PIEZAS</button>
        <button class="btn" onclick="location.reload()">SALIR</button>
    </div>

    <div id="visor-datos" class="panel" style="display:none;">
        <h2 id="titulo-visor">Información</h2>
        <div class="visor" id="contenido-visor">Esperando...</div>
        <button class="btn" onclick="showSection('dashboard')">VOLVER</button>
    </div>

    <script>
        let baseDeDatos = ["Eliana"];
        function hablar(t) { const m = new SpeechSynthesisUtterance(t); m.lang = 'es-VE'; window.speechSynthesis.speak(m); }
        function showSection(id) { document.querySelectorAll('.panel').forEach(p => p.style.display = 'none'); document.getElementById(id).style.display = 'block'; }
        
        function verificarAcceso() {
            if(baseDeDatos.includes(document.getElementById('login-nombre').value)) {
                hablar("Bienvenida, Eliana.");
                showSection('dashboard');
            } else { hablar("Usuario no registrado."); }
        }

        async function abrirVisor(tipo) {
            showSection('visor-datos');
            hablar("Accediendo a " + tipo);
            try { 
                const res = await fetch('/' + tipo); 
                document.getElementById('contenido-visor').innerText = await res.text(); 
            } catch(e) { document.getElementById('contenido-visor').innerText = "Error de conexión."; }
        }

        function toggleMenu() { const m = document.getElementById('menu-usuarios'); m.style.display = (m.style.display === 'block') ? 'none' : 'block'; }
    </script>
</body>
</html>
)rawliteral";
