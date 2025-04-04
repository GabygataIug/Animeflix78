<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>AnimeFlix</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: Arial, sans-serif; background-color: #000; color: #fff; }
    header { background: #141414; padding: 20px; display: flex; justify-content: space-between; align-items: center; }
    .logo { font-size: 28px; font-weight: bold; color: #e50914; }
    nav a { color: white; margin-left: 15px; text-decoration: none; }
    .container { padding: 20px; }
    .anime-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(150px, 1fr)); gap: 20px; }
    .anime-card { background: #222; border-radius: 8px; overflow: hidden; text-align: center; }
    .anime-card img { width: 100%; height: 200px; object-fit: cover; }
    .anime-card p { padding: 10px; }
    .player { max-width: 800px; margin: 20px auto; display: none; flex-direction: column; gap: 10px; }
    .player iframe { width: 100%; height: 450px; border: none; border-radius: 10px; }
    .login-container { max-width: 400px; margin: 100px auto; padding: 30px; background: #111; border-radius: 10px; display: none; }
    .login-container input { width: 100%; padding: 10px; margin: 10px 0; border-radius: 5px; border: none; }
    .login-container button { width: 100%; padding: 10px; background: #e50914; color: white; border: none; border-radius: 5px; cursor: pointer; }
    .search { margin: 20px 0; }
    .search input { width: 100%; padding: 10px; border-radius: 5px; border: none; }
    .btn { background: #e50914; color: white; padding: 5px 10px; border: none; border-radius: 5px; cursor: pointer; }
  </style>
</head>
<body>

  <div class="login-container" id="loginPage">
    <h2>Entrar no AnimeFlix</h2>
    <input type="text" id="usuario" placeholder="Usuário" />
    <input type="password" id="senha" placeholder="Senha" />
    <button onclick="fazerLogin()">Entrar</button>
  </div>

  <div id="mainApp" style="display: none;">
    <header>
      <div class="logo">AnimeFlix</div>
      <nav>
        <a href="#" onclick="mostrarPagina('catalogo')">Catálogo</a>
        <a href="#" onclick="mostrarPagina('favoritos')">Favoritos</a>
        <a href="#" onclick="mostrarPagina('historico')">Histórico</a>
      </nav>
    </header>

    <div class="container">
      <div class="search">
        <input type="text" id="searchInput" placeholder="Buscar anime..." oninput="filtrarAnimes()"/>
      </div>

      <div id="catalogo" class="anime-grid"></div>
      <div id="favoritos" class="anime-grid" style="display: none;"></div>
      <div id="historico" class="anime-grid" style="display: none;"></div>

      <div class="player" id="playerBox">
        <h3 id="playerTitle"></h3>
        <iframe id="playerIframe" allowfullscreen></iframe>
        <button class="btn" onclick="fecharPlayer()">Fechar</button>
      </div>
    </div>
  </div>

  <script>
    const senhaCorreta = "animeflix900";

    const animes = [
      {
        id: "demon-slayer",
        titulo: "Demon Slayer",
        imagem: "https://cdn.myanimelist.net/images/anime/1286/99889.jpg",
        video: "https://www.youtube.com/embed/VQGCKyvzIM4"
      },
      {
        id: "attack-on-titan",
        titulo: "Attack on Titan",
        imagem: "https://cdn.myanimelist.net/images/anime/10/47347.jpg",
        video: "https://www.youtube.com/embed/MGRm4IzK1SQ"
      },
      {
        id: "jujutsu-kaisen",
        titulo: "Jujutsu Kaisen",
        imagem: "https://cdn.myanimelist.net/images/anime/1635/109405.jpg",
        video: "https://www.youtube.com/embed/f7U2DJbGJ4Y"
      }
    ];

    function fazerLogin() {
      const u = document.getElementById("usuario").value;
      const s = document.getElementById("senha").value;
      if (s === senhaCorreta) {
        document.getElementById("loginPage").style.display = "none";
        document.getElementById("mainApp").style.display = "block";
        carregarCatalogo();
      } else {
        alert("Senha incorreta!");
      }
    }

    function carregarCatalogo() {
      const catalogo = document.getElementById("catalogo");
      catalogo.innerHTML = "";
      animes.forEach(anime => {
        const card = document.createElement("div");
        card.className = "anime-card";
        card.innerHTML = `
          <img src="${anime.imagem}" alt="${anime.titulo}">
          <p>${anime.titulo}</p>
          <button class="btn" onclick="assistirAnime('${anime.id}')">Assistir</button>
          <button class="btn" onclick="addFavorito('${anime.id}')">Favoritar</button>
        `;
        catalogo.appendChild(card);
      });
    }

    function assistirAnime(id) {
      const anime = animes.find(a => a.id === id);
      if (!anime) return;
      document.getElementById("playerBox").style.display = "flex";
      document.getElementById("playerTitle").innerText = anime.titulo;
      document.getElementById("playerIframe").src = anime.video;
      salvarHistorico(id);
    }

    function fecharPlayer() {
      document.getElementById("playerBox").style.display = "none";
      document.getElementById("playerIframe").src = "";
    }

    function addFavorito(id) {
      let favs = JSON.parse(localStorage.getItem("favoritos")) || [];
      if (!favs.includes(id)) favs.push(id);
      localStorage.setItem("favoritos", JSON.stringify(favs));
      alert("Adicionado aos favoritos!");
    }

    function mostrarPagina(pagina) {
      document.getElementById("catalogo").style.display = "none";
      document.getElementById("favoritos").style.display = "none";
      document.getElementById("historico").style.display = "none";
      document.getElementById("playerBox").style.display = "none";

      if (pagina === "catalogo") carregarCatalogo();
      if (pagina === "favoritos") carregarFavoritos();
      if (pagina === "historico") carregarHistorico();

      document.getElementById(pagina).style.display = "grid";
    }

    function carregarFavoritos() {
      const favs = JSON.parse(localStorage.getItem("favoritos")) || [];
      const box = document.getElementById("favoritos");
      box.innerHTML = "";
      favs.forEach(id => {
        const anime = animes.find(a => a.id === id);
        if (anime) {
          const card = document.createElement("div");
          card.className = "anime-card";
          card.innerHTML = `
            <img src="${anime.imagem}" alt="${anime.titulo}">
            <p>${anime.titulo}</p>
            <button class="btn" onclick="assistirAnime('${anime.id}')">Assistir</button>
          `;
          box.appendChild(card);
        }
      });
    }

    function salvarHistorico(id) {
      let hist = JSON.parse(localStorage.getItem("historico")) || [];
      hist = hist.filter(item => item !== id);
      hist.unshift(id);
      if (hist.length > 10) hist.pop();
      localStorage.setItem("historico", JSON.stringify(hist));
    }

    function carregarHistorico() {
      const hist = JSON.parse(localStorage.getItem("historico")) || [];
      const box = document.getElementById("historico");
      box.innerHTML = "";
      hist.forEach(id => {
        const anime = animes.find(a => a.id === id);
        if (anime) {
          const card = document.createElement("div");
          card.className = "anime-card";
          card.innerHTML = `
            <img src="${anime.imagem}" alt="${anime.titulo}">
            <p>${anime.titulo}</p>
            <button class="btn" onclick="assistirAnime('${anime.id}')">Assistir</button>
          `;
          box.appendChild(card);
        }
      });
    }

    function filtrarAnimes() {
      const termo = document.getElementById("searchInput").value.toLowerCase();
      const cards = document.querySelectorAll("#catalogo .anime-card");
      cards.forEach(card => {
        const titulo = card.querySelector("p").innerText.toLowerCase();
        card.style.display = titulo.includes(termo) ? "block" : "none";
      });
    }
  </script>
</body>
</html>

