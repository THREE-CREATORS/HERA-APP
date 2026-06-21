<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HERA System</title>
    <style>
        :root { --nav-blue: #001f3f; --accent-yellow: #ffcc00; --silver: #c0c0c0; }
        body { margin: 0; background: #001f3f; color: white; font-family: sans-serif; height: 100vh; overflow: hidden; }

        /* Barra superior */
        .top-bar { padding: 15px; border-bottom: 1px solid var(--silver); display: flex; align-items: center; }
        .menu-btn { background: none; border: 1px solid var(--silver); color: white; padding: 5px 15px; border-radius: 5px; cursor: pointer; }

        /* Pantalla de inicio */
        #home { display: flex; flex-direction: column; justify-content: center; align-items: center; height: 80vh; }
        .btn-main { background: var(--accent-yellow); color: var(--nav-blue); padding: 20px 40px; border-radius: 50px; font-weight: bold; border: none; font-size: 1.2rem; cursor: pointer; }

        /* Lista de usuarios (oculta inicialmente) */
        #user-list { display: none; padding: 20px; }
        
        /* Botón circular "+" */
        .fab { position: fixed; bottom: 30px; right: 30px; width: 60px; height: 60px; background: var(--accent-yellow); border-radius: 50%; font-size: 30px; border: none; box-shadow: 0 4px 10px rgba(0,0,0,0.3); cursor: pointer; }

        /* Formulario registro (oculto) */
        #register-form { display: none; padding: 20px; }
        input { width: 100%; padding: 10px; margin: 10px 0; border-radius: 5px; border: none; }
    </style>
</head>
<body onclick="iniciarVoz()"> <div class="top-bar">
        <button class="menu-btn" onclick="toggleMenu()">OPCIONES</button>
    </div>

    <div id="home">
        <h1>BIENVENIDO A HERA</h1>
        <button class="btn-main">INICIA SESIÓN</button>
    </div>

    <div id="user-list">
        <h2>Usuarios Registrados</h2>
        <ul><li>Juan Pérez</li><li>María García</li></ul>
    </div>

    <div id="register-form">
        <h2>Registrar Usuario</h2>
        <input type="text" placeholder="Nombre">
        <input type="text" placeholder="Apellido">
        <button class="btn-main">Activar Reconocimiento Facial</button>
    </div>

    <button class="fab" onclick="showRegister()">+</button>

    <script>
        // Iniciar voz automáticamente al primer toque
        let vozIniciada = false;
        function iniciarVoz() {
            if(!vozIniciada) {
                const msg = new SpeechSynthesisUtterance("Bienvenido al sistema Hera. Por favor, inicia sesión.");
                msg.lang = 'es-ES';
                window.speechSynthesis.speak(msg);
                vozIniciada = true;
            }
        }

        function toggleMenu() { 
            document.getElementById('home').style.display = 'none';
            document.getElementById('user-list').style.display = 'block';
        }

        function showRegister() {
            document.getElementById('user-list').style.display = 'none';
            document.getElementById('register-form').style.display = 'block';
        }
    </script>
</body>
</html>
