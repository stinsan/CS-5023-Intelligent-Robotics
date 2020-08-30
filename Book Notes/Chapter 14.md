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

 
