<script>
    import Blob from './Blob.svelte';
    let mouse = { x: 0, y: 0 };
    let width = 1000;
    function handleMouseMove(event) {
        const rect = event.currentTarget.getBoundingClientRect();
        mouse.x = event.clientX - rect.left;
        mouse.y = event.clientY - rect.top;
        width = rect.width;
        // console.log(mouse);
    }
</script>

<div 
    on:mousemove={handleMouseMove}
    class="z-10 absolute top-0 left-0 w-full h-full flex md:flex-row flex-col justify-center items-center gap-[1em]"
    >
    <Blob {mouse} {width}/>
    <img src="banner-font.png" class="w-80" alt="Banner font" />
    <Blob mirrorY={true} {mouse} {width}/>
</div>

