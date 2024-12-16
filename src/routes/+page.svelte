<script>
	import ITEMS_WEDNESDAY from '../data/schedule-wednesday.js';
	import ITEMS_THURSDAY from '../data/schedule-thursday.js';
	import ITEMS_FRIDAY from '../data/schedule-friday.js';
	import WORKSHOPS from '../data/workshops.js';

	import Animation from '$lib/animation/Animation.svelte';
	import MenuIcon from '$lib/icon-menu.svelte';
	import LabLogo from '$lib/lab-logo-weiss.svelte';
	import SpeakersDesktop from '$lib/speakers-desktop.svelte';
	import SpeakersMobile from '$lib/speakers-mobile.svelte';
	import TeamTile from '$lib/team-tile.svelte';
	import Logos from '$lib/logos.svelte';
	import Schedule from '$lib/schedule.svelte';
	// import AnimationMovieMP4 from '$lib/assets/reshape_8_1.mp4';

	let menuOpen = false;

	function scrollIntoView({ target }) {
		if(target.getAttribute('href').startsWith('http')) window.open(target.getAttribute('href'), '_blank');
		const el = document.querySelector(target.getAttribute('href'));
		if (!el) return;
		el.scrollIntoView({
			behavior: 'smooth'
		});
	}

	function toggleMenu() {
		menuOpen = !menuOpen;
	}

	const menuItems = [
		// { title: 'Programme', link: '#program' },
		// { title: 'Registration', link: '#anmeldung' },
		// { title: 'AI+D Lab', link: '#ai-d-lab' },
		//{ title: 'KITeGG', link: '#kitegg' },
		{ title: 'Reshape 2023', link: 'https://reshapeforum.hfg-gmuend.de/2023/' }
	];
</script>

