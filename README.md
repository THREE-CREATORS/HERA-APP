<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Bienvenida HERA</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background: linear-gradient(135deg, #001f3f, #003366); /* Azul marino profundo */
            color: #ffffff;
            font-family: 'Segoe UI', sans-serif;
            height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            text-align: center;
            overflow: hidden;
        }

        /* Detalles Plateados y Blancos */
        .glass-card {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(15px);
            border: 2px solid rgba(192, 192, 192, 0.5); /* Color plata */
            padding: 40px;
            border-radius: 30px;
            box-shadow: 0 0 20px rgba(255, 255, 255, 0.2);
            width: 80%;
            max-width: 500px;
        }

        h1 {
            color: #ffffff;
            text-shadow: 0 0 10px rgba(255, 255, 255, 0.5);
            font-size: 2rem;
            margin-bottom: 30px;
        }

        /* Detalle amarillo */
        .btn-start {
            background: #ffcc00; /* Amarillo HERA */
            color: #001f3f;
            padding: 15px 30px;
            border: none;
            border-radius: 50px;
            font-weight: bold;
            cursor: pointer;
            transition: transform 0.2s;
        }

        .btn-start:active { transform: scale(0.9); }
    </style>
</head>
<body>

    <div class="glass-card">
        <h1>BIENVENIDO A LA PÁGINA WEB DE HERA</h1>
        <button class="btn-start" onclick="iniciarHERA()">INICIAR SISTEMA</button>
    </div>

    <script>
        function iniciarHERA() {
            // HERA Hablando
            const mensaje = new SpeechSynthesisUtterance("Bienvenido a la página web de Hera");
            mensaje.lang = 'es-ES';
            window.speechSynthesis.speak(mensaje);
            
            // Aquí puedes redirigir a tu dashboard principal después de 2 segundos
            setTimeout(() => {
                alert("Entrando al sistema...");
                // window.location.href = "dashboard.html"; // Descomenta esto cuando tengas el otro archivo
            }, 2000);
        }
    </script>
</body>
</html>
