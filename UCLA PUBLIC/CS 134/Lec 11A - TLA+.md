- TLA: Temporal Logic of Actions
- Language for modeling digital systems:
	- **Design** level: Algorithms, programs, computer systems
	- **Designed for modeling concurrent and distributed systems**
	- Fix and correct design errors before writing code
- TLA+ Used in distributed systems
## How it works
- Create high-level models called **specifications**
- TLA+ checks properties of individual executions, not groups of executions
	- Verify system does not produce wrong answer
	- Cannot be used to verify 99% of executions produce right answer
- Execution: represented as a sequence of discrete steps
	- Sequence of states, sometimes called behavior
### State Machine
- Can be done in PL, turing machines, automata, hardware description languages
- State Machine:
	- defined by all initial states and set of possible next states given current state (variables, possible initial values, and relationships)
	- If no next state possible, machine halts (doesn't change state after)
### TLA+ Syntax
![Screenshot 2024-12-06 at 8.53.59 PM.png](../../_resources/Screenshot%202024-12-06%20at%208.53.59%20PM.png)
![Screenshot 2024-12-06 at 8.54.29 PM.png](../../_resources/Screenshot%202024-12-06%20at%208.54.29%20PM.png)
![Screenshot 2024-12-06 at 8.54.41 PM.png](../../_resources/Screenshot%202024-12-06%20at%208.54.41%20PM.png)
### Creating TLA+ Program
- Define type invariants
![Screenshot 2024-12-06 at 8.58.21 PM.png](../../_resources/Screenshot%202024-12-06%20at%208.58.21%20PM.png)
- Set initial State
![Screenshot 2024-12-06 at 8.58.35 PM.png](../../_resources/Screenshot%202024-12-06%20at%208.58.35%20PM.png)
- Define next state 
![Screenshot 2024-12-06 at 8.58.43 PM.png](../../_resources/Screenshot%202024-12-06%20at%208.58.43%20PM.png)
- Define predicates
![Screenshot 2024-12-06 at 8.59.12 PM.png](../../_resources/Screenshot%202024-12-06%20at%208.59.12%20PM.png)
![Screenshot 2024-12-06 at 8.59.18 PM.png](../../_resources/Screenshot%202024-12-06%20at%208.59.18%20PM.png)
- Set final invariant:
	- Toolbox will tell you how invariants can be broken, e.g. in this problem, set big != 4