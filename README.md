<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AnimeFlix - Portal de Animes</title>
<style>
  body { margin:0; background:#111; color:#fff; font-family:'Segoe UI', sans-serif; }
  header { position:fixed; top:0; width:100%; background:#141414; z-index:1000; display:flex; align-items:center; justify-content:space-between; padding:10px 20px; }
  header h1 { color:#e50914; font-size:24px; }
  nav button { background:none; border:none; color:#fff; margin-left:15px; font-size:16px; cursor:pointer; transition:0.3s; }
  nav button:hover { color:#e50914; }

  main { margin-top:70px; }

  .banner { position:relative; height:400px; background:#222; display:flex; align-items:flex-end; padding:20px; border-bottom:2px solid #333; }
  .banner img { position:absolute; top:0; left:0; width:100%; height:100%; object-fit:cover; filter:brightness(0.5); z-index:0; }
  .banner-content { position:relative; z-index:1; }
  .banner-content h2 { font-size:36px; margin-bottom:10px; }
  .banner-content button { background:#e50914; border:none; padding:10px 20px; color:#fff; font-size:16px; border-radius:5px; cursor:pointer; }

  .section { margin:20px 0; }
  .section h3 { margin-left:20px; font-size:22px; margin-bottom:10px; }
  .anime-row { display:flex; overflow-x:auto; padding-left:20px; gap:15px; scroll-behavior:smooth; }
  .anime-card { flex:0 0 auto; width:160px; transition:transform 0.3s; position:relative; cursor:pointer; }
  .anime-card img { width:100%; height:240px; object-fit:cover; border-radius:6px; }
  .anime-card h4 { text-align:center; margin-top:5px; font-size:14px; color:#f0f0f0; }
  .anime-card p { text-align:center; font-size:12px; color:#ccc; margin-top:2px; }

  /* Modal Player */
  .modal { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:rgba(0,0,0,0.95); justify-content:center; align-items:center; z-index:9999; padding:20px; overflow-y:auto; }
  .modal-content { background:#222; padding:15px; border-radius:10px; max-width:900px; width:100%; position:relative; }
  .modal-content video { width:100%; border-radius:8px; }
  .close-btn { position:absolute; top:10px; right:10px; background:#e50914; border:none; padding:6px 10px; border-radius:6px; color:#fff; cursor:pointer; font-weight:bold; }

  .episode-list { display:flex; flex-wrap:wrap; gap:10px; margin-top:10px; }
  .episode-list button { padding:5px 10px; border:none; border-radius:5px; background:#e50914; color:#fff; cursor:pointer; font-size:12px; }
  .episode-list button:hover { background:#b20710; }

  footer { text-align:center; padding:15px; background:#141414; font-size:14px; color:#9ca3af; margin-top:40px; }

  @media(max-width:600px){
    .anime-card img { height:180px; }
    .banner { height:180px; }
    .banner-content h2 { font-size:20px; }
  }
</style>
</head>
<body>

<header>
  <h1>AnimeFlix</h1>
  <nav>
    <button>Ação</button>
    <button>Romance</button>
    <button>Comédia</button>
    <button>Fantasia</button>
    <button>Mecha</button>
  </nav>
</header>

<main>
  <div class="banner">
    <img src="https://cdn.myanimelist.net/images/anime/1985/117326.jpg" alt="Banner Anime">
    <div class="banner-content">
      <h2>86 EIGHTY-SIX</h2>
      <button onclick="playAnime('https://lightspeedst.net/s1/mp4/86-2nd-season/sd/1.mp4','86 EIGHTY-SIX')">Assistir</button>
    </div>
  </div>

  <!-- Categorias -->
  <div class="section" id="acaoSection">
    <h3>Ação</h3>
    <div class="anime-row" id="acaoRow"></div>
  </div>
  <div class="section" id="romanceSection">
    <h3>Romance</h3>
    <div class="anime-row" id="romanceRow"></div>
  </div>
  <div class="section" id="comediaSection">
    <h3>Comédia</h3>
    <div class="anime-row" id="comediaRow"></div>
  </div>
  <div class="section" id="fantasiaSection">
    <h3>Fantasia</h3>
    <div class="anime-row" id="fantasiaRow"></div>
  </div>
  <div class="section" id="mechaSection">
    <h3>Mecha</h3>
    <div class="anime-row" id="mechaRow"></div>
  </div>
</main>

<!-- Modal Player -->
<div class="modal" id="modalPlayer">
  <div class="modal-content">
    <button class="close-btn" id="closeModal">X</button>
    <video controls id="videoPlayer">
      <source src="" type="video/mp4">
      Seu navegador não suporta vídeo HTML5.
    </video>
    <h3 id="videoTitle" style="text-align:center; margin-top:10px; color:#e50914;"></h3>
  </div>
</div>

<footer>
  AnimeFlix &copy; 2025 - Todos os direitos reservados
</footer>

<script>
// Dados dos animes
const animeData = {
  acao:[
    {title:'86 EIGHTY-SIX', img:'https://cdn.myanimelist.net/images/anime/1985/117326.jpg', episodes:['https://lightspeedst.net/s1/mp4/86-2nd-season/sd/1.mp4']},
    {title:'Attack on Titan', img:'https://cdn.myanimelist.net/images/anime/10/47347.jpg', episodes:['https://lightspeedst.net/s1/mp4/aot/sd/1.mp4']},
    {title:'Demon Slayer', img:'https://cdn.myanimelist.net/images/anime/1286/99889.jpg', episodes:['https://lightspeedst.net/s1/mp4/demon-slayer/sd/1.mp4']}
  ],
  romance:[
    {title:'Seijo no Maryoku wa Bannou Desu – 2ª Temporada', img:'https://cdn.myanimelist.net/images/anime/1142/118296.jpg',
     episodes:[
       'https://outrange.animesonline.red/assistironline/seijo-no-maryoku-wa-bannou-desu-2nd-season-dublado/seijo-no-maryoku-wa-bannou-desu-2nd-season-dublado-episodio-1.mp4'
     ]}
  ],
  comedia:[
    {title:'One Punch Man', img:'https://cdn.myanimelist.net/images/anime/12/76049.jpg', episodes:['https://lightspeedst.net/s1/mp4/one-punch-man/sd/1.mp4']},
    {title:'KonoSuba', img:'https://cdn.myanimelist.net/images/anime/8/77831.jpg', episodes:['https://lightspeedst.net/s1/mp4/konosuba/sd/1.mp4']}
  ],
  fantasia:[
    {title:'Sword Art Online', img:'https://cdn.myanimelist.net/images/anime/11/39717.jpg', episodes:['https://lightspeedst.net/s1/mp4/sao/sd/1.mp4']},
    {title:'Re:Zero', img:'https://cdn.myanimelist.net/images/anime/11/79410.jpg', episodes:['https://lightspeedst.net/s1/mp4/re-zero/sd/1.mp4']}
  ],
  mecha:[
    {title:'Gundam', img:'https://cdn.myanimelist.net/images/anime/1448/118792.jpg', episodes:['https://lightspeedst.net/s1/mp4/gundam/sd/1.mp4']},
    {title:'Evangelion', img:'https://cdn.myanimelist.net/images/anime/1314/108941.jpg', episodes:['https://lightspeedst.net/s1/mp4/evangelion/sd/1.mp4']}
  ]
};

// Função para criar cards e lista de episódios
function populateSection(sectionId, animes){
  const container = document.getElementById(sectionId);
  animes.forEach(anime=>{
    const card = document.createElement('div');
    card.className='anime-card';
    card.innerHTML=`<img src="${anime.img}" alt="${anime.title}"><h4>${anime.title}</h4><p>Total de episódios: ${anime.episodes.length}</p>`;
    card.addEventListener('click', ()=> showAnimeModal(anime));
    container.appendChild(card);
  });
}

// Mostra modal com lista de episódios
function showAnimeModal(anime){
  modal.style.display='flex';
  videoPlayer.src = anime.episodes[0];
  videoTitle.textContent = anime.title + ' - Episódio 1';
  
  const oldList = document.getElementById('episodeList');
  if(oldList) oldList.remove();
  
  const listContainer = document.createElement('div');
  listContainer.id = 'episodeList';
  listContainer.className = 'episode-list';
  
  anime.episodes.forEach((ep,index)=>{
    const btn = document.createElement('button');
    btn.textContent = 'Episódio ' + (index+1);
    btn.addEventListener('click', ()=>{
      videoPlayer.src = ep;
      videoTitle.textContent = anime.title + ' - Episódio ' + (index+1);
      videoPlayer.play();
    });
    listContainer.appendChild(btn);
  });
  
  modal.querySelector('.modal-content').appendChild(listContainer);
}

// Fecha modal
const modal = document.getElementById('modalPlayer');
const videoPlayer = document.getElementById('videoPlayer');
const videoTitle = document.getElementById('videoTitle');
const closeModal = document.getElementById('closeModal');
closeModal.addEventListener('click', ()=>{
  modal.style.display='none';
  videoPlayer.pause();
  videoPlayer.src='';
  const oldList = document.getElementById('episodeList');
  if(oldList) oldList.remove();
});

// Inicializa todas as seções
populateSection('acaoRow', animeData.acao);
populateSection('romanceRow', animeData.romance);
populateSection('comediaRow', animeData.comedia);
populateSection('fantasiaRow', animeData.fantasia);
populateSection('mechaRow', animeData.mecha);

// Função para banner assistir
function playAnime(videoUrl, title){
  videoPlayer.src = videoUrl;
  videoTitle.textContent = title;
  modal.style.display='flex';
}
</script>

</body>
</html>
