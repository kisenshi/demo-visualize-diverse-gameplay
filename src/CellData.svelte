<script>
    import VideoPlayer from 'svelte-video-player';
    import { Col, Container, Row } from 'sveltestrap';
    import { Badge } from 'sveltestrap';
    import { slide } from 'svelte/transition';
    import { Card, CardBody, CardHeader, CardTitle } from 'sveltestrap';
    import { Button } from 'sveltestrap';
    import { Progress } from 'sveltestrap';
    import { Table } from 'sveltestrap';

    export let agentData = '';
    export let dataInfo;
    export let configInfo;

    let color = "secondary";

    function playVideo(event) {
		alert(event.detail.text);
	}

    function isFeature(feature) {
        return ((feature == dataInfo["featureX"])||(feature == dataInfo["featureY"]));
    }
</script>

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
                                    Below, we present the stats resulting from this agent after playing the game 100 times. 
                                    The data shown is the average, serving as reference of what to expect when this agent plays the game.
                                    The results corresponding to the features conforming the map this agent was generated from are highlighted.
                                </p>
                                <p>The video, if available, shows a pre-recorded gameplay. Download an executable and instructions to run this agent locally in <a href="#">here</a>.</p>
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
                                            <td><Badge {color}>{(agentData.gameStats.gameOverTickStats.mean).toFixed(2)}</Badge></td>
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
                                    <!--<div class="data">
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
                                    </div>-->
                                </CardBody>
                            </Container>
                        </Card>
                    </Col>
                    <Col lg="8">
                        <Card>
                            <Container>
                                <CardBody>
                                    <VideoPlayer poster={dataInfo["gamePoster"]} source={agentData["videoUrl"]} color="#65a7ff"/>
                                </CardBody>
                            </Container>
                        </Card>
                    </Col>
                </Row>
            </Container>
        </Card>
    </Container>
</div>


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
</style>