<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HERA System Pro</title>
    <style>
        :root { --amber-gold: #d9a441; --bg-dark: #14100c; --text: #f5ead2; }
        body { font-family: 'Segoe UI', sans-serif; background-color: var(--bg-dark); color: var(--text); margin: 0; display: flex; flex-direction: column; align-items: center; justify-content: center; min-height: 100vh; }
        .panel { width: 90%; max-width: 600px; background: #1a150d; border: 1px solid var(--amber-gold); padding: 40px; border-radius: 15px; text-align: center; display: none; }
        h1, h2 { color: var(--amber-gold); }
        input { padding: 15px; width: 80%; border-radius: 8px; border: 1px solid var(--amber-gold); background: #000; color: #fff; margin-bottom: 20px; font-size: 1rem; }
        .btn-gold { background: var(--amber-gold); color: #000; padding: 15px 30px; border-radius: 8px; border: none; cursor: pointer; font-weight: bold; font-size: 1rem; margin: 10px; }
        .visor { background: #000; color: #0f0; padding: 20px; border-radius: 8px; border: 1px solid #333; text-align: left; font-family: 'Courier New', monospace; margin: 20px 0; min-height: 100px; }
    </style>
</head>
<body>

    <div id="p1-registro" class="panel" style="display: block;">
        <h1>SISTEMA HERA</h1>
        <p>Identificación de Investigador</p>
        <input type="text" id="input-nombre" placeholder="Nombre completo">
        <br>
        <button class="btn-gold" onclick="intentarIngresar()">INGRESAR</button>
        <button class="btn-gold" onclick="registrarUsuario()" style="background: #8b6b2d;">REGISTRAR NUEVO</button>
    </div>

    <div id="p2-bienvenida" class="panel">
        <h2 id="titulo-bienvenida"></h2>
        <button class="btn-gold" onclick="showSection('p3-traduccion')">TRADUCCIÓN</button>
        <button class="btn-gold" onclick="showSection('p4-piezas')">PIEZAS</button>
        <button class="btn-gold" style="background: #555;" onclick="location.reload()">CERRAR SESIÓN</button>
    </div>

    <div id="p3-traduccion" class="panel">
        <h2>Módulo de Traducción</h2>
        <div class="visor">Esperando entrada de datos...</div>
        <button class="btn-gold" onclick="hablarProfesional('Procesando datos de laboratorio.')">TRADUCIR</button>
        <button class="btn-gold" onclick="showSection('p2-bienvenida')">VOLVER</button>
    </div>

    <div id="p4-piezas" class="panel">
        <h2>Resultados de Servidor</h2>
        <div id="visor-nube" class="visor">Cargando...</div>
        <button class="btn-gold" onclick="showSection('p2-bienvenida')">VOLVER</button>
    </div>

    <script>
        // Función para determinar el saludo según el género del nombre
        function obtenerSaludo(nombre) {
            const esFemenino = nombre.toLowerCase().endsWith('a') && nombre.toLowerCase() !== 'lucas' && nombre.toLowerCase() !== 'jonas';
            return esFemenino ? "Bienvenida" : "Bienvenido";
        }

        function hablarProfesional(texto) {
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(texto);
            msg.lang = 'es-ES';
            msg.rate = 0.95; // Velocidad natural
            msg.pitch = 1.1; // Tono femenino constante
            
            const voces = window.speechSynthesis.getVoices();
            // Filtro por voces de alta calidad (Google o Microsoft)
            msg.voice = voces.find(v => v.name.includes('Google español') || v.name.includes('Microsoft Laura') || v.name.includes('Microsoft Helena')) || voces[0];
            
            window.speechSynthesis.speak(msg);
        }

        function showSection(id) {
            document.querySelectorAll('.panel').forEach(p => p.style.display = 'none');
            document.getElementById(id).style.display = 'block';
        }

        function intentarIngresar() {
            const input = document.getElementById('input-nombre').value.trim();
            const registrado = localStorage.getItem('usuarioHERA');

            if (input !== "" && input === registrado) {
                const saludo = obtenerSaludo(registrado);
                document.getElementById('titulo-bienvenida').innerText = saludo + ", " + registrado;
                showSection('p2-bienvenida');
                hablarProfesional(saludo + " de nuevo al laboratorio, " + registrado);
            } else {
                alert("Acceso denegado: El nombre no coincide con el registrado.");
            }
        }

        function registrarUsuario() {
            const nombre = document.getElementById('input-nombre').value.trim();
            if (nombre === "") return alert("Ingrese un nombre.");
            
            localStorage.setItem('usuarioHERA', nombre);
            const saludo = obtenerSaludo(nombre);
            document.getElementById('titulo-bienvenida').innerText = saludo + ", " + nombre;
            showSection('p2-bienvenida');
            hablarProfesional("Registro completado. " + saludo + " al sistema HERA, " + nombre);
        }

        window.speechSynthesis.onvoiceschanged = () => { window.speechSynthesis.getVoices(); };
    </script>
</body>
</html>
