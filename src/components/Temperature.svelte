<script lang="ts">
	import { temperature } from '~/store';
	import { writable } from 'svelte/store';
	import HelpPopover from './HelpPopover.svelte';

	export let disabled: boolean = false;
	const hoveredIndex = writable(null);

	// TEMPERATURE
	let temperatureIndex = 8; // Default to the value corresponding to 1.0 in the array
	const temperatureArray = [
		// 0.05, 0.1, // error in Math.exp()
		0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0, 2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0, 9.0, 10.0
	];
	const ticks = [0, temperatureArray.length - 1];

	$: temperatureTemp = temperatureArray[temperatureIndex];
	$: temperature.set(temperatureTemp);
</script>

<div class="temperature-slider shrink-0 text-gray-900">
	<div class="slider-container flex w-full flex-col items-end">
		<div class="flex w-full shrink-0 items-center justify-between">
			<div class="temperature-text flex items-center gap-[2px]">
				<div>Temperature</div>
				<HelpPopover id="temperature-help" placement="right">
					{`次のトークンの\n出力確率分布と\nランダム性を\n変更します。`}
				</HelpPopover>
			</div>
			<div class="temperature-value">
				<p>{temperatureTemp}</p>
			</div>
		</div>
		<input
			{disabled}
			class="slider"
			type="range"
			min={0}
			max={temperatureArray.length - 1}
			step={1}
			bind:value={temperatureIndex}
			on:click={(e) => {
				e.preventDefault();
				e.stopPropagation();
			}}
		/>
	</div>
</div>

<style lang="scss">
	.temperature-slider {
		max-width: 400px;
		min-width: 200px;
		width: 10%;
		padding-left: 1rem;
		padding-right: 1rem;
		display: flex;
		flex-direction: row;
		align-items: center;
		gap: 1rem;

		.icon {
			scale: 0.9;
		}
	}

	.temperature-text {
		font-size: 1rem;
		white-space: nowrap;
		flex-shrink: 0;
		font-size: 0.8rem;
		line-height: 0;
		font-weight: 500;
		color: theme('colors.gray.600');
	}

	.temperature-value {
		white-space: nowrap;
		flex-shrink: 0;
		font-size: 0.8rem;
		line-height: 0;
		font-family: monospace;
		color: theme('colors.gray.800');
	}

	.slider-container {
		display: flex;
		flex: 1 0 0;
		width: 100%;
		height: 2rem;
		gap: 0.6rem;
		justify-content: center;
	}

	.slider {
		-webkit-appearance: none; /* Override default CSS styles */
		appearance: none;
		border-radius: 0.25rem;
		width: 100%;
		height: 4px;
		background: theme('colors.gray.300');
		outline: none;
		opacity: 0.7;

		&::-webkit-slider-thumb {
			-webkit-appearance: none; /* Override default look */
			appearance: none;
			width: 12px;
			height: 12px;
			border-radius: 50%;
			border: 1px solid theme('colors.gray.400');
			background: white;
			cursor: pointer;
		}
		&:disabled::-webkit-slider-thumb {
			background: theme('colors.gray.200');
			cursor: not-allowed;
		}
	}
</style>
