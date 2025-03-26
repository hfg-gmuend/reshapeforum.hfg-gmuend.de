<script>
	import ScheduleItem from '$lib/schedule-item.svelte';
	export let scheduleItems = [];
	export let keyColor = 'inherit';
	export let bgMotive = '';
	let expandedIndex = -1;

	$: expandedDescription =
		expandedIndex > -1 && scheduleItems[expandedIndex].description
			? scheduleItems[expandedIndex].description
			: '';

	function toggle(index) {
		if (index === expandedIndex) {
			expandedIndex = -1;
		} else {
			expandedIndex = index;
		}
	}
</script>

<article class="background-motive-{bgMotive}my-[55px] md:my-[65px]">
	<div class="md:grid md:grid-cols-3 gap-10">
		<slot name="heading" />
		<ul class="time-table reshape-lead-light md:col-span-2">
			{#each scheduleItems as item, i}
				<li class="mb-[55px] md:mb-[55px]">
					<ScheduleItem
						timeslot={item.timeslot}
						title={item.title}
						subtitle={item.subtitle}
						description={item.description}
						color={i === expandedIndex ? keyColor : expandedIndex > -1 ? 'muted' : 'black'}
						on:click={() => toggle(i)}
					/>
					{#if i === expandedIndex}
						<div class="mt-4">
							<p class="reshape-copy">{@html expandedDescription}</p>
						</div>
					{/if}
				</li>
			{/each}
		</ul>
		<!-- <div><p class="reshape-copy max-md:hidden">{@html expandedDescription}</p></div> -->
	</div>
</article>

<style>
	article {
		background-size: cover;
	}

	.background-motive-wed {
		background-image: url('/bg-wed.svg');
	}

	.background-motive-thu {
		background-image: url('/bg-thu.svg');
	}

	.background-motive-fri {
		background-image: url('/bg-fri.svg');
	}
</style>
