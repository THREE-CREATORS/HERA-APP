<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>HERA System Pro</title>
    <style>
        :root { --amber-gold: #d9a441; --bg-dark: #14100c; --text: #f5ead2; }
        body { font-family: 'Segoe UI', sans-serif; background-color: var(--bg-dark); color: #f5ead2; margin: 0; padding: 20px; display: flex; align-items: flex-start; gap: 20px; min-height: 100vh; }
        
        /* Logo Fijo a la izquierda */
        .logo-sticky { flex: 0 0 150px; text-align: center; position: sticky; top: 20px; }
        .logo-sticky img { width: 150px; height: 150px; border: 2px solid var(--amber-gold); border-radius: 50%; object-fit: cover; }

        /* Contenedor de paneles */
        .content-area { flex: 1; max-width: 800px; }
        
        .panel { 
            background: #1a150d; 
            border: 2px solid var(--amber-gold); 
            padding: 30px; 
            border-radius: 20px; 
            text-align: center; 
            display: none; 
            box-sizing: border-box;
        }
        
        h1, h2 { color: var(--amber-gold); margin-top: 0; }
        input { padding: 15px; width: 60%; border-radius: 8px; border: 1px solid var(--amber-gold); background: #000; color: #fff; margin-bottom: 20px; font-size: 1.2rem; }
        .btn-gold { background: var(--amber-gold); color: #000; padding: 15px 30px; border-radius: 8px; border: none; cursor: pointer; font-weight: bold; margin: 10px; font-size: 1.1rem; }
        .btn-reset { background: #555; color: #fff; padding: 10px 20px; border-radius: 8px; border: none; cursor: pointer; margin-top: 20px; }
        .visor { background: #000; color: #0f0; padding: 20px; border-radius: 8px; border: 1px solid #333; text-align: left; font-family: monospace; margin: 20px 0; min-height: 150px; }
    </style>
</head>
<body>

    <!-- Logo siempre visible fuera del flujo de paneles -->
    <div class="logo-sticky">
        <img src="logo-hera.jpg" alt="Logo HERA">
        <p style="color: var(--amber-gold); font-size: 0.9rem; margin-top: 5px;">BRIGHT MINDS</p>
    </div>

    <!-- Área de contenido -->
    <div class="content-area">
        <!-- P1: REGISTRO -->
        <div id="p1-registro" class="panel" style="display: block;">
            <h1>SISTEMA HERA</h1>
            <input type="text" id="input-nombre" placeholder="Nombre de investigador">
            <br>
            <button class="btn-gold" onclick="intentarIngresar()">INGRESAR</button>
            <button class="btn-gold" onclick="registrarUsuario()">REGISTRAR</button>
            <br>
            <button class="btn-reset" onclick="location.reload()">REINICIAR SISTEMA</button>
        </div>

        <!-- P2: MENÚ -->
        <div id="p2-bienvenida" class="panel">
            <h2 id="titulo-bienvenida"></h2>
            <button class="btn-gold" onclick="navegar('p3-traduccion', 'Accediendo al análisis de manuscritos.')">ANÁLISIS DE MANUSCRITOS</button>
            <button class="btn-gold" onclick="navegar('p4-piezas', 'Accediendo al análisis de piezas.')">ANÁLISIS DE PIEZAS</button>
            <br>
            <button class="btn-reset" onclick="navegar('p1-registro', 'Volviendo al inicio.')">VOLVER</button>
        </div>

        <!-- P3: MANUSCRITOS -->
        <div id="p3-traduccion" class="panel">
            <h2>Análisis de Manuscritos</h2>
            <div class="visor">Esperando datos de laboratorio...</div>
            <button class="btn-gold" onclick="hablar('Procesando símbolos antiguos.')">INICIAR ANÁLISIS</button>
            <br>
            <button class="btn-reset" onclick="navegar('p2-bienvenida', 'Regresando al menú.')">VOLVER</button>
        </div>

        <!-- P4: PIEZAS -->
        <div id="p4-piezas" class="panel">
            <h2>Análisis de Piezas</h2>
            <div id="visor-nube" class="visor">Conexión establecida. ESTRUCTURA_VALIDADA_OK.</div>
            <br>
            <button class="btn-reset" onclick="navegar('p2-bienvenida', 'Regresando al menú.')">VOLVER</button>
        </div>
    </div>

    <script>
        // Tus funciones JS se mantienen igual
        function hablar(texto) {
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(texto);
            msg.lang = 'es-ES';
            msg.rate = 0.9;
            msg.pitch = 1.3; 
            window.speechSynthesis.speak(msg);
        }

        function navegar(id, mensaje) {
            document.querySelectorAll('.panel').forEach(p => p.style.display = 'none');
            document.getElementById(id).style.display = 'block';
            if(mensaje) hablar(mensaje);
        }

        function activarBienvenida(nombre) {
            const genero = (nombre.toLowerCase().endsWith('a') && nombre.toLowerCase() !== 'jonas') ? "Bienvenida" : "Bienvenido";
            document.getElementById('titulo-bienvenida').innerText = genero + ", " + nombre;
            navegar('p2-bienvenida', genero + ", " + nombre + ". Sistema HERA listo.");
        }

        function intentarIngresar() {
            const input = document.getElementById('input-nombre').value.trim();
            if (input === localStorage.getItem('usuarioHERA') && input !== "") {
                activarBienvenida(input);
            } else { hablar("Acceso denegado."); }
        }

        function registrarUsuario() {
            const nombre = document.getElementById('input-nombre').value.trim();
            if (nombre !== "") {
                localStorage.setItem('usuarioHERA', nombre);
                activarBienvenida(nombre);
            }
        }
    </script>
</body>
</html>
