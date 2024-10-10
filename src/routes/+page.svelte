<script>
    import { Palette, Calendar, Ruler, FileText, Package, Mail, Upload, Scissors, Circle, Maximize2, Minimize2 } from 'lucide-svelte'
    import Replicate from "replicate";

    const replicate = new Replicate();

    const allSockColors = [
        { id: 1, path: '/chaussettes_color/Blanc.jpg', hex: '#FFFFFF', sizes: ['36-41', '39-45'] },
        { id: 2, path: '/chaussettes_color/Bleu_canard.jpg', hex: '#12445b', sizes: ['36-41', '39-45'] },
        { id: 3, path: '/chaussettes_color/Bleu_cobalt.jpg', hex: '#272938', sizes: ['36-41', '39-45'] },
        { id: 4, path: '/chaussettes_color/Bleu_indigo.jpg', hex: '#1d1e32', sizes: ['36-41', '39-45'] },
        { id: 5, path: '/chaussettes_color/Bleu_majorelle.jpg', hex: '#232d68', sizes: ['36-41', '39-45'] },
        { id: 6, path: '/chaussettes_color/Bleu_nuit.jpg', hex: '#12445b', sizes: ['36-41', '39-45'] },
        { id: 7, path: '/chaussettes_color/Bleu_roi.jpg', hex: '#1a2869', sizes: ['36-41', '39-45'] },
        { id: 8, path: '/chaussettes_color/Bordeaux.jpg', hex: '#99313f', sizes: ['36-41', '39-45'] },
        { id: 9, path: '/chaussettes_color/Camel_praliné.jpg', hex: '#7d4311', sizes: ['36-41', '39-45'] },
        { id: 10, path: '/chaussettes_color/Gris_chiné.jpg', hex: '#a4a4a4', sizes: ['36-41', '39-45'] },
        { id: 11, path: '/chaussettes_color/Jaune_canari.jpg', hex: '#dfb32c', sizes: ['36-41', '39-45'] },
        { id: 12, path: '/chaussettes_color/Jaune_orangé.jpg', hex: '#d18120', sizes: ['36-41', '39-45'] },
        { id: 13, path: '/chaussettes_color/Marron.jpg', hex: '#301d19', sizes: ['36-41', '39-45'] },
        { id: 14, path: '/chaussettes_color/Mauve.jpg', hex: '#9c3a6b', sizes: ['36-41', '39-45'] },
        { id: 15, path: '/chaussettes_color/Moutarde.jpg', hex: '#a55f19', sizes: ['36-41', '39-45'] },
        { id: 16, path: '/chaussettes_color/Noir.jpg', hex: '#151515', sizes: ['36-41', '39-45'] },
        { id: 17, path: '/chaussettes_color/Orange_abricot.jpg', hex: '#98350c', sizes: ['36-41', '39-45'] },
        { id: 18, path: '/chaussettes_color/Rose.jpg', hex: '#ed1733', sizes: ['36-41', '39-45'] },
        { id: 19, path: '/chaussettes_color/Rose_fushia.jpg', hex: '#e43e62', sizes: ['36-41', '39-45'] },
        { id: 20, path: '/chaussettes_color/Rose_saumon.jpg', hex: '#cc6749', sizes: ['36-41', '39-45'] },
        { id: 21, path: '/chaussettes_color/Rouge_intense.jpg', hex: '#c51313', sizes: ['36-41', '39-45'] },
        { id: 22, path: '/chaussettes_color/Rouge.jpg', hex: '#a41212', sizes: ['36-41', '39-45'] },
        { id: 23, path: '/chaussettes_color/Terra_cotta.jpg', hex: '#7f3110', sizes: ['36-41', '39-45'] },
        { id: 24, path: '/chaussettes_color/Vert_bouteille.jpg', hex: '#181d17', sizes: ['36-41', '39-45'] },
        { id: 25, path: '/chaussettes_color/Vert_kaki_olive.jpg', hex: '#231f16', sizes: ['36-41', '39-45'] },
        { id: 26, path: '/chaussettes_color/Violet.jpg', hex: '#55243a', sizes: ['36-41', '39-45'] }
    ];

    let selectedColor = allSockColors[0];
    let step = 3;
    let formData = {
        dueDate: '',
        size: '',
        productionReason: '',
        quantity: 50,
        email: ''
    };

    $: availableColors = allSockColors.filter(color => color.sizes.includes(formData.size));
    $: {
        if (!availableColors.includes(selectedColor)) {
            selectedColor = availableColors[0] || allSockColors[0];
        }
    }

    function selectSockColor(color) {
        selectedColor = color;
    }

    let uploadedImage = null;
    let croppedImage = null;
    let isLoading = false;
    let imagePosition = { x: 38, y: 25 };
    let imageSize = 15;
    let imageOpacity = 0.8;
    let imageBlendMode = 'multiply';
    let isRoundImage = false;
    let backgroundColor = '';

    $: displayedImage = cartoonImage || croppedImage || uploadedImage;


    function handleSubmit() {
        if (formData.quantity < 50) {
            alert('La quantité minimum est de 50 paires.');
            return;
        }
        step++;
    }

    async function handleImageUpload(event) {
        const file = event.target.files[0];
        if (file) {
            uploadedImage = URL.createObjectURL(file);
            if (croppedImage) {
                croppedImage = uploadedImage;
            }
        }
    }

    let isFullScreen = false;

    function toggleFullScreen() {
        isFullScreen = !isFullScreen;
        if (isFullScreen) {
            imagePosition = { x: 33, y: 36 };
        } else {
            imagePosition = { x: 38, y: 25 };
        }
    }

    let cartoonImage = '';

    async function cropImage() {
        if (!uploadedImage) return;

        isLoading = true;

        const formData = new FormData();
        const response = await fetch(uploadedImage);
        const blob = await response.blob();
        formData.append('imageFile', blob, 'image.jpg');
        formData.append('background.color', 'transparent');
        formData.append('background.scaling', 'fill');
        formData.append('outputSize', '1000x1000');
        formData.append('padding', '0%');

        try {
            // 1. Envoyer l'image à l'API PhotoRoom pour la détourer
            const result = await fetch('https://image-api.photoroom.com/v2/edit', {
                method: 'POST',
                headers: {
                    'Accept': 'image/png, application/json',
                    'x-api-key': 'sandbox_94d27f005dd8cfaddb64864961f6215f59ad1340', // Remplace par ta clé API PhotoRoom
                },
                body: formData
            });

            if (result.ok) {
                // 2. Obtenir l'image détourée
                const imageBlob = await result.blob();
                const imageUrl = URL.createObjectURL(imageBlob);  // Convertir le blob en URL
                croppedImage = imageUrl;  // Utilisé si besoin de montrer l'image détourée à l'utilisateur

                // 3. Envoyer l'image détourée à l'API de Replicate pour appliquer l'effet cartoon
                const output = await replicate.run(
                    "catacolabs/cartoonify:f109015d60170dfb20460f17da8cb863155823c85ece1115e1e9e4ec7ef51d3b",
                    {
                        input: {
                            seed: 2862431,
                            image: imageUrl,  // On utilise l'URL de l'image détourée ici
                        },
                    }
                );

                // 4. Afficher l'image cartoonisée à l'utilisateur
                cartoonImage = output[0];  // URL renvoyée par Replicate (le premier élément du tableau)
                console.log(cartoonImage);
            } else {
                console.error('Erreur lors du détourage:', await result.text());
            }
        } catch (error) {
            console.error('Erreur lors de la requête:', error);
        } finally {
            isLoading = false;
        }
    }



    function updateImagePosition(dx, dy) {
        imagePosition.x = Math.max(0, Math.min(100, imagePosition.x + dx));
        imagePosition.y = Math.max(0, Math.min(100, imagePosition.y + dy));
    }

    function updateImageSize(delta) {
        if (imageSize < 20 || delta < 0)
            imageSize = Math.max(10, Math.min(100, imageSize + delta));
    }

    function updateImageOpacity(delta) {
        imageOpacity = Math.max(0.1, Math.min(1, imageOpacity + delta));
    }

    function toggleBlendMode() {
        imageBlendMode = imageBlendMode === 'multiply' ? 'screen' : 'multiply';
    }

    function toggleRoundImage() {
        isRoundImage = !isRoundImage;
    }

    // Get the minimum date (2 weeks from today)
    const minDate = new Date();
    minDate.setDate(minDate.getDate() + 14);
    const minDateString = minDate.toISOString().split('T')[0];
