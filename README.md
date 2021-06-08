# Demo: Visualize Diverse Gameplays Based on Agent Behaviour

## Overview 

This is an interactive tool that allows to visualize the gameplays of agents with 
different motivations and goals in a same game, by choosing a range of expected stats (features) for each of them. These features define the space where a series of agents are generated, which we call _a team_.

Each member of the team has different ways to interact with the game, given by 
the available behaviours and the weight assigned to each of them. The combination
of these behaviours drives the actions of the agent in the game and have an effect
on the resulting stats obtained, in terms of score, item collection, exploration of 
the map, enemies killed, etc.

This demo shows the diversity of the team of agents generated for each game and
pair of features, eliciting a diverse automated gameplay. It is possible to see
details about the agents generated, play a pre-recorded gameplay
of each of them and download an standalone and instructions to run the agents locally.

## Demo

The demo is live at: http://team-automated-gameplay-visualization.surge.sh

## Run locally

You need to have [Node.js](https://nodejs.org) installed. Go to the location of the project and 
execute the following commands:


```bash
npm install # Install the dependencies (only required once)
npm run dev # The app runs at http://localhost:5000/
```

Visit [Svelte](https://svelte.dev) for detailed information.

## Context

The work presented in this Demo is related to my PhD research. [MAP-Elites to Generate a Team of Agents that Elicits Diverse Automated Gameplay](http://kisenshi.github.io/files/map-elites-generation-team-agents-behaviour.pdf) describes in detail the motivation and experiments carried out to generate the data presented in this demo. This paper will be published in the Proceedings of the [3rd IEEE Conference on Games (CoG)](https://ieee-cog.org/2021/index.html) in August 2021.

## Credits

This project has been implemented using [Svelte](https://svelte.dev), [Sveltestrap](https://sveltestrap.js.org/v4/?path=/story/components--get-started) and [Plotly JS](https://plotly.com/javascript/). It uses the package [svelte-scrollto](https://www.npmjs.com/package/svelte-scrollto)
and the [Tab logic](https://svelte.dev/repl/8e68120858e5322272dc9136c4bb79cc?version=3.5.1) implemented by Rich Harris.





