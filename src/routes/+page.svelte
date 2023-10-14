<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	let interval: NodeJS.Timeout;
	let currentWordIndex = 0;
	let words: HTMLCollection | undefined;
	const lingerDuration = 8000;
	// TODO: remove once done testing
	const animate = true;
	let animationIndex = null;

	let animations = ['fade-in', 'grow-in', 'slide-in', 'rotate-in', 'fancy-rotate-in'];
	let animationsMap = new Map<string, HTMLDivElement>();

	onMount(() => {
		animations.forEach((animation) => {
			words?.item(0)?.classList.remove(animation);
		});
		console.log('on mount');
		words = document.getElementById('words')?.children ?? new HTMLCollection();
		animationsMap.set('fade-in', createTextHint('lover'));
		animationsMap.set('slide-in', createTextHint('red'));
		animationsMap.set('grow-in', createTextHint('taylor swift'));
		animationsMap.set('rotate-in', createTextHint('fearless'));
		animationsMap.set('fancy-rotate-in', createTextHint('speak now'));
		if (animate) {
			if (interval) {
				clearInterval(interval);
			}
			console.log('starting interval');
			doAnimation();
			interval = setInterval(doAnimation, lingerDuration);
		}

		// Intersection observer to fade in and out background
		let imgs = document.querySelectorAll('img');
		if (imgs) {
			let options = { root: null, threshold: 0.1 };
			let observer = new IntersectionObserver((entries, observer) => {
				entries.forEach((entry) => {
					if (entry.isIntersecting) {
						entry.target.classList.remove('opacity-0');
					} else {
						entry.target.classList.add('opacity-0');
					}
				});
			}, options);
			imgs.forEach((img) => {
				observer.observe(img);
			});
		}
	});
	function createTextHint(text: string) {
		const element = document.createElement('div');
		element.classList.add('text-lg');
		element.innerHTML = `(${text})`;
		return element;
	}

	onDestroy(() => {
		clearInterval(interval);
	});
	function doAnimation() {
		if (!words) {
			return;
		}
		let animation = animations[currentWordIndex % animations.length];
		if (animationIndex || animationIndex === 0) {
			animation = animations[animationIndex];
		}
		console.log(`animating ${animation} for ${currentWordIndex}`);
		let word = words?.item(currentWordIndex);
		if (word) {
			word.classList.add(animation);
			let child = animationsMap.get(animation) ?? createTextHint('fallback');
			word.appendChild(child);
			// word.innerHTML += '<br>lover';
			let currentWordIndexCopy = currentWordIndex;
			setTimeout(() => {
				console.log(`Removing ${animation} from ${currentWordIndexCopy}`);
				words?.item(currentWordIndexCopy)?.classList.remove(animation);
				if (word) {
					word.removeChild(child);
				} else {
					console.error(`Cannot find word ${currentWordIndexCopy}`);
				}
			}, lingerDuration);
		}

		// Increment index or wrap around to beginning again
		currentWordIndex = (currentWordIndex + 1) % words.length;
	}
</script>

<article class="h-screen scroller">
	<section class="min-h-screen flex flex-col text-3xl md:text-5xl pt-40 md:pt-20">
		<div class="flex justify-center">
			<div>Riley is</div>
		</div>
		<!-- have these classes used so they aren't auto removed -->
		<div class="grow-in slide-in fade-in rotate-in fancy-rotate-in" />
		<div id="words" class="words text-4xl md:text-8xl text-center">
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
	<section class="min-h-screen flex justify-center relative">
		<img class="landscape-img opacity-0" src="./lover-background.jpg" alt="" />
		<img class="portrait-img opacity-0" src="./lover-background-phone.jpg" alt="" />
		<audio controls preload="auto">
			<source src="./taylor_swift.ogg" type="audio/ogg" />
			<source src="./taylor_swift.mp3" type="audio/mp3" />
			Unable to load
		</audio>
	</section>
</article>

