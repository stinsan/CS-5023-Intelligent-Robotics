# 14. AI Reasoning Methods for Robotics
## 14.1. Why Should a Robot Use AI-Type Reasoning?
**Reasoning** means inferring the needed knowledge from what is spelled out explicitly in the available knowledge.
**Automated reasoning** means using AI methods such as knowledge representation and inference, heuristic search, and machine learning.

- An important task of AI reasoning techniques is: 
given a vague instruction, infer what is the appropriate action, what is the appropriate object to be acted on,
what is the appropriate order of action executions, and
what is the appropriate way of performing every action

Important kinds of reasoning that robots should be capable of:
- **Prediction**: what will probably happen if the intended action is executed?
- **Envisioning**: inferring all possible events and effects that will happen if a plan gets executed hypothetically.
- **Diagnosis**: inferring the cause of a particular event or effect in plan execution.
- **Query answering**: given some preconditions for plan execution, inferring pieces of knowledge that satisfy these preconditions.

## 14.2. Knowledge Representation and Processing
What are the formats suitable for the robot's representation of the environment (AKA knowledge representation or KR)?

The chosen KR must have a balance of both **epistemological adequacy** and **computational adequacy**.
- **epistemological adequacy**: Does the KR allow the environmental aspects to be expressed compactly and precisely?
- **computational adequacy**: Does the KR allow environmental inferences to be drawn effectively and efficiently?

### 14.2.1. Logic-Based Reasoning
**First-order predicate logic** (FOPL) typically does not qualify as a epistemologically or computationally adequate form of KR, but it is the background
for understanding conceptually and mathematically general relational languages.

This is an example of an ad-hoc FOPL language about a robot traversing an office building:

