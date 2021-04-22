<svelte:head>
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
</svelte:head>

<script>
	import { Tabs, TabList, TabPanel, Tab } from './TabLogic/tabs.js';
	import * as animateScroll from "svelte-scrollto";

	import Heatmap from './Heatmap.svelte';
	import CellData from './CellData.svelte';

	let jsonFileName = '';

	let gameName = '';
	let experimentId = '';

	let featureX = 'Non selected';
	let featureY = 'Non selected';

	let matrix;
	let xLabels;
	let yLabels;

	let agentStats;
	let videoUrl;
	let gamePoster = './img/demo.png';

	function loadMatrixData() {
		experimentId = '';
		/*
		return fetch('/json/test.json')
		.then(response => response.json());*/
		if (!jsonFileName) {
			console.log('not chosen')
			return;
		}

		let fetchFile = '/json/' + jsonFileName + '.json';
		console.log(fetchFile);

		fetch(fetchFile)
			.then((response) => response.json())
			.then((jsonData) => {
				gameName = jsonData.config.frameworkConfig.game;

				featureX = jsonData.config.mapElitesConfig.featureX;
				featureY = jsonData.config.mapElitesConfig.featureY;

				xLabels = jsonData.featuresDetails[featureX].bucketsRangeInfo;
				yLabels = jsonData.featuresDetails[featureY].bucketsRangeInfo;

				console.log(xLabels);
				console.log(yLabels);

				matrix = new Array(yLabels.length).fill(null).map(() => new Array(xLabels.length).fill(null));
				var mapElites = jsonData.result.mapElites;
				var occupiedCells = jsonData.result.occupiedCellsIdx;
				
				occupiedCells.forEach(agentData => {
					var x = agentData['x']
					var y = agentData['y']
					matrix[y][x] = mapElites[x][y].performance * (-1);
				});
			});
  	}

	const getCellInfo = (cellIdxFeatureX, cellIdxFeatureY) => {
		agentStats = "Feature X id: " + cellIdxFeatureX + " Feature Y id: " + cellIdxFeatureY;
		gamePoster = './img/demo.png';
		videoUrl = './video/demo.mp4';	

		animateScroll.scrollTo({element: '#agentData'});	
	}
</script>

<main>
	<Tabs>
		<TabList>
			<Tab>Welcome!</Tab>
			<Tab>Choose data</Tab>
			{#if matrix}
				<Tab>Team info</Tab>
			{/if}
		</TabList>
	
		<TabPanel>
			<div class="container info">
				<h1>Demo</h1>
				<h2>Visualize Diverse Gameplays Based on Agent Behaviour</h2>
				<h3>Cristina Guerrero-Romero, Jeremy Gow (Demo supervisor)</h3>
				<hr/>
				<p>
					Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum
				</p>
				<p>
					Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit, sed quia consequuntur magni dolores eos qui ratione voluptatem sequi nesciunt. Neque porro quisquam est, qui dolorem ipsum quia dolor sit amet, consectetur, adipisci velit, sed quia non numquam eius modi tempora incidunt ut labore et dolore magnam aliquam quaerat voluptatem. Ut enim ad minima veniam, quis nostrum exercitationem ullam corporis suscipit laboriosam, nisi ut aliquid ex ea commodi consequatur? Quis autem vel eum iure reprehenderit qui in ea voluptate velit esse quam nihil molestiae consequatur, vel illum qui dolorem eum fugiat quo voluptas nulla pariatur?
				</p>
				<p>
					At vero eos et accusamus et iusto odio dignissimos ducimus qui blanditiis praesentium voluptatum deleniti atque corrupti quos dolores et quas molestias excepturi sint occaecati cupiditate non provident, similique sunt in culpa qui officia deserunt mollitia animi, id est laborum et dolorum fuga. Et harum quidem rerum facilis est et expedita distinctio. Nam libero tempore, cum soluta nobis est eligendi optio cumque nihil impedit quo minus id quod maxime placeat facere possimus, omnis voluptas assumenda est, omnis dolor repellendus. Temporibus autem quibusdam et aut officiis debitis aut rerum necessitatibus saepe eveniet ut et voluptates repudiandae sint et molestiae non recusandae. Itaque earum rerum hic tenetur a sapiente delectus, ut aut reiciendis voluptatibus maiores alias consequatur aut perferendis doloribus asperiores repellat.
				</p>
			</div>
		</TabPanel>
	
		<TabPanel>
			<div class="container">
				<h1>Games</h1>
			
				<select bind:value={jsonFileName}>
					<option value="">----</option>
					<option value="test">Test data 1</option>
					<option value="test2">Test data 2</option>
				</select>

				<button on:click={loadMatrixData}>
					Load data
				</button>
			</div>
		</TabPanel>
	
		{#if matrix}
			<TabPanel>
				<div class="container">
					<h1>{gameName}</h1>
				</div>
				<div class="container">
					<Heatmap {matrix} {featureX} {xLabels} {featureY} {yLabels} {getCellInfo}/>
				</div>
				
					<CellData {agentStats} {videoUrl} {gamePoster}/>
				<div class="container" id="agentData">
				</div>
			</TabPanel>
		{/if}
	</Tabs>	
</main>

<style>
	:global(body) {
    	background-color: #65a7ff !important;
	}

	main {
		text-align: center;
    	color: #333;
    	background-color: #65a7ff;
	}

	.container {
		padding: 10px;
		max-width: 1000px;
		width: 100%;
		margin: 0 auto;
		background-color: #fff;
	}

	.container.info {
		text-align: left;
		padding: 25px;
	}

	h1 {	
		text-transform: uppercase;
		font-size: 2em;
		font-weight: 100;
		color: #65a7ff;
	}

	h2 {
		font-size: 1.5em;
		color: #65a7ff;
		font-weight: 400;
	}

	h3 {
		font-size: 1em;
		color: #a8ceff;
		font-weight: 400;
	}

	hr {
		border-top: 1px solid #65a7ff;
	}
</style>