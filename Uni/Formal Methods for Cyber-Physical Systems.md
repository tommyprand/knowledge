## Models of computation

We can distinguish between two models of computation: **Functional** and **Reactive**:
**Functional**: A program takes input(s) and produces output(s), this computation implements a function that realises our desired functionality. The canonical model for this is the _Turing machine_.
**Reactive**: The system operates in an ongoing manner, so it keeps operating producing sequences of input/output where the desired behaviour is represented by the acceptable pairs of input/output.
A further distinction can be made between **sequential** and **concurrent** computations:
**Sequential**: The computation in composed by a sequence of instructions executed once at a time.
**Concurrent**: There are multiple processes executing and evolving at the same time, there might be communication between these processes.

### Synchronous models

We will consider **synchronous models** (as opposed to asynchronous) which means that all components execute their code in a sequence of rounds in lock-step (discrete steps synced by a shared clock).
The benefit of this is making modelling and design simpler because it establishes a clear cause-effect relationship between instructions, but is poses the challenge of implementing synchronous execution on multi-core hardware.

The following assumptions are made:
1. The time needed to execute the update code at each time step is negligible, this implies that reception of inputs and production of outputs happens at the same time.
2. When multiple components are composed together they still execute synchronously and simultaneously during the ticking of the global clock.

In reality these assumptions should be enforced by the actual implementation.

## Synchronous Reactive Components

In order to describe the components of a system we will use a modelling language called **Synchronous Reactive Components**, with well defined _syntactic_ and _semantic_ definitions.

#### Example (Delay component)

![[IMG_5EF06708B9F9-1.jpeg]]

### Inputs

Each component has its set of (typed) input variables $I$, we define the _Input_ as the valuation of all the input variables at a certain time-step.
The set of all the possible inputs is denoted $Q_I$.

For a [[#Example (Delay component) | delay]] component
$$I = \{ \text{in of type bool} \}$$
$$Q_I = \{ 0, 1\}$$

### Outputs

Each component has its set of (typed) output variables $O$, we define the _Output_ as the valuation of all the output variables at a certain time-step.
The set of all the possible inputs is denoted $Q_O$.

For a [[#Example (Delay component) | delay]] component
$$O = \{ \text{out of type bool} \}$$
$$Q_O = \{ 0, 1\}$$

### State

Each component has its set of (typed) state variables $S$, we define the _State_ as the valuation of all the state variables at a certain time-step.
The set of all the possible states is denoted $Q_S$.

Note that the state is internal to the component (hidden to the outside), and preserved across rounds.

For a [[#Example (Delay component) | delay]] component
$$S = \{ \text{x of type bool} \}$$
$$Q_S = \{ 0, 1\}$$

### Initialisation

The state of each component is initialised with some value **before** the first update executes.
The set $Init$ contains the sequence of assignments made to initialise the state variables.
From the set $Init$ and the state $S$ we can derive the set $[\![Init]\!]$ that contains the possible initial states for the component. Note that $[\![Init]\!] \subseteq Q_S$

For a [[#Example (Delay component) | delay]] component
$$Init = \{ x := 0 \}$$
$$[\![Init]\!] = \{ 0 \}$$

### Reactions

The execution in every round is determined by the set $React$ that contains the sequence of instructions (assignments and conditionals) representing the code that updates internal state and produces output variables.

The set of _Reactions_ $[\![React]\!]$ contains tuples mapping all the possible states and inputs to all the possible new states and outputs; in fact $[\![React]\!] \subseteq Q_S \times Q_I \times Q_O \times Q_S$ 

For a [[#Example (Delay component) | delay]] component
$$React = \{ out := in; x := in \}$$
$$[\![React]\!] = \{ (0, 0, 0, 0), (0, 1, 1, 0), (1, 0, 0, 1), (1, 1, 1, 1) \}$$
Which correspond to this table:
$$
\begin{array}{ |cccc| }
	\hline
	x & in & out & s \\ \hline
	0 & 0 & 0 & 0 \\ \hline
	0 & 1 & 1 & 0 \\ \hline
	1 & 0 & 0 & 1 \\ \hline
	1 & 1 & 1 & 1 \\ \hline
\end{array}
$$

To create this table one should first write down all the possible input and old state combinations and from that derive all the possible output and state transitions.

#### Multiple reactions

If we replace $React$ with this new fragment of code: $React = \{ out := x; x := \text{choose}\{in, x\}\}$ then we create a non-deterministic reaction, which means that, given old _State_ and _Input_, the _Output_ and new _State_ **are not unique!**. This increase the number of reactions from 4 to 6.

Instead by replacing $React$ with $React = \{ \texttt{if } x != 0 \texttt{ then } \{ out := x; x := in \}\}$, we obtain that with some combinations of input and/or current state there will **not be any reaction**, thus now the set contains only 2 reactions.

### Update code constraints

The update code must follow these rules:
1. Types of variables and expressions should _match_
2. Output variables _must_ be written before being read
3. Output variables _must_ be explicitly assigned a value by the component
4. Input values _cannot_ be written

In case the update code violates those rules then $[\![React]\!] = \emptyset$.

### Formal notation

- Set $I$ of typed input variables $\rightarrow$ set $Q_I$ of possible input values
- Set $O$ of typed output variables $\rightarrow$ set $Q_O$ of possible output values
- Set $S$ of typed state variables $\rightarrow$ set $Q_S$ of possible state values
- Initialisation code $Init$ $\rightarrow$ set $[\![Init]\!]$ of possible initial states
- Update code $React$ $\rightarrow$ set $[\![React]\!]$ of possible reactions in the form $s \xrightarrow{\text{i/o}} t$.

- A component is formally annotated as $C = (I, O, S, Init, React)$ with the meaning as above
- The **executions** of a component is a sequence of states in which the component finds itself by arbitrarily choosing the initial state and the inputs at each round. A sample execution looks like this (for deterministic code...): $$s_0 \xrightarrow{i_1/o_1} s_1 \xrightarrow{i_2/o_2} s_2 \xrightarrow{i_3/o_3} \quad ...$$

### Events

A special type an input/output variable can have is the type _event_. An _event_ can be _absent_ or _present_, in the latter case the event holds a value.

- $\texttt{event } x$ means $x$ ranges over the values $\{\top \text{ (present) }, \bot \text{ (absent) }\}$
- $\texttt{event(bool) } x$ means $x$ ranges over the values $\{\bot, 0, 1\}$
- $\texttt{event(nat) } x$ means $x$ ranges over the values $\{\bot, 0, 1, ...\}$
-  $x?$ returns $1$ if the value present $0$ otherwise ($x \neq \bot$)
- $x!v$ is the assignment $x := v$
- By default an event is absent
- Event-triggered components only executes in those rounds where and actual input event is present

![[IMG_7F860A92599F-1.jpeg]]