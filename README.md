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

    <!-- PAGINA 1: REGISTRO MANUAL -->
    <div id="p1-registro" class="panel" style="display: block;">
        <h1>SISTEMA HERA</h1>
        <p>Por favor, registre su usuario para acceder al sistema.</p>
        <input type="text" id="input-nombre" placeholder="Nombre completo del investigador">
        <br>
        <button class="btn-gold" onclick="registrarUsuario()">REGISTRAR USUARIO</button>
    </div>

    <!-- PAGINA 2: BIENVENIDA -->
    <div id="p2-bienvenida" class="panel">
        <h2 id="titulo-bienvenida"></h2>
        <button class="btn-gold" onclick="showSection('p3-traduccion')">TRADUCCIÓN</button>
        <button class="btn-gold" onclick="showSection('p4-piezas')">PIEZAS (RESULTADOS NUBE)</button>
    </div>

    <!-- PAGINA 3: TRADUCCIÓN -->
    <div id="p3-traduccion" class="panel">
        <h2>Módulo de Traducción</h2>
        <div class="visor">Esperando entrada de datos...</div>
        <button class="btn-gold" onclick="hablarProfesional('Procesando traducción de manuscrito seleccionado.')">TRADUCIR</button>
        <button class="btn-gold" onclick="showSection('p2-bienvenida')">VOLVER</button>
    </div>

    <!-- PAGINA 4: RESULTADOS DE NUBE -->
    <div id="p4-piezas" class="panel">
        <h2>Resultados de Servidor (Nube)</h2>
        <div id="visor-nube" class="visor">Cargando datos remotos...</div>
        <button class="btn-gold" onclick="showSection('p2-bienvenida')">VOLVER</button>
    </div>

    <script>
        // Configuración de Voz Profesional
        function hablarProfesional(texto) {
            window.speechSynthesis.cancel(); // Detiene cualquier voz anterior
            const msg = new SpeechSynthesisUtterance(texto);
            msg.lang = 'es-ES';
            msg.rate = 0.9; // Velocidad pausada y profesional
            msg.pitch = 1.0; // Tono neutro/femenino
            
            // Intenta buscar una voz de alta calidad (Google o Microsoft)
            const voces = window.speechSynthesis.getVoices();
            msg.voice = voces.find(v => v.name.includes('Google español') || v.name.includes('Microsoft Maria')) || voces[0];
            
            window.speechSynthesis.speak(msg);
        }

        // Navegación
        function showSection(id) {
            document.querySelectorAll('.panel').forEach(p => p.style.display = 'none');
            document.getElementById(id).style.display = 'block';
            
            if(id === 'p4-piezas') {
                cargarDatosNube();
            }
        }

        // Registro Real
        function registrarUsuario() {
            const nombre = document.getElementById('input-nombre').value;
            if (nombre.trim() === "") {
                alert("Debe ingresar un nombre para registrarse.");
                return;
            }
            localStorage.setItem('usuarioHERA', nombre);
            document.getElementById('titulo-bienvenida').innerText = "Bienvenido al sistema, " + nombre;
            showSection('p2-bienvenida');
            hablarProfesional("Registro exitoso. Bienvenida al sistema HERA, " + nombre + ". Es un honor asistirle en sus investigaciones.");
        }

        // Carga automática en Pag 4
        function cargarDatosNube() {
            const datos = "STATUS_IP: 192.168.1.50\nSYNC_DATA: SUCCESS\nTEMP_PROC: 34C\nRES_01: ESTRUCTURA_VALIDADA\nRES_02: COMPOSICION_METALICA";
            document.getElementById('visor-nube').innerText = datos;
            hablarProfesional("Se han recibido los datos de la nube. La estructura ha sido validada exitosamente.");
        }

        // Inicialización para cargar voces al abrir
        window.speechSynthesis.onvoiceschanged = () => { window.speechSynthesis.getVoices(); };
    </script>
</body>
</html>
