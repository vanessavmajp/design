<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Hospedagem para seu melhor AUmigo | Dom Doggo</title>
  <link href="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.css" rel="stylesheet" />
  <script src="https://cdn.jsdelivr.net/npm/flatpickr"></script>
  <script src="https://cdn.jsdelivr.net/npm/flatpickr/dist/l10n/pt.js"></script>
  <style>
    .fade-in {
      animation: fadeIn 0.6s ease-in-out forwards;
    }
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(20px); }
      to { opacity: 1; transform: translateY(0); }
    }
    @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap');
    body {
      font-family: 'Poppins', sans-serif;
      background: #ffffff;
      color: #b30000;
      padding: 0;
      margin: 0;
    }
    .container {
      max-width: 800px;
      margin: 0 auto;
      padding: 2rem;
      background: #fff6f6;
      border-radius: 16px;
      box-shadow: 0 10px 20px rgba(0,0,0,0.05);
    }
    h1 {
      text-align: center;
      color: #b30000;
    }
    label {
      margin-top: 1.5rem;
      font-weight: 600;
      display: block;
    }
    input[type="text"], select {
      width: 100%;
      padding: 0.8rem;
      margin-top: 0.5rem;
      border-radius: 12px;
      border: 1px solid #ccc;
      font-size: 1rem;
    }
    .calendar-pair {
      display: flex;
      justify-content: space-between;
      gap: 1rem;
      flex-wrap: wrap;
    }
    button {
      background: #b30000;
      color: white;
      padding: 1rem;
      border: none;
      border-radius: 12px;
      margin-top: 2rem;
      font-size: 1rem;
      cursor: pointer;
      width: 100%;
      transition: background 0.3s;
    }
    button:hover {
      background: #8a0000;
    }
    .resumo {
      background: #ffecec;
      padding: 1rem;
      border-radius: 12px;
      margin-top: 1.5rem;
      font-size: 0.95rem;
      color: #222;
      text-align: center;
      display: none;
    }
    .horarios {
      background: #fff0f0;
      padding: 1rem;
      border-radius: 12px;
      margin-top: 2rem;
      border: 1px dashed #b30000;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Hospedagem para seu melhor AUmigo 🐾</h1>
    <label for="tipoServico">Tipo de serviço:</label>
    <select id="tipoServico">
      <option value="hospedagem" selected>Hospedagem</option>
      <option value="creche">Creche</option>
    </select>

    <div id="hospedagemCampos">
      <div class="calendar-pair">
        <div style="flex:1; border: 2px solid #d32f2f; border-radius: 12px; padding: 1rem;">
          <label for="checkin">Data de entrada (Check-in):</label>
          <input type="text" id="checkin" placeholder="Escolha a data de entrada">
        </div>
        <div style="flex:1; border: 2px solid #2e7d32; border-radius: 12px; padding: 1rem;">
          <label for="checkout">Data de saída (Check-out):</label>
          <input type="text" id="checkout" placeholder="Escolha a data de saída">
        </div>
      </div>
      <div id="horarioSaidaContainer" class="horarios">
        ⏰ <strong>Check-in:</strong> das 08h às 11h<br>
        ⏰ <strong>Check-out:</strong> das 08h às 11h ou das 15h às 17h<br>
        <label for="horarioSaida">Selecione o horário de saída:</label>
        <select id="horarioSaida">
          <option value="manha">Manhã (08h às 11h)</option>
          <option value="tarde">Tarde (15h às 17h) — acréscimo de meia diária</option>
        </select>
      </div>
    </div>

    <div id="crecheCalendario" style="display: none;">
      <label for="diasCreche">Selecione os dias que o doguinho virá para a creche:</label>
      <input type="text" id="diasCreche" placeholder="Escolha as datas da creche">
    </div>

    <div class="resumo" id="resumo"></div>
    <button id="btnRefazer" onclick="window.location.reload();" style="display:none; background:#999">🔁 Refazer simulação</button>
    <button id="btnReservar" onclick="enviarReserva();">🐶 Reservar Agora</button>
  </div>

  <script>
    const checkinPicker = flatpickr("#checkin", { dateFormat: "Y-m-d", locale: "pt", inline: true });
    const checkoutPicker = flatpickr("#checkout", { dateFormat: "Y-m-d", locale: "pt", inline: true });
    const crechePicker = flatpickr("#diasCreche", { mode: "multiple", dateFormat: "Y-m-d", locale: "pt", inline: true });

    document.getElementById('tipoServico').addEventListener('change', function () {
      const tipo = this.value;
      document.getElementById('hospedagemCampos').style.display = tipo === 'hospedagem' ? 'block' : 'none';
      document.getElementById('crecheCalendario').style.display = tipo === 'creche' ? 'block' : 'none';
      document.getElementById('horarioSaidaContainer').style.display = tipo === 'hospedagem' ? 'block' : 'none';
    });

    function enviarReserva() {
      const tipo = document.getElementById('tipoServico').value;
      let diarias = 0;

      if (tipo === 'creche') {
        const datasSelecionadas = crechePicker.selectedDates;
        diarias = datasSelecionadas.length;
        if (diarias === 0) {
          alert("Por favor, selecione ao menos uma data para a creche.");
          return;
        }
      } else {
        const checkin = new Date(document.getElementById('checkin').value);
        const checkout = new Date(document.getElementById('checkout').value);
        if (!checkin || !checkout || isNaN(checkin.getTime()) || isNaN(checkout.getTime())) {
          alert("Por favor, selecione datas válidas para hospedagem.");
          return;
        }
        const diasSemana = [1, 2, 3, 4, 5];
        const tempDate = new Date(checkin);
        while (tempDate <= checkout) {
          if (diasSemana.includes(tempDate.getDay())) diarias++;
          tempDate.setDate(tempDate.getDate() + 1);
        }
      }

      let valorDiaria = tipo === 'hospedagem' ? 100 : 80;
      let pacote = tipo === 'hospedagem' ? 'Diária padrão (R$ 100,00 por dia)' : 'Avulso (R$ 80,00 por dia)';
      if (diarias >= 20) { valorDiaria = 50; pacote = "Pacote 5x semana (R$ 50,00 por dia)"; }
      else if (diarias >= 16) { valorDiaria = 55; pacote = "Pacote 4x semana (R$ 55,00 por dia)"; }
      else if (diarias >= 12) { valorDiaria = 61.66; pacote = "Pacote 3x semana (R$ 61,66 por dia)"; }
      else if (diarias >= 8) { valorDiaria = 67.5; pacote = "Pacote 2x semana (R$ 67,50 por dia)"; }
      else if (diarias >= 4) { valorDiaria = 70; pacote = "Pacote 1x semana (R$ 70,00 por dia)"; }

      const horarioSaida = tipo === 'hospedagem' ? document.getElementById('horarioSaida').value : '';
      const taxaExtra = tipo === 'hospedagem' && horarioSaida === 'tarde' ? valorDiaria / 2 : 0;
      const totalCalculado = (diarias * valorDiaria) + taxaExtra;
      const entrada = totalCalculado / 2;
      const listaDatas = tipo === 'creche' ? crechePicker.selectedDates.map(d => d.toLocaleDateString('pt-BR')).join('<br>') : '';

      document.getElementById("btnReservar").style.display = 'none';
      document.getElementById("btnRefazer").style.display = 'block';
      const resumo = document.getElementById("resumo");
      resumo.style.display = 'block';
      resumo.classList.add('fade-in');

      resumo.innerHTML = `
        <strong>Resumo da Reserva:</strong><br>
        Serviço: <strong>${tipo === 'hospedagem' ? 'Hospedagem' : 'Creche'}</strong><br>
        🗓️ ${diarias} diária(s)<br>
        📦 ${pacote}<br>
        💰 Valor total: R$ ${totalCalculado.toFixed(2)}${taxaExtra > 0 ? ' (inclui meia diária adicional)' : ''}<br>
        💸 Entrada (50%): R$ ${entrada.toFixed(2)}<br>
        <strong style='color: #b30000;'>✨ Sua vaga está quase garantida! Para confirmar, é só realizar o pagamento da entrada via PIX.</strong><br>
        ${tipo === 'creche' ? '<br>📅 <strong>Datas escolhidas:</strong><br>' + listaDatas : ''}
        <br><br>
        <div style="margin-top: 1rem;">
          <strong>PIX para pagamento:</strong><br>
          Chave: <code>53562090000170</code><br>
          <img src="https://api.qrserver.com/v1/create-qr-code/?data=53562090000170&size=150x150" alt="QR Code PIX">
        </div>
        <br>
        <a id="whatsLink" href="#" target="_blank">
          <button style="background: #25D366; margin-top: 1rem;">📲 Enviar comprovante via WhatsApp</button>
        </a>`;

      const whatsLink = document.getElementById("whatsLink");
      const resumoTexto = `Olá! 🐾\nSegue o resumo da minha reserva no Dom Doggo:\nServiço: ${tipo === 'hospedagem' ? 'Hospedagem' : 'Creche'}\nDiárias: ${diarias}\nPacote: ${pacote}\nValor total: R$ ${totalCalculado.toFixed(2)}${taxaExtra > 0 ? ' (inclui meia diária adicional)' : ''}\nEntrada: R$ ${entrada.toFixed(2)}${tipo === 'creche' ? '\nDatas:\n' + crechePicker.selectedDates.map(d => d.toLocaleDateString('pt-BR')).join('\n') : ''}`;
      whatsLink.href = `https://wa.me/5585991816619?text=${encodeURIComponent(resumoTexto)}`;

      fetch("https://hook.us1.make.com/pd1g285let9154btigmmrt8t1b1ya69a", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          tipo_servico: tipo,
          diarias: diarias,
          pacote: pacote,
          valor_total: totalCalculado,
          entrada: entrada,
          horario_saida: horarioSaida || null,
          datas_creche: tipo === 'creche' ? crechePicker.selectedDates.map(d => d.toISOString()) : [],
          data_checkin: tipo === 'hospedagem' ? document.getElementById('checkin').value : null,
          data_checkout: tipo === 'hospedagem' ? document.getElementById('checkout').value : null
        })
      });
    }
  </script>
</body>
</html>
