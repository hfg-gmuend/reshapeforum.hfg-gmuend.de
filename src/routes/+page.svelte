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
		{ title: 'Programme', link: '#program' },
		{ title: 'Registration', link: '#anmeldung' },
		{ title: 'AI+D Lab', link: '#ai-d-lab' },
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
					<a href="#{item.link}" on:click|preventDefault={scrollIntoView}>{item.title}</a>
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
				<li><a href="#{item.link}" on:click|preventDefault={scrollIntoView}>{item.title}</a></li>
			{/each}
			<li><a href="#" class="font-medium">reshape</a></li>
			{#each menuItems.slice(Math.floor(menuItems.length / 2)) as item}
				<li><a href="#{item.link}" on:click|preventDefault={scrollIntoView}>{item.title}</a></li>
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
							>14.-<br />
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

	<section class="first-section">
		<p
			class="mt-[80px] md:mt-[134px] mb-[80px] md:mb-[120px] reshape-lead-medium md:reshape-desktop-lead-medium max-w-[1200px]"
		>
			How do Artificial Intelligence and Machine Learning reshape the landscape of art and design?
		</p>
		<div class="md:grid md:grid-cols-3 gap-10">
			<div />
			<p class="reshape-copy max-md:inset-left mb-[25px] md:mb-0 border-black">
				This is the third summer school organized by KITeGG, a research project of five German
				universities on Artificial Intelligence in Art and Design running from 2021-2025. Featuring
				a lineup of thought-provoking talks, engaging panel discussions, and hands-on workshops, we
				will explore how AI can expand art and design practices and enhance creativity, learn about
				the challenges and affordances of designing AI-based interfaces and products, and discover
				best practices for teaching AI to creatives. Join us for a refreshing and in-depth
				exploration of the many-sided world of creative AI.
			</p>
			<p class="reshape-copy max-md:inset-left mb-[25px] md:mb-0">
				Wow, what has happened in the last 12 months? We are experiencing a new wave of AI hype, and
				generative AI is at the heart of it. The success of tools like ChatGPT, Stable Diffusion or
				DALL-E, which are usable for everyone, have contributed to the fact that the topic of “AI
				and creativity” is now mainstream and widely discussed. New products and business models
				based on the technology of large language models (LLMs) are spawning everywhere. Signs of
				generative AI becoming a maturing technology.
			</p>
			<div />
			<p class="reshape-copy max-md:inset-left mb-[25px] md:mb-0">
				In the midst of this new "AI summer", the 3rd KITeGG summer school is taking place! Where do
				the KITeGG community and its five AI labs conceptually position themselves in 2023? What is
				different in this wave of AI hype than in the waves before? This symposium gathers a diverse
				group of experts to discuss exciting and finicky questions emerging when AI meets design and
				art along 3 axes:
			</p>

			<p class="reshape-copy max-md:inset-left mb-[25px] md:mb-0">
				<span class="font-[500]">1. Design for AI (Designing AI systems) </span><br /> What are the
				challenges and affordances for designing AI-based interfaces and products? What do designers
				need to know to design these systems responsibly? What is the broader societal context of
				these technologies, underlying practices and economies?
				<br /><br />

				<span class="font-[500]">2. AI for Design (Creative AI)</span><br /> How can AI augment art
				and design practices? How do AI technologies integrate as tools in the creative toolbox of
				artists and designers? How can AI act as a tool for ideation, enhancing rather than
				flattening creativity?
				<br /><br />

				<span class="font-[500]">3. Teaching AI to creatives</span><br /> Approaches and best practices
				for teaching AI to creatives. How to increase AI and data literacy? How do we help build up intuitions
				for AI use cases, consciously assess the benefits and risks and decide when to use or not use
				AI-based technologies?
			</p>
		</div>
	</section>

	<section class="mt-[120px] md:mt-[250px]">
		<div class="md:grid md:grid-cols-3 gap-10">
			<h2 class="reshape-section-header md:reshape-desktop-section-header mb-[25px] md:mb-0">
				Speakers
			</h2>
			<div class="col-span-2">
				<!-- MOBILE (carousel) -->
				<div class="overflow-hidden inset-negative md:hidden max-md:-mt-[55px]">
					<SpeakersMobile />
				</div>
				<!-- DESKTOP -->
				<div class="max-md:hidden md:visible">
					<SpeakersDesktop />
				</div>
			</div>
		</div>
	</section>
	<section id="program" class="pt-[250px]">
		<h2 class="reshape-section-header md:reshape-desktop-section-header mb-[25px] md:mb-[25px]">
			Public programme
		</h2>
		<p class="mb-[55px] md:mb-[65px] reshape-lead-light">
			May 10-12, 2023 <br /> (Wednesday - Friday)
		</p>
		<div class="inset-negative"><div class="seperator" /></div>
		<Schedule scheduleItems={ITEMS_WEDNESDAY} keyColor="reshape-lila" bgMotive="wed">
			<h3
				slot="heading"
				class="reshape-subsection-header text-reshape-lila basis-1/2 mb-[55px] md:mb-0"
			>
				Wednesday<br />May 10
			</h3>
		</Schedule>
		<div class="inset-negative"><div class="seperator" /></div>
		<Schedule scheduleItems={ITEMS_THURSDAY} keyColor="reshape-blau" bgMotive="thu"
			><h3
				slot="heading"
				class="reshape-subsection-header text-reshape-blau basis-1/2 mb-[55px] md:mb-0"
			>
				Thursday<br />May 11
			</h3></Schedule
		>
		<div class="inset-negative"><div class="seperator" /></div>
		<Schedule scheduleItems={ITEMS_FRIDAY} keyColor="reshape-gruen" bgMotive="fri">
			<h3
				slot="heading"
				class="reshape-subsection-header text-reshape-gruen basis-1/2 mb-[55px] md:mb-0"
			>
				Friday<br />May 12
			</h3>
		</Schedule>
		<div id="anmeldung" class="inset-negative"><div class="seperator" /></div>
		<!--<p
			class="reshape-lead-medium md:reshape-desktop-lead-medium mt-[55px] md:mt-[120px] max-w-[900px]"
		>
			<a
				href="#"
				target="_blank"
				rel="noopener noreferrer"
				>Join the conference remotely via <span class="text-[#6262FF]">Zoom</span>
				<span class="big-link w-[12px] md:w-8 md:h-8">&nbsp;</span>
			</a>
		</p> -->
	</section>
	<section class="mt-[80px] md:mt-[134px]">
		<div class="md:grid md:grid-cols-3 gap-10 md:mb-[55px]">
			<div class="col-span-2">
				<h2 class="reshape-section-header md:reshape-desktop-section-header mb-[25px] md:mb-[25px]">
					Internal program
				</h2>
				<p class="reshape-copy md:reshape-lead-light">
					Mai 8-10, 2023 <br />(Monday - Wednesday)
				</p>
				<p class="reshape-copy mt-[25px] inset-left">
					Please note that these workshops are intended for students of HfG and KITeGG partner
					universities as part of the "Internationale Seminarwoche" at HfG. If you're interested in
					the workshop results, join us on Friday 13:00 at the AI+D Lab at HfG for a presentation of
					the workshop results!
				</p>
			</div>
			<p class="reshape-copy inset-left mt-[25px] mb-[55px] md:mt-0 md:mb-0">
				Educators from KITeGG partner universities will offer a series of workshops in the days
				leading up to the conference, providing a hands-on experience of the range of possibilities
				that AI and machine learning hold for designers.
			</p>
		</div>

		<div class="inset-negative"><div class="seperator" /></div>

		<Schedule scheduleItems={WORKSHOPS}>
			<h3 slot="heading" class="reshape-subsection-header basis-1/2 mb-[55px] md:mb-0">
				Workshops (internal)
			</h3>
		</Schedule>
	</section>
	<section id="kitegg">
		<div class="inset-negative"><div class="seperator" /></div>

		<p
			class="reshape-lead-medium md:reshape-desktop-lead-medium mt-[165px] md:mt-[250px] max-w-[920px]"
		>
			KITeGG - A collaborative project of five universities for the integration of AI in the design
			teaching.
		</p>
		<div class="md:grid md:grid-cols-3 gap-10 pt-[80px] md:pt-[120px]">
			<h2 class="reshape-section-header md:reshape-desktop-section-header mb-[55px] md:mb-0">
				<a href="https://gestaltung.ai" target="_blank" rel="noopener noreferrer"
					>KITeGG <span class="big-link w-[12px] md:w-8 md:h-8">&nbsp;</span></a
				>
			</h2>
			<p class="reshape-copy max-md:inset-left">
				The joint project "KITeGG - Making AI tangible and comprehensible: Connecting technology and
				society through design" explores how AI can be integrated into design education. Project
				partners are the Mainz University of Applied Sciences, the Offenbach University of Art and
				Design, the Schwäbisch Gmünd University of Applied Design, the Cologne International School
				of Design and the Trier University of Applied Sciences.
			</p>
			<p class="reshape-copy max-md:inset-left mt-[25px] md:mt-0">
				As “designers of the future” students should have knowledge of current and future relevant
				technologies as well as profound technical knowledge and the ability to systematically
				consider user contexts and ethical and legal issues. They should be able to use machine
				learning reflectively as a tool and material for design. To this end, new teaching formats,
				tools, symposia and publications are to be developed within the framework of the project
				over the next three years. KITeGG is one of over 50 funded projects within the framework of
				the funding initiative "Künstliche Intelligenz in der Hochschulbildung".
			</p>
		</div>
	</section>
	<section>
		<div class="md:grid md:grid-cols-3 gap-10 pt-[80px] md:pt-[250px]" id="ai-d-lab">
			<h2 class="reshape-section-header md:reshape-desktop-section-header mb-[55px] md:mb-0">
				AI+D Lab
			</h2>
			<p class="reshape-copy max-md:inset-left">
				The AI+D Lab is the place at HfG Schwäbisch Gmünd for research and teaching that combines
				artificial intelligence and design. In the form of generated images and videos, intelligent
				sensor systems and language-based interfaces, Machine Learning will be increasingly used in
				design practice. We will explore the possibilities and challenges of artificial intelligence
				in design: How can AI technologies be integrated into the design process and how does this
				change the work of designers? What role can designers play in the (co-)design of AI-based
				applications and how do they need to be prepared for this in their education and how can we
				design desirable futures with AI technologies?
			</p>
			<p class="reshape-copy max-md:inset-left mt-[25px] md:mt-0">
				In order to be able to use AI technologies in the design process, it is important to build
				up sound knowledge about the possibilities and limitations of this technology. As a physical
				location, the lab is the first point of contact for students and teachers and provides the
				technical infrastructure for projects and experiments with different AI models and
				technologies. The AI+D Lab is part of KITeGG.
			</p>
		</div>
	</section>
	<section>
		<div class="md:grid md:grid-cols-3 gap-10 mt-[80px] md:mt-[250px]">
			<h2 class="reshape-section-header md:reshape-desktop-section-header mb-[55px] md:mb-0">
				AI+D Team
			</h2>
			<div
				class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-y-[55px] md:gap-y-[65px] col-span-2"
			>
				<TeamTile name="Rahel Flechtner" description="Visiting professor" file="rahel" />
				<TeamTile name="Aeneas Stankowski" description="Visiting professor" file="aeneas" />
				<TeamTile name="Felix Sewing" description="AI+D Lab lead" file="felix" />
				<TeamTile name="Alexa Steinbrück" description="Researcher" file="alexa" />
				<TeamTile name="Benedikt Groß" description="Project lead" file="bene" />
				<TeamTile name="Hartmut Bohnacker" description="Project lead" file="hartmut" />
				<TeamTile name="Christopher Pietsch" description="Researcher" file="chris" />
				<TeamTile name="Johannes Rothkegel" description="Student assistant" file="johannes" />
			</div>
		</div>
	</section>
	<section>
		<div class="md:grid md:grid-cols-3 gap-10 md:mt-[250px]">
			<h2 class="reshape-section-header md:reshape-desktop-section-header mt-[80px] md:mt-0">
				Reshape Team
			</h2>
			<div class="max-md:inset-left mt-[55px] md:mt-0">
				<p class="reshape-copy">
					<span class="font-medium">Programme & Coordination</span><br />Alexa Steinbrück
				</p>
				<p class="reshape-copy mt-[25px] md:mt-[25px]">
					<span class="font-medium">Graphic design</span><br />Lucie Paula Claire de Hair, Marlene
					Metschies, Kirsten Humpfer
				</p>
				<p class="reshape-copy mt-[25px] md:mt-[25px]">
					<span class="font-medium">Website development</span><br />Alexa Steinbrück
				</p>
				<p class="reshape-copy mt-[25px] md:mt-[25px]">
					<span class="font-medium">Animation</span><br />Hartmut Bohnacker
				</p>
			</div>
			<div class="max-md:inset-left reshape-copy mt-[25px] md:mt-0">
				<span class="font-medium">Production</span>
				<ul>
					<li>Felix Sewing</li>
					<li>Johannes Rothkegel</li>
					<li>Rahel Flechtner</li>
					<li>Aeneas Stankowski</li>
					<li>Benedikt Groß</li>
					<li>Christopher Pietsch</li>
					<li>Katharina Neugart</li>
					<li>Malte Fial</li>
				</ul>
			</div>
		</div>
	</section>
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
