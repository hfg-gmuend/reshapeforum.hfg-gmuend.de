<script>
    import Blob from './Blob.svelte';
</script>

<div class="z-10 absolute top-0 left-0 w-full h-full flex md:flex-row flex-col justify-center items-center gap-[1em]">
    <Blob />
    <img src="banner-font.png" class="w-80" alt="Banner font" />
    <Blob mirrorY={true} />
</div>

<style>

</style>