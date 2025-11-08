<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Azul — Learning Hub</title>
  <style>
    :root{
      --purple:#6b46c1;
      --purple-2:#8b5cf6;
      --bg:#f8f5ff;
      --card:#ffffff;
      --muted:#6b6b6b;
    }
    *{box-sizing:border-box;font-family:Inter, system-ui, -apple-system, 'Segoe UI', Roboto, 'Helvetica Neue', Arial}
    html,body{height:100%;margin:0;background:linear-gradient(180deg,var(--bg),#fff);}
    .app{min-height:100vh;display:flex;flex-direction:column;align-items:center;padding:28px}
    .container{width:100%;max-width:1100px}
    header{display:flex;align-items:center;justify-content:space-between;margin-bottom:18px}
    .brand{display:flex;align-items:center;gap:12px}
    .logo{width:48px;height:48px;border-radius:12px;background:linear-gradient(135deg,var(--purple),var(--purple-2));display:flex;align-items:center;justify-content:center;color:white;font-weight:700;font-size:20px}
    h1{margin:0;font-size:18px}
    p.lead{margin:0;color:var(--muted);font-size:13px}

    .viewport{position:relative;overflow:hidden;border-radius:16px;background:var(--card);box-shadow:0 6px 20px rgba(107,70,193,0.08);padding:20px}
    .panels{display:flex;transition:transform .35s cubic-bezier(.2,.9,.3,1);width:200%}
    .panel{width:50%;padding:10px}

    .nav{display:flex;gap:8px;align-items:center;margin-top:12px}
    .btn{background:transparent;border:1px solid rgba(107,70,193,0.12);padding:8px 12px;border-radius:10px;cursor:pointer}
    .btn.primary{background:linear-gradient(90deg,var(--purple),var(--purple-2));color:white;border:0}

    /* Tracker grid */
    .months{display:grid;grid-template-columns:repeat(3,1fr);gap:12px}
    .month{background:#fbf9ff;border-radius:12px;padding:10px;border:1px solid rgba(107,70,193,0.04)}
    .month h3{margin:0;font-size:14px;color:var(--purple)}
    .grid{display:grid;grid-template-columns:repeat(7,1fr);gap:6px;margin-top:8px}
    .day{background:white;border-radius:8px;padding:8px;min-height:36px;display:flex;align-items:center;justify-content:center;border:1px solid rgba(16,24,40,0.04);cursor:pointer;user-select:none}
    .day.done{background:linear-gradient(90deg,rgba(107,70,193,0.12),rgba(139,92,246,0.12));border-color:rgba(107,70,193,0.18)}
    .day .tick{font-weight:700;color:var(--purple)}
    .day.empty{background:transparent;border:0;cursor:default}

    /* Notes */
    .notes{display:flex;flex-direction:column;gap:8px}
    textarea{min-height:360px;padding:12px;border-radius:10px;border:1px solid rgba(16,24,40,0.06);resize:vertical}
    .meta{display:flex;justify-content:space-between;align-items:center}
    .small{font-size:13px;color:var(--muted)}

    footer{margin-top:16px;color:var(--muted);font-size:13px;text-align:center}

    /* responsive */
    @media (max-width:900px){.months{grid-template-columns:repeat(2,1fr)} .panel{padding:6px}}
    @media (max-width:600px){.months{grid-template-columns:1fr} .container{padding:0 8px}}
  </style>
</head>
<body>
  <div class="app">
    <div class="container">
      <header>
        <div class="brand">
          <div class="logo">A</div>
          <div>
            <h1>Azul — Learning Hub</h1>
            <p class="lead">Tracker anual + notas — guardado automático en tu navegador</p>
          </div>
        </div>
        <div>
          <button class="btn" id="exportBtn">Exportar JSON</button>
        </div>
      </header>

      <div class="viewport">
        <div class="panels" id="panels">
          <!-- Panel 1: Tracker anual -->
          <section class="panel" id="panel-tracker">
            <div style="display:flex;justify-content:space-between;align-items:center">
              <div>
                <h2 style="margin:0">Tracker — Año completo</h2>
                <div class="small">Haz click en un día para marcarlo. Tus marcas se guardan automáticamente.</div>
              </div>
              <div>
                <button class="btn" id="clearTracker">Limpiar</button>
              </div>
            </div>

            <div class="months" id="months"></div>
          </section>

          <!-- Panel 2: Aprendizajes -->
          <section class="panel" id="panel-notes">
            <div class="meta">
              <div>
                <h2 style="margin:0">Aprendizajes</h2>
                <div class="small">Escribe lo que aprendiste. Se guarda automáticamente en tu navegador.</div>
              </div>
              <div>
                <button class="btn" id="clearNotes">Limpiar notas</button>
              </div>
            </div>

            <div class="notes" style="margin-top:12px">
              <textarea id="notes" placeholder="Escribe aquí tus aprendizajes..."></textarea>
              <div style="display:flex;gap:8px;align-items:center;justify-content:flex-end">
                <button class="btn" id="saveNotes">Guardar ahora</button>
              </div>
            </div>
          </section>
        </div>
      </div>

      <div class="nav" style="justify-content:center">
        <button class="btn" id="prevBtn">⬅️ Anterior</button>
        <button class="btn primary" id="nextBtn">Siguiente ➡️</button>
      </div>

      <footer>
        Hecho para ti — sube este archivo a un repositorio en GitHub y activa GitHub Pages en la rama <code>main</code>.
      </footer>
    </div>
  </div>

  <script>
    // Datos de meses y días correctos
    const monthsInfo = [
      { name: 'Enero', days: 31 }, { name: 'Febrero', days: 28 }, { name: 'Marzo', days: 31 },
      { name: 'Abril', days: 30 }, { name: 'Mayo', days: 31 }, { name: 'Junio', days: 30 },
      { name: 'Julio', days: 31 }, { name: 'Agosto', days: 31 }, { name: 'Septiembre', days: 30 },
      { name: 'Octubre', days: 31 }, { name: 'Noviembre', days: 30 }, { name: 'Diciembre', days: 31 }
    ];

    const panelsEl = document.getElementById('panels');
    const monthsEl = document.getElementById('months');
    const notesEl = document.getElementById('notes');
    const prevBtn = document.getElementById('prevBtn');
    const nextBtn = document.getElementById('nextBtn');
    const clearTrackerBtn = document.getElementById('clearTracker');
    const clearNotesBtn = document.getElementById('clearNotes');
    const exportBtn = document.getElementById('exportBtn');
    const saveNotesBtn = document.getElementById('saveNotes');

    let viewIndex = 0; // 0 = tracker, 1 = notes

    // --- Local storage keys ---
    const STORAGE_TRACKER = 'azul_tracker_v1';
    const STORAGE_NOTES = 'azul_notes_v1';

    // cargar estado
    const trackerState = JSON.parse(localStorage.getItem(STORAGE_TRACKER) || '{}');
    notesEl.value = localStorage.getItem(STORAGE_NOTES) || '';

    function buildMonths() {
      monthsEl.innerHTML = '';
      monthsInfo.forEach((m, mi) => {
        const monthDiv = document.createElement('div');
        monthDiv.className = 'month';
        const title = document.createElement('h3');
        title.textContent = m.name;
        monthDiv.appendChild(title);

        const grid = document.createElement('div');
        grid.className = 'grid';

        // create placeholders so the month starts at day 1 on Monday isn't required here
        for(let d=1; d<=m.days; d++){
          const key = `${mi+1}-${d}`; // month-day
          const cell = document.createElement('div');
          cell.className = 'day';
          cell.dataset.key = key;
          cell.innerHTML = `<span class="num">${d}</span>`;
          if(trackerState[key]){
            cell.classList.add('done');
            cell.innerHTML = `<span class="tick">✅</span>`;
          }
          cell.addEventListener('click', ()=> toggleDay(cell));
          grid.appendChild(cell);
        }

        monthDiv.appendChild(grid);
        monthsEl.appendChild(monthDiv);
      });
    }

    function toggleDay(cell){
      const key = cell.dataset.key;
      if(cell.classList.contains('done')){
        cell.classList.remove('done');
        cell.innerHTML = `<span class="num">${key.split('-')[1]}</span>`;
        delete trackerState[key];
      } else {
        cell.classList.add('done');
        cell.innerHTML = `<span class="tick">✅</span>`;
        trackerState[key] = true;
      }
      saveTracker();
    }

    function saveTracker(){
      localStorage.setItem(STORAGE_TRACKER, JSON.stringify(trackerState));
    }

    function clearTracker(){
      if(!confirm('¿Borrar todas las marcas del tracker?')) return;
      for(const k in trackerState) delete trackerState[k];
      saveTracker();
      buildMonths();
    }

    // notes
    function saveNotes(){
      localStorage.setItem(STORAGE_NOTES, notesEl.value);
      alert('Notas guardadas.');
    }

    function clearNotes(){
      if(!confirm('¿Borrar todas las notas?')) return;
      notesEl.value = '';
      localStorage.removeItem(STORAGE_NOTES);
    }

    // navegación
    function updateView(){
      panelsEl.style.transform = `translateX(${viewIndex * -50}%)`;
    }
    prevBtn.addEventListener('click', ()=>{ viewIndex = Math.max(0, viewIndex-1); updateView(); });
    nextBtn.addEventListener('click', ()=>{ viewIndex = Math.min(1, viewIndex+1); updateView(); });

    // eventos
    clearTrackerBtn.addEventListener('click', clearTracker);
    saveNotesBtn.addEventListener('click', saveNotes);
    clearNotesBtn.addEventListener('click', clearNotes);
    notesEl.addEventListener('input', ()=>{
      // guardado automático con debounce
      if(window._notesTimeout) clearTimeout(window._notesTimeout);
      window._notesTimeout = setTimeout(()=>localStorage.setItem(STORAGE_NOTES, notesEl.value), 700);
    });

    exportBtn.addEventListener('click', ()=>{
      const data = { tracker: trackerState, notes: localStorage.getItem(STORAGE_NOTES) || '' };
      const blob = new Blob([JSON.stringify(data, null, 2)], {type:'application/json'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url; a.download = 'azul-learning-export.json'; document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
    });

    // init
    buildMonths();
    updateView();

    // accessibility: keyboard navigation left/right
    document.addEventListener('keydown', (e)=>{
      if(e.key === 'ArrowLeft') prevBtn.click();
      if(e.key === 'ArrowRight') nextBtn.click();
    });
  </script>
</body>
</html>

<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mi página morada 💜</title>
  <style>
    body {
      background-color: purple; /* Cambia a violet, lavender o un código hexadecimal si quieres otro tono */
      color: white; /* Para que el texto se vea bien sobre el fondo */
      font-family: Arial, sans-serif;
      text-align: center;
      padding-top: 100px;
    }
  </style>
</head>
<body>
  <h1>Bienvenido a mi página 💜</h1>
  <p>El fondo es morado, ¡qué elegante!</p>
</body>
</html>

<body style="background-color: purple;">
  <h1>Mi página Flask 💜</h1>
</body>
<!DOCTYPE html>
<html>
<head>
  <style>
    body {
      background-color: purple; /* Fondo morado */
    }
  </style>
</head>
<body>
  <h1>¡Hola, mundo!</h1>
  <p>Este fondo es morado 💜</p>
</body>
</html>

<img width="1080" height="1080" alt="Post de Instagram con Diagrama Infográfico de Ideas Simple Multicolor" src="https://github.com/user-attachments/assets/76dd515c-a633-4bc4-9bcf-cfd90d67e69e" />
# -azul123.github.io-
PROGRESO PARA APRENDIZAJE RICARDO
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Clases de Inglés con Azul</title>
  <style>
    body {
      background-color: #f7f7f7;
      font-family: Arial, sans-serif;
      text-align: center;
      padding: 40px;
    }
    h1 {
      color: #4a4e69;
    }
    p {
      color: #222;
    }
    button {
      background-color: #4a4e69;
      color: white;
      border: none;
      padding: 10px 20px;
      border-radius: 8px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <h1>Bienvenidos a mis clases 💫</h1>
  <p>Aprende inglés conmigo desde cualquier parte del mundo.</p>
  <button>Contáctame</button>
</body>
</html>

mi-pagina/
│
├── index.html          ← página principal
├── about.html          ← subpágina “Sobre mí”
├── contact.html        ← subpágina “Contacto”
└── style.css           ← estilos compartidos

