<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>My Wishlist</title>
  <style>
    :root { --bg:#0b0f19; --card:#121a2a; --text:#e8eefc; --muted:#a9b4d0; --btn:#2b5cff; --danger:#ff3b3b; }
    * { box-sizing: border-box; }
    body { margin:0; font-family: system-ui, -apple-system, Arial; background: radial-gradient(1200px 600px at 20% 0%, #182448, var(--bg)); color: var(--text); }
    .wrap { max-width: 980px; margin: 0 auto; padding: 28px 16px 60px; }
    header { display:flex; justify-content: space-between; align-items: end; gap: 16px; margin-bottom: 18px; }
    h1 { margin: 0; font-size: 28px; letter-spacing: .2px; }
    .sub { margin: 6px 0 0; color: var(--muted); font-size: 14px; }

    .panel {
      background: rgba(255,255,255,.06);
      border: 1px solid rgba(255,255,255,.10);
      border-radius: 16px;
      padding: 14px;
      backdrop-filter: blur(8px);
      margin-bottom: 16px;
    }

    form { display:grid; grid-template-columns: 1.2fr 1.5fr 1fr auto; gap: 10px; }
    input, select, button {
      padding: 10px 12px;
      border-radius: 12px;
      border: 1px solid rgba(255,255,255,.15);
      background: rgba(10,14,25,.65);
      color: var(--text);
      outline: none;
      font-size: 14px;
    }
    input::placeholder { color: rgba(232,238,252,.55); }
    button { cursor:pointer; border: none; background: var(--btn); font-weight: 700; }
    button:hover { filter: brightness(1.05); }

    .grid { display:grid; grid-template-columns: repeat(3, minmax(0, 1fr)); gap: 14px; }
    @media (max-width: 900px) { .grid { grid-template-columns: repeat(2, minmax(0, 1fr)); } form { grid-template-columns: 1fr 1fr; } }
    @media (max-width: 560px) { .grid { grid-template-columns: 1fr; } }

    .card {
      background: rgba(255,255,255,.06);
      border: 1px solid rgba(255,255,255,.10);
      border-radius: 18px;
      overflow: hidden;
      transition: transform .12s ease;
    }
    .card:hover { transform: translateY(-2px); }

    .thumb {
      height: 140px;
      display:flex; align-items:center; justify-content:center;
      background: linear-gradient(135deg, rgba(43,92,255,.22), rgba(255,255,255,.04));
      border-bottom: 1px solid rgba(255,255,255,.08);
      position: relative;
    }
    .emoji { font-size: 48px; }
    .img {
      width: 100%; height: 100%;
      object-fit: cover;
      display:none;
    }

    .content { padding: 12px 12px 14px; }
    .title { margin: 0; font-size: 16px; }
    .meta { margin: 6px 0 10px; color: var(--muted); font-size: 13px; line-height: 1.3; word-break: break-word; }

    .actions { display:flex; gap: 10px; }
    .btn {
      flex: 1;
      padding: 10px 12px;
      border-radius: 12px;
      border: 1px solid rgba(255,255,255,.12);
      background: rgba(255,255,255,.08);
      color: var(--text);
      font-weight: 700;
      text-align:center;
      text-decoration:none;
      cursor:pointer;
      user-select:none;
    }
    .btn:hover { background: rgba(255,255,255,.12); }
    .btn-open { background: rgba(43,92,255,.25); border-color: rgba(43,92,255,.35); }
    .btn-open:hover { background: rgba(43,92,255,.33); }
    .btn-del { background: rgba(255,59,59,.16); border-color: rgba(255,59,59,.26); }
    .btn-del:hover { background: rgba(255,59,59,.22); }

    .row { display:flex; justify-content: space-between; align-items:center; gap: 10px; margin-bottom: 8px; }
    .smallbtn { background: rgba(255,255,255,.10); border: 1px solid rgba(255,255,255,.12); }
    .smallbtn:hover { background: rgba(255,255,255,.14); }
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <div>
        <h1>My Wishlist</h1>
        <p class="sub">Click “Open” to go to the item page. Add icons (emoji) or an image URL.</p>
      </div>
      <button class="smallbtn" id="resetBtn" type="button">Reset Demo</button>
    </header>

    <div class="panel">
      <form id="addForm">
        <input id="name" placeholder="Item name (ex: AirPods)" required />
        <input id="link" placeholder="Link (https://...)" required />
        <input id="image" placeholder="Image URL (optional)" />
        <select id="icon">
          <option value="🎮">🎮 Gaming</option>
          <option value="👟">👟 Shoes</option>
          <option value="🎧">🎧 Audio</option>
          <option value="📱">📱 Phone</option>
          <option value="💻">💻 Laptop</option>
          <option value="🧢">🧢 Style</option>
          <option value="🧸">🧸 Other</option>
        </select>
        <button type="submit">Add</button>
      </form>
    </div>

    <div id="grid" class="grid"></div>
  </div>

  <script>
    const KEY = "wishlist_items_v2";

    const demoItems = [
      { id: crypto.randomUUID(), name: "Cool headphones", link: "https://example.com", image: "", icon: "🎧" },
      { id: crypto.randomUUID(), name: "New shoes", link: "https://example.com", image: "", icon: "👟" },
      { id: crypto.randomUUID(), name: "Game controller", link: "https://example.com", image: "", icon: "🎮" },
    ];

    let items = load();
    if (items.length === 0) { items = demoItems; save(items); }

    const grid = document.getElementById("grid");
    const form = document.getElementById("addForm");
    const nameEl = document.getElementById("name");
    const linkEl = document.getElementById("link");
    const imageEl = document.getElementById("image");
    const iconEl = document.getElementById("icon");
    const resetBtn = document.getElementById("resetBtn");

    render();

    form.addEventListener("submit", (e) => {
      e.preventDefault();

      const name = nameEl.value.trim();
      const link = linkEl.value.trim();
      const image = imageEl.value.trim();
      const icon = iconEl.value;

      if (!name || !link) return;

      // Basic link normalize: add https:// if missing
      const fixedLink = /^(https?:)?\/\//i.test(link) ? link : `https://${link}`;

      items.unshift({ id: crypto.randomUUID(), name, link: fixedLink, image, icon });
      save(items);
      form.reset();
      render();
    });

    resetBtn.addEventListener("click", () => {
      items = demoItems;
      save(items);
      render();
    });

    function removeItem(id) {
      items = items.filter(x => x.id !== id);
      save(items);
      render();
    }

    function render() {
      grid.innerHTML = "";

      for (const item of items) {
        const card = document.createElement("div");
        card.className = "card";

        const thumb = document.createElement("div");
        thumb.className = "thumb";

        const emoji = document.createElement("div");
        emoji.className = "emoji";
        emoji.textContent = item.icon || "🧸";

        const img = document.createElement("img");
        img.className = "img";
        img.alt = item.name;

        if (item.image) {
          img.src = item.image;
          img.onload = () => { img.style.display = "block"; emoji.style.display = "none"; };
          img.onerror = () => { img.style.display = "none"; emoji.style.display = "block"; };
        }

        thumb.appendChild(emoji);
        thumb.appendChild(img);

        const content = document.createElement("div");
        content.className = "content";

        const title = document.createElement("h3");
        title.className = "title";
        title.textContent = item.name;

        const meta = document.createElement("div");
        meta.className = "meta";
        meta.textContent = item.link;

        const actions = document.createElement("div");
        actions.className = "actions";

        const openA = document.createElement("a");
        openA.className = "btn btn-open";
        openA.textContent = "Open";
        openA.href = item.link;
        openA.target = "_blank";
        openA.rel = "noopener noreferrer";

        const delBtn = document.createElement("button");
        delBtn.className = "btn btn-del";
        delBtn.type = "button";
        delBtn.textContent = "Remove";
        delBtn.addEventListener("click", () => removeItem(item.id));

        actions.appendChild(openA);
        actions.appendChild(delBtn);

        content.appendChild(title);
        content.appendChild(meta);
        content.appendChild(actions);

        card.appendChild(thumb);
        card.appendChild(content);

        grid.appendChild(card);
      }
    }

    function load() {
      try {
        return JSON.parse(localStorage.getItem(KEY) || "[]");
      } catch {
        return [];
      }
    }
    function save(data) {
      localStorage.setItem(KEY, JSON.stringify(data));
    }
  </script>
</body>
</html>
