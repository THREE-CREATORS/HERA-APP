<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HERA Dashboard</title>
    <style>
        :root { --nav-blue: #001f3f; --accent-yellow: #ffcc00; --silver: #c0c0c0; }
        body { margin: 0; background: #001f3f; color: white; font-family: 'Segoe UI', sans-serif; height: 100vh; overflow-y: auto; }

        .section { padding: 40px 20px; display: none; text-align: center; }
        #home { display: flex; flex-direction: column; justify-content: center; align-items: center; height: 100vh; }
        
        h1 { color: white; margin-bottom: 30px; }
        .btn { padding: 20px 30px; border-radius: 15px; border: none; cursor: pointer; font-weight: bold; font-size: 1.1rem; margin: 10px; width: 90%; max-width: 400px; transition: 0.3s; }
        
        .btn-main { background: var(--accent-yellow); color: var(--nav-blue); }
        .btn-opt { background: rgba(255,255,255,0.1); color: white; border: 1px solid var(--silver); }
        
        .status-badge { padding: 5px 15px; border-radius: 20px; background: #28a745; display: inline-block; margin-bottom: 20px; font-size: 0.8rem; }
    </style>
</head>
<body onclick="iniciarVoz()">

    <div id="home" class="section" style="display:block;">
        <h1>HERA</h1>
        <button class="btn btn-main" onclick="showSection('dashboard')">EMPIEZA A DESCUBRIR CON HERA</button>
    </div>

    <div id="dashboard" class="section">
        <div class="status-badge">● CONECTADO</div>
        <h1>Panel de Control</h1>
        
        <button class="btn btn-opt" onclick="alert('Iniciando traducción...')">Traducción de Lenguajes</button>
        <button class="btn btn-opt" onclick="alert('Identificando piezas...')">Identificación de Piezas</button>
        <button class="btn btn-opt" onclick="alert('Lecturas en curso. Tiempo faltante: 12 min.')">Empezar las lecturas / Tiempo faltante</button>
        <button class="btn btn-opt" onclick="alert('Reconectando sistema...')">Conectar / Conectado</button>
        
        <br><br>
        <button class="btn" style="background:transparent; border: 1px solid #ff4d4d; color: #ff4d4d; width: 50%;" onclick="showSection('home')">SALIR</button>
    </div>

    <script>
        function iniciarVoz() {
            // Solo habla una vez gracias a localStorage
            if(!localStorage.getItem('bienvenida_hera')) {
                const msg = new SpeechSynthesisUtterance("Bienvenido a Hera, aquí podrás descubrir nuestro pasado.");
                msg.lang = 'es-ES';
                window.speechSynthesis.speak(msg);
                localStorage.setItem('bienvenida_hera', 'true');
            }
        }

        function showSection(id) {
            document.querySelectorAll('.section').forEach(s => s.style.display = 'none');
            document.getElementById(id).style.display = 'block';
        }
    </script>
</body>
</html>
