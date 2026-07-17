<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>HERA System Pro</title>
    <style>
        :root { --amber-gold: #d9a441; --bg-dark: #14100c; --text: #f5ead2; }
        body { font-family: 'Segoe UI', sans-serif; background-color: var(--bg-dark); color: var(--text); margin: 0; display: flex; flex-direction: column; align-items: center; justify-content: center; min-height: 100vh; }
        .panel { width: 90%; max-width: 600px; background: #1a150d; border: 1px solid var(--amber-gold); padding: 40px; border-radius: 15px; text-align: center; display: none; }
        h1, h2 { color: var(--amber-gold); }
        input { padding: 15px; width: 80%; border-radius: 8px; border: 1px solid var(--amber-gold); background: #000; color: #fff; margin-bottom: 20px; }
        .btn-gold { background: var(--amber-gold); color: #000; padding: 15px 30px; border-radius: 8px; border: none; cursor: pointer; font-weight: bold; margin: 10px; }
        .visor { background: #000; color: #0f0; padding: 20px; border-radius: 8px; border: 1px solid #333; text-align: left; font-family: monospace; margin: 20px 0; }
    </style>
</head>
<body>

    <!-- P1: INGRESO -->
    <div id="p1-registro" class="panel" style="display: block;">
        <h1>SISTEMA HERA</h1>
        <input type="text" id="input-nombre" placeholder="Nombre de investigador">
        <br>
        <button class="btn-gold" onclick="intentarIngresar()">INGRESAR</button>
        <button class="btn-gold" onclick="registrarUsuario()" style="background: #8b6b2d;">REGISTRAR</button>
    </div>

    <!-- P2: MENÚ -->
    <div id="p2-bienvenida" class="panel">
        <h2 id="titulo-bienvenida"></h2>
        <button class="btn-gold" onclick="navegar('p3-traduccion', 'Accediendo al módulo de traducción histórica')">TRADUCCIÓN</button>
        <button class="btn-gold" onclick="navegar('p4-piezas', 'Consultando datos del servidor en la nube')">DATOS NUBE</button>
    </div>

    <!-- P3: TRADUCCIÓN -->
    <div id="p3-traduccion" class="panel">
        <h2>Traducción</h2>
        <div class="visor">Sistema listo para decodificar...</div>
        <button class="btn-gold" onclick="hablar('Iniciando proceso de traducción no invasiva. Análisis en curso.')">INICIAR ANÁLISIS</button>
        <button class="btn-gold" onclick="navegar('p2-bienvenida', 'Regresando al menú principal')">VOLVER</button>
    </div>

    <!-- P4: NUBE -->
    <div id="p4-piezas" class="panel">
        <h2>Datos Remotos</h2>
        <div id="visor-nube" class="visor">Conectando...</div>
        <button class="btn-gold" onclick="navegar('p2-bienvenida', 'Regresando al menú principal')">VOLVER</button>
    </div>

    <script>
        // Voz Femenina de Alta Calidad
        function hablar(texto) {
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(texto);
            msg.lang = 'es-ES';
            msg.rate = 0.9;
            msg.pitch = 1.2; // Tono más agudo/femenino
            
            // Intenta encontrar voces naturales instaladas
            const voces = window.speechSynthesis.getVoices();
            msg.voice = voces.find(v => v.name.includes('Google') || v.name.includes('Microsoft Maria') || v.name.includes('Laura')) || voces[0];
            
            window.speechSynthesis.speak(msg);
        }

        function navegar(id, mensaje) {
            document.querySelectorAll('.panel').forEach(p => p.style.display = 'none');
            document.getElementById(id).style.display = 'block';
            hablar(mensaje);
            if(id === 'p4-piezas') document.getElementById('visor-nube').innerText = "DATA: ESTRUCTURA_VALIDADA_OK";
        }

        function intentarIngresar() {
            const input = document.getElementById('input-nombre').value.trim();
            const registrado = localStorage.getItem('usuarioHERA');
            if (input === registrado && input !== "") {
                const saludo = (registrado.toLowerCase().endsWith('a')) ? "Bienvenida" : "Bienvenido";
                document.getElementById('titulo-bienvenida').innerText = saludo + ", " + registrado;
                document.getElementById('p1-registro').style.display = 'none';
                document.getElementById('p2-bienvenida').style.display = 'block';
                hablar("Identidad confirmada. " + saludo + " de nuevo al sistema, " + registrado);
            } else {
                hablar("Error de autenticación. Usuario no registrado o nombre incorrecto.");
            }
        }

        function registrarUsuario() {
            const nombre = document.getElementById('input-nombre').value.trim();
            if (nombre !== "") {
                localStorage.setItem('usuarioHERA', nombre);
                document.getElementById('titulo-bienvenida').innerText = "Bienvenido, " + nombre;
                document.getElementById('p1-registro').style.display = 'none';
                document.getElementById('p2-bienvenida').style.display = 'block';
                hablar("Registro exitoso. Es un placer conocerle, " + nombre);
            }
        }

        window.speechSynthesis.onvoiceschanged = () => { window.speechSynthesis.getVoices(); };
    </script>
</body>
</html>
