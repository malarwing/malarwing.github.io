<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>My Wishlist</title>

  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f2f2f2;
      padding: 20px;
    }

    h1 {
      text-align: center;
    }

    .wishlist {
      max-width: 800px;
      margin: auto;
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
      gap: 16px;
    }

    .card {
      background: white;
      border-radius: 10px;
      padding: 16px;
      text-align: center;
      box-shadow: 0 2px 6px rgba(0,0,0,0.1);
    }

    .icon {
      font-size: 48px;
      margin-bottom: 10px;
    }

    .card h2 {
      font-size: 18px;
      margin: 10px 0;
    }

    .card a {
      display: inline-block;
      margin-top: 10px;
      padding: 8px 12px;
      background: #007aff;
      color: white;
      text-decoration: none;
      border-radius: 6px;
    }

    .card a:hover {
      background: #005edc;
    }
  </style>
</head>

<body>
  <h1>My Wishlist</h1>

  <div class="wishlist">
    <div class="card">
      <div class="icon">🎧</div>
      <h2>Headphones</h2>
      <a href="https://example.com" target="_blank">View Item</a>
    </div>

    <div class="card">
      <div class="icon">👟</div>
      <h2>Shoes</h2>
      <a href="https://example.com" target="_blank">View Item</a>
    </div>

    <div class="card">
      <div class="icon">🎮</div>
      <h2>Game Controller</h2>
      <a href="https://example.com" target="_blank">View Item</a>
    </div>
  </div>
</body>
</html>
