<script>
	import { onMount } from 'svelte';
	import chroma from 'chroma-js';
	import Grid from './Grid.svelte';

	// Number of squares
	let squareCount = 10;

	// Size of the squares
	let squareSize = 20;
	// Outer corner radius of the squares
	let outerRadius = 1.8;
	// Inner corner radius of the joints
	let innerRadius = 3.6;
	// Size of the blur
	let blur = 1.5;

	let colorOuter = '#B468FF';
	let colorInner = '#B468FF';
	let colorBackground = '#fff0';

	// colors for the scale. needed to add a color between green and blue to make the morphing nicer
	// let colors = [
	// 	'#6D59A2',
	// 	'#5C67AD',
	// 	'#ECE76F',
	// 	'#F19645',
	// 	'#E98AB7',
	// 	'#6D59A2'
	// ];
	let colors = [
		'#8b8b8b',
	];
	let colorScale = chroma
		.scale(colors)
		.domain([0, 0.24, 0.3, 0.44, 0.47, 0.5, 0.74, 0.77, 0.8, 0.94, 1])
		.mode('lch');

	// length in seconds for the color cycling
	let colorCycleLength = 42;

	// Threshold parameter for the animation when showing (or hiding) and moving squares.
	// The animation runs from 0 to 1. When reaching the threshold, the circle will be
	// full in size. The rest of the time is used to grow the joints and reduce the corner radius.
	let animationThreshold = 0.2;
	// Duration of the animations
	let duration = 1200;
	// Provide a function for the duration. This way this parameter is dynamic for the tweens.
	let animationDuration = (oldVal, newVal) => {
		return duration;
	};
	// Gives the interval in milliseconds after which animations will start
	let animationInterval = 1800;
	// Gives the probability in percent for a square to move
	let animationProbability = 40;

	// Automatic animation on or off
	let autoAnimate = true;
	let aniButtonText = autoAnimate ? 'Stop Animation' : 'Start Animation';

	// Organizing the grid. The grid is a 2d-Array filled with objects.
	// This way, the squares can change the values of the cells and other squares will know.
	let margin = squareSize / 2;
	let gridSizeX = 7;
	let gridSizeY = 6;
	let width = gridSizeX * squareSize + margin * 2;
	let height = gridSizeY * squareSize + margin * 2;

	function toggleAnimation() {
		autoAnimate = !autoAnimate;
		aniButtonText = autoAnimate ? 'Stop Animation' : 'Start Animation';
	}

	// animate color
	onMount(() => {
		let frame;

		function loop() {
			frame = requestAnimationFrame(loop);
			let millis = performance.now() % (colorCycleLength * 1000);
			colorOuter = colorScale(millis / (colorCycleLength * 1000)).hex();
		}

		loop();

		return () => cancelAnimationFrame(frame);
	});

	// element is the svg element itself or a container in which the svg is nested. If undefined, the document is searched.
	// if fileName is undefined it will be constructed with date and time information
	function saveSVG(element, fileName) {
		if (!element) {
			element = document.querySelector('svg');
			console.log(element);
		}

		if (!fileName) {
			let d = new Date();
			d = d.toISOString();
			d = d.replace('T', ' ');
			d = d.replace('Z', '');
			d = d.replaceAll(':', '.');
			fileName = 'image_' + d + '.svg';
		}

		let svgString =
			element.nodeName == 'svg' ? element.outerHTML : element.querySelector('svg').outerHTML;

		download(svgString, fileName, 'image/svg+xml');
	}

	// Function to download data to a file
	function download(data, filename, type) {
		let file = new Blob([data], { type: type });
		if (window.navigator.msSaveOrOpenBlob)
			// IE10+
			window.navigator.msSaveOrOpenBlob(file, filename);
		else {
			// Others
			let a = document.createElement('a'),
				url = URL.createObjectURL(file);
			a.href = url;
			a.download = filename;
			document.body.appendChild(a);
			a.click();
			setTimeout(function () {
				document.body.removeChild(a);
				window.URL.revokeObjectURL(url);
			}, 0);
		}
	}
</script>

<!-- <div class="flex toolbar">
  <ColorPicker bind:hex={colorBackground} label="Background color" />
  <ColorPicker bind:hex={colorOuter} label="Shape outer color" />
  <ColorPicker bind:hex={colorInner} label="Shape inner color" />
</div> -->

<svg
	version="1.1"
	xmlns="http://www.w3.org/2000/svg"
	xmlns:xlink="http://www.w3.org/1999/xlink"
	viewBox="0 0 {width} {height}"
>
	<filter id="blurMe">
		<feGaussianBlur in="SourceGraphic" stdDeviation={blur} />
	</filter>

	<symbol id="gridSymbol" {width} {height} viewbox="0 0 {width} {height}">
		<Grid
			{squareCount}
			{gridSizeX}
			{gridSizeY}
			{margin}
			{squareSize}
			{outerRadius}
			{innerRadius}
			{autoAnimate}
			{animationDuration}
			{animationThreshold}
			{animationInterval}
			{animationProbability}
		/>
	</symbol>

	<mask id="gridMask">
		<g id="grid" fill="white" stroke="white" stroke-width="0.5">
			<use href="#gridSymbol" />
		</g>
	</mask>

	<rect id="gridBackground" {width} {height} fill={colorBackground} />

	<g id="grid" fill={colorOuter} filter="url(#blurMe)" mask="url(#gridMask)">
		<rect id="gridColor" {width} {height} fill={colorOuter} />
		<g>
			<use href="#gridSymbol" />
		</g>
	</g>
</svg>

<style>
	/* .flex {
    display: flex;
  } */

	svg {
		width: 100%;
		height: 100%;
		padding-bottom: 2px;
	}
</style>
