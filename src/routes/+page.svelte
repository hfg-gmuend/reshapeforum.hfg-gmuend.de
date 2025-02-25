<script>
	import ITEMS_WEDNESDAY from '../data/schedule-wednesday.js';
	import ITEMS_THURSDAY from '../data/schedule-thursday.js';
	import ITEMS_FRIDAY from '../data/schedule-friday.js';
	import WORKSHOPS from '../data/workshops.js';

	import Animation from '$lib/animation-25/Animation.svelte';
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
		if (target.getAttribute('href').startsWith('http'))
			window.open(target.getAttribute('href'), '_blank');
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
		{ title: 're shape 2023', link: 'https://reshapeforum.hfg-gmuend.de/2023/' }
	];

	function randomPosition() {
		return {
			top: Math.random() * 70 + '%',
			left: Math.random() * 70 + '%'
		};
	}

	const emojis = ['❓', '🤖', '🎨', '📄'];
	const emojiPositions = emojis.map(() => randomPosition());
</script>

<!-- Mobile Menu -->
{#if menuOpen}
	<nav class="fixed w-full z-30 h-screen pt-11 bg-reshape-mauve inset text-4xl md:hidden">
		<ul class="h-full flex flex-col pt-[80px]">
			{#each menuItems as item}
				<li class="basis-[95px]" on:click={() => (menuOpen = false)}>
					<a href={item.link} on:click|preventDefault={scrollIntoView} target="_blank"
						>{item.title}</a
					>
				</li>
			{/each}
		</ul>
	</nav>
{/if}

<header
	class="fixed w-full z-30 inset border-y-2 border-black h-11 flex items-center bg-black text-white"
>
	<nav class="font-light w-full max-md:hidden">
		<ul class="flex justify-between">
			{#each menuItems.slice(0, Math.floor(menuItems.length / 2)) as item}
				<li><a href={item.link} on:click|preventDefault={scrollIntoView}>{item.title}</a></li>
			{/each}
			<li><a href="#" class="font-medium">re shape</a></li>
			{#each menuItems.slice(Math.floor(menuItems.length / 2)) as item}
				<li><a href={item.link} on:click|preventDefault={scrollIntoView}>{item.title}</a></li>
			{/each}
		</ul>
	</nav>
	<span class="font-medium text-base md:hidden">re shape</span>
	<button class="md:hidden ml-auto p-2" on:click={toggleMenu} class:rotate-45={!menuOpen}
		><MenuIcon /></button
	>
</header>

<main class=" inset ">
	<div class="pt-11 inset-negative w-screen h-screen border-b-2 border-black">
		<!-- Desktop Animation -->
		<div class="absolute top-0 right-0 left-0 bottom-0 "><Animation /></div>
		<!-- End of Desktop Animation -->
		<!-- <div class="animation-fallback-gif absolute top-0 right-0 left-0 bottom-0 md:hidden">
			<video autoplay="true" loop="true" contols>
				<track kind="captions">
				<source src={AnimationMovieMP4} type="video/mp4" />
			</video> 
		</div> -->
		<div class=" absolute top-0 right-0 left-0 bottom-0 pointer-events-none">
			<div class="absolute left-0 bottom-0 right-0 z-10">
				<div class="inset pb-[24px] flex items-end justify-end">
					<div class="hidden">
						<span class="block md:text-[64px] md:leading-[4rem]"
							><span class="md:text-[32px] md:leading-[4rem]">upcoming:</span><br />
							14.-<br />
							16.5.25</span
						>
						<span class="reshape-copy">Hochschule für Gestaltung <br />Schwäbisch Gmünd</span>
					</div>
					<div class="w-28 md:w-40">
						<a href="https://aid-lab.hfg-gmuend.de/" target="_blank" rel="noopener noreferrer">
							<LabLogo />
						</a>
					</div>
				</div>
			</div>
			<div class="absolute top-0 left-0 bottom-0 right-0 hidden">
				<div class="h-full inset grid grid-cols-2 md:grid-cols-6 gap-5 md-gap-2 content-center">
					<div class="md:col-span-2" />

					<div class="-ml-[50%]">
						<h1 class="z-20 reshape-title">re shape</h1>
						<h2 class="z-20 reshape-subtitle">
							Forum for Artificial Intelligence<br />in Art and Design
						</h2>
					</div>
				</div>
			</div>
		</div>
	</div>

	<section class="first-section z-20 relative">
		<div class="md:grid md:grid-cols-3 gap-10 mt-[80px] md:mt-[134px] mb-[80px] md:mb-[120px]">
			<div class="relative w-full h-full min-h-[300px]">
				{#each emojis as emoji, i}
					<img
						class="floating{[i+1]} w-20 h-20"
						style="position: absolute; top: {emojiPositions[i].top}; left: {emojiPositions[i].left};"
						src="images/{[i+1]}.png"
						alt="Floating {emojis[i]} emoji"

					/>
				{/each}
			</div>
			<div class="md:col-span-2">
				<!-- Heading -->
				<h1 class="text-5xl md:text-6xl leading-tight mb-12 text-gray-900">
					How does Machine Learning reshape the landscape of art and design?
				</h1>

				<!-- Content Columns -->
				
					<p class="reshape-copy">
						Join us at the re shape Forum for Artificial Intelligence in Art and Design. A dynamic two-day convergence where AI and creative practice intersect. This forum brings together leading thinkers who critically examine the role of AI in shaping design practice, ethical frameworks, and user experiences. Through speculative approaches, narrative exploration, and critical engineering, these experts challenge dominant paradigms and propose new ways of engaging with technology.
						
					</p>
					
				
			</div>
		</div>
	</section>

	<section>
		<div class="md:grid md:grid-cols-3 gap-10 mt-[80px] md:mt-[250px]">
			<h2 class="reshape-section-header md:reshape-desktop-section-header mb-[55px] md:mb-0">
				Contact
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
					href="https://sigmoid.social/@gestaltungai"
					target="_blank"
					rel="noopener noreferrer">Mastodon</a
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
		background: #ece76f;
		padding: 0.5em;
		border-radius: 0.5em;
		display: inline-flex;
		padding-top: 0.1em;
		padding-bottom: 0.2em;
	}

	.reshape-subtitle {
		position: relative;
		font-size: 16px;
		line-height: 19px;
		font-weight: 500;
		margin-top: 1em;
		margin-left: 2em;
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
			font-weight: 500;
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

	.animation-fallback-gif-off {
		background-image: url('$lib/assets/static-back.png');
	}

	.big-link {
		display: inline-block;
		height: auto;
		background-image: url('$lib/assets/arrow-big.svg');
		background-repeat: no-repeat;
		background-size: contain;
		background-position: center;
	}

	@keyframes floating1 {
		0% { transform: translate3d(0, 0, 0) rotate(0deg); }
		33% { transform: translate3d(-10px, -15px, 0) rotate(-5deg); }
		66% { transform: translate3d(15px, -5px, 0) rotate(5deg); }
		100% { transform: translate3d(0, 0, 0) rotate(0deg); }
	}

	@keyframes floating2 {
		0% { transform: translate3d(0, 0, 0) rotate(0deg); }
		33% { transform: translate3d(15px, 10px, 0) rotate(8deg); }
		66% { transform: translate3d(-8px, -12px, 0) rotate(-8deg); }
		100% { transform: translate3d(0, 0, 0) rotate(0deg); }
	}

	@keyframes floating3 {
		0% { transform: translate3d(0, 0, 0) rotate(0deg); }
		33% { transform: translate3d(-12px, -20px, 0) rotate(-10deg); }
		66% { transform: translate3d(20px, -8px, 0) rotate(10deg); }
		100% { transform: translate3d(0, 0, 0) rotate(0deg); }
	}

	@keyframes floating4 {
		0% { transform: translate3d(0, 0, 0) rotate(0deg); }
		33% { transform: translate3d(8px, 5px, 0) rotate(5deg); }
		66% { transform: translate3d(-5px, -8px, 0) rotate(-5deg); }
		100% { transform: translate3d(0, 0, 0) rotate(0deg); }
	}

	.floating1 {
		animation: floating1 22s ease-in-out infinite;
	}

	.floating2 {
		animation: floating2 42s ease-in-out infinite;
	}

	.floating3 {
		animation: floating3 34s ease-in-out infinite;
	}

	.floating4 {
		animation: floating4 14s ease-in-out infinite;
	}
</style>
