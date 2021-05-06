<script>
    import VideoPlayer from 'svelte-video-player';
    import { Col, Container, Row } from 'sveltestrap';
    import { Badge } from 'sveltestrap';
    import { fade, slide, fly } from 'svelte/transition';

    export let agentData = '';
    export let dataInfo;

    let color = "info";

    function playVideo(event) {
		alert(event.detail.text);
	}
</script>
<Container>
    {#if agentData}
        <div in:slide>
            <hr/>
            <div class="information">
                <h2>Agent details</h2>
                <h3>Selected cell [{agentData.cellX}, {agentData.cellY}]</h3>
                <div>
                    Stats resulting from playing the game 100 times; the data shown is the average.<br/>
                    The video shows a pre-recorded gameplay of this agent.
                </div>
            </div>
            <Row>
                <Col lg="4">
                    <div class="data">
                        <span>Game over ts: <Badge {color}>{(agentData.gameStats.gameOverTickStats.mean).toFixed(2)}</Badge></span>
                        <span>Wins: <Badge {color}>{(agentData.gameStats.winStats.mean * 100).toFixed(2)}%</Badge></span>
                        <span>Score: <Badge {color}>{(agentData.gameStats.scoreStats.mean).toFixed(2)}</Badge></span>
                        <span>% Explored: <Badge {color}>{(agentData.gameStats.percentageExploredStats.mean * 100).toFixed(2)}</Badge></span>
                        <span>Unique interactions: <Badge {color}>{(agentData.gameStats.nUniqueSpriteInteractionsStats.mean).toFixed(2)}</Badge></span>
                        <span>Curiosity: <Badge {color}>{(agentData.gameStats.nCuriosityInteractionsStats.mean).toFixed(2)}</Badge></span>
                        <span>Total interactions: <Badge {color}>{(agentData.gameStats.nTotalInteractionsStats.mean).toFixed(2)}</Badge></span>
                        <span>Collisions: <Badge {color}>{(agentData.gameStats.nUniqueSpriteInteractionsStats.mean).toFixed(2)}</Badge></span>
                        <span>Hits: <Badge {color}>{(agentData.gameStats.nTotalHitsStats.mean).toFixed(2)}</Badge></span>
                        <span>Kills: <Badge {color}>{(agentData.gameStats.nTotalKillsStats.mean).toFixed(2)}</Badge></span>
                        <span>Items collected: <Badge {color}>{(agentData.gameStats.nTotalItemsCollectedStats.mean).toFixed(2)}</Badge></span>
                    </div>
                </Col>
                <Col lg="8">
                    <VideoPlayer poster={dataInfo["gamePoster"]} source={agentData["videoUrl"]}/>
                </Col>
            </Row>
        </div>
    {/if}
</Container>

<style>
    span {
        display: block;
        padding: 5px;
    }

    .information {
        margin: 40px;
    }

    .data {
        text-align: right;
    }

    @media only screen and (max-width: 991px) {
        .data {
            text-align: center;
        }
    }
</style>