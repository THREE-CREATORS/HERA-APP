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

    <div id="p1-registro" class="panel" style="display: block;">
        <h1>SISTEMA HERA</h1>
        <input type="text" id="input-nombre" placeholder="Nombre de investigador">
        <br>
        <button class="btn-gold" onclick="intentarIngresar()">INGRESAR</button>
        <button class="btn-gold" onclick="registrarUsuario()" style="background: #8b6b2d;">REGISTRAR</button>
    </div>

    <div id="p2-bienvenida" class="panel">
        <h2 id="titulo-bienvenida"></h2>
        <button class="btn-gold" onclick="navegar('p3-traduccion', 'Accediendo al módulo de traducción. Analizando manuscritos arqueológicos.')">TRADUCCIÓN</button>
        <button class="btn-gold" onclick="navegar('p4-piezas', 'Consultando el servidor de la nube. Procesando variables de laboratorio.')">DATOS NUBE</button>
    </div>

    <div id="p3-traduccion" class="panel">
        <h2>Traducción</h2>
        <div class="visor">Esperando datos de laboratorio...</div>
        <button class="btn-gold" onclick="hablar('Iniciando escaneo no invasivo. Procesando símbolos antiguos.')">INICIAR ANÁLISIS</button>
        <button class="btn-gold" onclick="navegar('p2-bienvenida', 'Volviendo al menú principal.')">VOLVER</button>
    </div>

    <div id="p4-piezas" class="panel">
        <h2>Datos Remotos</h2>
        <div id="visor-nube" class="visor">Conexión establecida.</div>
        <button class="btn-gold" onclick="navegar('p2-bienvenida', 'Volviendo al menú principal.')">VOLVER</button>
    </div>

    <script>
        // FORZAR VOZ FEMENINA
        function hablar(texto) {
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(texto);
            msg.lang = 'es-ES';
            msg.rate = 0.85; // Velocidad pausada
            msg.pitch = 1.3; // Tono más agudo, femenino
            
            const voces = window.speechSynthesis.getVoices();
            
            // PRIORIDAD DE VOCES FEMENINAS
            const vozElegida = voces.find(v => 
                v.name.includes('Google español') || 
                v.name.includes('Microsoft Helena') || 
                v.name.includes('Microsoft Laura') ||
                v.name.includes('Samantha') ||
                v.name.includes('Paulina')
            ) || voces[0];

            msg.voice = vozElegida;
            window.speechSynthesis.speak(msg);
        }

        function navegar(id, mensaje) {
            document.querySelectorAll('.panel').forEach(p => p.style.display = 'none');
            document.getElementById(id).style.display = 'block';
            hablar(mensaje);
        }

        function intentarIngresar() {
            const input = document.getElementById('input-nombre').value.trim();
            const registrado = localStorage.getItem('usuarioHERA');
            if (input === registrado && input !== "") {
                const genero = (registrado.toLowerCase().endsWith('a') && registrado.toLowerCase() !== 'jonas') ? "Bienvenida" : "Bienvenido";
                document.getElementById('titulo-bienvenida').innerText = genero + ", " + registrado;
                document.getElementById('p1-registro').style.display = 'none';
                document.getElementById('p2-bienvenida').style.display = 'block';
                hablar("Identidad verificada. " + genero + " de nuevo, " + registrado + ". El sistema HERA está listo.");
            } else {
                hablar("Acceso denegado. Usuario no registrado.");
            }
        }

        function registrarUsuario() {
            const nombre = document.getElementById('input-nombre').value.trim();
            if (nombre !== "") {
                localStorage.setItem('usuarioHERA', nombre);
                const genero = (nombre.toLowerCase().endsWith('a')) ? "Bienvenida" : "Bienvenido";
                document.getElementById('titulo-bienvenida').innerText = genero + ", " + nombre;
                document.getElementById('p1-registro').style.display = 'none';
                document.getElementById('p2-bienvenida').style.display = 'block';
                hablar("Registro completado. " + genero + " al sistema HERA, " + nombre + ". Es un placer trabajar con usted.");
            }
        }

        // Cargar voces al iniciar
        window.speechSynthesis.onvoiceschanged = () => { window.speechSynthesis.getVoices(); };
    </script>
</body>
</html>
