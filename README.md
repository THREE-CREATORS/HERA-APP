<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>HERA System Pro</title>
    <style>
        :root { --amber-gold: #d9a441; --bg-dark: #14100c; --text: #f5ead2; }
        body { font-family: 'Segoe UI', sans-serif; background-color: var(--bg-dark); color: #f5ead2; margin: 0; display: flex; align-items: center; justify-content: flex-start; min-height: 100vh; padding-left: 50px; }
        
        .main-container { display: flex; align-items: center; gap: 60px; width: 95%; }
        
        .logo-box { flex: 0 0 220px; text-align: center; }
        
        /* Estilo mejorado para el texto corporativo con los colores del sistema */
        .brand-title {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            font-size: 1.8rem;
            font-weight: 800;
            letter-spacing: 2px;
            background: linear-gradient(135deg, #fff 20%, var(--amber-gold) 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            text-shadow: 0 2px 10px rgba(217, 164, 65, 0.3);
            margin: 0;
            line-height: 1.2;
        }

        /* Panel en formato rectángulo alargado horizontalmente */
        .panel { 
            flex-grow: 1; 
            background: #1a150d; 
            border: 2px solid var(--amber-gold); 
            padding: 40px; 
            border-radius: 20px; 
            text-align: center; 
            display: none;
            box-sizing: border-box;
            height: auto;
        }
        
        h1 { color: var(--amber-gold); margin-top: 0; border-bottom: 1px solid var(--amber-gold); padding-bottom: 20px; }
        input { padding: 15px; width: 60%; border-radius: 8px; border: 1px solid var(--amber-gold); background: #000; color: #fff; margin: 20px 0; font-size: 1.2rem; }
        .btn-gold { background: var(--amber-gold); color: #000; padding: 15px 30px; border-radius: 8px; border: none; cursor: pointer; font-weight: bold; margin: 10px; font-size: 1.1rem; }
        .btn-reset { background: #555; color: #fff; padding: 10px 20px; border-radius: 8px; border: none; cursor: pointer; margin-top: 20px; }
        .visor { background: #000; color: #0f0; padding: 20px; border-radius: 8px; border: 1px solid #333; text-align: left; font-family: monospace; margin: 20px 0; min-height: 150px; }
    </style>
</head>
<body>

    <div class="main-container">
        <!-- Lado Izquierdo: Texto corporativo mejorado -->
        <div class="logo-box">
            <h2 class="brand-title">BRIGHT<br>MINDS</h2>
        </div>

        <!-- Lado Derecho: Panel Rectangular Alargado -->
        <div id="p1-registro" class="panel" style="display: block;">
            <h1>BIENVENID@ AL SISTEMA HERA</h1>
            <input type="text" id="input-nombre" placeholder="Nombre de investigador">
            <br>
            <button class="btn-gold" onclick="intentarIngresar()">INGRESAR</button>
            <button class="btn-gold" onclick="registrarUsuario()">REGISTRAR</button>
            <br>
            <button class="btn-reset" onclick="location.reload()">REINICIAR SISTEMA</button>
        </div>

        <div id="p2-bienvenida" class="panel">
            <h2 id="titulo-bienvenida" style="color: var(--amber-gold);"></h2>
            <button class="btn-gold" onclick="navegar('p3-traduccion', 'Accediendo al análisis de manuscritos.')">ANÁLISIS DE MANUSCRITOS</button>
            <button class="btn-gold" onclick="navegar('p4-piezas', 'Accediendo al análisis de piezas.')">ANÁLISIS DE PIEZAS</button>
            <br>
            <button class="btn-reset" onclick="navegar('p1-registro', 'Volviendo al inicio.')">VOLVER</button>
        </div>

        <div id="p3-traduccion" class="panel">
            <h2 style="color: var(--amber-gold);">Análisis de Manuscritos</h2>
            <div class="visor">Esperando datos de laboratorio...</div>
            <button class="btn-gold" onclick="hablar('Procesando símbolos antiguos.')">INICIAR ANÁLISIS</button>
            <br>
            <button class="btn-reset" onclick="navegar('p2-bienvenida', 'Regresando al menú.')">VOLVER</button>
        </div>

        <div id="p4-piezas" class="panel">
            <h2 style="color: var(--amber-gold);">Análisis de Piezas</h2>
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
            msg.pitch = 1.4; // Tono más agudo para voz femenina
            const voces = window.speechSynthesis.getVoices();
            // Selección de voz femenina
            msg.voice = voces.find(v => v.name.includes('Google español') || v.name.includes('Microsoft Helena') || v.name.includes('female')) || voces[0];
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
            hablar(textoCompleto + ". Sistema HERA listo.");
        }

        function intentarIngresar() {
            const input = document.getElementById('input-nombre').value.trim();
            const registrado = localStorage.getItem('usuarioHERA');
            if (input === registrado && input !== "") {
                activarBienvenida(registrado);
            } else {
                hablar("Acceso denegado.");
            }
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
