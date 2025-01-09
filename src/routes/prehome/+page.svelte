<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Black Friday - Chaussette et Compagnie</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/poppins/4.0.0/css/poppins.min.css" rel="stylesheet">
    <style>
        body {
            margin: 0;
            padding: 0;
            background: #f5f8fc;
            font-family: 'Poppins', sans-serif;
        }

        .overlay {
            z-index: 500;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .promo-container {
            position: relative;
            width: 70vw;
            height: 70vh;
            background: white;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.05);
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }

        .close-button {
            position: absolute;
            top: 20px;
            right: 20px;
            width: 30px;
            height: 30px;
            background: white;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            z-index: 100;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            transition: transform 0.3s;
        }

        .close-button:hover {
            transform: rotate(90deg);
        }

        .close-button::before,
        .close-button::after {
            content: '';
            position: absolute;
            width: 15px;
            height: 2px;
            background: #1e3c72;
        }

        .close-button::before {
            transform: rotate(45deg);
        }

        .close-button::after {
            transform: rotate(-45deg);
        }

        .header {
            background: linear-gradient(135deg, #e1e1e1 , #2a5298);
            padding: 20px;
            text-align: center;
            position: relative;
        }

        .logo {
            width: 150px;
            height: 60px;
            object-fit: contain;
        }

        .promo-content {
            padding: 30px;
            text-align: center;
            flex-grow: 1;
            overflow-y: auto;
        }

        .promo-title {
            background: linear-gradient(45deg, #1e3c72, #2a5298);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            font-size: 2.8em;
            font-weight: 700;
            margin-bottom: 20px;
            letter-spacing: -1px;
        }

        .offer-description {
            font-size: 1.8em;
            margin-bottom: 30px;
            font-weight: 600;
            color: #2a5298;
        }

        .products {
            display: flex;
            justify-content: center;
            gap: 30px;
            margin: 20px 0;
        }

        .product-card {
            width: 220px;
            padding: 20px;
            border-radius: 15px;
            background: #ffffff;
            box-shadow: 0 5px 15px rgba(0,0,0,0.05);
            transition: transform 0.3s, box-shadow 0.3s;
        }

        .product-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 25px rgba(0,0,0,0.1);
        }

        .product-image {
            width: 200px;
            height: 200px;
            object-fit: cover;
            border-radius: 12px;
            margin-bottom: 15px;
        }

        .product-card h3 {
            color: #1e3c72;
            font-weight: 600;
            margin: 10px 0;
        }

        .timer {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin: 20px 0;
        }

        .timer-block {
            background: linear-gradient(135deg, #1e3c72, #2a5298);
            color: white;
            padding: 10px 15px;
            border-radius: 10px;
            min-width: 60px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .timer-value {
            font-size: 1.6em;
            font-weight: 600;
        }

        .timer-label {
            font-size: 0.8em;
            opacity: 0.9;
            text-transform: uppercase;
        }

        .cta-button {
            background: linear-gradient(45deg, #1e3c72, #2a5298);
            color: white;
            padding: 15px 40px;
            border: none;
            border-radius: 30px;
            font-size: 1.2em;
            font-weight: 500;
            cursor: pointer;
            transition: transform 0.3s, box-shadow 0.3s;
            margin-top: 20px;
        }

        .cta-button:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(42,82,152,0.3);
        }

        .price-tag {
            background: #1e3c72;
            color: white;
            padding: 5px 15px;
            border-radius: 20px;
            display: inline-block;
            margin-top: 10px;
        }

        .expired-message {
            display: none;
            font-size: 2em;
            color: #1e3c72;
            text-align: center;
            margin-top: 50px;
        }

        .hidden {
            display: none !important;
        }
    </style>
</head>
<body>
<div class="overlay" id="promo-overlay">
    <div class="promo-container">
        <div class="close-button" onclick="hidePromo()"></div>


        <div class="header">
            <img src="https://cdn.shopify.com/s/files/1/0454/8290/1671/files/logo-chaussettes-et-comagnie-2.png?v=1726755776" alt="Chaussette et Compagnie Logo" class="logo">
        </div>

        <div class="promo-content">
            <h1 class="promo-title">BLACK FRIDAY</h1>

            <div class="offer-description">
                2 CALEÇONS ULLYS ACHETÉS = 1 OFFERT !
            </div>

            <div class="timer">
                <div class="timer-block">
                    <span class="timer-value" id="days">00</span>
                    <span class="timer-label">Jours</span>
                </div>
                <div class="timer-block">
                    <span class="timer-value" id="hours">00</span>
                    <span class="timer-label">Heures</span>
                </div>
                <div class="timer-block">
                    <span class="timer-value" id="minutes">00</span>
                    <span class="timer-label">Minutes</span>
                </div>
                <div class="timer-block">
                    <span class="timer-value" id="seconds">00</span>
                    <span class="timer-label">Secondes</span>
                </div>
            </div>

            <div id="expired-message" class="expired-message">
                L'offre est terminée !<br>
                Rendez-vous l'année prochaine
            </div>

            <div class="products">
                <div class="product-card">
                    <img src="https://cdn.shopify.com/s/files/1/0454/8290/1671/files/ULLYS_CALECON-HOMME-COTON-LE-RAYE-III-TERRACOTTA-BLANC-3.png?v=1701081228" alt="Caleçon 1" class="product-image">
                </div>
                <div class="product-card">
                    <img src="https://cdn.shopify.com/s/files/1/0454/8290/1671/files/ULLYS_CALECON-HOMME-COTON-LE-RAYE-II-BLEU-ORANGE-BLANC-BEIGE-2.png?v=1701077181" alt="Caleçon 2" class="product-image">
                </div>
                <div class="product-card">
                    <img src="https://cdn.shopify.com/s/files/1/0454/8290/1671/files/ULLYS_CALECON-HOMME-COTON-LE-LEGER-TURQUOISE-BLANC-BLEU-3.png?v=1701076120" alt="Caleçon 3" class="product-image">
                </div>
            </div>

            <a href="/collections/calecons/caleçons">
                <button class="cta-button">PROFITER DE L'OFFRE</button>
            </a>
        </div>
    </div>
</div>

<script>
    function hidePromo() {
        document.getElementById('promo-overlay').classList.add('hidden');
    }

    function updateTimer() {
        const endDate = new Date('2024-11-25T09:00:00');
        const now = new Date();
        const diff = endDate - now;

        if (diff <= 0) {
            // L'offre est terminée
            document.querySelector('.products').style.display = 'none';
            document.querySelector('.cta-button').style.display = 'none';
            document.querySelector('.timer').style.display = 'none';
            document.getElementById('expired-message').style.display = 'block';
            return;
        }

        const days = Math.floor(diff / (1000 * 60 * 60 * 24));
        const hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
        const minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
        const seconds = Math.floor((diff % (1000 * 60)) / 1000);

        document.getElementById('days').textContent = String(days).padStart(2, '0');
        document.getElementById('hours').textContent = String(hours).padStart(2, '0');
        document.getElementById('minutes').textContent = String(minutes).padStart(2, '0');
        document.getElementById('seconds').textContent = String(seconds).padStart(2, '0');
    }

    // Mettre à jour le timer toutes les secondes
    setInterval(updateTimer, 1000);
    updateTimer(); // Première mise à jour immédiate
</script>
</body>
</html>