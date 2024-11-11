<script>
    import { onMount } from "svelte";

    let image = '/logo.png';  // Remplacez avec le chemin de votre image
    let zoom = 1;  // Zoom initial
    let offsetX = 0;  // Déplacement horizontal
    let offsetY = 0;  // Déplacement vertical
    let isDragging = false;  // État de drag
    let dragStartX, dragStartY;  // Positions de départ du drag

    // Gérer le zoom de l'image
    function handleZoom(event) {
        if (event.deltaY < 0) {
            zoom = Math.min(zoom + 0.1, 3);  // Zoomer (max zoom 3x)
        } else {
            zoom = Math.max(zoom - 0.1, 1);  // Dézoomer (min zoom 1x)
        }
    }

    // Commence le déplacement au clic
    function startDrag(event) {
        isDragging = true;
        dragStartX = event.clientX - offsetX;
        dragStartY = event.clientY - offsetY;

        // Ajout des écouteurs pour déplacer et arrêter le drag
        document.addEventListener("mousemove", dragMove);
        document.addEventListener("mouseup", stopDrag);
    }

    // Gère le déplacement pendant le drag
    function dragMove(event) {
        if (isDragging) {
            offsetX = event.clientX - dragStartX;
            offsetY = event.clientY - dragStartY;
        }
    }

    // Arrête le déplacement
    function stopDrag() {
        isDragging = false;
        document.removeEventListener("mousemove", dragMove);
        document.removeEventListener("mouseup", stopDrag);
    }
</script>

<style>
    .crop-container {
        position: relative;
        width: 400px;  /* Taille fixe du cadre */
        height: 400px;
        overflow: hidden;
        background-color: black;  /* Contour noir */
        cursor: move;
        border: 2px solid #fff;  /* Bordure blanche pour indiquer le cadre de découpe */
    }

    .image {
        position: absolute;
        top: 50%;
        left: 50%;
        transform-origin: center center;  /* Centre l'image pour le zoom */
        will-change: transform;
    }
</style>

<div
        class="crop-container"
        on:wheel={handleZoom}
        on:mousedown={startDrag}
>
    <img
            src={image}
            alt="Image à recadrer"
            class="image"
            draggable="false"
            style="transform: translate({offsetX}px, {offsetY}px) scale({zoom});"
    />
</div>
