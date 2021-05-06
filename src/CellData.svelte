<script>
    import VideoPlayer from 'svelte-video-player';
    import { Col, Container, Row } from 'sveltestrap';
    import { Badge } from 'sveltestrap';
    import { slide } from 'svelte/transition';
    import { Card, CardBody, CardHeader, CardTitle } from 'sveltestrap';
    import { Button } from 'sveltestrap';
    import { Progress } from 'sveltestrap';

    export let agentData = '';
    export let dataInfo;
    export let configInfo;

    let color = "secondary";

    function playVideo(event) {
		alert(event.detail.text);
	}
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
                                            <Progress value={agentData.heuristicsWeightList[i] * 100} />
                                        </span>
                                    {/each}
                                    </div>
                                </CardBody>
                            </Col>
                            <Col lg="8">
                                <CardBody>
                                    <div class="information">
                                        <p>We show the <i>weight</i> each of the behaviours has in this particular agent, driving its motivation and influencing its actions in the game.</p>
                                        <p>Below, we present the stats resulting from this agent after playing the game 100 times. The data shown is the average, serving as reference of what to expect when this agent plays the game.</p>
                                        <p>The video shows a pre-recorded gameplay; download an executable and instructions to run this agent locally in <a href="#">here</a>.</p>
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
                                        </CardBody>
                                    </Container>
                                </Card>
                            </Col>
                            <Col lg="8">
                                <Card>
                                    <Container>
                                        <CardBody>
                                            <VideoPlayer poster={dataInfo["gamePoster"]} source={agentData["videoUrl"]}/>
                                        </CardBody>
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

    .data {
        text-align: right;
    }

    @media only screen and (max-width: 991px) {
        .data {
            text-align: center;
        }
    }
</style>