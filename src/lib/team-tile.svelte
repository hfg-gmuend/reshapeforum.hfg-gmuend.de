<script>
	export let name = '';
	export let description = '';
	export let file = '';
</script>

<div class="reshape-copy">
	<div class="border-black border-2 rounded-full w-36 h-36 image {file}" />
	<p class=" mt-4 name">{name}</p>
	<p>{description}</p>
</div>

<style>
	.name {
		font-weight: 500;
	}
	.image {
		background-image: url('$lib/assets/form-background.png');
		background-size: contain;
		background-repeat: no-repeat;
	}
	.felix {
		background-image: url('$lib/assets/ai-d-portraits/ai-d-portrait-felix.jpg');
	}
	.rahel {
		background-image: url('$lib/assets/ai-d-portraits/ai-d-portrait-rahel.jpg');
	}
	.jordi {
		background-image: url('$lib/assets/ai-d-portraits/ai-d-portrait-jordi.png');
	}
	.moritz {
		background-image: url('$lib/assets/ai-d-portraits/ai-d-portrait-moritz.png');
	}
	.bene {
		background-image: url('$lib/assets/ai-d-portraits/ai-d-portrait-bene.jpg');
	}
	.hartmut {
		background-image: url('$lib/assets/ai-d-portraits/ai-d-portrait-hartmut.jpg');
	}
	.chris {
		background-image: url('$lib/assets/ai-d-portraits/ai-d-portrait-chris.jpg');
	}
	.maxime {
		background-image: url('$lib/assets/ai-d-portraits/ai-d-portrait-maxime.png');
	}
</style>
