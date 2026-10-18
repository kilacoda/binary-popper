<script lang="ts">
	import { browser } from '$app/env';

	let poppers = $state(1);
	let counts = $state([
		{
			id: 1,
			value: 0,
			popToggle: 0
		}
	]);

	function playSound() {
		let popSound = browser ? new Audio('src/pop.mp3') : null;
		popSound.play();
	}

	function countBinary() {
		let binSum = '';

		counts.forEach((popper) => {
			// binSum += popper.popToggle * 10 ** (popper.id - 1);
			binSum = `${popper.popToggle}` + binSum;
		});

		return binSum;
	}

	function countDecimal() {
		let deciSum = BigInt(0);

		counts.forEach((popper) => {
			deciSum += BigInt(popper.popToggle * 2 ** (popper.id - 1));
		});

		return deciSum;
	}

	function countHexa() {
		let hexaSum = ``;

		for (let i = 0; i < counts.length; i += 4) {
			let block = counts.slice(i, i + 4);

			let blockSum = 0;

			block.forEach((popper) => {
				blockSum += popper.popToggle * 2 ** ((popper.id - 1) % 4);
			});
			// console.log(`From ${i} to ${i + 4}, sum = ${blockSum}`);
			if (blockSum < 10) {
				hexaSum = `${blockSum}` + hexaSum;
			} else {
				switch (blockSum) {
					case 10:
						hexaSum = 'A' + hexaSum;
						break;

					case 11:
						hexaSum = 'B' + hexaSum;
						break;

					case 12:
						hexaSum = 'C' + hexaSum;
						break;

					case 13:
						hexaSum = 'D' + hexaSum;
						break;

					case 14:
						hexaSum = 'E' + hexaSum;
						break;

					case 15:
						hexaSum = 'F' + hexaSum;
						break;

					default:
						break;
				}
			}
		}

		return hexaSum;
	}
</script>

<div class="text">
	<h1>Binary Poppers!</h1>
	<br>
	<p>
		Number of poppers:
		<input
			type="number"
			defaultValue="1"
			min="1"
			max="1024"
			oninput={() => {
				if (this.value === '') {
					return;
				}

				if (this.value >= 1024) {
					this.value = 1024;
				}
				let old_poppers = poppers;
				poppers = this.value;

				if (poppers > counts.length) {
					for (let i = Number(old_poppers) + 1; i <= poppers; i++) {
						counts.push({
							id: i,
							value: 0,
							popToggle: 0
						});
					}
				} else if (poppers <= counts.length) {
					for (let i = old_poppers; i > poppers + 1; i--) {
						counts.pop();
					}
				}
			}}
		/>
	</p>
</div>

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
			<p>{circ.popToggle}</p>
		</button>
	{/each}
</div>
<br>
<div class="text" id="content">
	<div class="calculate">
		<div>
			<b>Binary:</b>
			<p class="number">
				{countBinary()}
			</p>
		</div>
		<div>
			<b>Decimal:</b>
			<p class="number">{countDecimal()}</p>
		</div>
		<div>
			<b>Hexadecimal:</b>
			<p class="number">{countHexa()}</p>
		</div>
	</div>
	<br>
	<button
		type="button"
		onclick={() => {
			poppers = 1;
			counts = [
				{
					id: 1,
					value: 0,
					popToggle: 0
				}
			];
		}}
		class="button"
	>
		Reset
	</button>
</div>

<style>
	* {
		font-family: Arial, Helvetica, sans-serif;
		margin: 0 auto;
		padding: 0 auto;
		box-sizing: border-box;
	}
	div {
		--pop-id: -1;
		--pop-toggle: 0;
	}

	body {
		display: flex;
		justify-content: center;
		align-items: center;
	}
	.pops {
		display: flex;
		flex-direction: row-reverse;
		align-items: center;
		justify-content: space-evenly;
		flex-wrap: wrap-reverse;

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

		background-image: if(
			style(--pop-toggle: 0): radial-gradient(white 1%, orange 99%) ;
				else: radial-gradient(gray, orange)
		);
	}

	.text {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		text-align: center;
		text-wrap: wrap;
		margin-bottom: 30px;
	}

	.calculate {
		display: flex;
		justify-content: center;
		align-items: center;
		gap: 10px;
	}

	.number {
		text-wrap: wrap;
		overflow-wrap: break-word;
		word-break: break-all;
	}

	.button {
		padding: 10px 40px;
		border-radius: 10px;
		border: 1px solid black;
	}

	.button:hover {
		background: #e8e7ea;
	}

	.button:active {
		transform: translateY(1px);
	}


</style>
