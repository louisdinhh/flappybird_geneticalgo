<p align="center">
  <img src="https://github.com/louisdinhh/flappybird_geneticalgo/blob/main/photo" alt="Flappy Bird AI" width="400">
</p>

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

## How It Works

### 1. The Game Environment

The simulation runs at 60 frames per second. Pipes move from right to left, and each bird is affected by gravity. A bird is removed from the active population when it collides with a pipe or the ground.

All living birds are updated in parallel. When every bird in the current generation has died, the population is evaluated and a new generation is created.

### 2. Neural Network Inputs

Each bird uses three normalized inputs describing its position relative to the closest pipe:

| Input | Description |
| --- | --- |
| `vision[0]` | Vertical distance from the bird to the bottom edge of the upper pipe |
| `vision[1]` | Horizontal distance from the bird to the closest pipe |
| `vision[2]` | Vertical distance from the bird to the top edge of the lower pipe |

The distances are divided by the game height/scale factor (500) to keep the input values in a manageable range.

### 3. Neural Network Architecture

The current brain is a small feed-forward network:

```text
Upper-pipe clearance ─┐
Horizontal distance ──┼──> Output neuron ──> Flap / Do nothing
Lower-pipe clearance ─┘          ▲
                                 │
                              Bias = 1
```

- **Inputs:** 3 sensory values.
- **Bias:** 1 constant bias node.
- **Output:** 1 neuron with a sigmoid activation function.
- **Decision rule:** The bird flaps when the output is greater than `0.73`; otherwise, it continues falling under gravity.

The initial network connects each input and the bias node to the output neuron. Connection weights are initialized randomly between `-1` and `1`.

### 4. Fitness Evaluation

An agent's fitness is its lifespan, measured in simulation frames:

```python
fitness = lifespan
```

Birds that survive longer receive higher fitness scores. This provides a direct measure of how effectively each neural network navigates the pipes.

### 5. Evolution Across Generations

When the population becomes extinct, the genetic algorithm performs the following steps:

1. **Speciation** — Agents are grouped according to the difference between their neural-network connection weights.
2. **Fitness evaluation** — Each bird's fitness is calculated from its lifespan, and each species' average fitness is updated.
3. **Remove extinct species** — Species without members are discarded.
4. **Remove stale species** — Species that have not improved for eight generations can be removed, while retaining at least one species.
5. **Rank species and agents** — Individuals are sorted by fitness, and species are ranked by their best-performing member.
6. **Preserve champions** — The best individual from each surviving species is cloned into the next generation.
7. **Create offspring** — Additional agents are cloned from members of each species and their brains are mutated.
8. **Start the next generation** — The population is replenished to its configured size and the simulation continues.

### 6. Mutation

Mutation changes the weights of an offspring's neural network. The current implementation applies mutation to all connections when the brain's mutation step is selected (80% probability).

For each connection:

- There is a 10% chance that its weight is replaced with a new random value between `-1` and `1`.
- Otherwise, a small Gaussian perturbation is added to the existing weight.
- The resulting weight is clamped to the range `[-1, 1]`.

This introduces variation while allowing successful agents' learned behaviours to be carried forward.

## Project Structure

```text
flappybird_geneticalgo/
├── brain.py          # Neural-network construction, feed-forward logic, cloning and mutation
├── components.py     # Game environment components: ground and pipes
├── config.py         # Window dimensions and shared game state
├── connection.py     # Neural-network connection weights and weight mutation
├── main.py           # Pygame initialisation and main simulation loop
├── node.py           # Neural-network nodes and sigmoid activation
├── player.py         # Bird physics, pipe sensing, decisions and fitness
├── population.py     # Population management and generation evolution
├── species.py        # Species grouping, fitness ranking and offspring creation
└── photo/            # Project image assets
```

## Requirements

- Python 3
- Pygame

## Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/louisdinhh/flappybird_geneticalgo.git
   cd flappybird_geneticalgo
   ```

2. (Optional) Create and activate a virtual environment:

   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   ```

   On Windows:

   ```powershell
   .venv\Scripts\activate
   ```

3. Install Pygame:

   ```bash
   python -m pip install pygame
   ```

## Run the Simulation

From the project directory, run:

```bash
python main.py
```

The simulation starts automatically with a population of 100 AI-controlled birds. The next generation is created after all birds in the current generation have been eliminated.

Close the Pygame window to exit.

## Configuration

The current settings are defined in the source files:

| Setting | Current value | Location |
| --- | ---: | --- |
| Population size | 100 | `main.py` |
| Window width | 550 px | `config.py` |
| Window height | 720 px | `config.py` |
| Simulation frame rate | 60 FPS | `main.py` |
| Pipe opening | 100 px | `components.py` |
| Output activation threshold | 0.73 | `player.py` |
| Stale-species limit | 8 generations | `population.py` |
| Species similarity threshold | 1.2 | `species.py` |

## Current Scope and Possible Extensions

The current implementation focuses on evolving connection weights while maintaining a fixed network structure. Some possible next steps are:

- Add structural mutations that introduce new neurons or connections.
- Track connection innovations so networks with different structures can be compared reliably.
- Implement crossover between parent networks.
- Experiment with alternative fitness functions that reward both survival and successfully passing pipes.
- Record generation statistics, such as best fitness and average fitness, to visualize learning progress.

These extensions would move the project closer to **NeuroEvolution of Augmenting Topologies (NEAT)**, where both network weights and network structure evolve.

## Concepts Demonstrated

- Neural networks and feed-forward inference
- Genetic algorithms and evolutionary computation
- Fitness-based selection
- Mutation and elitism
- Speciation and diversity preservation
- AI agent interaction with a simulated environment

## License

No license file is currently included in the repository. Add a `LICENSE` file if you intend to distribute the project under a particular open-source license.

