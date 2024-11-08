<script>
    AOS.init({
        duration: 1000,
        once: true
    });
</script>

<meta charset="UTF-8"> <meta name="viewport" content="width=device-width, initial-scale=1.0"> <meta name="description" content="Découvrez notre collection exclusive de chaussettes design Paris et Lille. Des motifs uniques représentant les emblèmes iconiques de ces villes françaises. Chaussettes de qualité, fabriquées avec soin."> Chaussettes Paris &amp; Lille | Collection Villes Françaises | Design Unique<script src="https://cdnjs.cloudflare.com/ajax/libs/aos/2.3.4/aos.js"></script> <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/aos/2.3.4/aos.css">
<style>
:root {
    --primary: #1a1a1a;
    --secondary: #f5f5f5;
    --accent: #e31f3b;
}

* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Helvetica Neue', sans-serif;
    line-height: 1.6;
    color: var(--primary);
}

.hero {
    height: 80vh;
    background: var(--secondary);
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
    position: relative;
    overflow: hidden;
}

.hero-content {
    max-width: 800px;
    padding: 2rem;
    z-index: 2;
}

h1 {
    font-size: 3rem;
    margin-bottom: 1rem;
    color: var(--primary);
}

.subtitle {
    font-size: 1.5rem;
    color: #666;
    margin-bottom: 2rem;
}

.collection {
    padding: 4rem 2rem;
}

.collection-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 2rem;
    max-width: 1200px;
    margin: 0 auto;
}

.product-card {
    background: white;
    border-radius: 8px;
    overflow: hidden;
    box-shadow: 0 4px 6px rgba(0,0,0,0.1);
    transition: transform 0.3s ease;
}

.product-card:hover {
    transform: translateY(-5px);
}

.product-image {
    width: 100%;
    height: 300px;
    background: #ddd;
    display: flex;
    align-items: center;
    justify-content: center;
}

.product-info {
    padding: 1.5rem;
}

.product-title {
    font-size: 1.25rem;
    margin-bottom: 0.5rem;
}

.product-description {
    color: #666;
    margin-bottom: 1rem;
}

.features {
    padding: 4rem 2rem;
    background: var(--secondary);
}

.features-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 2rem;
    max-width: 1200px;
    margin: 0 auto;
}

.feature-card {
    text-align: center;
    padding: 2rem;
}

@media (max-width: 768px) {
    h1 {
        font-size: 2rem;
    }

    .subtitle {
        font-size: 1.25rem;
    }
}
</style>
<header class="hero">
    <div data-aos="fade-up" class="hero-content">
        <h1>Collection Chaussettes Villes de France</h1>
        <p class="subtitle">Des designs uniques célébrant Paris et Lille</p>
    </div>
</header>
<section class="collection">
    <div class="collection-grid">
        <article data-aos="fade-up" class="product-card">
            <div class="product-image"><img alt="Chaussettes Paris - Design Panneau Iconique" src="/api/placeholder/300/300"></div>
            <div class="product-info">
                <h2 class="product-title">Chaussettes Paris - Panneau Emblématique</h2>
                <p class="product-description">Un design élégant mettant en vedette l'iconique panneau parisien. Le symbole parfait de la Ville Lumière à vos pieds.</p>
            </div>
        </article>
        <article data-aos-delay="100" data-aos="fade-up" class="product-card">
            <div class="product-image"><img alt="Chaussettes Paris - Multi-Panneaux" src="/api/placeholder/300/300"></div>
            <div class="product-info">
                <h2 class="product-title">Chaussettes Paris - Collection Panneaux</h2>
                <p class="product-description">Un motif unique regroupant les panneaux emblématiques de Paris. Pour les véritables amoureux de la capitale.</p>
            </div>
        </article>
        <article data-aos-delay="200" data-aos="fade-up" class="product-card">
            <div class="product-image"><img alt="Chaussettes Lille - Panneau Principal" src="/api/placeholder/300/300"></div>
            <div class="product-info">
                <h2 class="product-title">Chaussettes Lille - Grand Panneau</h2>
                <p class="product-description">Affichez fièrement votre amour pour la capitale des Flandres avec ce design mettant en valeur le panneau emblématique de Lille.</p>
            </div>
        </article>
        <article data-aos-delay="300" data-aos="fade-up" class="product-card">
            <div class="product-image"><img alt="Chaussettes Lille - Multi-Panneaux" src="/api/placeholder/300/300"></div>
            <div class="product-info">
                <h2 class="product-title">Chaussettes Lille - Collection Panneaux</h2>
                <p class="product-description">Une création unique rassemblant les différents panneaux de Lille. Le cadeau parfait pour les amoureux du Nord.</p>
            </div>
        </article>
    </div>
</section>
<section class="features">
    <div class="features-grid">
        <div data-aos="fade-up" class="feature-card">
            <h3>Qualité Premium</h3>
            <p>Matériaux soigneusement sélectionnés pour un confort optimal</p>
        </div>
        <div data-aos-delay="100" data-aos="fade-up" class="feature-card">
            <h3>Design Unique</h3>
            <p>Motifs exclusifs créés par nos designers</p>
        </div>
        <div data-aos-delay="200" data-aos="fade-up" class="feature-card">
            <h3>Fabrication Soignée</h3>
            <p>Attention particulière aux détails et finitions</p>
        </div>
    </div>
</section>
