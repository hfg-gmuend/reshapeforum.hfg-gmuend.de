<script>
	import SpeakerTile from '$lib/speaker-tile.svelte';
	import SPEAKERS from '../data/speakers.js';
	let expandedIndex = -1;
	const nPerGridRow = 3;
	$: expandedDescription = expandedIndex >= 0 ? SPEAKERS[expandedIndex].description : '';
	$: nGridRows = Math.ceil(SPEAKERS.length / nPerGridRow);

	function idx(row, i) {
		return row * nPerGridRow + i;
	}

	function toggle(index) {
		if (expandedIndex === index) {
			expandedIndex = -1;
		} else {
			expandedIndex = index;
		}
	}

	function getItemsPerRow(rowIndex) {
		return SPEAKERS.slice(rowIndex * nPerGridRow, rowIndex * nPerGridRow + 3);
	}

	function isPartOfRow(idx, rowIndex) {
		return idx >= rowIndex * nPerGridRow && idx < (rowIndex + 1) * nPerGridRow;
	}

	function getOpacity(idx, expandedIndex) {
		if (expandedIndex < 0) {
			return 1;
		}
		return idx !== expandedIndex ? '0.5' : 1;
	}
</script>

<div class="grid grid-cols-3 gap-y-[65px]">
	{#each Array(nGridRows) as _, rowIndex}
		<!-- svelte-ignore a11y-click-events-have-key-events -->
		{#each getItemsPerRow(rowIndex) as item, colIndex}
			<div
				style:opacity={getOpacity(idx(rowIndex, colIndex), expandedIndex)}
				class:cursor-pointer={idx(rowIndex, colIndex) !== expandedIndex}
				on:click={() => toggle(idx(rowIndex, colIndex))}
			>
<SpeakerTile
	tint={idx(rowIndex, colIndex) !== expandedIndex && 'reshape-lila'}
	name={item.name}
	subtitle={item.subtitle}
	imageName={item.image}
	youtube={item.youtube}
/>
			</div>
		{/each}
		<div class="col-span-full md:-my-[65px]" class:hidden={!isPartOfRow(expandedIndex, rowIndex)}>
			<p class="reshape-copy mb-8 md:mt-[25px]">
				{@html expandedDescription}
			</p>
		</div>
	{/each}
</div>
