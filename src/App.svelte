<!-- 
	This is the skeleton code provided by Prof. Minsuk Kahng.
	Please feel free to revise the existing code.
-->
<script>
	import { onMount } from "svelte";
	import { scaleLinear } from "d3-scale";

	let instances;

	const numClasses = 10;
	const numBins = 10;
	let binsByClasses = [];

	onMount(async () => {
		const fetched = await fetch("static/prediction_results.json");
		instances = (await fetched.json()).test_instances;
		console.log(instances);

		// Transform data
		for (let k = 0; k < numClasses; ++k) {
			let binsForClass = [];
			for (let b = 0; b < numBins; ++b) {
				binsForClass.push({
					"class": k,
					"binNo": b,
					"instances": instances.filter(instance =>
						k == instance.true_label &&
						b == Math.min(Math.floor(instance.predicted_scores[instance.true_label] * numBins), numBins - 1)
					)});
			}
			binsByClasses.push({"class": k, "bins": binsForClass});
		}
		console.log(binsByClasses);
		

	});

	

</script>

<main>
	<h1>Visual Analytics HW 3</h1>

	<div id="container">
		<div id="sidebar" style="width: 450px;">
			<div id="projection-view" class="view-panel">
				<div class="view-title">Projection View</div>
				<svg >
					

				</svg>
			</div>
			<div id="selected-image-view" class="view-panel">
				<div class="view-title">Selected Image</div>
				<div id="selected-image-view-content">


				</div>
			</div>
		</div>

		<div id="main-section" style="width: 1000px;">
			<div id="score-distributions-view" class="view-panel">
				<div class="view-title">Score Distributions</div>
				<svg >
					{#if instances !== undefined}
						{#each binsByClasses as binsForClass}
							<g transform="translate(0, {binsForClass.class * 20 + 15})">
								<text>{binsForClass.class}</text>

								{#each binsForClass.bins as bin}
									<g transform="translate({bin.binNo * 25 + 50}, 0)">
										<text>{bin.instances.length}</text>
									</g>
								{/each}
							</g>
						{/each}
					{/if}
				</svg>
			</div>
		</div>
	</div>
</main>

<style>
	h1 {
		font-size: 1.3rem;
		font-weight: 500;
		margin-top: 0;
	}
	#container {
		display: flex;
	}
	#sidebar, #main-section {
		display: flex;
		flex-direction: column;
	}
	.view-panel {
		border: 2px solid #eee;
		margin-bottom: 15px;
		margin-right: 15px;
	}
	.view-title {
		background-color: #f3f3f3;
		font-size: 1.0rem;
		margin-bottom: 8px;
		padding: 3px 8px 5px 12px;
	}
	#selected-image-view-content {
		height: 100px;
		padding: 15px;
	}
	svg {
		margin: 5px;
	}



</style>