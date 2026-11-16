---
layout: post
title: Formal Design Gramar
date: 2026-08-08
tags:
  - computational-design
  - design-grammar
---

How are **design grammar** formal or informal?
Formal means _computable, structured, and rigorous and not ad hoc._ [[formal_engineering_design_synthesis]]

What is **design**?
It is the process to produce a system that achieves a desired function or behavior.

What is **grammar**?

What is **design grammar**?
Using grammar like formalism with a vocabulary, a set of rules, and series of computations that can produce designs as if they were "sentences". They are also defined as the cases in which: The target designs to be generated are not known. They are interchangeably used with shape grammars which Stiny and Gip refer to as visual design grammars.

- Shape Grammars are rule based formalism that say see Shape_1 do Shape_2.
-

What is analytical grammar?
The target designs to be generated are known

Why design grammar?

- Almost 55 years since the introduction of design grammars by Stiny and Gip
- great generative power for creating variety of designs
- automatically generate and search complex design spaces

---

What is the current status in application of **Formal Design Grammars and Languages** for design synthesis?

### What has been the challenge in design grammar so far?

- lack of support for designers to develop and implement formal design grammars
  - What kind of support?
    support in developing design grammar and interpreting the design grammar
- challenges in knowledge acquisition and knowledge formalization have restricted their application in industry
- some research aimed at automating the design grammar using Bayesian grammar induction, PCA, etc but each limited in its scope, generality and scale.
- increasing in design grammar complexity leads to complexity of interpreting as well
- A complete shape grammar interpreter is lacking
- LLMs and AI agents struggle to achieve the results of past work in generative design using design grammars for tasks that incorporate behavior simulation, optimization and search.
- **Open Research Challenge until now**:
  - How to support designers through the design grammar development process which is ill-defined, iterative, non-deterministic, and complex problem?
  - How can define design grammars through grammar interpreters so that design grammars be computationally executed to generate designs?

### How can LLM help? What needs to be done?

- LLM can help design grammar development due to their ability to generate high quality information spanning multiple domain even on challenging problems
- Need for an extensive research on the potential of LLMs to support the process of design grammar development and implementation

### What has been done in this work?

- Explore the potential of LLMs to act as a collaborative grammar development partner in ill-define task where a design grammar is not know a priori as well as for the well-defined design grammar
- Explore the potential of LLMs to acts as grammar interpreter that converts text descriptions of design grammar into executable codes
- Two examples to demonstrate the potential of LLMs to automate the interpreter in design grammar synthesis (this means automating conversion of design grammar to DSL)
  - Truss Design
  - Shape Design
- Third example
  - Technical process grammar
    - Define and implement a design grammar collaboratively

### Background need to look more into

1. Computation Design Synthesis using Generative Design Grammars
   - Use of shape grammars to find the rules and process of various artists and architects: chinese lattice designs, Palladio styled villa, coffee maker grammar, motorcycle grammar, etc.
   - Model design across various domains, levels of abstractions and representations with main focus on graphs and shapes
   - Shape annealing to automate the generation of directed shapes using shape grammars
   - _Analysis Grammar_, _Design Grammar_, _Algorithmic Design Grammar_ (problem solving design grammar which incorporates more logic into the grammar itself)
2. Generative Design Grammar Interpreters
   - The final challenge is to develop a computational framework in which any design grammar can be readily input and its application interpreted by a computer to automatically generate designs (More like Design Grammar --> Python code using LLMs--> Generate designs)
   - In this work, half-hexagon shape grammar which is represented as set grammar is explored as a new pathway to overcome the lack of a complete shape grammar interpreter
3. Large Language Models for Supporting Design
   - LLMs used in problem definition, conceptual design, embodiment design, detailed design, material selection, etc.
   - LLMs have issues in successfully interpreting designs of high complexity

### What are their questions and what methodology is used to answer those questions?

_Q1. How can LLMs provide a new means to develop design grammars and implements classes of grammars using different design representations?_
Methodology : Use LLM as collaborative grammar development partner and grammar interpreter in three case studies

**How to define a design grammar?**

- Vocabulary for the designs (class of designs, main components, grammar representation)
- Syntax (defining the constraints for design generation)
- Grammar rules adhering to defined constraints
- Initial design based on defined vocabulary
- Generate alternative designs
  With LLMs, in the case of design grammar known a priori "one-shot" single clear prompt is provided with the goal to specify one design grammar and produce executable python code that can be used to generate designs within the design language specified.

For design grammar development task, "least-to-most" prompting approach of decomposing a complex problem into smaller problems and solving smaller problems in sequence is utilized which is beneficial to solving complex tasks. Here, 'technical process design grammar' is developed in collaboration with LLM.

### Case Studies

1. Truss Design Grammar
   - Five design rules with operations such as subdividing a triangle, merging triangles, etc. such that they adhere to Maxwell's rule for stability of pin-jointed structures
   - LLMs as grammar rules implementer and shows that LLM can construct larger structural systems through iterative application of grammar rules and allows structural simulation and optimization
2. Half-Hexagon Shape Design Grammar
   - Predefining shapes and the operations (rules) that can be performed in these shapes
   - This is generative task that allows design space exploration in high dimensional space and thus allows optimization but this is confined due to the initial shape used and the rules that can be performed.
3. Technical Processes Grammar
   - LLM (GPTo1) is used to generate a graph grammar for a fully mechanical and manual seed planting process. Through iterative development, it can create rules $R_i$ to transform a graph $G_{i-1} \xRightarrow{R_i,\rho} G_i$ . This is useful in expanding the design.

### Discussion and Future Challenges

- New potential for LLMs to overcome the difficulties with design grammar development but further investigations needed.
- **Issues in Design Grammar Implementation:**
  - Issue 1
    - Visualization issue with node labels (numbers instead of letters despite the instruction in the prompt): Labelling is important as it signifies spatial relationships and rule application. Mistake here can make the whole design degenerate.
  - Issue 2
    - Disconnected nodes and not removing them
  - Issue 3
    - Limited memory per session and forget what is generated before (True with API call as well)
  - Issue 4
    - Amount of natural language required to describe a design grammar compared to the visualization of the design grammar rules. Formal design grammars has to be compact and precise knowledge encapsulation representations in comparison to the LLM natural language descriptions.
- **Issues in Design Grammar Development:** - Issue 1 - Design Grammar is still less explainable due to the ill-structured nature of grammar design problem -
  Future Directions
- Missing in the literature: capability to reason across multiple design grammars
- How good a design grammar is needs to be revisited for the design grammars developed with LLMs based on the performance metrics, size and diversity of solution space defined.
- Emergence is another future area for investigation for the combination of design grammars and LLMs that could lead to new discoveries
- Automated grammar generation could be explored within LLMs for future work with prior works such as statistical analysis approach
- Can contribute to the interpretability as well but not focus of the design really
