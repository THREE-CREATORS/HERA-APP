#include <WiFi.h>
#include <ESPAsyncWebServer.h>
#include <HTTPClient.h>

const char* ssid = "HERA_Local_Net";
const char* password = "123456789";
const char* IP_NUBE = "http://TU_IP_DE_LA_NUBE_AQUI"; // REEMPLAZAR AQUÍ

AsyncWebServer server(80);

const char index_html[] PROGMEM = R"rawliteral(
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HERA System</title>
    <style>
        :root { --nav-blue: #001f3f; --accent-yellow: #ffcc00; --text-black: #000000; --danger: #ff4d4d; }
        body { margin: 0; background: var(--nav-blue); color: white; font-family: 'Segoe UI', sans-serif; display: flex; flex-direction: column; align-items: center; min-height: 100vh; }
        .section { padding: 40px 20px; display: none; text-align: center; width: 100%; flex-direction: column; align-items: center; }
        #btn-menu { position: absolute; top: 15px; right: 15px; padding: 10px 15px; background: var(--accent-yellow); color: var(--text-black); border-radius: 10px; cursor: pointer; font-weight: bold; font-size: 0.9rem; z-index: 1000; }
        #menu-usuarios { display: none; position: absolute; top: 60px; right: 15px; background: #00274d; padding: 15px; border-radius: 10px; border: 1px solid var(--accent-yellow); width: 200px; text-align: left; }
        .btn { padding: 25px; border-radius: 20px; border: none; cursor: pointer; font-weight: bold; margin: 10px; width: 90%; max-width: 400px; font-size: 1.2rem; background: var(--accent-yellow); color: var(--text-black); }
        .btn-small { padding: 15px 30px; width: auto; }
        .btn-danger { background: #ff4d4d; color: white; }
        .row { display: flex; gap: 20px; justify-content: center; margin-top: 20px; }
        input { padding: 15px; margin: 10px; border-radius: 10px; border: none; width: 80%; max-width: 300px; }
        .visor { background: #000; padding: 30px; border-radius: 20px; margin: 20px auto; width: 80%; border: 3px solid var(--accent-yellow); color: #fff; font-size: 1.2rem; min-height: 150px; text-align: left; }
    </style>
</head>
<body>
    <div id="btn-menu" onclick="toggleMenu()">USUARIOS 👤</div>
    <div id="menu-usuarios"><h3>Registrados</h3><div id="lista-nombres"></div></div>

    <div id="splash" class="section" style="display: flex;">
        <h1 style="font-size: 4rem;">HERA</h1>
        <input type="text" id="login-nombre" placeholder="Ingrese nombre de usuario">
        <button class="btn" onclick="verificarAcceso()">INGRESAR</button>
        <button class="btn btn-small" onclick="registrarUsuario()">REGISTRAR</button>
    </div>

    <div id="presentacion" class="section">
        <h1>HOLA, SOY HERA.</h1>
        <p style="font-size: 1.5rem;">¿ESTAS LISTO PARA DESCUBRIR NUESTRO PASADO?</p>
        <div class="row">
            <button class="btn btn-small" onclick="showSection('dashboard'); hablar('Accediendo al panel.')">SÍ</button>
            <button class="btn btn-small btn-danger" onclick="volver()">NO</button>
        </div>
    </div>

    <div id="dashboard" class="section">
        <h1>Panel de Control</h1>
        <button class="btn btn-small" onclick="conectarNube()">CONECTAR A NUBE</button>
        <div class="row">
            <button class="btn" onclick="abrirVisor('traducciones')">TRADUCCIÓN</button>
            <button class="btn" onclick="abrirVisor('piezas')">PIEZAS</button>
        </div>
        <button class="btn" onclick="toggleTarea()">INICIAR TAREA</button>
        <button class="btn btn-danger" onclick="volver()">SALIR</button>
    </div>

    <div id="visor-datos" class="section">
        <h2 id="titulo-visor">Información</h2>
        <div class="visor" id="contenido-visor">Cargando...</div>
        <button class="btn" onclick="showSection('dashboard')">VOLVER AL PANEL</button>
    </div>

    <script>
        let baseDeDatos = ["Eliana"];
        const IP_NUBE = "http://TU_IP_DE_LA_NUBE_AQUI"; 

        function hablar(t) { window.speechSynthesis.cancel(); const m = new SpeechSynthesisUtterance(t); m.lang = 'es-VE'; window.speechSynthesis.speak(m); }
        function showSection(id) { document.querySelectorAll('.section').forEach(s => s.style.display = 'none'); document.getElementById(id).style.display = 'flex'; }
        function toggleMenu() { const menu = document.getElementById('menu-usuarios'); menu.style.display = (menu.style.display === 'block') ? 'none' : 'block'; actualizarLista(); }
        function actualizarLista() { document.getElementById('lista-nombres').innerHTML = baseDeDatos.map((u, index) => `<p>${u} <span onclick="eliminarUsuario(${index})">❌</span></p>`).join(''); }
        function eliminarUsuario(index) { baseDeDatos.splice(index, 1); actualizarLista(); }
        function verificarAcceso() { if(baseDeDatos.includes(document.getElementById('login-nombre').value)) { hablar("Bienvenido"); showSection('presentacion'); } else { hablar("Usuario no encontrado."); } }
        function registrarUsuario() { let n = document.getElementById('login-nombre').value; if(n && !baseDeDatos.includes(n)) { baseDeDatos.push(n); hablar("Registrado"); actualizarLista(); } }
        
        function toggleTarea() {
            showSection('visor-datos');
            document.getElementById('titulo-visor').innerText = "MANUAL DE INICIO";
            document.getElementById('contenido-visor').innerHTML = "<p>1. Ubique los dos botones laterales.<br>2. Presiónelos al mismo tiempo.<br>3. Espere la confirmación visual.</p>";
            hablar("Presione los dos botones del robot simultáneamente.");
        }

        async function conectarNube() { try { await fetch(IP_NUBE + '/status'); hablar("Conectado."); } catch(e) { alert("Error"); } }
        async function abrirVisor(tipo) {
            showSection('visor-datos');
            document.getElementById('titulo-visor').innerText = tipo.toUpperCase();
            try { const res = await fetch(IP_NUBE + '/' + tipo); document.getElementById('contenido-visor').innerText = await res.text(); } 
            catch(e) { document.getElementById('contenido-visor').innerText = "Error de conexión."; }
        }
        function volver() { showSection('splash'); }
    </script>
</body>
</html>
)rawliteral";

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  
  while (WiFi.status() != WL_CONNECTED) delay(500);
  
  // Conexión automática a la nube al arrancar
  HTTPClient http;
  http.begin(String(IP_NUBE) + "/status");
  http.GET();
  http.end();

  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){ request->send_P(200, "text/html", index_html); });
  server.begin();
}

void loop() {}
