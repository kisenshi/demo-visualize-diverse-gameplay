<script>
    import { Col, Container, Row } from 'sveltestrap';
    import { Badge } from 'sveltestrap';
    import { slide } from 'svelte/transition';
    import { Card, CardBody, CardHeader, CardTitle } from 'sveltestrap';
    import { Progress } from 'sveltestrap';
    import { Table } from 'sveltestrap';

    import GameplayVideo from './GameplayVideo.svelte';

    export let agentData;
    export let dataInfo;
    export let configInfo;
    export let loadingVideo;
    export let videoAvailable;
    export let videoSrc;

    let color = "secondary";

    function isFeature(feature) {
        return ((feature == dataInfo["featureX"])||(feature == dataInfo["featureY"]));
    }

    let openDownloadDetails = false;
	const toggleDownload = () => openDownloadDetails = !openDownloadDetails;

    let performanceColor;
    var performanceWindow = dataInfo["performanceMax"] - dataInfo["performanceMin"];
    var performanceWindowSize = performanceWindow/3;
    $: if (agentData.gameStats.gameOverTickStats.mean < (dataInfo["performanceMin"] + performanceWindowSize)){
        performanceColor = "success";
        openDownloadDetails = false;
    } else if (agentData.gameStats.gameOverTickStats.mean < (dataInfo["performanceMin"] + 2*performanceWindowSize)){
        performanceColor = "warning";
        openDownloadDetails = false;
    } else {
        performanceColor = "danger";
        openDownloadDetails = false;
    }

    $: teamMemberConfig = {
        game: dataInfo['gameName'],
        level: dataInfo['level'],
        behaviours: dataInfo['behaviours'],
        weights: agentData['heuristicsWeightList']
    }

    $: jsonData = "text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(teamMemberConfig,null,'\t'));
</script>