<style>
	audio {
		position: absolute;
		top: 85%;
	}

	img {
		width: 100%;
		height: 100vh;
		object-fit: cover;
		transition: opacity 3s ease-in;
	}
	.landscape-img {
		display: none;
	}
	@media (min-aspect-ratio: 7/5) {
		audio {
			top: 75%;
		}
		img {
			width: 100%;
			height: 100vh;
			object-fit: cover;
		}
		.landscape-img {
			display: block;
		}
		.portrait-img {
			display: none;
		}
	}
	.scroller {
		overflow-y: scroll;
		scroll-snap-type: y mandatory;
	}
	.scroller > section {
		scroll-snap-align: start;
	}
	section {
		background-color: #111;
		color: white;
	}
	.words {
		--showtime: 6s;
		position: relative;
		perspective: 10em;
	}
	.words div:first-child {
		opacity: 1;
	}
	@keyframes gradientFlow {
		0% {
			background-position: 0 0;
		}
		100% {
			background-position: 100% 100%;
		}
	}
	.words > div {
		/* --color1: #008fff;
		--color2: #a000a0; */
		background-image: linear-gradient(
			90deg,
			var(--color1),
			var(--color2),
			var(--color1),
			var(--color2)
		);
		background-clip: text;
		background-size: 300%;
		color: transparent;
		position: absolute;
		opacity: 0;
		left: 50%;
		transform: translate(-50%, 0);
		line-height: normal;
	}
	.fancy-rotate-in {
		/* speak now */
		--color1: #caa7c7;
		--color2: #974a9f;
		/* --color1: #008fff;
		--color2: #a000a0; */
		animation: fancy-rotate-in 750ms ease-in-out 1 forwards,
			fade-in 2s ease-out calc(var(--showtime)) 1 forwards reverse, gradientFlow 3s linear infinite;
	}
	.rotate-in {
		/* fearless */
		--color1: #ece4d7;
		--color2: #8a5d20;
		animation: rotate-in 750ms ease-in-out 1 forwards,
			fade-in 2s ease-out calc(var(--showtime)) 1 forwards reverse, gradientFlow 3s linear infinite;
	}
	.grow-in {
		/* taylor swift */
		--color1: #0fbaed;
		--color2: #068d41;
		animation: grow-in 750ms ease-in-out 1 forwards,
			fade-in 2s ease-out calc(var(--showtime)) 1 forwards reverse, gradientFlow 3s linear infinite;
	}
	.slide-in {
		/* red */
		--color1: #ef1014;
		--color2: #c5b29a;
		animation: slide-in 750ms ease-in-out 1 forwards,
			fade-in 2s ease-out calc(var(--showtime)) 1 forwards reverse, gradientFlow 3s linear infinite;
	}
	.fade-in {
		/* lover */
		--color1: #74bfed;
		--color2: #cf3172;
		animation: fade-in 1.5s ease-in-out 1 forwards,
			fade-in 2s ease-out calc(var(--showtime)) 1 forwards reverse, gradientFlow 3s linear infinite;
	}
	@keyframes fade-in {
		0% {
			opacity: 0;
		}
		100% {
			opacity: 1;
		}
	}
	@keyframes fancy-rotate-in {
		0% {
			transform: translate(-50%, 0) scale(0) rotate(180deg) rotateX(270deg);
			opacity: 1;
		}
		100% {
			transform: translate(-50%) scale(1);
			opacity: 1;
		}
	}
	@keyframes rotate-in {
		0% {
			transform: translate(-50%, 0) rotateX(270deg);
			opacity: 1;
		}
		100% {
			transform: translate(-50%);
			opacity: 1;
		}
	}
	@keyframes grow-in {
		0% {
			transform: translate(-50%, 0) scale(0);
			opacity: 1;
		}
		100% {
			transform: translate(-50%) scale(1);
			opacity: 1;
		}
	}
	@keyframes slide-in {
		0% {
			transform: translate(-150%, 0) skew(70deg);
			opacity: 1;
			left: 0;
		}
		100% {
			left: 50%;
			transform: translate(-50%);
			opacity: 1;
		}
	}
</style>
