<script>
    import {
        Palette,
        Calendar,
        Ruler,
        FileText,
        Package,
        Mail,
        Upload,
        Scissors,
        Circle,
        Maximize2,
        Minimize2,
        ArrowLeft,
        Type,
        PersonStanding, User, Factory, Phone
    } from 'lucide-svelte'
    import html2canvas from "html2canvas";
    import { getStorage, ref, uploadBytes, getDownloadURL } from "firebase/storage";

    import { initializeApp } from "firebase/app";
    import Cropper from 'svelte-easy-crop';
    import ColorPicker from "svelte-awesome-color-picker";


    const firebaseConfig = {
        apiKey: "AIzaSyDiO5a8OxZ3kDNdaALZUbSIxH5dnlLJ7fU",
        authDomain: "chaussettesetcompagnie-ea084.firebaseapp.com",
        projectId: "chaussettesetcompagnie-ea084",
        storageBucket: "chaussettesetcompagnie-ea084.firebasestorage.app",
        messagingSenderId: "1084007280685",
        appId: "1:1084007280685:web:ef7f79d222d401adf288a4",
        measurementId: "G-8B7Y17Q90V"
    };

    const allSockColors = [
        { id: 1, path: '/chaussettes_color/Blanc.jpg', hex: '#FFFFFF', name: 'Blanc', sizes: ['36-41', '39-45'] },
        { id: 2, path: '/chaussettes_color/Bleu_canard.jpg', hex: '#12445b', name: 'Bleu canard', sizes: ['36-41', '39-45'] },
        { id: 3, path: '/chaussettes_color/Bleu_cobalt.jpg', hex: '#272938', name: 'Bleu cobalt', sizes: ['36-41', '39-45'] },
        { id: 4, path: '/chaussettes_color/Bleu_indigo.jpg', hex: '#1d1e32', name: 'Bleu indigo', sizes: ['36-41', '39-45'] },
        { id: 5, path: '/chaussettes_color/Bleu_majorelle.jpg', hex: '#232d68', name: 'Bleu majorelle', sizes: ['36-41', '39-45'] },
        { id: 6, path: '/chaussettes_color/Bleu_nuit.jpg', hex: '#12445b', name: 'Bleu nuit', sizes: ['36-41', '39-45'] },
        { id: 7, path: '/chaussettes_color/Bleu_roi.jpg', hex: '#1a2869', name: 'Bleu roi', sizes: ['36-41', '39-45'] },
        { id: 8, path: '/chaussettes_color/Bordeaux.jpg', hex: '#99313f', name: 'Bordeaux', sizes: ['36-41', '39-45'] },
        { id: 9, path: '/chaussettes_color/Camel_praliné.jpg', hex: '#7d4311', name: 'Camel praliné', sizes: ['36-41', '39-45'] },
        { id: 10, path: '/chaussettes_color/Gris_chiné.jpg', hex: '#a4a4a4', name: 'Gris chiné', sizes: ['36-41', '39-45'] },
        { id: 11, path: '/chaussettes_color/Jaune_canari.jpg', hex: '#dfb32c', name: 'Jaune canari', sizes: ['36-41', '39-45'] },
        { id: 12, path: '/chaussettes_color/Jaune_orangé.jpg', hex: '#d18120', name: 'Jaune orangé', sizes: ['36-41', '39-45'] },
        { id: 13, path: '/chaussettes_color/Marron.jpg', hex: '#301d19', name: 'Marron', sizes: ['36-41', '39-45'] },
        { id: 14, path: '/chaussettes_color/Mauve.jpg', hex: '#9c3a6b', name: 'Mauve', sizes: ['36-41', '39-45'] },
        { id: 15, path: '/chaussettes_color/Moutarde.jpg', hex: '#a55f19', name: 'Moutarde', sizes: ['36-41', '39-45'] },
        { id: 16, path: '/chaussettes_color/Noir.jpg', hex: '#151515', name: 'Noir', sizes: ['36-41', '39-45'] },
        { id: 17, path: '/chaussettes_color/Orange_abricot.jpg', hex: '#98350c', name: 'Orange abricot', sizes: ['36-41', '39-45'] },
        { id: 18, path: '/chaussettes_color/Rose.jpg', hex: '#ed1733', name: 'Rose', sizes: ['36-41', '39-45'] },
        { id: 19, path: '/chaussettes_color/Rose_fushia.jpg', hex: '#e43e62', name: 'Rose fushia', sizes: ['36-41', '39-45'] },
        { id: 20, path: '/chaussettes_color/Rose_saumon.jpg', hex: '#cc6749', name: 'Rose saumon', sizes: ['36-41', '39-45'] },
        { id: 21, path: '/chaussettes_color/Rouge_intense.jpg', hex: '#c51313', name: 'Rouge intense', sizes: ['36-41', '39-45'] },
        { id: 22, path: '/chaussettes_color/Rouge.jpg', hex: '#a41212', name: 'Rouge', sizes: ['36-41', '39-45'] },
        { id: 23, path: '/chaussettes_color/Terra_cotta.jpg', hex: '#7f3110', name: 'Terra cotta', sizes: ['36-41', '39-45'] },
        { id: 24, path: '/chaussettes_color/Vert_bouteille.jpg', hex: '#181d17', name: 'Vert bouteille', sizes: ['36-41', '39-45'] },
        { id: 25, path: '/chaussettes_color/Vert_kaki_olive.jpg', hex: '#231f16', name: 'Vert kaki olive', sizes: ['36-41', '39-45'] },
        { id: 26, path: '/chaussettes_color/Violet.jpg', hex: '#55243a', name: 'Violet', sizes: ['36-41', '39-45'] }
    ];

    let selectedColor = allSockColors[0];
    let step = 1;
    let formData = {
        dueDate: '',
        size: '',
        productionReason: '',
        quantity3641: null,
        quantity3945: null,
        email: '',
        nom: '',
        prenom: '',
        societe: '',
        telephone: '',
    };

    let customText = '';
    let customText2 = '';
    let selectedFont = 'Pacifico';
    let textColor = '#000000';
    let textSize = 20;
    let textRotation = -10;
    let textPosition = { x: 30, y: 37 };
    let isCropModalOpen = false;
    let crop = { x: 0, y: 0 };
    let zoom = 1;
    let previewElement;


    async function createImage(url) {
        return new Promise((resolve, reject) => {
            const image = new Image();
            image.addEventListener('load', () => resolve(image));
            image.addEventListener('error', error => reject(error));
            image.setAttribute('crossOrigin', 'anonymous');
            image.src = url;
        });
    }

    async function getCroppedImg(imageSrc, crop, zoom = 1) {
        const image = await createImage(imageSrc);
        const canvas = document.createElement('canvas');
        const ctx = canvas.getContext('2d');

        if (!ctx) {
            return null;
        }

        // On définit la taille de sortie souhaitée
        const outputWidth = 400;
        const outputHeight = 400;
        canvas.width = outputWidth;
        canvas.height = outputHeight;

        // On calcule la taille de la zone visible avec le zoom
        const visibleArea = {
            width: image.width / zoom,
            height: image.height / zoom
        };

        // On calcule les coordonnées de la zone à découper
        const cropX = (image.width - visibleArea.width) / 2 + crop.x;
        const cropY = (image.height - visibleArea.height) / 2 + crop.y;

        // On s'assure que les coordonnées restent dans les limites de l'image
        const safeX = Math.max(0, Math.min(cropX, image.width - visibleArea.width));
        const safeY = Math.max(0, Math.min(cropY, image.height - visibleArea.height));

        // On dessine l'image recadrée
        ctx.drawImage(
            image,
            safeX,
            safeY,
            visibleArea.width,
            visibleArea.height,
            0,
            0,
            outputWidth,
            outputHeight
        );

        // On convertit en blob et on retourne l'URL
        return new Promise((resolve) => {
            canvas.toBlob((blob) => {
                if (blob) {
                    resolve(URL.createObjectURL(blob));
                }
            }, 'image/jpeg', 1);
        });
    }

    async function applyCrop() {
        if (!image || !crop) return;

        try {
            const croppedImageUrl = await getCroppedImg(image, crop, zoom);
            if (croppedImageUrl) {
                uploadedImage = croppedImageUrl;
                isCropModalOpen = false;
                // Reset crop and zoom values
                crop = { x: 0, y: 0 };
                zoom = 1;
            }
        } catch (e) {
            console.error("Erreur lors du recadrage:", e);
        }
    }

    const fonts = [
        { name: 'Pacifico', value: 'Pacifico', defaultSize: 20 },
        { name: 'Meow Script', value: 'Meow Script', defaultSize: 28 },
        { name: 'League Script', value: 'League Script', defaultSize: 20 },
        { name: 'Bilbo', value: 'Bilbo', defaultSize: 28 },
        { name: 'Charm', value: 'Charm', defaultSize: 23 },
        { name: 'Italianno', value: 'Italianno', defaultSize: 30 },
        { name: 'Impact', value: 'Impact', defaultSize: 20 },
    ];

    const colors = [
        { hex: '#000000', name: 'Noir' },
        { hex: '#FFFFFF', name: 'Blanc' },
        { hex: '#C41E3A', name: 'Rouge' },
        { hex: '#1B4D89', name: 'Bleu marine' },
        { hex: '#B76E79', name: 'Rose poudré' },
        { hex: '#006B54', name: 'Vert émeraude' },
        { hex: '#feb500', name: 'Or' },
        { hex: '#C0C0C0', name: 'Argent' },
        { hex: '#4B0082', name: 'Indigo' },
        { hex: '#800020', name: 'Bordeaux' },
        { hex: '#06005d', name: 'Bleu nuit' },
        { hex: '#704214', name: 'Brun' },
        { hex: '#FF69B4', name: 'Rose vif' },
        { hex: '#FF8C00', name: 'Orange' },
        { hex: '#4169E1', name: 'Bleu royal' },
        { hex: '#228B22', name: 'Vert forêt' },
        { hex: '#BA55D3', name: 'Violet' },
        { hex: '#CD853F', name: 'Beige' },
        { hex: '#FF4500', name: 'Rouge orangé' },
        { hex: '#20B2AA', name: 'Turquoise' }
    ];


    $: isTextValid = customText.length <= 16;
    $: textError = !isTextValid ? 'Le texte ne peut pas dépasser 16 caractères' : '';

    const exampleImages = {
        sticking: [
            '/exemple/sticking1.jpg',
            '/exemple/sticking3.jpg',
            '/exemple/sticking2.jpg'
        ],
        embroidery: [
            '/exemple/broderie1.jpg',
            '/exemple/broderie2.jpg',
            '/exemple/broderie3.jpeg'
        ],
        lettrage: [
            '/exemple/lettrage1.jpg',
            '/exemple/lettrage2.jpg',
            '/exemple/lettrage3.jpg'
        ]
    };

    $: availableColors = allSockColors;
    $: {
        if (!availableColors.includes(selectedColor)) {
            selectedColor = availableColors[0] || allSockColors[0];
        }
    }

    function selectSockColor(color) {
        selectedColor = color;
    }

    function resetPersonalization() {
        // Reset text-related variables
        customText = '';
        customText2 = '';
        selectedFont = 'Pacifico';
        textColor = '#000000';
        textSize = 20;
        textRotation = -10;
        textPosition = { x: 30, y: 37 };

        // Reset image-related variables
        uploadedImage = null;
        croppedImage = null;
        imageFile = null;
        imagePosition = { x: 30, y: 43 };
        imageSize = 15;
        imageOpacity = 1;
        image = null;
        imageBlendMode = 'normal';
        isRoundImage = false;
        backgroundColor = '';
    }


    function goBack() {
        if (step > 1) {
            step--;

            // If going back from step 3 to step 2, reset personalization
            if (step === 2) {
                resetPersonalization();
            }
        }
    }

    let uploadedImage = null;
    let croppedImage = null;
    let isLoading = false;
    let imagePosition = { x: 30, y: 43 };
    let imageSize = 15;
    let imageOpacity = 1;
    let image = null;
    let imageBlendMode = 'normal';
    let isRoundImage = false;
    let backgroundColor = '';
    let wantCartoline = true;
    let files = [];
    let cartolineDetails = '';
    let previsuUrl = null
    let techProd = null
    let min_paire= 50


    $: displayedImage = croppedImage || uploadedImage;

    const app = initializeApp(firebaseConfig);
    const storage = getStorage(app);

    async function finalizeOrder() {
        const firstStepId = localStorage.getItem('firstStepId');

        if (!firstStepId) {
            alert('Erreur : Les informations de la première étape sont manquantes.');
            return;
        }

        try {
            let shopifyImageUrl = null;

            if (imageFile) {
                console.log("Uploading image:", imageFile);
                const storageRef = ref(storage, `images/${imageFile.name}`);

                try {
                    const snapshot = await uploadBytes(storageRef, imageFile);
                    shopifyImageUrl = await getDownloadURL(snapshot.ref);
                    console.log("Image uploaded successfully:", shopifyImageUrl);
                } catch (error) {
                    console.error("Upload failed:", error);
                    throw error;
                }
            }

            const completeOrderData = {
                firstStepId: firstStepId,
                selectedColor: selectedColor,
                customText: customText,
                customText2: customText2,
                selectedFont: selectedFont,
                textColor: textColor,
                textSize: textSize,
                textRotation: textRotation,
                textPosition: textPosition,
                uploadedImage: shopifyImageUrl,
                croppedImage: croppedImage,
                techProd: techProd,
                wantCartoline: wantCartoline,
                cartolineDetails: cartolineDetails,
                cartolineImageUrls: cartolineImageUrls,
                previsuUrl: previsuUrl,
            };

            const response = await fetch('https://api-sock.vercel.app/orders', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify(completeOrderData),
            });

            if (response.ok) {
                const result = await response.json();
                console.log('Commande finalisée avec succès:', result);
                step++; // Passer à l'étape 4
            } else {
                throw new Error('Erreur lors de la finalisation de la commande');
            }
        } catch (error) {
            console.error('Erreur:', error);
            alert('Une erreur s\'est produite. Veuillez réessayer.');
        }
    }

    function getDefaultSize(fontName) {
        const font = fonts.find(f => f.name === fontName);
        return font ? font.defaultSize : null; // Retourne la taille par défaut ou null si non trouvé
    }

    let cartolineImageUrls = [];

    async function capturePreview() {
        if (previewElement) {
            const canvas = await html2canvas(previewElement);
            const imageUrl = canvas.toDataURL("image/png"); // Convertit l’image en format PNG
            const imageBlob = dataURLtoBlob(imageUrl); // Convertir en Blob

            // Envoie l'image au serveur pour stockage
            uploadprevisu(imageBlob);
        }
    }

    // Fonction pour convertir une data URL en Blob
    function dataURLtoBlob(dataURL) {
        const byteString = atob(dataURL.split(',')[1]);
        const mimeString = dataURL.split(',')[0].split(':')[1].split(';')[0];
        const arrayBuffer = new ArrayBuffer(byteString.length);
        const uint8Array = new Uint8Array(arrayBuffer);

        for (let i = 0; i < byteString.length; i++) {
            uint8Array[i] = byteString.charCodeAt(i);
        }

        return new Blob([uint8Array], { type: mimeString });
    }

    async function uploadprevisu(imageBlob) {
        const firstStepId = localStorage.getItem('firstStepId');

        if (!firstStepId) {
            alert('Erreur : Les informations de la première étape sont manquantes.');
            return;
        }

        const storagePath = `previsu/${firstStepId}/previsu.png`; // Assure-toi d'ajouter l'extension
        const storageRef = ref(storage, storagePath);

        try {
            await uploadBytes(storageRef, imageBlob);  // Upload du fichier (converti en Blob)
            console.log("Fichier uploadé avec succès :", storagePath);

            // Obtenir l'URL de téléchargement du fichier
            previsuUrl = await getDownloadURL(storageRef);
            console.log("URL du fichier téléchargé :", previsuUrl);

        } catch (error) {
            console.error("Erreur lors de l'upload :", error);
        }
    }


    async function handleFileUploadCarto(event) {
        const firstStepId = localStorage.getItem('firstStepId');

        if (!firstStepId) {
            alert('Erreur : Les informations de la première étape sont manquantes.');
            return;
        }

        const selectedFiles = event.target.files;
        if (!selectedFiles || selectedFiles.length === 0) return;

        // Parcours chaque fichier sélectionné et upload
        for (let i = 0; i < selectedFiles.length; i++) {
            const file = selectedFiles[i];
            const storagePath = `cartoline/${firstStepId}/${file.name}`;
            const storageRef = ref(storage, storagePath);

            try {
                await uploadBytes(storageRef, file);  // Upload du fichier
                console.log("Fichier uploadé avec succès :", storagePath);

                // Obtenir l'URL de téléchargement du fichier
                const downloadURL = await getDownloadURL(storageRef);
                console.log("URL du fichier téléchargé :", downloadURL);

                // Ajouter l'URL au tableau
                cartolineImageUrls.push(downloadURL);

                // Optionnel : Ajouter le fichier au tableau des fichiers sélectionnés pour affichage
                files.push(file);

            } catch (error) {
                console.error("Erreur lors de l'upload :", error);
            }
        }
    }


    async function saveFirstStep() {
        // Préparer les données de la première étape
        const firstStepData = {
            dueDate: formData.dueDate,
            productionReason: formData.productionReason,
            quantity3641: formData.quantity3641,
            quantity3945: formData.quantity3945,
            email: formData.email,
            techProd: techProd,
            nom: formData.nom,
            prenom: formData.prenom,
            societe: formData.societe,
            telephone: formData.telephone,
        };

        try {
            // Envoyer les données à l'API NestJS
            const response = await fetch('https://api-sock.vercel.app/orders/first-step', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify(firstStepData),
            });

            if (response.ok) {
                const result = await response.json();
                console.log('First step saved:', result);

                // Stocker l'ID de la première étape pour l'utiliser plus tard
                localStorage.setItem('firstStepId', result.id);

                // Passer à l'étape suivante
                step++;
            } else {
                console.error('Failed to save first step');
                alert('Erreur lors de la sauvegarde des informations. Veuillez réessayer.');
            }
        } catch (error) {
            console.error('Error:', error);
            alert('Une erreur s\'est produite. Veuillez réessayer.');
        }
    }

    // Modifier la fonction handleSubmit pour appeler saveFirstStep
    function handleSubmit() {
        if (formData.quantity3641 + formData.quantity3945 < min_paire) {
            alert(`La quantité minimum est de ${min_paire} paires.`);
            return;
        }
        saveFirstStep(); // Appeler la fonction pour sauvegarder les données
    }

    let imageFile = null;

    async function handleImageUpload(event) {
        const file = event.target.files[0];
        if (!file) {
            console.error("Aucun fichier sélectionné !");
            return;
        }

        // Stocker le fichier pour l'upload ultérieur
        imageFile = file;

        // Créer l'URL de prévisualisation pour l'affichage
        uploadedImage = URL.createObjectURL(file);

        // Si on a une image détourée, on la réinitialise
        croppedImage = null;

        console.log("Image sélectionnée :", file);
    }

    let isFullScreen = false;

    function toggleFullScreen() {
        isFullScreen = !isFullScreen;
        if (isFullScreen) {
            let size = getDefaultSize(selectedFont);
            console.log(size);
            textSize = size + 40;
            imagePosition = { x: 30, y: 43 };
        } else {
            textSize = getDefaultSize(selectedFont);
            imagePosition = { x: 30, y: 43 };
        }
    }


    async function cropImage() {
        if (!uploadedImage) return;

        isLoading = true;

        const formData = new FormData();
        formData.append('imageFile', imageFile, 'image.jpg');  // Utiliser imageFile ici
        formData.append('background.color', 'transparent');
        formData.append('background.scaling', 'fill');
        formData.append('outputSize', '1000x1000');
        formData.append('padding', '0%');

        try {
            const result = await fetch('https://image-api.photoroom.com/v2/edit', {
                method: 'POST',
                headers: {
                    'Accept': 'image/png, application/json',
                    'x-api-key': '94d27f005dd8cfaddb64864961f6215f59ad1340',
                },
                body: formData
            });

            if (result.ok) {
                const imageBlob = await result.blob();
                croppedImage = URL.createObjectURL(imageBlob);
            } else {
                console.error('Erreur lors du détourage:', await result.text());
            }
        } catch (error) {
            console.error('Erreur lors de la requête:', error);
        } finally {
            isLoading = false;
        }
    }

    // Get the minimum date (2 weeks from today)
    const minDate = new Date();
    minDate.setDate(minDate.getDate() + 28);
    const minDateString = minDate.toISOString().split('T')[0];

    let hue = 0;
    let saturation = 100;
    let lightness = 50;
    let isDragging = false;


    function updateColor(e, rect) {
        // Get coordinates relative to picker element
        const x = Math.max(0, Math.min(e.clientX - rect.left, rect.width));
        const y = Math.max(0, Math.min(e.clientY - rect.top, rect.height));

        // Convert to percentages
        // Invert the saturation calculation to match the visual gradient
        saturation = Math.round((x / rect.width) * 100);

        // Ajust lightness calculation to match visual gradient
        // When y is 0 (top), lightness should be 100%
        // When y is rect.height (bottom), lightness should be 0%
        lightness = Math.round(100 - (y / rect.height) * 100);

        // Clamp values to ensure they stay within valid ranges
        saturation = Math.max(0, Math.min(100, saturation));
        lightness = Math.max(0, Math.min(100, lightness));
    }

