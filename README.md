
<html lang="id">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Kuis Misteri — Tebak yang Suka</title>
  <meta name="description" content="Kuis lucu dan romantis untuk dikirim ke crush. Responsive, interaktif, dan ringan." />
  <style>
    :root{
      --bg:#fff8f2; --card:#ffffff; --accent:#ff6b81; --muted:#6b6b6b; --glass: rgba(255,255,255,0.6);
      --shadow: 0 6px 18px rgba(0,0,0,0.08);
    }
    *{box-sizing:border-box}
    body{font-family:Inter,ui-sans-serif,system-ui,-apple-system,Segoe UI,Roboto,'Helvetica Neue',Arial; background:linear-gradient(180deg,#fff8f2 0%, #fff 70%); margin:0; color:#222}
    .wrap{max-width:760px;margin:28px auto;padding:18px}
    header{display:flex;align-items:center;gap:12px}
    .logo{width:64px;height:64px;border-radius:12px;background:linear-gradient(135deg,#ffd3da,var(--accent));display:flex;align-items:center;justify-content:center;font-weight:700;color:#fff;box-shadow:var(--shadow)}
    h1{margin:0;font-size:1.25rem}
    p.lead{margin:8px 0 18px;color:var(--muted)}

    .card{background:var(--card);border-radius:14px;padding:18px;box-shadow:var(--shadow)}
    .question{margin:12px 0}
    .q-title{font-weight:600;margin-bottom:8px}
    .choices{display:flex;flex-wrap:wrap;gap:8px}
    .choice{flex:1 1 calc(50% - 8px);min-width:120px;background:var(--bg);padding:10px;border-radius:10px;border:1px solid rgba(0,0,0,0.06);cursor:pointer;text-align:center}
    .choice.selected{border-color:var(--accent);box-shadow:0 6px 18px rgba(255,107,129,0.12);transform:translateY(-2px)}
    .controls{display:flex;gap:8px;align-items:center;margin-top:14px}
    button{background:var(--accent);color:#fff;border:0;padding:10px 14px;border-radius:10px;cursor:pointer}
    button.secondary{background:transparent;color:var(--accent);border:1px solid var(--accent)}
    .small{font-size:0.9rem;color:var(--muted)}

    .progress{height:10px;background:linear-gradient(90deg,#ffe9ec,#fff);border-radius:999px;overflow:hidden;margin-bottom:12px}
    .progress > i{display:block;height:100%;background:linear-gradient(90deg,var(--accent),#ff9db0);width:0%}

    .result{padding:12px;border-radius:12px;background:linear-gradient(180deg,#fff 0%, #fff7f8 100%);margin-top:14px}
    .clues{margin-top:8px;padding:10px;border-radius:8px;background:linear-gradient(180deg,#fff9fa,#fff);border:1px dashed rgba(255,107,129,0.18)}

    footer{margin-top:18px;text-align:center;color:var(--muted);font-size:0.85rem}

    @media (max-width:520px){.choice{flex:1 1 100%}}

    /* little playful animations */
    .sparkle{display:inline-block;transform-origin:center center}
    .heart{display:inline-block;margin-left:6px;transform:translateY(2px)}
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <div class="logo">💌</div>
      <div>
        <h1>Kuis Misteri: Tebak yang Suka</h1>
        <p class="lead">Kirim kuis ini ke crush-mu. Jawabannya lucu, ada sentuhan romantis, dan... pembuatnya tidak langsung ketahuan. 🕵️‍♂️💖</p>
      </div>
    </header>

    <main class="card" id="app">
      <div class="progress" aria-hidden><i id="prog"></i></div>

      <div id="questionArea"></div>

      <div class="controls">
        <button id="prevBtn" class="secondary" style="display:none">Kembali</button>
        <button id="nextBtn">Lanjut</button>
        <div style="flex:1"></div>
        <button id="downloadBtn" class="secondary" title="Unduh file HTML untuk dikirim">Unduh</button>
      </div>

      <div class="small" style="margin-top:10px">Catatan: Hasil kuis berisi petunjuk halus (3 petunjuk) agar crush bisa menebak — tanpa menyebutkan nama langsung.</div>

      <div id="resultBox" style="display:none" class="result">
        <!-- hasil muncul di sini -->
      </div>
    </main>

    <footer>hasil gabut</footer>
  </div>

  <script>
    // Kumpulan pertanyaan lucu & romantis. Nilai tiap jawaban mempengaruhi 'tipe' hasil.
    const questions = [
      {
        id:1, title: 'Kalau weekend, kamu lebih suka...',
        choices: [
          {txt:'Nonton film romantis di rumah', val:2},
          {txt:'Ngobrol sambil jalan-jalan santai', val:3},
          {txt:'Main game bareng teman', val:1},
          {txt:'Mencoba kafe baru', val:2}
        ]
      },
      {
        id:2, title: 'Kamu paling gampang tersentuh kalau orang lain...',
        choices:[
          {txt:'Bisa dengarkan cerita aku', val:3},
          {txt:'Ngasih kejutan kecil', val:2},
          {txt:'Bantuin tugas tanpa diminta', val:1},
          {txt:'Nulis pesan manis sesekali', val:3}
        ]
      },
      {
        id:3, title: 'Apa soundtrack yang pas buat momen berdua?',
        choices:[
          {txt:'Lagu akustik mellow', val:3},
          {txt:'Beat santai yang chill', val:1},
          {txt:'Lagu pop ceria', val:2},
          {txt:'Instrumental piano', val:3}
        ]
      },
      {
        id:4, title: 'Kalau dikasih pilihan kado kecil, kamu pilih...',
        choices:[
          {txt:'Surat tangan lucu', val:3},
          {txt:'Sticky notes berisi pujian', val:2},
          {txt:'Snack favorit', val:1},
          {txt:'Tanaman kecil', val:2}
        ]
      },
      {
        id:5, title: 'Kalau ketemu di sekolah, yang bikin hati deg-degan adalah...',
        choices:[
          {txt:'Senyuman tiba-tiba', val:3},
          {txt:'Sapa yang tulus', val:2},
          {txt:'Disenggol dan saling ngetawain', val:1},
          {txt:'Obrol pendek tapi hangat', val:3}
        ]
      }
    ];

    // Petunjuk berdasarkan skor (halus, tidak menyebut nama)
    const hintsByType = {
      1: {
        title: 'Si Penggemar Ceria',
        msg: 'Seseorang yang suka bikin suasana jadi ringan — sering menawarkan snack atau ngajak bercanda. Mereka terlihat santai tapi perhatian.',
        clues: ['Warna favorit: Cerah atau pastel','Tempat sering terlihat: Area kantin / lapangan sekolah','Suka: Game ringan & jokes']
      },
      2: {
        title: 'Si Penyayang Perhatian',
        msg: 'Seseorang yang sering ingat hal-hal kecil: tugas, tanggal, atau makanan favoritmu. Gaya mereka hangat dan perhatian.',
        clues: ['Warna favorit: Netral atau hangat','Tempat sering terlihat: Perpustakaan / koridor kelas','Suka: Mencoba kafe baru & kejutan kecil']
      },
      3: {
        title: 'Si Romantis Halus',
        msg: 'Seseorang yang suka detail dan gestur manis — surat, lagu mellow, atau pesan yang tiba-tiba. Mereka pendiam tapi manis.',
        clues: ['Warna favorit: Soft / pastel','Tempat sering terlihat: Bangku taman / pojok baca','Suka: Lagu akustik & surat tangan']
      }
    };

    let idx = 0;
    let answers = {};
    const qArea = document.getElementById('questionArea');
    const prog = document.getElementById('prog');

    function renderQuestion(i){
      const q = questions[i];
      qArea.innerHTML = `
        <div class="question">
          <div class="q-title">${i+1}. ${escapeHtml(q.title)}</div>
          <div class="choices" id="choices${q.id}"></div>
        </div>
      `;
      const choicesEl = document.getElementById('choices'+q.id);
      q.choices.forEach((c,ci)=>{
        const btn = document.createElement('div');
        btn.className='choice';
        btn.innerText = c.txt;
        btn.dataset.val = c.val;
        btn.dataset.idx = ci;
        btn.addEventListener('click', ()=>{
          // mark selected
          document.querySelectorAll('#choices'+q.id+' .choice').forEach(x=>x.classList.remove('selected'));
          btn.classList.add('selected');
          answers[q.id] = c.val; // simpan nilai
        });
        choicesEl.appendChild(btn);
      });

      // jika sudah jawab sebelumnya, restore selection
      if(answers[q.id]){
        const sel = [...choicesEl.children].find(ch=>ch.dataset.val==answers[q.id]);
        if(sel) sel.classList.add('selected');
      }

      updateProgress();
      toggleNav();
    }

    function updateProgress(){
      const pct = Math.round((Object.keys(answers).length / questions.length) * 100);
      prog.style.width = pct + '%';
    }

    function toggleNav(){
      document.getElementById('prevBtn').style.display = idx>0 ? 'inline-block' : 'none';
      document.getElementById('nextBtn').innerText = (idx<questions.length-1) ? 'Lanjut' : 'Selesai';
    }

    document.getElementById('nextBtn').addEventListener('click', ()=>{
      // ensure current question answered
      const curQ = questions[idx];
      if(!answers[curQ.id]){ alert('Pilih salah satu jawabannya dulu ya 😊'); return; }
      if(idx < questions.length-1){ idx++; renderQuestion(idx); }
      else { showResult(); }
    });

    document.getElementById('prevBtn').addEventListener('click', ()=>{ if(idx>0) { idx--; renderQuestion(idx); } });

    function showResult(){
      // calculate score
      const total = Object.values(answers).reduce((s,v)=>s+Number(v),0);
      // normalize to type 1,2,3
      const avg = total / questions.length;
      let type;
      if(avg <= 1.6) type = 1;
      else if(avg <= 2.4) type = 2;
      else type = 3;

      const res = hintsByType[type];
      const box = document.getElementById('resultBox');
      box.style.display='block';
      box.innerHTML = `
        <h3>Hasil: ${res.title} <span class="sparkle">✨</span></h3>
        <p>${escapeHtml(res.msg)}</p>
        <div class="clues">
          <strong>Petunjuk halus (bisa jadi &nbsp;– tebak sendiri):</strong>
          <ol>
            ${res.clues.map(c=>`<li>${escapeHtml(c)}</li>`).join('')}
          </ol>
        </div>
        <p style="margin-top:10px">Kalau kamu penasaran siapa pembuatnya, mereka sengaja bikin teka-teki: <em>perhatikan warna favorit, tempat yang sering kamu kunjungi, dan hal kecil yang mereka lakukan</em>. Kalau mau, klik tombol <strong>Bagikan</strong> untuk kirim hasil ini ke dia.</p>
        <div style="display:flex;gap:8px;margin-top:8px">
          <button id="shareBtn">Bagikan via WhatsApp</button>
          <button id="replayBtn" class="secondary">Main Lagi</button>
        </div>
      `;

      document.getElementById('shareBtn').addEventListener('click', ()=>{
        const msg = `Hei! Aku kirim kuis lucu nih — coba jawab ya. Hasilku: ${res.title}. Petunjuk: ${res.clues.join(' | ')}. Mau tebak siapa pengirim?`;
        const url = location.href;
        const wa = `https://wa.me/?text=${encodeURIComponent(msg + '\n' + url)}`;
        window.open(wa,'_blank');
      });

      document.getElementById('replayBtn').addEventListener('click', ()=>{ answers={}; idx=0; document.getElementById('resultBox').style.display='none'; renderQuestion(0); prog.style.width='0%'; });

      // scroll to result
      box.scrollIntoView({behavior:'smooth'});
    }

    // small helper
    function escapeHtml(s){ return String(s).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;'); }

    // initial render
    renderQuestion(0);

    // Download the current HTML file so user can send the page
    document.getElementById('downloadBtn').addEventListener('click', ()=>{
      const html = `<!doctype html>\n${document.documentElement.outerHTML}`;
      const blob = new Blob([html], {type:'text/html'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a'); a.href = url; a.download = 'kuis-misteri-crush.html'; document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
    });

  </script>
</body>
</html>
