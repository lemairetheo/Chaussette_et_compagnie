// ImageCropper.svelte
<script>
    import Cropper from 'cropperjs';
    import { onMount, onDestroy } from 'svelte';
    import { Maximize2, Minimize2, Move, ZoomIn, ZoomOut, RotateCw } from 'lucide-svelte';

    export let imageUrl;
    export let onCropComplete;

    let cropperInstance;
    let imageElement;
    let cropperModal;
    let isCropperVisible = false;

    onMount(() => {
        if (imageElement && isCropperVisible) {
            initCropper();
        }
    });

    onDestroy(() => {
        if (cropperInstance) {
            cropperInstance.destroy();
        }
    });

    function initCropper() {
        cropperInstance = new Cropper(imageElement, {
            aspectRatio: 1,
            viewMode: 1,
            dragMode: 'move',
            autoCropArea: 0.8,
            responsive: true,
            restore: false,
            guides: true,
            center: true,
            highlight: false,
            cropBoxMovable: true,
            cropBoxResizable: true,
            toggleDragModeOnDblclick: false,
        });
    }

    function showCropper() {
        isCropperVisible = true;
        setTimeout(() => {
            initCropper();
        }, 100);
    }

    function hideCropper() {
        isCropperVisible = false;
        if (cropperInstance) {
            cropperInstance.destroy();
        }
    }

    function applyCrop() {
        if (cropperInstance) {
            const croppedCanvas = cropperInstance.getCroppedCanvas();
            const croppedImage = croppedCanvas.toDataURL('image/png');
            onCropComplete(croppedImage);
            hideCropper();
        }
    }

    function zoomIn() {
        cropperInstance.zoom(0.1);
    }

    function zoomOut() {
        cropperInstance.zoom(-0.1);
    }

    function rotate() {
        cropperInstance.rotate(90);
    }
</script>

{#if isCropperVisible}
    <div class="fixed inset-0 bg-black bg-opacity-50 z-50 flex items-center justify-center p-4">
        <div class="bg-white rounded-lg w-full max-w-4xl p-4">
            <div class="flex justify-between items-center mb-4">
                <h3 class="text-lg font-semibold">Recadrer l'image</h3>
                <div class="flex gap-2">
                    <button
                            class="p-2 hover:bg-gray-100 rounded-full"
                            on:click={zoomIn}
                    >
                        <ZoomIn size={20} />
                    </button>
                    <button
                            class="p-2 hover:bg-gray-100 rounded-full"
                            on:click={zoomOut}
                    >
                        <ZoomOut size={20} />
                    </button>
                    <button
                            class="p-2 hover:bg-gray-100 rounded-full"
                            on:click={rotate}
                    >
                        <RotateCw size={20} />
                    </button>
                </div>
            </div>

            <div class="relative w-full h-[60vh]">
                <img
                        bind:this={imageElement}
                        src={imageUrl}
                        alt="Image à recadrer"
                        class="max-w-full h-full object-contain"
                />
            </div>

            <div class="flex justify-end gap-4 mt-4">
                <button
                        class="px-4 py-2 text-gray-600 hover:bg-gray-100 rounded"
                        on:click={hideCropper}
                >
                    Annuler
                </button>
                <button
                        class="px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700"
                        on:click={applyCrop}
                >
                    Appliquer
                </button>
            </div>
        </div>
    </div>
{/if}

<button
        class="mt-4 w-full bg-blue-600 text-white py-3 px-4 rounded-lg hover:bg-blue-700 transition-colors"
        on:click={showCropper}
>
    Recadrer l'image
</button>

<style>
    @import 'cropperjs/dist/cropper.css';
</style>