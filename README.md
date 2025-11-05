<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Novichok | Agenda tu presentación</title>
  <style>
    body {
      margin: 0;
      font-family: 'Poppins', sans-serif;
      background-color: #0a0a0a;
      color: #fff;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
    }
    .container {
      background-color: #111;
      border: 2px solid #d40000;
      border-radius: 16px;
      padding: 32px;
      width: 90%;
      max-width: 700px;
      box-shadow: 0 0 30px rgba(212, 0, 0, 0.6);
      text-align: center;
    }
    img.logo {
      width: 100px;
      height: auto;
      margin-bottom: 16px;
    }
    h1 {
      color: #ff2b2b;
      margin-bottom: 24px;
      text-shadow: 0 0 10px #ff2b2b;
    }
    form {
      display: flex;
      flex-direction: column;
      gap: 16px;
    }
    input, textarea {
      background-color: #1b1b1b;
      border: 1px solid #ff2b2b;
      border-radius: 8px;
      padding: 10px;
      color: #fff;
      font-size: 1em;
      outline: none;
      transition: 0.3s;
    }
    input:focus, textarea:focus {
      border-color: #ff4747;
      box-shadow: 0 0 10px #ff2b2b;
    }
    button {
      background-color: #ff2b2b;
      border: none;
      color: #fff;
      font-weight: bold;
      padding: 12px;
      border-radius: 8px;
      cursor: pointer;
      font-size: 1.1em;
      transition: 0.3s;
      box-shadow: 0 0 10px #ff2b2b;
    }
    button:hover {
      background-color: #ff4747;
      transform: scale(1.05);
    }

    /* Calendario futurista */
    .calendar {
      display: grid;
      grid-template-columns: repeat(7, 1fr);
      gap: 8px;
      background-color: #0f0f0f;
      border: 1px solid #ff2b2b;
      border-radius: 10px;
      padding: 14px;
      margin-top: 10px;
      box-shadow: inset 0 0 15px rgba(255, 43, 43, 0.3);
    }
    .calendar-day {
      background-color: #1b1b1b;
      border: 1px solid #ff2b2b;
      border-radius: 8px;
      padding: 10px;
      text-align: center;
      transition: all 0.25s ease;
      cursor: pointer;
      font-weight: 600;
    }
    .calendar-day:hover, .calendar-day.active {
      background-color: #ff2b2b;
      color: #fff;
      box-shadow: 0 0 12px #ff2b2b;
      transform: scale(1.1);
    }
    .calendar-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-top: 12px;
      color: #fff;
      font-weight: 600;
      text-shadow: 0 0 10px #ff2b2b;
    }
    .calendar-header button {
      background: none;
      border: none;
      color: #ff2b2b;
      font-size: 1.6em;
      cursor: pointer;
      transition: 0.2s;
    }
    .calendar-header button:hover {
      color: #ff4747;
      transform: scale(1.2);
    }

    /* Modal */
    .modal {
      display: none;
      position: fixed;
      top: 0; left: 0; width: 100%; height: 100%;
      background-color: rgba(0,0,0,0.85);
      justify-content: center;
      align-items: center;
      z-index: 10;
    }
    .modal-content {
      background-color: #111;
      padding: 30px;
      border-radius: 12px;
      text-align: center;
      border: 2px solid #ff2b2b;
      width: 90%;
      max-width: 400px;
      box-shadow: 0 0 25px #ff2b2b;
    }
    .modal-content h2 {
      color: #ff2b2b;
      margin-bottom: 12px;
      text-shadow: 0 0 10px #ff2b2b;
    }
  </style>