![](https://github.com/stinsan/CS-5023-Intelligent-Robotics/blob/master/Screenshots/001.PNG)

Below are the variables:
- Constants D_i and variable d denote doors.
- Constant R_i denote rooms.
- Constant C_i denote corridors.
- Variables l and l_i denot locations, i.e. rooms and corridors.

#### Description Logics
**Description logics** (DL) is another KR form which introduces domain concepts and relations between these concepts. One feature of the DL representation is the superclass-subclass relation, which forms a conecpt heirarchy similar to object-oriented programming. 

A **concept** in the DL language is a unary predicate, e.g. _Door_ or _Location_.\
Concept heirarchy comes into play by defining concept equality (=) or a subconcept property, e.g.  _Room_ ⊑ _Location_ and _Corridor_ ⊑ _Location_.\
Concepts can also be combined with conjunction, disjunction, and negation (⨅, ⨆, ¬), e.g. _Location_ = _Room_ ⨆ _Corridor_, _Door_ = _Closed_ ⨆ _Open_, and _Open_ = ¬ _Closed_.

A **role** in a DL language correspond to binary preidcates, such as _leadsTo_ for a door and a location.\
Inversity, intersection, and union affect roles as expected. For example, _leadsTo_ = _leadsFrom_ <sup>-1</sup>.\
Roles can also be composed. For example ,since location _l_ is adjancent to _m_ iff some door connects them, the role _adjacent_ = _leadsFrom_ ∘ _leadsTo_ can be defined.
Another example of a role composition is the role _BlockedLocation_ = _Location_ ⨅ ¬∃ _leadsFrom_.

In a DL, the individual objects have to be introduced to the language of concepts and roles.\
For example, _Room(R<sub>509</sub>)_, _leadsTo(D<sub>509</sub> , R<sub>509</sub>)_, and _Closed(D<sub>509</sub>)_ can be asserted.

#### Logics of High-Level Robot Control
The **frame problem**: How do we formalize actions in a logical language so that facts can be inferred efficiently that may or may not have changed after the robot takes a sequence of actions? If a robot goes from location A to location B, of course its physical location changes, but it may also change the battery state and the mileage count. However, it does not change the layout of the building or Donald Trump's name.

Another problem concerns **updating the robot's knowledge base**. If the robot is sitting in front of a door, which it believes is open, but perceives (via sensors) that it is actually closed, we must update the knowledge base to make it consistent with its perception. After replacing old information with new ones, we have to make sure that the consequences of the retracted information are no longer believed.

### 14.2.2. Probabilistic Reasoning
A KR based on logics is good when the robot is faced with factual data. However, sometimes the robot is uncertain about the environment, whether because of lack of knowledge or other reasons. In the case where a large number of facts are undetermined, logics will fail because, technically, everything is possible. Yet, the different possibilities
may differ considerably in likelihood, which is where probabilistic reasoning comes into play.

There are two popular and powerful probablistic knowledge representations: Bayesian networks (BNs) and Markov decision processes (MDPs).

#### Bayesian Networks
Inference in Bayesian probablity means to infer the probablility of some event of interest, given other prior probabilities that are dependent. 

**Problem**: For a potential cause _C_ and an observed effect _E_: given prior probabilities _P(C)_ and _P(E)_ and the conditional probability _P(E|C)_, determine the posterior _P(C|E)_. 

**Solution**: Bayes' Rule defined as

![](https://github.com/stinsan/CS-5023-Intelligent-Robotics/blob/master/Screenshots/002.PNG)

However, if there are many effects observed (_E_<sub>1</sub> , ..., _E_<sub>n</sub>), it's extremely difficult to specify all of the conditional probabilities involved. This is where Bayesian networks (BNs) come in.

The main idea of **Bayesian networks** is to represent the random variables as nodes in a directed acyclic graph, where a node is directly preceded by a set of parent nodes iff it is directly conditionally dependent on the corresponding parent variables.

![](https://github.com/stinsan/CS-5023-Intelligent-Robotics/blob/master/Screenshots/003.PNG)

A BN can be interpreted in both directions, bottom-up and top-down. When lookign at a BN bottom-up, we can explain the event using the conditional probabilities (diagnosis).
When looking at a BN top-down, we can propegate evidence to compute the probability of an event happening (causation).

For systems evolving over time, for which the Markov property holds (meaning that future states are not dependent on the past, only the present), a BN takes on a specific form: a **dynamic Bayesian network** (DBN).
 
 ![](https://github.com/stinsan/CS-5023-Intelligent-Robotics/blob/master/Screenshots/004.PNG)
 
 The variables in a new state correspond to a BN slice (i.e. what's shown in Figure 14.1) and whose nodes depend only on each other and on nodes in the previous state.
 
 #### Markov Decision Processes
 In order to cope with uncertainties in the environment, Markov decision processes (MDPs) and, in the case of partial observability of the environment, partially observable Markov decision processes (POMDPs) are used.
 
 **Formal Definition**: an MDP is formally defined by a four-tuple <_S_, _A_, _T_, _R_>.
 | Variable | Definition |
 | -------- | ---------- |
 | _S_ | finite set of states |
 | _A_ | finite set of actions |
 | _T_ : _S_ x _A_ ⭢ _S_ | transition function defining the probability of state change upon application of a given action at time _t_<br> T(s, a, s') = Pr{s<sub>t+1</sub> = s' &#124; s<sub>t</sub> = s, a<sub>t</sub> = a} 
 | _R_(_s_, _a_), _R_ : _S_ x _A_ ⭢ 𝕽 | reward received by the robot after acheieving action _a_ leading to state _s_ |
 
 Below is an example of an MDP:
  ![](https://github.com/stinsan/CS-5023-Intelligent-Robotics/blob/master/Screenshots/005.PNG)
 
 Solving an MDP is an optimization problem where we want the sum of the expected rewards, called **utility**, to be maximized. There are two ways to solve an MDP:
 - value iteration
 - policy iteration
 
 In **value iteration** we want to compute all utilities _U(s)_ of all states first, then compute 𝜋<sup>*</sup>(_s_) which provides the optimal action for each state. To compute the utilities, we use the Bellman equation
 ![](https://github.com/stinsan/CS-5023-Intelligent-Robotics/blob/master/Screenshots/006.PNG)
where 0 < 𝛾 < 1 is a discount factor. After computing utilities, the optimal policy is the one that provides the best action for each state. So, the result of the MDP is the best local decision.

In **policy iteration**, we start with an initial, random policy 𝜋. Then, we try to improve it gradually using an iterative algorithm that looks at actions that augment utility.

#### Partially Observable Markov Decision Processes
**Formal Definition**: A POMDP is a six-tuple <_S_, _A_, _T_, _R_, 𝛺, _O_>. The variables _S_, _A_, _T_, _R_ are the same as a MDP. The variables 𝛺 and _O_ are defined below:
 | Variable | Definition |
 | -------- | ---------- |
 | 𝛺 | finite set of observations _o_ |
 | _O_ : _S_ x _A_ ⭢ 𝛺 | the observation model _O(s', a, o)_ that provides the probability to obtain observation _o_ after action _a_ which has lead to state _s'_ |
 
 The **robot belief state** is all the probabilities of being in all the states. The belief to be in a given state is noted as _b(s)_. The belief is update after an action and observation by
 ![](https://github.com/stinsan/CS-5023-Intelligent-Robotics/blob/master/Screenshots/007.PNG)
 where 𝛼 is a normalization constant.
 
 ## 14.3. Reasoning and Decision Making
