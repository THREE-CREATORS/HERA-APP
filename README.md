R"rawliteral(
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HERA System</title>
    <style>
        :root { --nav-blue: #001f3f; --accent-yellow: #ffcc00; --text-black: #000000; --danger: #ff4d4d; }
        body { margin: 0; background: var(--nav-blue); color: white; font-family: 'Segoe UI', sans-serif; display: flex; flex-direction: column; align-items: center; min-height: 100vh; }
        .section { padding: 40px 20px; display: none; text-align: center; width: 100%; flex-direction: column; align-items: center; }
        #btn-menu { display: none; position: absolute; top: 15px; right: 15px; padding: 10px 15px; background: var(--accent-yellow); color: var(--text-black); border-radius: 10px; cursor: pointer; font-weight: bold; }
        #menu-usuarios { display: none; position: absolute; top: 60px; right: 15px; background: #00274d; padding: 15px; border-radius: 10px; border: 1px solid var(--accent-yellow); width: 200px; text-align: left; }
        .btn { padding: 20px; border-radius: 20px; border: none; cursor: pointer; font-weight: bold; margin: 10px; width: 90%; max-width: 400px; font-size: 1.2rem; background: var(--accent-yellow); color: var(--text-black); }
        .btn-danger { background: var(--danger); color: white; }
        .row { display: flex; gap: 20px; justify-content: center; margin-top: 20px; }
        .visor { background: #000; padding: 20px; border-radius: 20px; margin: 20px auto; width: 80%; border: 2px solid var(--accent-yellow); color: #fff; text-align: left; min-height: 200px; }
    </style>
</head>
<body>
    <div id="btn-menu" onclick="toggleMenu()">USUARIOS 👤</div>
    <div id="menu-usuarios"><h3>Registrados</h3><div id="lista-nombres"></div></div>

    <div id="splash" class="section" style="display: flex;">
        <h1>HERA</h1>
        <input type="text" id="login-nombre" placeholder="Usuario" style="padding:15px; border-radius:10px; width:80%; max-width:300px;">
        <button class="btn" onclick="verificarAcceso()">INGRESAR</button>
        <button class="btn" onclick="registrarUsuario()">REGISTRAR</button>
    </div>

    <div id="dashboard" class="section">
        <h1>Panel de Control</h1>
        <div class="row">
            <button class="btn" onclick="abrirVisor('traducciones')">TRADUCCIÓN</button>
            <button class="btn" onclick="abrirVisor('piezas')">PIEZAS</button>
        </div>
        <button class="btn" onclick="toggleTarea()">INICIAR TAREA</button>
        <button class="btn btn-danger" onclick="location.reload()">SALIR</button>
    </div>

    <div id="visor-datos" class="section">
        <h2 id="titulo-visor">Información</h2>
        <div class="visor" id="contenido-visor">Cargando...</div>
        <button class="btn" onclick="showSection('dashboard')">VOLVER AL PANEL</button>
    </div>

    <script>
        let baseDeDatos = ["Eliana"];
        function hablar(t) { window.speechSynthesis.cancel(); const m = new SpeechSynthesisUtterance(t); m.lang = 'es-VE'; window.speechSynthesis.speak(m); }
        
        function showSection(id) { 
            document.querySelectorAll('.section').forEach(s => s.style.display = 'none'); 
            document.getElementById(id).style.display = 'flex'; 
        }

        async function conectarNube() { try { await fetch(IP_NUBE + '/status'); hablar("Conectado a la nube."); } catch(e) { hablar("Error de conexión."); } }

        function verificarAcceso() { 
            if(baseDeDatos.includes(document.getElementById('login-nombre').value)) {
                hablar("Bienvenida, Eliana.");
                document.getElementById('btn-menu').style.display = 'block';
                showSection('dashboard');
                conectarNube();
            } else { hablar("Usuario no registrado."); }
        }

        function registrarUsuario() {
            let n = document.getElementById('login-nombre').value;
            if(n && !baseDeDatos.includes(n)) { baseDeDatos.push(n); hablar("Usuario registrado."); }
        }

        function toggleTarea() {
            showSection('visor-datos');
            document.getElementById('titulo-visor').innerText = "MANUAL DE INICIO";
            document.getElementById('contenido-visor').innerHTML = "1. Ubique botones laterales.<br>2. Presione simultáneamente.<br>3. Espere confirmación.";
            hablar("Por favor, presione los dos botones del robot.");
        }

        async function abrirVisor(tipo) {
            showSection('visor-datos');
            document.getElementById('titulo-visor').innerText = tipo.toUpperCase();
            hablar("Accediendo a " + tipo);
            try { 
                const res = await fetch(IP_NUBE + '/' + tipo); 
                document.getElementById('contenido-visor').innerText = await res.text(); 
            } catch(e) { document.getElementById('contenido-visor').innerText = "Error de conexión."; }
        }

        function toggleMenu() { 
            const menu = document.getElementById('menu-usuarios'); 
            menu.style.display = (menu.style.display === 'block') ? 'none' : 'block';
            document.getElementById('lista-nombres').innerHTML = baseDeDatos.map(u => `<p>${u}</p>`).join('');
        }
    </script>
</body>
</html>
)rawliteral";
