Elijah Yoo, Ethan Wong

This lab is implemented in Python using the notebook Lab4.ipynb. To open the file, run jupyter notebook from your terminal and use the web interface to navigate to the file.

The notebook contains seven code cells, which should be executed in order for the system to work properly.

Code Cell Descriptions
Code Cell 1: Import Dependencies
This cell loads all external libraries used by the program. These include tools for regular expressions and randomness, which are essential for handling pattern matching and probabilistic decisions.

Code Cell 2: Initialize Global Variables
Here, key variables used across the program are defined. Some are updated throughout gameplay:

position_to_index: maps board labels (A–I) to logical indices (0–8)

board_state: the current game configuration as a 9-character string ('x', 'o', or '-')

turn_count: a counter tracking the number of moves made

winning_patterns: regex expressions used to identify win conditions

game_history: stores a history of the AI’s decision process in the current game

Code Cell 3: Define Game Functions
All reusable logic is defined here:

initialize_orientation(): determines how to map visual board letters to logical indices based on the first non-center move

display_board(): shows a readable, labeled version of the board

execute_turn(): alternates play between the human and AI, updates the game state, and logs moves for learning

choose_move(): selects the AI’s move based on learned probabilities stored in the STM, or fills in the last remaining space on turn 8

update_learning(): updates the STM after each game using Algedonic compensation

generate_next_states(): computes all legal next moves from a given board state; used to build the STM in Code Cell 4

Code Cell 4: Build the STM
This cell constructs the State Transition Matrix (STM), a critical data structure for learning. It can be rerun to reset learning progress.

The STM is structured as a list of four dictionaries, one for each even-numbered turn (0, 2, 4, 6). This makes retrieval efficient.

Each entry maps a board state to a dictionary of possible outcomes, each associated with a probability.

Duplicate and end-game states are filtered out to optimize size.

Building the STM is relatively quick; a message is printed when complete.

Code Cell 5: Set Learning Parameters
This cell allows you to set the AI’s learning rates:

reward_rate: applied when the AI wins or ties

punishment_rate: applied when the AI loses

Both values should be between 0 and 1. These parameters directly affect how much the AI adapts after each game and are used in update_learning().

Code Cell 6: Run a Game
This is the main interactive component, where you play Tic Tac Toe against the AI. Each run starts a new game, and learning continues between sessions.

You’ll be prompted whether to show internal state strings after each move.

The AI always plays first as 'x'.

On your turn, enter a letter corresponding to an open space.

The game ends when someone wins or all spaces are filled. The AI then updates its learning and prints a message with a :) for a reward or :( for punishment.

Code Cell 7: Inspect the STM
This cell prints the contents of the STM at any point.

You can set filter_by_state = True and assign a valid 9-character target_state (with equal numbers of 'x' and 'o') to view only relevant entries.

This is helpful for debugging or reviewing how learning is evolving.

Playing Against the AI
After launching Code Cell 6, you’re asked whether to show the state strings. This is optional but useful if you want to copy a state into Code Cell 7 for analysis.

The game proceeds with the AI making the first move. You’ll input moves by typing letters like 'A' or 'F'. If you input an invalid move, you'll be prompted again.

The game continues until someone wins or the board fills. Afterward, the board is shown, the winner is announced, and the AI updates its STM based on the outcome.

How the Program Was Built
Reducing Redundant States
One early challenge was avoiding unnecessary duplication of states. To solve this, I separated the visual board (A–I) from the logical state string (indices 0–8).
Once the first non-center move is made, initialize_orientation() maps the board to one of four rotational states based on where the move occurred:


First Move	Mapped Grid
A or B	0 1 2 / 3 4 5 / 6 7 8
C or F	6 3 0 / 7 4 1 / 8 5 2
I or H	8 7 6 / 5 4 3 / 2 1 0
G or D	2 5 8 / 1 4 7 / 0 3 6
This dynamic mapping prevents equivalent positions (under rotation/reflection) from being redundantly represented.

Handling Game Over Conditions
Before each move, the program checks:

Victory: If the current board matches any pattern in winning_patterns, the game ends and the winner is announced.

Tie: If turn_count reaches 9 without a win, it’s a draw, and the AI receives a reward.

STM Generation
Initially, I tried hardcoding the STM but quickly realized it was impractical due to the vast number of states. I pivoted to building it dynamically using generate_next_states() and looping structures in Code Cell 4. This not only sped up development but allowed for flexible scalability.

Implementing Learning
Once the STM was functional, I added learning through update_learning(), which uses Algedonic compensation—a method of adjusting probabilities based on game outcome. The game history is logged in game_history, and the AI updates probabilities after each match using either reward_rate or punishment_rate.

Discovering a Logic Bug
While testing, I encountered a bug where the sum of probabilities in choose_move() didn’t equal 1, which triggered an error message. After reviewing the STM, I realized that earlier game results were affecting future decisions incorrectly.

The issue was traced to a flaw in how I computed the adjustment term in update_learning(). Instead of referencing the correct probability of the chosen action, I had been mistakenly using the probability of the current iteration in the inner loop.

I reviewed the equations by hand, compared against the code, and reached out for clarification. With guidance, I corrected the logic and verified that learning stabilized in subsequent games.

Wrapping Up
After resolving the learning logic, I completed additional testing and finalized the system. The AI now learns progressively across games and responds accurately to both success and failure. I also explored different learning rates and documented their effects in report.txt.

This lab was both an engaging technical challenge and a hands-on exploration of reinforcement learning principles in action.