</head>
<body>
  <div class="container">
    <img src="https://raw.githubusercontent.com/NachFig19/FORMULARIO-NVCK/8740fe9f20cceb6c5aa2252c1d145b79d0890be0/NVCK%20logo.png" alt="Novichok Logo" class="logo">

    <h1>Novichok | Agenda tu presentación</h1>
    <form id="bookingForm">
      <input type="text" id="nombre" name="nombre" placeholder="Nombre del solicitante" required>
      <input type="email" id="email" name="email" placeholder="Correo electrónico" required>
      <input type="tel" id="celular" name="celular" placeholder="Celular (10 dígitos)" pattern="[0-9]{10}" maxlength="10" required>
      <input type="text" id="evento" name="evento" placeholder="Tipo de evento" required>

      <div class="calendar-header">
        <button type="button" id="prevMonth">◀</button>
        <span id="monthYear"></span>
        <button type="button" id="nextMonth">▶</button>
      </div>
      <div id="calendarContainer" class="calendar"></div>

      <input type="time" id="hora" name="hora" required>

      <input type="text" id="lugar" name="lugar" placeholder="Lugar del evento" required>
      <input type="number" id="tiempo" name="tiempo" placeholder="Duración de presentación (min)" required>
      <input type="number" id="presupuesto" name="presupuesto" placeholder="Presupuesto ($ MXN)" required>
      <button type="submit">Agendar</button>
    </form>
  </div>

  <div class="modal" id="modalConfirm">
    <div class="modal-content">
      <h2>Tu fecha quedó registrada 🎸</h2>
      <p>¡Pronto daremos confirmación!</p>
      <button onclick="closeModal()">Cerrar</button>
    </div>
  </div>

  <script>
    const form = document.getElementById('bookingForm');
    const modal = document.getElementById('modalConfirm');
    const calendarContainer = document.getElementById('calendarContainer');
    const monthYear = document.getElementById('monthYear');
    const prevMonth = document.getElementById('prevMonth');
    const nextMonth = document.getElementById('nextMonth');
    let selectedDate = null;
    let currentDate = new Date();

    function renderCalendar(date) {
      calendarContainer.innerHTML = '';
      const year = date.getFullYear();
      const month = date.getMonth();
      const firstDay = new Date(year, month, 1);
      const lastDay = new Date(year, month + 1, 0);
      monthYear.textContent = date.toLocaleDateString('es-MX', { month: 'long', year: 'numeric' }).toUpperCase();

      for (let i = 0; i < firstDay.getDay(); i++) {
        const blank = document.createElement('div');
        calendarContainer.appendChild(blank);
      }

      for (let d = 1; d <= lastDay.getDate(); d++) {
        const day = document.createElement('div');
        day.classList.add('calendar-day');
        day.textContent = d;
        day.addEventListener('click', () => {
          document.querySelectorAll('.calendar-day').forEach(el => el.classList.remove('active'));
          day.classList.add('active');
          selectedDate = new Date(year, month, d);
        });
        calendarContainer.appendChild(day);
      }
    }

    prevMonth.addEventListener('click', () => {
      currentDate.setMonth(currentDate.getMonth() - 1);
      renderCalendar(currentDate);
    });

    nextMonth.addEventListener('click', () => {
      currentDate.setMonth(currentDate.getMonth() + 1);
      renderCalendar(currentDate);
    });

    renderCalendar(currentDate);

    form.addEventListener('submit', async (e) => {
      e.preventDefault();

      const celularVal = form.celular.value.trim();
      if (!/^\d{10}$/.test(celularVal)) {
        alert('El número de celular debe tener exactamente 10 dígitos.');
        return;
      }

      if (!selectedDate) {
        alert('Por favor selecciona una fecha en el calendario.');
        return;
      }

      const data = {
        nombre: form.nombre.value.trim(),
        email: form.email.value.trim(),
        celular: celularVal,
        evento: form.evento.value.trim(),
        fecha: selectedDate.toISOString().split('T')[0],
        hora: form.hora.value,
        lugar: form.lugar.value.trim(),
        tiempo: form.tiempo.value,
        presupuesto: form.presupuesto.value
      };

      const webhookURL = "https://hook.us2.make.com/9hra3akxe68msm699uod7beplphxgb4m";

      try {
        await fetch(webhookURL, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify(data)
        });

        modal.style.display = 'flex';
        form.reset();
        selectedDate = null;
        document.querySelectorAll('.calendar-day').forEach(el => el.classList.remove('active'));
      } catch (error) {
        alert('Error al enviar los datos. Verifica tu conexión.');
        console.error(error);
      }
    });

    function closeModal() {
      modal.style.display = 'none';
    }
  </script>
</body>
</html>