<!-- Mobile Menu -->
{#if menuOpen}
	<nav class="fixed w-full z-30 h-screen pt-11 bg-reshape-mauve inset text-4xl md:hidden">
		<ul class="h-full flex flex-col pt-[80px]">
			{#each menuItems as item}
				<li class="basis-[95px]" on:click={() => (menuOpen = false)}>
					<a href="{item.link}" on:click|preventDefault={scrollIntoView} target="_blank">{item.title}</a>
				</li>
			{/each}
		</ul>
	</nav>
{/if}

<header
	class="fixed w-full z-30 inset border-y-2 border-black h-11 flex items-center reshape-lead-light"
	class:bg-white={!menuOpen}
	class:bg-reshape-mauve={menuOpen}
>
	<nav class="font-light w-full max-md:hidden">
		<ul class="flex justify-between">
			{#each menuItems.slice(0, Math.floor(menuItems.length / 2)) as item}
				<li><a href="{item.link}" on:click|preventDefault={scrollIntoView}>{item.title}</a></li>
			{/each}
			<li><a href="#" class="font-medium">reshape</a></li>
			{#each menuItems.slice(Math.floor(menuItems.length / 2)) as item}
				<li><a href="{item.link}" on:click|preventDefault={scrollIntoView}>{item.title}</a></li>
			{/each}
		</ul>
	</nav>
	<span class="font-medium text-base md:hidden">reshape</span>
	<button class="md:hidden ml-auto p-2" on:click={toggleMenu} class:rotate-45={!menuOpen}
		><MenuIcon /></button
	>
</header>

<main class=" inset reshape-gradient-bg">
	<div class="pt-11 inset-negative w-screen h-screen border-b-2 border-black">
		<!-- Desktop Animation -->
		<div class="absolute top-0 right-0 left-0 bottom-0 max-md:hidden"><Animation /></div>
		<!-- End of Desktop Animation -->
		<div class="animation-fallback-gif absolute top-0 right-0 left-0 bottom-0 md:hidden">
			<!-- <video autoplay="true" loop="true" contols>
				<track kind="captions">
				<source src={AnimationMovieMP4} type="video/mp4" />
			</video> -->
		</div>
		<div class=" absolute top-0 right-0 left-0 bottom-0">
			<div class="absolute left-0 bottom-0 right-0 z-10">
				<div class="inset pb-[24px] flex items-end justify-between">
					<div>
						<span class="block md:text-[64px] md:leading-[4rem]"
							><span class="md:text-[32px] md:leading-[4rem]"
							>upcoming:</span
							><br />
							14.-<br />
							16.5.25</span
						>
						<span class="reshape-copy">Hochschule für Gestaltung <br />Schwäbisch Gmünd</span>
					</div>
					<LabLogo />
				</div>
			</div>
			<div class="absolute top-0 left-0 bottom-0 right-0">
				<div class="h-full inset grid grid-cols-2 md:grid-cols-6 gap-5 md-gap-2 content-center">
					<div class="md:col-span-2" />

					<div class="-ml-[50%]">
						<h1 class="z-20 reshape-title">reshape</h1>
						<h2 class="z-20 reshape-subtitle">
							Forum for<br />Artificial Intelligence<br />in Art and Design
						</h2>
					</div>
				</div>
			</div>
		</div>
	</div>

	
	<section>
		<div class="md:grid md:grid-cols-3 gap-10 mt-[80px] md:mt-[250px]">
			<h2 class="reshape-section-header md:reshape-desktop-section-header mb-[55px] md:mb-0">
				Kontakt
			</h2>
			<p class="reshape-copy">
				<span class="font-medium">Hoch­schule für Gestal­tung Schwä­bisch Gmünd</span>
				<br />Univer­sity of Applied Sciences <br />Rektor-Klaus-Straße 100 <br />D‑73525
				Schwä­bisch Gmünd
				<a
					class="block reshape-link mt-[25px] md:mt-0"
					href="https://goo.gl/maps/FCYPLeJzBLvpGkQNA"
					target="_blank"
					rel="noopener noreferrer">Google Maps</a
				>
			</p>
			<p class="reshape-copy mt-[55px] md:mt-0">
				<span class="font-medium block mb-[25px] md:mb-0">Let's get social</span>
				<a
					class="reshape-link mr-14"
					href="https://www.instagram.com/hfg.gmuend/?hl=en"
					target="_blank"
					rel="noopener noreferrer">Instagram</a
				>
				<a
					class="reshape-link"
					href="https://twitter.com/gestaltungai"
					target="_blank"
					rel="noopener noreferrer">Twitter</a
				>
			</p>
		</div>
	</section>
	<footer class="pb-8 mt-[120px] md:mt-[250px]">
		<Logos />
	</footer>

	<footer class="flex gap-2 p-2 md:p-8 inset-negative border-black border-t-2 md:justify-around">
		<div class="flex flex-col md:flex-row gap-2 md:gap-4">
			<a href="https://www.hfg-gmuend.de/datenschutz" target="_blank" rel="noopener noreferrer"
				>Privacy policy</a
			>
			<a href="https://www.hfg-gmuend.de/impressum" target="_blank" rel="noopener noreferrer"
				>Imprint</a
			>
		</div>
		<button
			class="md:hidden reshape-copy flex-grow"
			on:click={() => {
				document.body.scrollIntoView({ behavior: 'smooth' });
			}}
			>Back to top <svg
				style:display="inline"
				style:margin-bottom="5px"
				width="15"
				height="15"
				viewBox="0 0 15 15"
				fill="none"
				xmlns="http://www.w3.org/2000/svg"
			>
				<path
					d="M7.19649 13.5598L7.36396 0.99932M7.36396 0.99932L1 7.36328M7.36396 0.99932L13.7279 7.36328"
					stroke="black"
					stroke-width="2"
					stroke-linecap="round"
					stroke-linejoin="round"
				/>
			</svg></button
		>
	</footer>
</main>

<style>
	.reshape-title {
		position: relative;
		font-size: 55px;
	}

	.reshape-subtitle {
		position: relative;
		font-size: 16px;
		line-height: 19px;
		font-weight: 300;
	}

	@media (min-width: 768px) {
		.reshape-title {
			font-size: 70px;
			line-height: 84px;
			font-weight: 500;
		}

		.reshape-subtitle {
			font-size: 20px;
			line-height: 23px;
			font-weight: 400;
		}
	}

	div.seperator {
		border-top: 2px solid black;
	}

	footer {
		font-size: 14px;
		font-weight: 300;
	}

	@media (min-width: 768px) {
		footer {
			font-size: 16px;
		}
	}

	.animation-fallback-gif {
		background-image: url('$lib/assets/animation-20fps-loop.gif');
	}

	.big-link {
		display: inline-block;
		height: auto;
		background-image: url('$lib/assets/arrow-big.svg');
		background-repeat: no-repeat;
		background-size: contain;
		background-position: center;
	}
</style>
