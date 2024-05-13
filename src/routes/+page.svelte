<script>
	import Button from '$lib/Button.svelte';
	import DateInput from '$lib/DateInput.svelte';
	import { onMount } from 'svelte';

	const days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];

	let dayColors = new Array(7);

	let start = new Date('2024');
	let end = new Date(new Date('2025').getTime() - 1);

	/** @type {number | undefined} */
	let timer;
	/** @type {number} */
	let timerValue = 0;
	let showTimer = true;

	let questionDate = new Date();

	$: formattedDate = questionDate.toLocaleDateString('en', {
		year: 'numeric',
		month: 'long',
		day: 'numeric'
	});

	onMount(() => {
		Reset();
	});

	function Reset() {
		// Generate a new date
		let startTime = start.getTime();
		let endTime = end.getTime();
		questionDate = new Date(startTime + Math.random() * (endTime - startTime));

		// Reset everything else
		dayColors = new Array(7);

		clearInterval(timer);
		let started = Date.now();
		timer = setInterval(() => {
			timerValue = Math.floor((Date.now() - started) / 1000);
		}, 1000);
		timerValue = 0;
	}

	function answer(day) {
		const idx = days.indexOf(day);
		if (questionDate.getDay() === idx) {
			dayColors[idx] = 'bg-green-500';
			clearInterval(timer);
			timer = undefined;
		} else {
			dayColors[idx] = 'bg-red-500';
		}
	}
</script>

<h1 class="text-2xl font-bold">Doomsday Practice</h1>

<div class="flex flex-col items-center">
	<p class="m-4">
		Find the weekday for<br />
		<strong>{formattedDate}</strong>
	</p>

	<div class="flex flex-row flex-wrap justify-center">
		{#each days as day, i}
			<Button on:click={() => answer(day)} disabled={!timer} class={dayColors[i]}>{day}</Button>
		{/each}
	</div>

	{#if showTimer}
		<p>{timerValue}s</p>
	{/if}

	<Button on:click={Reset} class="m-4 {timer ? 'bg-orange-200' : 'bg-orange-200'}">New Date</Button>

	<hr class="outline-1 border-black w-full m-2" />
</div>

<div class="m-2">
	<p>Options:</p>

	<DateInput bind:date={start}>Start:</DateInput>
	<DateInput bind:date={end}>End:</DateInput>

	<br />

	<label for="timer">
		Timer
		<input type="checkbox" bind:checked={showTimer} />
	</label>
</div>

<div class="m-2">
	<p>Quick Presets:</p>
	<Button
		on:click={() => {
			start = new Date(new Date().getFullYear(), 0, 1);
			end = new Date(new Date().getFullYear(), 11, 31);
		}}>This year</Button
	>
	<Button
		on:click={() => {
			// TODO: fix me in 86 years
			start = new Date('2000');
			end = new Date('2100');
		}}>This century</Button
	>
	<Button
		on:click={() => {
			start = new Date('1700');
			end = new Date('2200');
		}}>1700 - 2200</Button
	>
	<Button
		on:click={() => {
			start = new Date('1582');
			end = new Date();
		}}>The Gregorian Calendar</Button
	>
</div>
