<script>
    import { Palette } from 'lucide-svelte';

    const sockColors = [
        { id: 1, path: '/chaussettes_color/Blanc.jpg', hex: '#FFFFFF' },
        { id: 2, path: '/chaussettes_color/Bleu_canard.jpg', hex: '#12445b' },
        { id: 3, path: '/chaussettes_color/Bleu_cobalt.jpg', hex: '#272938' },
        { id: 4, path: '/chaussettes_color/Bleu_indigo.jpg', hex: '#1d1e32' },
        { id: 5, path: '/chaussettes_color/Bleu_majorelle.jpg', hex: '#232d68' },
        { id: 6, path: '/chaussettes_color/Bleu_nuit.jpg', hex: '#12445b' },
        { id: 7, path: '/chaussettes_color/Bleu_roi.jpg', hex: '#1a2869' },
        { id: 8, path: '/chaussettes_color/Bordeaux.jpg', hex: '#99313f' },
        { id: 9, path: '/chaussettes_color/Camel_praliné.jpg', hex: '#7d4311' },
        { id: 10, path: '/chaussettes_color/Gris_chiné.jpg', hex: '#a4a4a4' },
        { id: 11, path: '/chaussettes_color/Jaune_canari.jpg', hex: '#dfb32c' },
        { id: 12, path: '/chaussettes_color/Jaune_orangé.jpg', hex: '#d18120' },
        { id: 13, path: '/chaussettes_color/Marron.jpg', hex: '#301d19' },
        { id: 14, path: '/chaussettes_color/Mauve.jpg', hex: '#9c3a6b' },
        { id: 15, path: '/chaussettes_color/Moutarde.jpg', hex: '#a55f19' },
        { id: 16, path: '/chaussettes_color/Noir.jpg', hex: '#151515' },
        { id: 17, path: '/chaussettes_color/Orange_abricot.jpg', hex: '#98350c' },
        { id: 18, path: '/chaussettes_color/Rose.jpg', hex: '#ed1733' },
        { id: 19, path: '/chaussettes_color/Rose_fushia.jpg', hex: '#e43e62' }, 
        { id: 20, path: '/chaussettes_color/Rose_saumon.jpg', hex: '#cc6749' }, 
        { id: 21, path: '/chaussettes_color/Rouge_intense.jpg', hex: '#c51313' },
        { id: 22, path: '/chaussettes_color/Rouge.jpg', hex: '#a41212' },
        { id: 23, path: '/chaussettes_color/Terra_cotta.jpg', hex: '#7f3110' },
        { id: 24, path: '/chaussettes_color/Vert_bouteille.jpg', hex: '#181d17' },
        { id: 25, path: '/chaussettes_color/Vert_kaki_olive.jpg', hex: '#231f16' },
        { id: 26, path: '/chaussettes_color/Violet.jpg', hex: '#55243a' }
    ];

    let selectedColor = sockColors[0];

    let step = 1

    function selectSockColor(color) {
    selectedColor = color;
    }
</script>

<!-- TODO demander pour quand, taille (couleur en fonction), raison de production, quantité desirée, demander email -->

{#if step === 1}

    <div class="min-h-screen bg-gray-100 flex items-center justify-center p-4">
        <div class="bg-white rounded-lg shadow-lg w-full max-w-4xl p-6">
            <h1 class="text-3xl font-bold text-center mb-6">Personnalisez vos chaussettes</h1>
            
            <div class="flex flex-col md:flex-row gap-8">
                <div class="flex-1">
                <img src={selectedColor.path} alt="Chaussette" class="w-full h-auto object-contain rounded-lg shadow-lg" />
                </div>
                <div class="flex-1">
                <h2 class="text-xl font-semibold mb-4 flex items-center">
                    <Palette class="mr-2" />
                    Étape 1 : Choisissez votre couleur
                </h2>
                <div class="grid grid-cols-6 gap-2">
                    {#each sockColors as color}
                    <button
                        class="w-8 h-8 rounded-full border-2 border-gray-300 hover:border-gray-500 transition-all"
                        style="background-color: {color.hex}"
                        on:click={() => selectSockColor(color)}
                    ></button>
                    {/each}
                </div>
                <button on:click={() => step++} class="mt-8 w-full bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700 transition-colors">
                    Passer à l'étape suivante
                </button>
                </div>
            </div>
        </div>
    </div>
{/if}

{#if step === 2}

    <div class="min-h-screen bg-gray-100 flex items-center justify-center p-4">
        <div class="bg-white rounded-lg shadow-lg w-full max-w-4xl p-6">
            <h1 class="text-3xl font-bold text-center mb-6">Personnalisez vos chaussettes</h1>
            
            <div class="flex flex-col md:flex-row gap-8">
                <div class="flex-1">
                <img src={selectedColor.path} alt="Chaussette" class="w-full h-auto object-contain rounded-lg shadow-lg" />
                </div>
                <div class="flex-1">
                <h2 class="text-xl font-semibold mb-4 flex items-center">
                    <Palette class="mr-2" />
                    Étape 2 : Importer votre image :
                </h2>
                <div class="grid grid-cols-6 gap-2">
                    <input 
                        type="file" 
                        accept="image/*" 
                        class="col-span-6 bg-gray-100 border border-gray-300 rounded-lg p-2"
                    />
                </div>
                <button on:click={() => step++} class="mt-8 w-full bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700 transition-colors">
                    Passer à l'étape suivante
                </button>
                </div>
            </div>
        </div>
    </div>
{/if}