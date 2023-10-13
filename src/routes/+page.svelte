<script lang="ts">
	import { onMount } from 'svelte';
	let timeout;
	let currentWordIndex = 0;
	let words: HTMLCollection | undefined;
	let undoAnimation = [{ opacity: 1 }, { opacity: 0 }];
	const lingerDuration = 6000;
	let animations = [
		{
			animation: [{ opacity: 0 }, { opacity: 1 }],
			duration: 2000,
			undoAnimation: [{ opacity: 1 }, { opacity: 0 }]
		},
		{
			animation: {
				opacity: [1, 1],
				transform: ['translate(-50%, 0%) scale(0)', 'translate(-50%, 0%) scale(1)']
			},
			duration: 750,
			undoAnimation: [{ opacity: 1 }, { opacity: 0 }]
		},
		{
			animation: {
				transform: ['translate(-50%, 0) rotateX(90deg)', 'translate(-50%, 0)'],
				opacity: [1, 1]
			},
			duration: 750,
			undoAnimation: [{ opacity: 1 }, { opacity: 0 }]
		},
		{
			animation: {
				opacity: [1, 1],
				transform: [
					'translate(-50%, 0) scale(0) rotate(180deg)',
					'translate(-50%, 0) scale(1) rotate(0)'
				]
			},
			duration: 750,
			undoAnimation: [{ opacity: 1 }, { opacity: 0 }]
		}
	];

	onMount(() => {
		words = document.getElementById('words')?.children ?? new HTMLCollection();
		currentWordIndex = 0;
		timeout = setTimeout(doAnimation, lingerDuration);
		console.log(words);
	});
	function getRandomAnimation() {
		return animations[Math.floor(Math.random() * animations.length)];
	}
	function doAnimation() {
		if (!words) {
			return;
		}
		words?.item(currentWordIndex)?.animate(undoAnimation, {
			duration: 2000,
			fill: 'forwards',
			easing: 'ease-in-out'
		});
		// Increment index or wrap around to beginning again
		currentWordIndex = (currentWordIndex + 1) % words.length;

		const animation = getRandomAnimation();
		words?.item(currentWordIndex)?.animate(animation.animation, {
			duration: 750,
			delay: 2000,
			fill: 'forwards',
			easing: 'ease-in-out'
		});
		undoAnimation = animation.undoAnimation;
		timeout = setTimeout(doAnimation, lingerDuration);
	}
</script>

<section class=" min-h-screen flex flex-col text-3xl md:text-5xl pt-12 md:pt-20">
	<div class="flex justify-center">
		<div>Riley is</div>
	</div>
	<div id="words" class="words text-5xl md:text-9xl text-center">
		<div>EXTRAORDINARY</div>
		<div>AMAZING</div>
		<div>BEAUTIFUL</div>
		<div>WONDERFUL</div>
		<div>LOVELY</div>
		<div>INCREDIBLE</div>
		<div>GORGEOUS</div>
		<div>SPECIAL</div>
		<div>KIND</div>
		<div>SWEET</div>
		<div>PERFECT</div>
		<div>ADORABLE</div>
		<div>FUNNY</div>
		<div>CARING</div>
		<div>EXCEPTIONAL</div>
		<div>UNIQUE</div>
		<div>FANTASTIC</div>
		<div>MAGNIFICENT</div>
		<div>PRECIOUS</div>
		<div>IRREPLACEABLE</div>
	</div>
</section>

<style>
	section {
		background-color: black;
		color: white;
	}
	.words {
		position: relative;
	}
	.words div:first-child {
		opacity: 1;
	}
	.words div {
		position: absolute;
		opacity: 0;
		left: 50%;
		perspective: 10em;
		transform: translate(-50%, 0%);
		line-height: normal;
	}
</style>
