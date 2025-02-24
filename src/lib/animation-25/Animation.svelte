<script>
    import Blob from './Blob.svelte';
</script>

<div class="z-10 absolute top-0 left-0 w-full h-full flex md:flex-row flex-col justify-center items-center gap-[2em]">
    <Blob />
    <Blob mirrorY={true} />
</div>

<style>

</style>