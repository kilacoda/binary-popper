<script lang="ts">
	import { browser } from '$app/env';

	function playSound() {
		let popSound = browser ? new Audio('src/pop.mp3') : null;
		popSound.play();
	}
	let poppers = $state(1);
	let counts = $state([
		{
			id: 0,
			value: 0,
			popToggle: 0
		}
	]);
</script>

<p>
	Number of poppers: <button
		type="button"
		onclick={() => {
			if (poppers > 1) {
				poppers--;
				counts.pop();
			}
		}}>-</button
	>
	{poppers}
	<button
		type="button"
		onclick={() => {
			poppers++;
			counts.push({ id: poppers, value: 0, popToggle: 0 });
		}}>+</button
	>
</p>

<div class="pops">
	{#each counts as circ (circ.id)}
		<button
			class="circle"
			style:--pop-id={circ.id}
			style:--pop-toggle={circ.popToggle}
			onclick={() => {
				playSound();
				circ.popToggle = 1 - circ.popToggle;
				// popSound.pause();
			}}
		>
			<span>{circ.popToggle}</span>
		</button>
	{/each}
</div>

<style>
	div {
		--pop-id: -1;
		--pop-toggle: 0;
	}

	.pops {
		display: flex;
		align-items: center;
		justify-content: space-evenly;
		flex-wrap: wrap;

		background-color: orange;
		border: 5px black;
		border-radius: 30px;
		padding: 5px 20px;
		gap: 15px;
	}

	.circle {
		/* background-color: orangered; */
		border-radius: 50%;
		aspect-ratio: 1/1;
		width: 50px;
		height: 50px;

		display: flex;
		justify-content: center;
		align-items: center;

		background-color: if(style(--pop-toggle: 0): orangered; else: pink);
	}
</style>