</script>



<main class="min-h-screen bg-gradient-to-br from-blue-100 to-purple-100 flex items-center justify-center p-4 relative">
    <img src="/logo.png" alt="logo" class="md:block fixed hidden top-5 right-5 w-64" />



    <div class="xl:block hidden">
        <h1 class="fixed text-center text-5xl font-bold text-blue-800 top-[23vh] right-5 w-64">
            Pour vous<br>
            comme<br>
            pour nous
        </h1>

        <h1 class="fixed text-center text-3xl  text-blue-800 top-[47vh] right-5 w-64">
            Faible minimum
        </h1>

        <h1 class="fixed text-center text-3xl  text-blue-800 top-[60vh] right-5 w-64">
            Branding et<br>
            conception<br>
            de A à Z
        </h1>

        <h1 class="fixed text-center text-3xl  text-blue-800 top-[80vh] right-5 w-64">
            Rapidité<br>
            d'exécution
        </h1>

        <img src="/logo.png" alt="logo" class="fixed top-5 left-5 w-64" />

        <h1 class="fixed text-center text-5xl font-bold text-blue-800 top-[23vh] left-5 w-64">
            Création<br>sur<br>mesure
        </h1>

        <h1 class="fixed text-center text-3xl  text-blue-800 top-[47vh] left-5 w-64">
            Un produit<br>
            tendance
        </h1>

        <h1 class="fixed text-center text-3xl  text-blue-800 top-[60vh] left-5 w-64">
            Une manière<br>
            originale de<br>
            communiquer
        </h1>

        <h1 class="fixed text-center text-3xl  text-blue-800 top-[80vh] left-5 w-64">
            Un produit<br>
            accessible
        </h1>
    </div>


    {#if step > 1}
        <button
                on:click={goBack}
                class="absolute top-5 left-5 p-2 bg-white rounded-full shadow-lg hover:bg-gray-100 transition-all">
            <ArrowLeft size={24} />
        </button>
    {/if}

    <div class="bg-white  rounded-2xl shadow-xl w-full max-w-4xl p-8">

        <div class="w-full flex flex-col justify-center items-center">
            <img src="/logo.png" alt="logo" class="md:hidden block  w-64" />
        </div>


        <h1 class="md:text-4xl text-2xl font-bold text-center mb-8 text-gray-800">Personnalisez facilement vos chaussettes grâce à notre outil en ligne</h1>

        {#if step === 1}
            <form on:submit|preventDefault={handleSubmit} class="space-y-6 md:pt-4">


                <div>
                    <label for="size" class="block text-2xl font-medium text-gray-700 mb-1">3 possibilités de personnalisation</label>
                    <div class="flex gap-4 flex-col space-y-4">
                        <div
                                on:click={() => {techProd = "Broderie", min_paire = 50}}
                                class:clicked={techProd === "Broderie"}
                                class="cursor-pointer rounded-2xl md:hover:scale-105 transition-all">
                            <div class:clicked={techProd === "Broderie"} class="bg-white p-4  rounded-2xl shadow-lg">
                                <h3 class="text-xl font-semibold mb-2">1. Broderie</h3>
                                <p class:clicked={techProd === "Broderie"} class="text-gray-600 mb-4">Idéal pour vos logos, blasons, emblèmes... Brodeuse de haute précision.</p>
                                <div class="grid md:grid-cols-3 grid-rows-3 md:grid-rows-1 gap-2">
                                    {#each exampleImages.embroidery as image}
                                        <img src={image} alt="Exemple broderie" class="rounded-lg w-full h-60 object-cover object-bottom" />
                                    {/each}
                                </div>
                            </div>
                        </div>
                            <div class="relative">
                                <div
                                        on:click={() => {techProd = "Lettrage", min_paire = 50}}
                                        class:clicked={techProd === "Lettrage"}
                                        class="cursor-pointer rounded-2xl md:hover:scale-105 transition-all">
                                    <div class:clicked={techProd === "Lettrage"} class="bg-white p-4  rounded-2xl shadow-lg">
                                        <h3 class="text-xl font-semibold mb-2">2. Lettrage</h3>
                                        <p class:clicked={techProd === "Lettrage"} class="text-gray-600 mb-4">Un mot, une date, une punchline... Parce que la concision a toujours raison.</p>
                                        <div class="grid md:grid-cols-3 grid-rows-3 md:grid-rows-1 gap-2">
                                            {#each exampleImages.lettrage as image, i}
                                                <img src={image} class:object-bottom={i == 2} alt="Exemple broderie" class="rounded-lg w-full h-60 object-cover" />
                                            {/each}
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="relative">
                                <div
                                        on:click={() => {techProd = "Sticking", min_paire = 20}}
                                        class:clicked={techProd === "Sticking"}
                                        class="cursor-pointer rounded-2xl md:hover:scale-105 transition-all">
                                    <div class:clicked={techProd === "Sticking"} class="bg-white p-4 rounded-2xl shadow-lg">
                                        <h3 class="text-xl font-semibold mb-2">3. Sticking / Flocage</h3>
                                        <p class:clicked={techProd === "Sticking"} class="text-gray-600 mb-4">Idéal pour vos photos, visuels, et images en tout genre.</p>
                                        <div class="grid md:grid-cols-3 grid-rows-3 md:grid-rows-1 gap-2">
                                            {#each exampleImages.sticking as image}
                                                <img src={image} alt="Exemple sticking" class="rounded-lg w-full h-60 object-cover" />
                                            {/each}
                                        </div>
                                    </div>
                                </div>
                        </div>
                    </div>
                </div>

                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">Taille et quantité <span class="text-red-600 text-ellipsis">*</span></label>
                    {#if (formData.quantity3641 > 0 || formData.quantity3945 > 0) &&  formData.quantity3641 + formData.quantity3945 < min_paire}
                        <p class="text-red-600">
                            ⚠️ Il faut un minimum de {min_paire} paires pour commander
                        </p>
                    {/if}
                    <div class="flex gap-4 flex-col sm:flex-row">
                        <div class="flex-1">
                            <label for="size-36-41" class="block text-sm font-medium text-gray-700 mb-1">36-41</label>
                            <div class="relative">
                                <Ruler class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" size={20} />
                                <input bind:value={formData.quantity3641} type="number" id="size-36-41" class="pl-10 w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500" placeholder="Quantité" />
                            </div>
                        </div>
                        <div class="flex-1">
                            <label for="size-39-45" class="block text-sm font-medium text-gray-700 mb-1">39-45</label>
                            <div class="relative">
                                <Ruler class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" size={20} />
                                <input bind:value={formData.quantity3945} type="number" id="size-39-45" class="pl-10 w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500" placeholder="Quantité" />
                            </div>
                        </div>
                    </div>
                </div>

                <div>
                    <label for="dueDate" class="block text-sm font-medium text-gray-700 mb-1">⚠️ Date de livraison souhaitée (Il faut environ un mois après validation du projet) <span class="text-red-600 text-ellipsis">*</span></label>
                    <div class="relative">
                        <Calendar class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" size={20} />
                        <input bind:value={formData.dueDate} type="date" id="dueDate" required min={minDateString} class="pl-10 w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500" />
                    </div>
                </div>

                <div>
                    <label for="productionReason" class="block text-sm font-medium text-gray-700 mb-1">decrivez votre projet, vos envies, vos besoins <span class="text-red-600 text-ellipsis">*</span></label>
                    <div class="relative">
                        <FileText class="absolute left-3 top-3 text-gray-400" size={20} />
                        <textarea bind:value={formData.productionReason} id="productionReason" placeholder="Événement important, cadeaux d'invités ou d'annonciation pour un mariage, cadeaux d'affaires, cadeaux clients, support original de communication... Donnez nous le maximum d'information pour comprendre au mieux votre besoin." required class="pl-10 w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500" rows="3"></textarea>
                    </div>
                </div>

                <div>
                    <label for="email" class="block text-sm font-medium text-gray-700 mb-1">Nom <span class="text-red-600 text-ellipsis">*</span></label>
                    <div class="relative">
                        <User class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" size={20} />
                        <input bind:value={formData.nom} type="text" id="email" required class="pl-10 w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500" />
                    </div>
                </div>

                <div>
                    <label for="email" class="block text-sm font-medium text-gray-700 mb-1">Prénom <span class="text-red-600 text-ellipsis">*</span></label>
                    <div class="relative">
                        <User class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" size={20} />
                        <input bind:value={formData.prenom} type="text" id="email" required class="pl-10 w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500" />
                    </div>
                </div>

                <div>
                    <label for="email" class="block text-sm font-medium text-gray-700 mb-1">Société (facultatif)</label>
                    <div class="relative">
                        <Factory class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" size={20} />
                        <input bind:value={formData.societe} type="text" id="email" class="pl-10 w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500" />
                    </div>
                </div>

                <div>
                    <label for="email" class="block text-sm font-medium text-gray-700 mb-1">Email <span class="text-red-600 text-ellipsis">*</span></label>
                    <div class="relative">
                        <Mail class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" size={20} />
                        <input bind:value={formData.email} type="email" id="email" required class="pl-10 w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500" />
                    </div>
                </div>

                <div>
                    <label for="email" class="block text-sm font-medium text-gray-700 mb-1">Numéro de téléphone <span class="text-red-600 text-ellipsis">*</span></label>
                    <div class="relative">
                        <Phone class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" size={20} />
                        <input bind:value={formData.telephone} type="tel" id="email" required class="pl-10 w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500" />
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
                    <p class="mb-4 text-gray-600">Couleur sélectionnée : {selectedColor.name}</p>
                    <div class="grid grid-cols-6 gap-3">
                        {#each availableColors as color}
                            <button
                                    class="relative group"
                                    on:click={() => selectSockColor(color)}
                            >
                                <div
                                        class="w-10 h-10 rounded-full border-2 border-gray-300 hover:border-blue-500 transition-all focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2"
                                        style="background-color: {color.hex}"
                                ></div>
                                <span class="absolute bottom-full left-1/2 transform -translate-x-1/2 bg-gray-800 text-white text-xs rounded py-1 px-2 opacity-0 group-hover:opacity-100 transition-opacity whitespace-nowrap">
                                    {color.name}
                                </span>
                            </button>
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
                <!-- Preview Section -->
                <div bind:this={previewElement} class="flex-1 h-fit relative">
                    <!-- Base Sock Image -->
                    <img src={selectedColor.path} alt="Chaussette" class="w-full h-auto object-contain rounded-lg shadow-lg" />

                    <!-- Text Overlay -->
                    {#if customText || customText2}
                        <div class="absolute top-0 left-0 w-full h-full overflow-hidden pointer-events-none">
                        <span
                                class="absolute whitespace-nowrap"
                                style="
                                left: {textPosition.x - (customText2 ? 1 : 0)}%;
                                top: {textPosition.y - (customText2 ? 1 : 0)}%;
                                transform: translate(-50%, -50%) rotate({textRotation}deg);
                                color: {textColor};
                                font-family: {selectedFont};
                                font-size: {isFullScreen ? window.innerWidth <= 768 ? getDefaultSize(selectedFont) - 5 : getDefaultSize(selectedFont) + 20 : window.innerWidth <= 768 ? getDefaultSize(selectedFont) - 5 : getDefaultSize(selectedFont)}px;
                                TODO
                            "
                        >{customText}</span>
                            {#if customText2}
                                <span
                                        class="absolute whitespace-nowrap"
                                        style="
                                    left: {textPosition.x}%;
                                    top: {textPosition.y + (customText ? 4 : 0)}%;
                                    transform: translate(-50%, -50%) rotate({textRotation}deg);
                                    color: {textColor};
                                    font-family: {selectedFont};
                                    font-size: {isFullScreen ? window.innerWidth <= 768 ? getDefaultSize(selectedFont) - 5 : getDefaultSize(selectedFont) + 20 : window.innerWidth <= 768 ? getDefaultSize(selectedFont) - 5 : getDefaultSize(selectedFont)}px;
                                "
                                >{customText2}</span>
                            {/if}
                        </div>
                    {/if}

                    <!-- Custom Image Overlay -->
                    {#if displayedImage}
                        <div class="absolute top-0 left-0 w-full h-full overflow-hidden pointer-events-none">
                            <img
                                    src={displayedImage}
                                    alt="Image personnalisée"
                                    class="absolute object-contain"
                                    class:rounded-full={isRoundImage}
                                    style="
                            left: {imagePosition.x}%;
                            top: {imagePosition.y}%;
                            width: {imageSize}%;
                            transform: translate(-50%, -50%) rotate(-10deg);
                            opacity: {imageOpacity};
                            mix-blend-mode: {imageBlendMode};
                            filter: contrast(1.1) saturate(1.1);
                        "
                            />
                        </div>
                    {/if}

                    <!-- Fullscreen Toggle Button -->
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

                <!-- Customization Panel -->
                {#if !isFullScreen}
                    <div class="flex-1 space-y-8">
                        <!-- Text Customization Section -->
                        {#if techProd == "Lettrage" }
                            <div>
                                <h2 class="text-2xl font-semibold mb-6 flex items-center text-gray-800">
                                    <Type class="mr-2" />
                                    Personnalisez votre texte
                                </h2>

                                <div class="space-y-4">
                                    <!-- Text Input -->
                                    <div>
                                        <label class="block text-sm font-medium text-gray-700 mb-1">
                                            Texte Ligne 1 (max 8 caractères)
                                        </label>
                                        <input
                                                type="text"
                                                bind:value={customText}
                                                maxlength="8"
                                                class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                                                placeholder="Votre texte ici"
                                        />
                                    </div>

                                    <div>
                                        <label class="block text-sm font-medium text-gray-700 mb-1">
                                            Texte Ligne 2 (max 8 caractères)
                                        </label>
                                        <input
                                                type="text"
                                                bind:value={customText2}
                                                maxlength="8"
                                                class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                                                placeholder="Votre texte ici"
                                        />
                                    </div>

                                    {#if textError}
                                        <p class="text-red-500 text-sm mt-1">{textError}</p>
                                    {/if}

                                    <!-- Font Selection -->
                                    <div>
                                        <label class="block text-sm font-medium text-gray-700 mb-1">
                                            Police de caractères
                                        </label>
                                        <select
                                                bind:value={selectedFont}
                                                class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                                        >
                                            {#each fonts as font}
                                                <option value={font.value} style="font-family: {font.value}">
                                                    {font.name}
                                                </option>
                                            {/each}
                                        </select>
                                    </div>


                                    <ColorPicker
                                            bind:hex={textColor}
                                            position="responsive"
                                            label="Choisir une couleur"
                                    />
                                    <!--
                                    <div class="space-y-4">
                                        <label class="block text-sm font-medium text-gray-700 mb-1">
                                            Couleur du texte
                                        </label>
                                        <div class="grid grid-cols-5 gap-3">
                                            {#each colors as color}
                                                <button
                                                        on:click={() => textColor = color.hex}
                                                        class="group relative"
                                                >
                                                    <div
                                                            class="w-12 h-12 rounded-lg shadow-sm transition-all hover:scale-105 {color.hex === textColor ? 'ring-2 ring-blue-500 ring-offset-2' : 'ring-1 ring-gray-200'} {color.hex === '#FFFFFF' ? 'border border-gray-200' : ''}"
                                                            style="background-color: {color.hex}"
                                                    />
                                                    <span class="absolute bottom-full left-1/2 transform -translate-x-1/2 mb-2 px-2 py-1 text-xs text-white bg-gray-800 rounded-md opacity-0 group-hover:opacity-100 transition-opacity whitespace-nowrap">
                    {color.name}
                </span>
                                                </button>
                                            {/each}
                                        </div>
                                    </div>
                                    Text Color -->

                                </div>
                            </div>
                        {/if}

                        <!-- Image Upload Section -->
                        {#if techProd != "Lettrage" }
                            <div>
                                <h2 class="text-2xl font-semibold mb-6 flex items-center text-gray-800">
                                    <Upload class="mr-2" />
                                    Importez et placez votre image
                                </h2>
                                <div class="mt-4">
                                    <label for="image-upload" class="block text-sm font-medium text-gray-700 mb-2">
                                        Choisissez une image
                                    </label>
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
                                        <div class="flex gap-2 mt-4">
                                            <button
                                                    on:click={cropImage}
                                                    class="flex-1 bg-blue-600 text-white py-3 px-4 rounded-lg hover:bg-blue-700 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 flex items-center justify-center"
                                                    disabled={isLoading}
                                            >
                                                <Scissors class="mr-2" />
                                                {isLoading ? 'Détourage en cours...' : 'Détourer l\'image'}
                                            </button>
                                        </div>
                                    </div>
                                {/if}

                                {#if displayedImage}
                                    <div class="mt-4">



                                    </div>
                                {/if}
                            </div>
                        {/if}

                        <!-- Finish Button -->
                        <button
                                on:click={() => uploadedImage || customText || customText2 ? step++ && capturePreview() : alert("Vous devez d'abord personalisez votre chaussette")}
                                class="mt-8 w-full bg-blue-600 text-white py-3 px-4 rounded-lg hover:bg-blue-700 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2"
                        >
                            Passer à l'étape suivante
                        </button>
                    </div>
                {/if}
            </div>
        {/if}
        {#if step === 4}
            <div class="bg-white rounded-2xl shadow-xl w-full max-w-4xl p-8">
                <h1 class="md:text-4xl text-xl font-bold text-center mb-8 text-gray-800">Personnalisation de la cartoline</h1>

                <div class="flex flex-col md:flex-row gap-8">
                    <div class="flex-1">
                        <img src="/cartoline.png" alt="Exemple de cartoline" class="w-full h-auto object-contain rounded-t-lg shadow-lg" />
                        <img src="/cartoline2.webp" alt="Exemple de cartoline" class="w-full rotate-180 h-auto object-contain rounded-b-lg" />
                    </div>

                    <div class="flex-1 space-y-6">
                        <div class="bg-blue-50 p-6 rounded-lg">
                            <h2 class="text-2xl font-semibold mb-4 text-gray-800">La cartoline, qu'est-ce que c'est ?</h2>
                            <p class="text-gray-700">La cartoline est un petit support cartonné qui accompagne vos chaussettes. Elle peut être entièrement personnalisée pour créer une présentation unique et un rendu professionnel.</p>
                        </div>

                        <div class="space-y-4">
                            <h3 class="text-xl font-semibold text-gray-800">Souhaitez-vous personnaliser la cartoline ?</h3>

                            <div class="flex gap-4">
                                <button
                                        on:click={() => wantCartoline = true}
                                        class:clicked={wantCartoline}
                                        class="flex-1 py-3 px-4 rounded-lg  bg-gray-50 text-black hover:bg-blue-700 transition-colors ">
                                    Oui, je suis intéressé(e)
                                </button>
                                <button
                                        on:click={() => wantCartoline = false}
                                        class:clicked={!wantCartoline}
                                        class="flex-1 py-3 px-4 rounded-lg border border-gray-50 text-black hover:bg-blue-700 transition-colors">
                                    Non merci
                                </button>
                            </div>
                            {#if wantCartoline}
                                <div class="space-y-4 mt-4 bg-gray-50 p-4 rounded-lg">
                                    <h4 class="font-semibold text-gray-800">Que souhaitez-vous voir apparaître sur votre cartoline ?</h4>
                                    <p class="text-gray-600 text-sm">Élément de langage, punchline, logo, photo... Donnez nous le maximum d'informations afin que nous puissions vous envoyer par la suite une simulation précise par rapport à vos attentes.</p>

                                    <textarea
                                            bind:value={cartolineDetails}
                                            class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                            rows="4"
                                            placeholder="Décrivez votre projet de personnalisation ici..."></textarea>

                                    <div class="bg-blue-100 p-4 rounded-lg">
                                        <h4 class="font-semibold text-gray-800 mb-2">Envoyez des fichiers</h4>
                                        <p class="text-gray-600 text-sm mb-3">Si vous êtes une entreprise ou que vous maîtrisez la création et les outils de PAO, vous pouvez nous envoyer directement le fichier tel que vous le souhaitez. Vous avez les dimensions sur l'image en haut à gauche.</p>

                                        <label for="file-upload" class="cursor-pointer bg-white text-blue-600 py-2 px-4 border border-blue-500 rounded-lg hover:bg-blue-50 transition-colors inline-block">
                                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 inline-block mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12" />
                                            </svg>
                                            Choisir des fichiers
                                        </label>
                                        <input
                                                id="file-upload"
                                                type="file"
                                                multiple
                                                on:change={handleFileUploadCarto}
                                                class="hidden"
                                                accept=".jpg,.jpeg,.png,.pdf,.ai,.psd" />

                                        {#if files && files.length > 0}
                                            <div class="mt-3">
                                                <p class="text-sm font-medium text-gray-700">Fichiers sélectionnés:</p>
                                                <ul class="text-sm text-gray-600 mt-1">
                                                    {#each files as file}
                                                        <li class="flex items-center">
                                                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1 text-green-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
                                                            </svg>
                                                            {file.name}
                                                        </li>
                                                    {/each}
                                                </ul>
                                            </div>
                                        {/if}
                                    </div>

                                    <p class="text-amber-700 text-sm mt-2">
                                        La personnalisation de la cartoline entraîne un surcoût de 0,20€ par paire.
                                    </p>
                                </div>
                            {/if}
                        </div>



                        <button
                                on:click={async ()  => {
                            await finalizeOrder();
                            alert('Merci pour votre commande ! ' + (wantCartoline ? 'Nous vous recontacterons bientôt pour la personnalisation de votre cartoline.' : ''));
                            step++;
                        }}
                                class="w-full bg-green-600 text-white py-3 px-4 rounded-lg hover:bg-green-700 transition-colors focus:outline-none focus:ring-2 focus:ring-green-500 focus:ring-offset-2">
                            Demander un devis
                        </button>

                        <p>
                            Une urgence ou une demande d'accompagnement supplémentaire, contactez nous directement en clickant <a class="underline" href="mailto:personnalisation@chaussettesetcompagnie.fr">ici</a>
                        </p>
                    </div>
                </div>
            </div>

        {/if}
        {#if step >= 5}
            <div class=" justify-center items-center flex flex-col">
                <h1 class="text-xl text-center mt-10">
                    Nous vous remercions pour votre intérêt et demande de chaussettes personnalisées. Nous vous reconterons très rapidement par mail afin de vous fournir un devis précis par rapport à votre projet.
                    <br><br>
                    Il est également possible qu'une personne de l'équipe vous appelle directement en cas d'un besoin de précision.
                </h1>
                
                <img class="w-64 mt-10" src="/logo.png">
            </div>
        {/if}
    </div>



</main>

<style>
    @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap');

    :global(body) {
        font-family: 'Poppins', sans-serif;
    }

    .clicked {
        @apply bg-blue-800 text-white hover:scale-100;
    }
</style>
