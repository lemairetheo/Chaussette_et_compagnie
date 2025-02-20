<script>
    import Cropper from "svelte-easy-crop";

    let crop = { x: 0, y: 0 };
    let zoom = 1;
    let image =
        "https://img.huffingtonpost.com/asset/5ab4d4ac2000007d06eb2c56.jpeg?cache=sih0jwle4e&ops=1910_1000";
</script>

<Cropper {image} bind:crop bind:zoom />
