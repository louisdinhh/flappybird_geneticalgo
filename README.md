![Alt text](https://github.com/louisdinhh/flappybird_geneticalgo/blob/main/photo)

# Flappy Bird AI — Neuroevolution with a Genetic Algorithm

An AI-controlled Flappy Bird simulation built with **Python** and **Pygame**. Instead of manually controlling a bird, the game runs a population of AI agents whose neural-network controllers make decisions based on the bird's position relative to the next pipe.

The agents are evaluated through gameplay and improved over successive generations using evolutionary techniques: **fitness evaluation, speciation, elitism, selection, and mutation**.

> **Implementation note:** The current code evolves neural-network connection weights, but it does not yet add or remove neurons or connections. Each brain currently has a fixed topology: three input neurons, one bias neuron, and one output neuron. The project is therefore a neuroevolution implementation with weight evolution and species-based selection, rather than full topology evolution (such as NEAT).

## Features

- Pygame-based Flappy Bird simulation.
- Population-based AI: 100 birds are evaluated in each generation.
- Neural-network controllers make flap/no-flap decisions.
- Three environmental inputs describe the bird's position relative to the nearest pipe.
- Genetic mutation adjusts neural-network connection weights.
- Species group agents with similar connection weights.
- Champion preservation (elitism) carries the best agent from each species into the next generation.
- Stale species are removed to help allocate the population to improving lineages.

