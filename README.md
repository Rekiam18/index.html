<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculadora Premium</title>
    <style>
        /* Diseño general de la página */
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #0f172a; /* Fondo oscuro moderno */
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            margin: 0;
        }

        /* Contenedor principal de la calculadora */
        .calculadora {
            background-color: #1e293b;
            padding: 24px;
            border-radius: 24px;
            box-shadow: 0px 20px 40px rgba(0, 0, 0, 0.4);
            width: 320px;
        }

        /* Pantalla */
        .pantalla {
            width: 100%;
            height: 75px;
            background-color: #0f172a;
            border: none;
            border-radius: 14px;
            color: #ffffff;
            font-size: 2.2rem;
            text-align: right;
            padding: 15px;
            box-sizing: border-box;
            margin-bottom: 20px;
        }

        /* Cuadrícula de botones */
        .botones {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 12px;
        }

        /* Estilo de los botones */
        button {
            height: 62px;
            border: none;
            border-radius: 50%;
            font-size: 1.4rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s ease;
        }

        button:active {
            transform: scale(0.92);
        }

        .numero { background-color: #334155; color: #f8fafc; }
        .numero:hover { background-color: #475569; }

        .operador { background-color: #6366f1; color: white; } /* Azul/Morado premium */
        .operador:hover { background-color: #4f46e5; }

        .especial { background-color: #94a3b8; color: #0f172a; }
        .especial:hover { background-color: #cbd5e1; }

        .cero { grid-column: span 2; border-radius: 30px; }

        /* --- ESTILOS DE LA ALERTA PROFESIONAL (MODAL) --- */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(15, 23, 42, 0.75); /* Fondo oscuro translúcido */
            backdrop-filter: blur(8px); /* Efecto borroso elegante */
            display: flex;
            justify-content: center;
            align-items: center;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.3s ease;
            z-index: 1000;
        }

        /* Cuando la alerta está activa */
        .modal-overlay.activo {
            opacity: 1;
            pointer-events: auto;
        }

        .modal-box {
            background: #1e293b;
            border: 1px solid #334155;
            padding: 30px;
            border-radius: 20px;
            width: 340px;
            text-align: center;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
            transform: scale(0.8);
            transition: transform 0.3s ease;
            color: white;
        }

        .modal-overlay.activo .modal-box {
            transform: scale(1);
        }

        .modal-icono {
            font-size: 3rem;
            margin-bottom: 15px;
            display: inline-block;
        }

        .modal-box h3 {
            margin: 0 0 10px 0;
            font-size: 1.4rem;
            font-weight: 700;
        }

        .modal-box p {
            color: #94a3b8;
            font-size: 0.95rem;
            line-height: 1.5;
            margin: 0 0 24px 0;
        }

        /* Botón de pago premium */
        .btn-pagar {
            background: linear-gradient(135deg, #6366f1 0%, #a855f7 100%);
            color: white;
            border: none;
            padding: 14px 28px;
            font-size: 1rem;
            font-weight: bold;
            border-radius: 12px;
            width: 100%;
            cursor: pointer;
            box-shadow: 0 4px 15px rgba(99, 102, 241, 0.4);
            transition: all 0.2s ease;
        }

        .btn-pagar:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(99, 102, 241, 0.6);
        }

        .btn-cerrar {
            background: transparent;
            color: #64748b;
            border: none;
            margin-top: 14px;
            font-size: 0.9rem;
            cursor: pointer;
            text-decoration: underline;
        }
    </style>
</head>
<body>

    <!-- CALCULADORA -->
    <div class="calculadora">
        <input type="text" class="pantalla" id="pantalla" readonly value="0">
        
        <div class="botones">
            <button class="especial" onclick="limpiar()">C</button>
            <button class="especial" onclick="borrarUno()">←</button>
            <button class="operador" onclick="agregar('%')">%</button>
            <button class="operador" onclick="agregar('/')">÷</button>

            <button class="numero" onclick="agregar('7')">7</button>
            <button class="numero" onclick="agregar('8')">8</button>
            <button class="numero" onclick="agregar('9')">9</button>
            <button class="operador" onclick="agregar('*')">×</button>

            <button class="numero" onclick="agregar('4')">4</button>
            <button class="numero" onclick="agregar('5')">5</button>
            <button class="numero" onclick="agregar('6')">6</button>
            <button class="operador" onclick="agregar('-')">-</button>

            <button class="numero" onclick="agregar('1')">1</button>
            <button class="numero" onclick="agregar('2')">2</button>
            <button class="numero" onclick="agregar('3')">3</button>
            <button class="operador" onclick="agregar('+')">+</button>

            <button class="numero cero" onclick="agregar('0')">0</button>
            <button class="numero" onclick="agregar('.')">.</button>
            <button class="operador" onclick="calcular()">=</button>
        </div>
    </div>

    <!-- ESTRUCTURA DE LA ALERTA PROFESIONAL (MODAL INTERNO) -->
    <div class="modal-overlay" id="alertaPremium">
        <div class="modal-box">
            <span class="modal-icono">✨</span>
            <h3>Función Premium</h3>
            <p>Para ver los resultados de tus operaciones necesitas adquirir una membresía ilimitada.</p>
            <button class="btn-pagar" onclick="simularPago()">Desbloquear por 10 lukas</button>
            <button class="btn-cerrar" onclick="cerrarAlerta()">Tal vez más tarde</button>
        </div>
    </div>

    <!-- LÓGICA JAVASCRIPT -->
    <script>
        const pantalla = document.getElementById('pantalla');
        const alerta = document.getElementById('alertaPremium');
        let tieneMembresia = false; 

        function agregar(valor) {
            if (pantalla.value === '0' || pantalla.value === 'Premium' || pantalla.value === 'Error') {
                pantalla.value = valor;
            } else {
                pantalla.value += valor;
            }
        }

        function limpiar() { pantalla.value = '0'; }
        
        function borrarUno() {
            if (pantalla.value.length > 1) {
                pantalla.value = pantalla.value.slice(0, -1);
            } else {
                pantalla.value = '0';
            }
        }

        // Muestra la ventana profesional si no ha pagado
        function calcular() {
            if (!tieneMembresia) {
                alerta.classList.add('activo'); // Enciende la ventana flotante
                pantalla.value = "Premium";
                return; 
            }

            try {
                pantalla.value = eval(pantalla.value);
            } catch (error) {
                pantalla.value = 'Error';
            }
        }

        // Cierra la alerta si pulsa "Más tarde"
        function cerrarAlerta() {
            alerta.classList.remove('activo');
        }

        // Simula la compra y oculta la ventana flotante
        function simularPago() {
            tieneMembresia = true;
            cerrarAlerta();
            pantalla.value = "0";
            // Esta última alerta es nativa, pero la puedes quitar o personalizar igual después
            alert("🎉 ¡Membresía activada! Ya puedes calcular sin límites.");
        }
    </script>

</body>
</html>
