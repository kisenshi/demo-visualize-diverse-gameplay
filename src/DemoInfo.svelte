<script>
    import Toggle from './ToggleLogic/Toggle.svelte';
    import { Container } from 'sveltestrap';
    import { Table } from 'sveltestrap';
    import { Jumbotron } from 'sveltestrap';

    export let configInfo = {};

    let availableBehaviours = ["WINNER", "EXPLORER", "CURIOUS", "KILLER", "COLLECTOR"];
    let availableGames = ["BUTTERFLIES", "ZELDA", "DIGDUG"];
    let availableFeatures = ["EXPLORATION_PERCENTAGE", "SCORE", "WINS", "CURIOSITY", "INTERACTIONS", "COLLISIONS", "KILLS", "ITEMS"];
</script>

<h1>Demo</h1>

<Jumbotron class="p-3">
    <h2>Visualize Diverse Gameplays Based on Agent Behaviour</h2>
    <h3><a href="https://scholar.google.com/citations?user=06wiSCoAAAAJ" target="_blank">Cristina Guerrero-Romero</a></h3>
    <hr/>
    <p> 
        The work presented in this Demo is related to my PhD research. 
    <a href="http://kisenshi.github.io/files/map-elites-generation-team-agents-behaviour.pdf" target="_blank">
        MAP-Elites to Generate a Team of Agents that Elicits Diverse Automated Gameplay</a> describes 
    in detail the motivation and experiments carried out to generate the data presented in this demo.
    This paper will be published in the Proceedings of the <a href="https://ieee-cog.org/2021/index.html"
    target="_blank">3rd IEEE Conference on Games (CoG)</a> in August 2021. 
    </p>
</Jumbotron>
<p>
    This is an interactive tool that allows to visualize the gameplays of agents 
    with different motivations and goals in a same game, by choosing a range 
    of expected stats (features) for each of them. These features define the space
    where a series of agents are generated, which we call <i>a team</i>.
</p>
<p>
    Each member of the team has different ways to interact with the game, given by 
    the available behaviours and the weight assigned to each of them. The combination
    of these behaviours drives the actions of the agent in the game and have an effect
    on the resulting stats obtained, in terms of score, item collection, exploration of 
    the map, enemies killed, etc.
</p>
<p>
    This demo shows the diversity of the team of agents generated for each game and
    pair of features, eliciting a diverse automated gameplay. It is possible to see
    details about the agents generated, play a pre-recorded gameplay
    of each of them and download an standalone and instructions to run the agents locally.
</p>
<Toggle title="Behaviours">
    <p>
        Five different behaviours (heuristics) have been identified and implemented for the agents. The
        agent is defined by the <i>weight</i> assigned to each of these behaviours, driving its actions
        and reactions to the game. Not all of the behaviours are enabled in all games. For more details
        about the motivation, implementation and usage of these behaviours, refer to the paper linked
        above.
    </p>
    <Table responsive>
        <thead>
            <tr>
                <th>Behaviour</th>
                <th>Description</th>
            </tr>
        </thead>
        <tbody class="info-data-table">
            {#each availableBehaviours as behaviour}
                <tr>
                    <td>{configInfo.behaviours[behaviour].name}</td>
                    <td>{configInfo.behaviours[behaviour].description}</td>
                </tr>
            {/each}
            <tr><td></td><td></td></tr>
        </tbody>
    </Table>
</Toggle><br/>
<h2>Instructions</h2>
<ol>
    <li>
        Go to <i>Choose Data</i> to <b>select the game and pair of features</b> to see details about the <b>team 
        of agents</b> generated for them.
        <Container>
            <Toggle title="Games">
               <p>
                   We have generated agents with distinct behaviours for three games supported by the GVGAI Framework. Refer to the paper linked above for more details.
               </p>
                <Table responsive>
                    <thead>
                        <tr>
                            <th>Game</th>
                            <th>Description</th>
                            <th>Level screenshot</th>
                        </tr>
                    </thead>
                    <tbody class="info-data-table">
                        {#each availableGames as game}
                            <tr>
                                <td>{configInfo.games[game].name}</td>
                                <td width="50%">{configInfo.games[game].description}</td>
                                <td class="lvl-screenshot"><img src={configInfo.games[game].screenshot} alt="{configInfo.games[game].name} level screenshot"/></td>
                            </tr>
                        {/each}
                        <tr><td></td><td></td><td></td></tr>
                    </tbody>
                </Table>
                
            </Toggle>
            <Toggle title="Features">
                <p>
                    There are a series of features to choose from that define the space of the agents generated.
                    These features are related to the results of the actions of the agents in the game and are 
                    defined by the stats obtained after playing the game several times. These values help 
                    to understand what to expect from each agent during its gameplay.
                </p>
                <Table responsive>
                    <thead>
                        <tr>
                            <th>Feature</th>
                            <th>Description</th>
                        </tr>
                    </thead>
                    <tbody class="info-data-table">
                        {#each availableFeatures as feature}
                            <tr>
                                <td>{configInfo.features[feature].name}</td>
                                <td>{configInfo.features[feature].description}</td>
                            </tr>
                        {/each}
                        <tr><td></td><td></td></tr>
                    </tbody>
                </Table>
            </Toggle>
        </Container>
    </li><br/>
    <li>
        Go to <i>Team Info</i> to <b>visualize the data generated</b> for the game and pair of features selected.
        The information displayed is as follows:
        <ul>
            <li>
                <b>Overview</b> of the game, enabled behaviours for the agents and selected features.
            </li>
            <li>
                <b>Heatmap (team of agents)</b>. Each cell describes an agent generated for the selected features. 
                The axis represent the average of resulting value for each of the features for that 
                agent after playing the game a total of 100 times.
                The colour assigned to the cell is the performance of the agent, used to find the 
                <i>best</i> agent for each pair of features (red -> green). The criteria used for the 
                performance is the end of game time, considering <i>better</i> and agent that reaches 
                the "game over" state earlier than another one, while ending up with a value for the 
                features in a similar range.
            </li>
        </ul>
    </li><br/>
    <li>
        Choose agent to <b>see details</b>. Click on one of the cells to display details about the agent.
        <ul>
            <li>
                <b>Weight assigned to each behaviour</b>. The weight and combination of the behaviours
                describes the motivations of the agent, driving and influencing its actions in the game.
            </li>
            <li>
                <b>Stats</b>. Resulting after playing the game 100 times. The data shown is the average, 
                serving as reference of what to expect when the agent plays the game.
            </li>
        </ul>
    </li><br/>
    <li>
        See <b>pre-recorded gameplay</b> video or <b>download</b> and run the agent locally.
        <ul>
            <li>
                <b>Gameplay</b>. Each agent behaves and interacts with the same game in different ways, 
                based on its motivations. A pre-recorded video of each agent playing the game is available
                to witness its behaviour and impact on the game.
            </li>
            <li>
                <b>Run the agent locally.</b> You can download a zip containing the files needed to
                trigger an automated gameplay of a game of the GVGAI Framework. We generate 
                a json for each agent that serves as its configuration and allows running it. It is 
                possible to download this json file as well or just copying and pasting in into the
                config file provided. The zip contains the instructions to execute the code.
            </li>
        </ul>
    </li>
  </ol>

<style>
    img {
		width: 100%;
	}

    th {
        text-align: center;
    }

   .info-data-table td {
       vertical-align: middle;
   }
</style>