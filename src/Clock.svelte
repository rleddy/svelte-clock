<script>
	import { onMount } from 'svelte';

	export let timezone

	let time = new Date();

	let night_or_day = '#000'

	// these automatically update when `time`
	// changes, because of the `$:` prefix
	let tz_str = ""
	let hr_update = 0
	$: {
		tz_str = time.toLocaleTimeString("en-US", { timeZone: timezone })
		let tparts = tz_str.split(':')
		hr_update = parseInt(tparts[0])

		let rest = tparts[2].split(' ')
		let night_n_day = rest[1].trim()

		if ( night_n_day === "PM" ) {
			night_or_day = '#000'
		} else {
			night_or_day = '#E96'
		}

	}
	

	$: hours = hr_update // time.getHours();
	$: minutes = time.getMinutes();
	$: seconds = time.getSeconds();

	onMount(() => {
		const interval = setInterval(() => {
			time = new Date();
		}, 1000);

		return () => {
			clearInterval(interval);
		};
	});
</script>

<svg  viewBox='-50 -50 100 100'>
	<circle class='clock-face' r='48'  stroke="{night_or_day}"/>

	<!-- markers -->
	{#each [0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55] as minute}
		<line
			class='major'
			y1='35'
			y2='45'
			transform='rotate({30 * minute})'
		/>

		{#each [1, 2, 3, 4] as offset}
			<line
				class='minor'
				y1='42'
				y2='45'
				transform='rotate({6 * (minute + offset)})'
			/>
		{/each}
	{/each}

	<!-- hour hand -->
	<line
		class='hour'
		y1='2'
		y2='-20'
		transform='rotate({30 * hours + minutes / 2})'
	/>

	<!-- minute hand -->
	<line
		class='minute'
		y1='4'
		y2='-30'
		transform='rotate({6 * minutes + seconds / 10})'
	/>

	<!-- second hand -->
	<g transform='rotate({6 * seconds})'>
		<line class='second' y1='10' y2='-38'/>
		<line class='second-counterweight' y1='10' y2='2'/>
	</g>

</svg>

<style>
	svg {
		width: 100%;
		height: 100%;
	}

	.clock-face {
		fill: white;
		stroke-width: 3;
	}

	.minor {
		stroke: #999;
		stroke-width: 1.5;
	}

	.major {
		stroke: #333;
		stroke-width: 2;
	}

	.hour {
		stroke: #333;
		stroke-width: 4;
	}

	.minute {
		stroke: #666;
		stroke-width: 2;
	}

	.second, .second-counterweight {
		stroke: rgb(180,0,0);
	}

	.second-counterweight {
		stroke-width: 6;
	}
</style>
