<script>
    import { onMount, onDestroy } from 'svelte';
    import Cropper from 'cropperjs';
    import 'cropperjs/dist/cropper.css';

    let imageElement;
    let cropperInstance;
    let resultCanvas;
    let showCropModal = false;
    let tempImage = null;

    export let uploadedImage = null; // L'image source à cropper
    export let onCropComplete = (croppedImage) => {}; // Callback pour récupérer l'image croppée

    onMount(() => {
        if (imageElement && uploadedImage) {
            initCropper();
        }
    });

    onDestroy(() => {
        if (cropperInstance) {
            cropperInstance.destroy();
        }
    });

    function initCropper() {
        if (cropperInstance) {
            cropperInstance.destroy();
        }

        cropperInstance = new Cropper(imageElement, {
            aspectRatio: 16 / 9,
            viewMode: 1,
            dragMode: 'move',
            cropBoxMovable: true,
            cropBoxResizable: true,
            crop(event) {
                if (cropperInstance) {
                    resultCanvas = cropperInstance.getCroppedCanvas();
                    // Met à jour le canvas de résultat en temps réel
                    if (resultCanvas) {
                        const result = document.getElementById('cropResult');
                        if (result) {
                            result.innerHTML = '';
                            result.appendChild(resultCanvas);
                        }
                    }
                }
            }
        });
    }

    function openCropModal(image) {
        tempImage = image;
        showCropModal = true;
        // Attendre que le DOM soit mis à jour avant d'initialiser Cropper
        setTimeout(() => {
            if (imageElement) {
                initCropper();
            }
        }, 100);
    }

    function closeCropModal() {
        if (cropperInstance) {
            cropperInstance.destroy();
            cropperInstance = null;
        }
        showCropModal = false;
    }

    function applyCrop() {
        if (cropperInstance) {
            const canvas = cropperInstance.getCroppedCanvas();
            if (canvas) {
                const croppedDataUrl = canvas.toDataURL('image/png');
                onCropComplete(croppedDataUrl);
                closeCropModal();
            }
        }
    }
</script>

{#if showCropModal}
    <div class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
        <div class="bg-white rounded-lg p-6 w-[90vw] max-w-4xl max-h-[90vh] flex flex-col">
            <div class="flex justify-between items-center mb-4">
                <h3 class="text-xl font-semibold">Recadrer l'image</h3>
                <button
                        on:click={closeCropModal}
                        class="text-gray-500 hover:text-gray-700">
                    ✕
                </button>
            </div>

            <div class="flex-1 overflow-hidden">
                <div class="cropper-container" style="height: 60vh;">
                    <img
                            bind:this={imageElement}
                            src={tempImage}
                            alt="Image à recadrer"
                            class="max-w-full"
                    />
                </div>
                <div id="cropResult" class="mt-4 border rounded p-2">
                    <!-- Le résultat du cropping sera affiché ici -->
                </div>
            </div>

            <div class="flex justify-end gap-4 mt-4">
                <button
                        on:click={closeCropModal}
                        class="px-4 py-2 bg-gray-200 text-gray-800 rounded hover:bg-gray-300">
                    Annuler
                </button>
                <button
                        on:click={applyCrop}
                        class="px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700">
                    Appliquer
                </button>
            </div>
        </div>
    </div>
{/if}

<style>
    :global(.cropper-container) {
        width: 100%;
        max-width: 100%;
    }

    :global(.cropper-view-box) {
        outline: none;
        border: 2px solid #fff;
    }
</style>