{#if agentData}
    <div in:slide>
        <hr/>
        <h2>Details of agent in cell [{agentData.cellX}, {agentData.cellY}]</h2>
        <Container>
            <Card>
                <Container>
                    <Row>
                        <Col lg="4">
                            <CardBody>
                                <div class="information">
                                    {#each dataInfo['behaviours'] as behaviour, i}
                                    <span>
                                        <Badge color="info">{configInfo["behaviours"][behaviour]["name"]}</Badge>
                                        <Progress color="info" value={agentData.heuristicsWeightList[i] * 100} />
                                    </span>
                                {/each}
                                </div>
                            </CardBody>
                        </Col>
                        <Col lg="8">
                            <CardBody>
                                <div class="information">
                                    <p>We show the <i>weight</i> each of the behaviours has in this particular agent, driving its motivation and influencing its actions in the game.</p>
                                    <p>
                                        We present the stats resulting from this agent after playing the game 100 times. 
                                    We present the stats resulting from this agent after playing the game 100 times. 
                                        We present the stats resulting from this agent after playing the game 100 times. 
                                        The data shown is the average, serving as reference of what to expect when this agent plays the game.
                                        The stat corresponding to the performance is displayed in 3 colours: green, yellow or red, depending on its value compared to the rest of agents generated for the selected features.
                                        The results corresponding to the features conforming the map are highlighted in blue.
                                    </p>
                                    <p>The video, if available, shows a pre-recorded gameplay. Download an executable and instructions to run this agent locally below.</p>
                                </div>
                            </CardBody>
                        </Col>
                    </Row>
                </Container>
            </Card>
        </Container>
        <Container>
            <Card>
                <Container>
                    <Row>
                        <Col lg="4">
                            <Card>
                                <Container>
                                    <CardBody>
                                        <Table>
                                            <thead>
                                                <tr>
                                                <th>Stats</th>
                                                <th>Result</th>
                                                </tr>
                                            </thead>
                                            <tbody>
                                                <tr>
                                                <td class="statCell">Game over ts</td>
                                                <td><Badge color="{performanceColor}">{(agentData.gameStats.gameOverTickStats.mean).toFixed(2)}</Badge></td>
                                                </tr>
                                                <tr>
                                                <td class="statCell">Wins</td>
                                                <td><Badge color="{isFeature("WINS") ? "primary" : color}">{(agentData.gameStats.winStats.mean * 100).toFixed(2)}%</Badge></td>
                                                </tr>
                                                <tr>
                                                <td class="statCell">Score</td>
                                                <td><Badge color="{isFeature("SCORE") ? "primary" : color}">{(agentData.gameStats.scoreStats.mean).toFixed(2)}</Badge></td>
                                                </tr>
                                                <tr>
                                                <td class="statCell">% Explored</td>
                                                <td><Badge color="{isFeature("EXPLORATION_PERCENTAGE") ? "primary" : color}">{(agentData.gameStats.percentageExploredStats.mean * 100).toFixed(2)}</Badge></td>
                                                </tr>
                                                <tr>
                                                <td class="statCell">Unique interactions</td>
                                                <td><Badge color="{isFeature("SPRITES_INTERACTION") ? "primary" : color}">{(agentData.gameStats.nUniqueSpriteInteractionsStats.mean).toFixed(2)}</Badge></td>
                                                </tr>
                                                <tr>
                                                <td class="statCell">Curiosity</td>
                                                <td><Badge color="{isFeature("CURIOSITY") ? "primary" : color}">{(agentData.gameStats.nCuriosityInteractionsStats.mean).toFixed(2)}</Badge></td>
                                                </tr>
                                                <tr>
                                                <td class="statCell">Total interactions</td>
                                                <td><Badge color="{isFeature("INTERACTIONS") ? "primary" : color}">{(agentData.gameStats.nTotalInteractionsStats.mean).toFixed(2)}</Badge></td>
                                                </tr>
                                                <tr>
                                                <td class="statCell">Collisions</td>
                                                <td><Badge color="{isFeature("COLLISIONS") ? "primary" : color}">{(agentData.gameStats.nUniqueSpriteInteractionsStats.mean).toFixed(2)}</Badge></td>
                                                </tr>
                                                <tr>
                                                <td class="statCell">Hits</td>
                                                <td><Badge color="{isFeature("HITS") ? "primary" : color}">{(agentData.gameStats.nTotalHitsStats.mean).toFixed(2)}</Badge></td>
                                                </tr>
                                                <tr>
                                                <td class="statCell">Kills</td>
                                                <td><Badge color="{isFeature("KILLS") ? "primary" : color}">{(agentData.gameStats.nTotalKillsStats.mean).toFixed(2)}</Badge></td>
                                                </tr>
                                                <tr>
                                                <td class="statCell">Items collected</td>
                                                <td><Badge color="{isFeature("ITEMS") ? "primary" : color}">{(agentData.gameStats.nTotalItemsCollectedStats.mean).toFixed(2)}</Badge></td>
                                                </tr>
                                            </tbody>
                                        </Table>
                                    </CardBody>
                                </Container>
                            </Card>
                        </Col>
                        <Col lg="8">
                            <Card>
                                <Container>
                                    <CardBody>
                                        <GameplayVideo {loadingVideo} {videoAvailable} {videoSrc} videoPoster={dataInfo['gamePoster']}></GameplayVideo>
                                    </CardBody>
                                </Container>
                            </Card>
                            <Card>
                                <Container>
                                    <button class="accordion" on:click={toggleDownload} aria-expanded={openDownloadDetails}>
                                        <svg style="tran"  width="20" height="20" fill="none" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" viewBox="0 0 24 24" stroke="currentColor">
                                            <path d="M9 5l7 7-7 7"></path>
                                        </svg> Download and run locally
                                    </button>
                                    {#if openDownloadDetails}
                                        <div transition:slide={{ duration: 300 }} class="information">
                                            <CardBody>
                                                <p>To see for yourself how this agent behaves, you can download the following files and run it locally:</p>
                                                <ul>
                                                    <li>
                                                        <a href="download/automatedGameplay.zip">automatedGameplay.zip</a>. Contains the instructions
                                                        and necessary files to run the automated gameplay. You only need to download this 
                                                    and necessary files to run the automated gameplay. You only need to download this 
                                                        and necessary files to run the automated gameplay. You only need to download this 
                                                        once.
                                                    </li>
                                                    <li>
                                                        <a href="data:{jsonData}" download="automatedGameplayConfig.json">Config file</a>. Contains the description of the agent and information about the game and level
                                                        to play. Alternatively, you can copy and paste the folloein json data in the <i>automatedGameplayConfig.json</i> provided by <i>automatedGameplay.zip</i>. 
                                                    to play. Alternatively, you can copy and paste the folloein json data in the <i>automatedGameplayConfig.json</i> provided by <i>automatedGameplay.zip</i>. 
                                                        to play. Alternatively, you can copy and paste the folloein json data in the <i>automatedGameplayConfig.json</i> provided by <i>automatedGameplay.zip</i>. 
                                                    </li>
                                                </ul>
                                                <p></p>
                                                <pre>{JSON.stringify(teamMemberConfig,null,'\t')}</pre>
                                            </CardBody>
                                        </div>
                                    {/if}
                                </Container>
                            </Card>
                        </Col>
                    </Row>
                </Container>
            </Card>
        </Container>
    </div>
{/if}


<style>
    span {
        display: block;
        padding: 5px;
    }

    .information {
        text-align: left;
    }

    .statCell {
        text-align: left;
    }

    @media only screen and (max-width: 991px) {
        .statCell {
            text-align: center;
        }
    }

	.accordion {
        border: none; 
        background: none;
        display:block; 
        color: inherit; 
        cursor: pointer; 
        margin: 0; 
        padding-bottom: 0.5em; 
        padding-top: 0.5em
    }

	svg { 
        transition: transform 0.2s ease-in;
	}
	
	[aria-expanded=true] svg { transform: rotate(0.25turn); }
</style>