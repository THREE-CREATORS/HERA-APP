<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>HERA System Pro</title>
    <style>
        :root { --amber-gold: #d9a441; --bg-dark: #14100c; --text: #f5ead2; }
        body { font-family: 'Segoe UI', sans-serif; background-color: var(--bg-dark); color: #f5ead2; margin: 0; display: flex; flex-direction: column; align-items: center; justify-content: center; min-height: 100vh; overflow-x: hidden; }
        
        .main-container { width: 95vw; max-width: 1200px; display: flex; align-items: center; gap: 20px; }
        
        .logo-box { flex: 0 0 200px; text-align: center; }
        .logo-box img { width: 100%; max-width: 200px; border: 2px solid var(--amber-gold); border-radius: 50%; }

        .panel { 
            flex: 1;
            background: #1a150d; 
            border: 2px solid var(--amber-gold); 
            padding: 30px; 
            border-radius: 20px; 
            text-align: center; 
            display: none; 
            box-sizing: border-box;
        }
        
        h1, h2 { color: var(--amber-gold); margin-top: 0; }
        input { padding: 15px; width: 70%; max-width: 500px; border-radius: 8px; border: 1px solid var(--amber-gold); background: #000; color: #fff; margin-bottom: 20px; font-size: 1.2rem; }
        .btn-gold { background: var(--amber-gold); color: #000; padding: 15px 30px; border-radius: 8px; border: none; cursor: pointer; font-weight: bold; margin: 10px; font-size: 1.1rem; }
        .btn-reset { background: #555; color: #fff; padding: 10px 20px; border-radius: 8px; border: none; cursor: pointer; margin-top: 20px; }
        .visor { background: #000; color: #0f0; padding: 30px; border-radius: 8px; border: 1px solid #333; text-align: left; font-family: monospace; margin: 20px 0; font-size: 1.2rem; min-height: 200px; }
    </style>
</head>
<body>

    <div class="main-container">
        <!-- Logo del equipo -->
        <div class="logo-box">
            <img src="logo-hera.jpg" alt="Logo HERA Bright Minds">
            <p style="color: var(--amber-gold); font-size: 0.9rem; margin-top: 10px;">BRIGHT MINDS</p>
        </div>

        <!-- P1: REGISTRO/INGRESO -->
        <div id="p1-registro" class="panel" style="display: block;">
            <h1>SISTEMA HERA</h1>
            <input type="text" id="input-nombre" placeholder="Nombre de investigador">
            <br>
            <button class="btn-gold" onclick="intentarIngresar()">INGRESAR</button>
            <button class="btn-gold" onclick="registrarUsuario()">REGISTRAR</button>
            <br>
            <button class="btn-reset" onclick="location.reload()">REINICIAR SISTEMA</button>
        </div>

        <!-- P2: MENÚ PRINCIPAL -->
        <div id="p2-bienvenida" class="panel">
            <h2 id="titulo-bienvenida"></h2>
            <button class="btn-gold" onclick="navegar('p3-traduccion', 'Accediendo al análisis de manuscritos, investigador.')">ANÁLISIS DE MANUSCRITOS</button>
            <button class="btn-gold" onclick="navegar('p4-piezas', 'Accediendo al análisis de piezas.')">ANÁLISIS DE PIEZAS</button>
            <br>
            <button class="btn-reset" onclick="navegar('p1-registro', 'Volviendo al inicio.')">VOLVER</button>
        </div>

        <!-- P3: ANÁLISIS DE MANUSCRITOS -->
        <div id="p3-traduccion" class="panel">
            <h2>Análisis de Manuscritos</h2>
            <div class="visor">Esperando datos de laboratorio...</div>
            <button class="btn-gold" onclick="hablar('Procesando símbolos antiguos y caracteres.')">INICIAR ANÁLISIS</button>
            <br>
            <button class="btn-reset" onclick="navegar('p2-bienvenida', 'Regresando al menú.')">VOLVER</button>
        </div>

        <!-- P4: ANÁLISIS DE PIEZAS -->
        <div id="p4-piezas" class="panel">
            <h2>Análisis de Piezas</h2>
            <div id="visor-nube" class="visor">Conexión establecida. ESTRUCTURA_VALIDADA_OK.</div>
            <br>
            <button class="btn-reset" onclick="navegar('p2-bienvenida', 'Regresando al menú.')">VOLVER</button>
        </div>
    </div>

    <script>
        function hablar(texto) {
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(texto);
            msg.lang = 'es-ES';
            msg.rate = 0.9;
            msg.pitch = 1.3; 
            
            const voces = window.speechSynthesis.getVoices();
            msg.voice = voces.find(v => 
                v.name.includes('Google español') || 
                v.name.includes('Microsoft Helena') || 
                v.name.includes('Microsoft Laura')
            ) || voces[0];
            
            window.speechSynthesis.speak(msg);
        }

        function navegar(id, mensaje) {
            document.querySelectorAll('.panel').forEach(p => p.style.display = 'none');
            document.getElementById(id).style.display = 'block';
            if(mensaje) hablar(mensaje);
        }

        function activarBienvenida(nombre) {
            const genero = (nombre.toLowerCase().endsWith('a') && nombre.toLowerCase() !== 'jonas') ? "Bienvenida" : "Bienvenido";
            const textoCompleto = genero + ", " + nombre;
            
            document.getElementById('titulo-bienvenida').innerText = textoCompleto;
            document.querySelectorAll('.panel').forEach(p => p.style.display = 'none');
            document.getElementById('p2-bienvenida').style.display = 'block';
            
            hablar(textoCompleto + ". El sistema HERA está listo para iniciar su labor científica.");
        }

        function intentarIngresar() {
            const input = document.getElementById('input-nombre').value.trim();
            const registrado = localStorage.getItem('usuarioHERA');
            if (input === registrado && input !== "") {
                activarBienvenida(registrado);
            } else {
                hablar("Acceso denegado. Usuario no registrado o nombre incorrecto.");
            }
        }

        function registrarUsuario() {
            const nombre = document.getElementById('input-nombre').value.trim();
            if (nombre !== "") {
                localStorage.setItem('usuarioHERA', nombre);
                activarBienvenida(nombre);
            } else {
                hablar("Por favor, ingrese un nombre válido para registrarse.");
            }
        }

        window.speechSynthesis.onvoiceschanged = () => { window.speechSynthesis.getVoices(); };
    </script>
</body>
</html>