</script>

<main class="min-h-screen bg-gradient-to-br from-blue-100 to-purple-100 flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl shadow-xl w-full max-w-4xl p-8">
        <h1 class="text-4xl font-bold text-center mb-8 text-gray-800">Personnalisez vos chaussettes</h1>

        {#if step === 1}
            <form on:submit|preventDefault={handleSubmit} class="space-y-6">
                <div>
                    <label for="dueDate" class="block text-sm font-medium text-gray-700 mb-1">Date de livraison souhaitée</label>
                    <div class="relative">
                        <Calendar class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" size={20} />
                        <input bind:value={formData.dueDate} type="date" id="dueDate" required min={minDateString} class="pl-10 w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500" />
                    </div>
                </div>

                <div>
                    <label for="size" class="block text-sm font-medium text-gray-700 mb-1">Taille</label>
                    <div class="relative">
                        <Ruler class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" size={20} />
                        <select bind:value={formData.size} id="size" required class="pl-10 w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500">
                            <option value="">Sélectionnez une taille</option>
                            <option value="36-41">36-41</option>
                            <option value="39-45">39-45</option>
                        </select>
                    </div>
                </div>

                <div>
                    <label for="productionReason" class="block text-sm font-medium text-gray-700 mb-1">Raison de production</label>
                    <div class="relative">
                        <FileText class="absolute left-3 top-3 text-gray-400" size={20} />
                        <textarea bind:value={formData.productionReason} id="productionReason" required class="pl-10 w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500" rows="3"></textarea>
                    </div>
                </div>

                <div>
                    <label for="quantity" class="block text-sm font-medium text-gray-700 mb-1">Quantité désirée (minimum 50 paires)</label>
                    <div class="relative">
                        <Package class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" size={20} />
                        <input bind:value={formData.quantity} type="number" id="quantity" required min="50" class="pl-10 w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500" />
                    </div>
                </div>

                <div>
                    <label for="email" class="block text-sm font-medium text-gray-700 mb-1">Email</label>
                    <div class="relative">
                        <Mail class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" size={20} />
                        <input bind:value={formData.email} type="email" id="email" required class="pl-10 w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500" />
                    </div>
                </div>

                <button type="submit" class="w-full bg-blue-600 text-white py-3 px-4 rounded-lg hover:bg-blue-700 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2">
                    Passer à l'étape suivante
                </button>
            </form>
        {/if}

        {#if step === 2}
            <div class="flex flex-col md:flex-row gap-8">
                <div class="flex-1">
                    <img src={selectedColor.path} alt="Chaussette" class="w-full h-auto object-contain rounded-lg shadow-lg" />
                </div>
                <div class="flex-1">
                    <h2 class="text-2xl font-semibold mb-6 flex items-center text-gray-800">
                        <Palette class="mr-2" />
                        Choisissez votre couleur
                    </h2>
                    <p class="mb-4 text-gray-600">Couleurs disponibles pour la taille {formData.size} :</p>
                    <div class="grid grid-cols-6 gap-3">
                        {#each availableColors as color}
                            <button
                                    class="w-10 h-10 rounded-full border-2 border-gray-300 hover:border-blue-500 transition-all focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2"
                                    style="background-color: {color.hex}"
                                    on:click={() => selectSockColor(color)}
                            ></button>
                        {/each}
                    </div>
                    <button on:click={() => step++} class="mt-8 w-full bg-blue-600 text-white py-3 px-4 rounded-lg hover:bg-blue-700 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2">
                        Passer à l'étape suivante
                    </button>
                </div>
            </div>
        {/if}

        {#if step === 3}
            <div class="flex flex-col md:flex-row gap-8">
                <div class="flex-1 relative">
                    <img src={selectedColor.path} alt="Chaussette" class="w-full h-auto object-contain rounded-lg shadow-lg" />
                    {#if displayedImage}
                        <div
                                class="absolute top-0 left-0 w-full h-full overflow-hidden"
                                style="pointer-events: none; transform: rotate(-10deg)"
                        >
                            <img
                                    src={displayedImage}
                                    alt="Image personnalisée"
                                    class:reset={isFullScreen}
                                    class="absolute object-contain md:mt-0 md:ml-0 ml-[-15px] mt-[30px]"
                                    class:rounded-full={isRoundImage}
                                    style="
                                    left: {imagePosition.x}%;
                                    top: {imagePosition.y}%;
                                    width: {imageSize}%;
                                    transform: translate(-50%, -50%);
                                    opacity: {imageOpacity};
                                    mix-blend-mode: {imageBlendMode};
                                    filter: contrast(1.1) saturate(1.1);
                                    background-color: {backgroundColor};
                                "
                            />
                        </div>
                    {/if}
                    <button
                            on:click={toggleFullScreen}
                            class="absolute top-2 right-2 p-2 bg-white rounded-full shadow-md hover:bg-gray-100 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500"
                    >
                        {#if isFullScreen}
                            <Minimize2 size={24} />
                        {:else}
                            <Maximize2 size={24} />
                        {/if}
                    </button>
                </div>
                {#if !isFullScreen}
                    <div class="flex-1">
                        <h2 class="text-2xl font-semibold mb-6 flex items-center text-gray-800">
                            <Upload class="mr-2" />
                            Importez et placez votre image
                        </h2>
                        <div class="mt-4">
                            <label for="image-upload" class="block text-sm font-medium text-gray-700 mb-2">Choisissez une image</label>
                            <input
                                    type="file"
                                    id="image-upload"
                                    accept="image/*"
                                    on:change={handleImageUpload}
                                    class="w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-semibold file:bg-blue-50 file:text-blue-700 hover:file:bg-blue-100"
                            />
                        </div>
                        {#if uploadedImage}
                            <div class="mt-4">
                                <img src={uploadedImage} alt="Image uploadée" class="w-full h-auto object-contain rounded-lg shadow-lg" />
                                <button
                                        on:click={cropImage}
                                        class="mt-4 w-full bg-green-600 text-white py-3 px-4 rounded-lg hover:bg-green-700 transition-colors focus:outline-none focus:ring-2 focus:ring-green-500 focus:ring-offset-2 flex items-center justify-center"
                                        disabled={isLoading}
                                >
                                    <Scissors class="mr-2" />
                                    {isLoading ? 'Détourage en cours...' : 'Détourer l\'image'}
                                </button>
                            </div>
                        {/if}
                        {#if displayedImage}
                            <div class="mt-4">
                                <h3 class="text-lg font-semibold mb-2">Ajustez l'image :</h3>
                                <div class="flex flex-wrap gap-2 mb-2">
                                    <button on:click={() => updateImageSize(-5)} class="p-2 bg-blue-100 rounded">-</button>
                                    <button on:click={() => updateImageSize(5)} class="p-2 bg-blue-100 rounded">+</button>
                                    <button on:click={() => updateImageOpacity(-0.1)} class="p-2 bg-blue-100 rounded">Moins opaque</button>
                                    <button on:click={() => updateImageOpacity(0.1)} class="p-2 bg-blue-100 rounded">Plus opaque</button>
                                    <button on:click={toggleRoundImage} class="p-2 bg-blue-100 rounded">
                                        {isRoundImage ? 'Image carrée' : 'Image ronde'}
                                    </button>
                                </div>
                                <div class="mt-2">
                                    <label for="background-color" class="block text-sm font-medium text-gray-700 mb-1">Couleur de fond</label>
                                    <input
                                            type="color"
                                            id="background-color"
                                            bind:value={backgroundColor}
                                            class="w-full h-10 p-1 border border-gray-300 rounded"
                                    />
                                </div>
                            </div>
                        {/if}
                        <button
                                on:click={() => alert('Commande finalisée!')}
                                class="mt-8 w-full bg-blue-600 text-white py-3 px-4 rounded-lg hover:bg-blue-700 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2"
                        >
                            Finaliser la commande
                        </button>
                    </div>
                {/if}
            </div>
        {/if}
    </div>
</main>

<style>
    @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap');

    .reset{
        margin: 0;
    }

    :global(body) {
        font-family: 'Poppins', sans-serif;
    }
</style>