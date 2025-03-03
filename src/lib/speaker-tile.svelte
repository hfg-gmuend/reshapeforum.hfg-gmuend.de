<script>
	export let name = '';
	export let subtitle = '';
	export let description = '';
	export let tint = 'reshape-blau';
	export let imageName = '';
</script>

<div class="reshape-copy pr-4">
	<!-- Rather ugly CSS hack to make Tailwind generate those dynamic classes :-/ -->
	<div
		class="bg-reshape-lila bg-reshape-blau bg-reshape-gruen border-reshape-lila border-reshape-blau border-reshape-gruen"
	/>
	<!-- End of rather ugly hack -->
	<div class="max-w-xs">
		<div class="wrapper square">
			<div
				class="speaker-image border-{tint} bg-{tint} border-2 rounded-full speaker-{imageName}"
			/>
		</div>
	</div>

	<h3 class="mt-[25px] md:mt-[15px] block reshape-lead-light">{name}</h3>
	<h4 class="reshape-copy">{subtitle}</h4>
	<p class="reshape-copy mt-4 md:mt-0 max-md:max-w-[70vw]">
		{@html description}
	</p>
</div>

<style>
	.wrapper {
		position: relative;
	}

	.square {
		width: 100%;
		padding-bottom: 100%;
	}
	.speaker-image {
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background-image: url($lib/assets/form-background.png);
		background-size: cover;
		background-repeat: no-repeat;
		background-blend-mode: screen;
	}

	.speaker-niko-klein {
		background-image: url($lib/assets/speaker-portraits/niko-klein.jpg);
	}

	.speaker-tobias-revell {
		background-image: url($lib/assets/speaker-portraits/tobias-revell.jpg);
	}

	.speaker-maria-luce-lupetti {
		background-image: url($lib/assets/speaker-portraits/maria-luce-lupetti.jpg);
	}

	.speaker-niklas-muhs {
		background-image: url($lib/assets/speaker-portraits/niklas-muhs.jpg);
	}

	.speaker-elisa-giaccardi {
		background-image: url($lib/assets/speaker-portraits/elisa-giaccardi.jpg);
	}

	.speaker-andreas-refsgaard {
		background-image: url($lib/assets/speaker-portraits/andreas-refsgaard.jpg);
	}

	.speaker-julian-oliver {
		background-image: url($lib/assets/speaker-portraits/julian-oliver.jpg);
	}

	.speaker-iohanna-nicenboim {
		background-image: url($lib/assets/speaker-portraits/iohanna-nicenboim.jpg);
	}

	.speaker-eryk-salvaggio {
		background-image: url($lib/assets/speaker-portraits/eryk-salvaggio.jpg);
	}

	.speaker-image:hover {
		background-blend-mode: normal;
		border-color: inherit;
	}
</style>
