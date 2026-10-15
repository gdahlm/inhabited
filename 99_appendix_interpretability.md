# The Real World: Why LLM Interpretability Is Fundamentally Existential

An appendix to the thread. The six notes built a chain of limits:
undecidability at the core of first-order logic (`3_church_trakhtenbrot.md`),
no nontrivial semantic property of computation is decidable
(`4_rice_theorem.md`), witness extraction reaches exactly the Σ₁ level
(`5_markov_principle.md`), and truth cannot even be stated from inside a
formal system (`6_godel_tarski.md`). This appendix shows that the chain is
not merely mathematical furniture: it describes, almost exactly, the current
shape of one of the most active engineering efforts in computer science,
understanding and verifying large language models.

**Interpretability is an existential enterprise, and each theorem in the
thread bounds what an existential enterprise can guarantee.** A circuit
explanation is a witness; "the model has an interpretation" is an ∃-claim;
and the theorems say when such claims are semi-decidable (find it by search),
when they are undecidable (no uniform procedure at all), and when they cannot
even be formally stated from inside (Tarski). What the theorems do not say is
that particular explanations cannot be found: pointwise, local, empirical
witnesses keep getting found. The limits are on *uniform guarantees*, not on
*particular discoveries*. That distinction, not any single theorem, is what
practice should be built on.

Roadmap: §1 sketches the real-world effort (levels of interpretability,
current tools) in plain terms. §2 reframes it in the thread's vocabulary:
explanations are witnesses. §3 applies Rice to behavioral properties. §4
applies Church and Trakhtenbrot to the dream of formal verification. §5
locates exactly where witness extraction (Markov's principle) reaches and
stops, including the same shape in classical PAC learning. §6 adds the
Tarski/Gödel layer. §7 states what this means for practice.

## 1. What the effort looks like

**A trained LLM is a black box by construction: billions of parameters,
non-linear operations, no readable source. Interpretability research is the
attempt to open the box and say why it says what it says.**

The field works at three levels:

- **Mechanistic interpretability**: reverse-engineer the network into
  circuits: which attention heads or neuron groups implement which subtasks
  (tracking agreement, copying, induction).
- **Representation analysis**: study the high-dimensional embedding spaces
  where concepts live: how the model groups similar ideas, facts, and biases.
- **Behavioral interpretability**: treat the model as a black box and probe
  it through inputs alone: prompt variants, counterfactuals, adversarial
  cases.

The current toolbox: attention maps (which tokens influenced a prediction),
feature attribution (gradient-based saliency), probing (training small
auxiliary models on internal layers to test whether a fact or property is
represented there), and sparse autoencoders (SAEs): decomposing dense,
entangled hidden states into millions of sparse, individually inspectable
features. SAEs are the current main event: they turn "a direction in
4096-dimensional space" into a named feature with a firing condition.

Why it matters, concretely: safety (find the internal triggers for deceptive
or harmful behavior before they fire), debugging and editing (locate and
correct a stored false fact), and regulation (laws such as the EU AI Act
increasingly require traceability for high-risk systems).

## 2. Explanations are witnesses

**The core question of interpretability has the form ∃w. E(w): there exists a
witness w (a circuit, a feature, a human-readable description) with property
E (it correctly explains the target behavior).**

Fix a model M and a behavior B (say: "M hallucinates a specific false fact on
prompts of class P"). An *explanation* is a finite object w (a set of heads, a
sparse feature, a description) such that E(w, B) holds. The interpretability
question is then exactly the existential question the thread has been
studying:

- If E is **checkable** (decidable on a finite test suite), then "a witness
  exists" is a Σ₁ claim: enumerate candidate explanations, test each, halt on
  the first that passes. This is the semi-decidable regime of
  `5_markov_principle.md` §3.
- The failure mode is not that witnesses fail to exist; it is that their
  *absence* cannot be certified: no explanation found on a suite is not a
  proof that none exists.
- The honest caveat, stated up front: "explains correctly" in the strong
  sense (on *all* inputs, including counterfactuals) is not checkable at all;
  it ranges over unbounded behavior. Practice approximates E with a finite
  suite, and every guarantee in this appendix lives or dies on that
  approximation.

Note the vocabulary alignment with `2_mere_props.md`: "B has an explanation"
is a mere proposition (a truth value), while the space of explanations may
carry rich structure (many different circuits can explain the same behavior).
Truncation, in this setting, is the difference between "we know some circuit
exists" and "here it is."

## 3. Rice: no uniform decider for behavioral questions

**To the extent that a model's input-output behavior can encode arbitrary
computation, no algorithm can read any nontrivial fact about that behavior off
the weights.**

The conditional matters, so let us state both sides honestly:

- A *fixed, finite* model with bounded context is a finite object. Any
  specific question about its behavior on all inputs in its (finite) domain is
  decidable in principle by exhaustive simulation. Finiteness buys
  decidability for the *particular*.
- But the safety literature reasons explicitly about models whose input-output
  map can simulate arbitrary programs on suitable inputs, and the input space
  is unbounded in length. Once that assumption is in place, "does M ever do
  X?" (over all inputs) is a semantic property of a computable function, and
  `4_rice_theorem.md` applies: every nontrivial such property is undecidable.

The interpretability consequences, direct from the theorem:

- "Is this model safe (never produces X)?" is not decidable by reading
  weights.
- "Does this circuit implement rule R?" is not decidable uniformly.
- The weights are the intension; the behavior is the extension, and the
  intension carries no nontrivial decidable shadow of the extension
  (`4_rice_theorem.md` §5).

This is why "reading the weights" can never be a *certificate*. It is
evidence: local, empirical, probabilistic. Pointwise classical, uniformly
not: for each fixed model and fixed input, everything is settled; no uniform
procedure exists over all models and inputs.

## 4. Church and Trakhtenbrot: why formal verification cannot close the loop

**The dream of a formal specification language plus a verifier ("this model
never violates rule X") runs into the thread's two undecidability results,
from both directions.**

- **Church's direction (rich behavior).** The verification question "does
  rule R hold for all inputs?" is a validity-style question over the
  structure of the model's behavior. If that structure is rich enough to
  encode computation (and any class of behaviors that includes arbitrary
  input-output patterns qualifies), then by `3_church_trakhtenbrot.md` §2 no
  algorithm decides it: the halting problem hides inside "will M ever violate
  X."
- **Trakhtenbrot's direction (the finiteness trap).** The natural escape is
  "but the model and its inputs are finite": fixed weights, bounded context,
  finite token sequences. Finiteness feels like it should make formal
  reasoning *easier*. It does not: first-order validity over finite structures
  is not even recursively enumerable (`3_church_trakhtenbrot.md` §3). A formal
  language aimed exactly at finite token graphs (attention patterns, finite
  sequences) inherits the same non-axiomatizability: no r.e. set of axioms
  captures exactly what is true in all of them. And FOL itself cannot even say
  "finite" (compactness), so the restriction is invisible from inside the
  language.

Both escape routes close: rich behavior gives Church, finite behavior gives
Trakhtenbrot. The practical methods that survive (test suites, red-teaming,
runtime monitors) are not a failure of ambition; they are the only regime the
mathematics leaves open.

## 5. Where witness extraction reaches: the exact shape of current tooling

**The only principled witness extraction available is at the Σ₁ level, and it
is exactly what interpretability tooling does: search for candidates, test
them against behavior, halt on the first that works.**

Markov's principle (`5_markov_principle.md` §2 and §3) says: for a decidable
check E, "a witness cannot be shown not to exist" (¬¬∃w. E(w)) licenses
concluding ∃w. E(w) and running the search until it halts. The Russian school's
justification ("set the machine working") is precisely the operating principle
of a probe or SAE pipeline: enumerate sparse candidate features, check each
against the model's behavior on a suite, keep what passes. The successes are
pointwise witnesses; the guarantee behind them is semi-decidability, not
completeness.

The shape, as code:

```python
# Interpretability tooling, as a witness search.
#
# A candidate explanation (a sparse feature, a circuit) is "correct" if it
# passes a decidable check against the model's behavior on a test suite.
# Enumerating candidates and testing each is a Sigma_1 procedure: it halts
# with the witness iff one exists; otherwise it runs forever.

import itertools

def fits(threshold_t, suite):
    # Decidable check: does the rule "x > t" agree with the observed
    # behavior on every (input, expected) pair in the suite?
    return all((x > threshold_t) == expected for x, expected in suite)

def find_feature(suite, candidates):
    for t in candidates:        # enumerate candidate features
        if fits(t, suite):      # decidable check against behavior
            return t            # witness found; the search halts here
    # No return: "no feature fits" has no such procedure at all.

# A toy instance: the "feature" is a threshold rule on a 1-D input, and the
# suite says "fire exactly on inputs above 3". The search halts at the first
# candidate that fits, exactly as a real probe pipeline halts at the first
# sparse feature that passes its checks.

suite = [(x, x > 3) for x in range(8)]
t = find_feature(suite, itertools.count())
print("first fitting feature: x >", t)   # -> first fitting feature: x > 3
```

Where the extraction stops: "M never does X" is Π₁ (a universal claim over
inputs); "this circuit explains *all* of M's behavior" ranges beyond Σ₁
entirely. No witness-extraction principle reaches them; it is the same
boundary `3_church_trakhtenbrot.md` drew for Trakhtenbrot's finite validity
and `4_rice_theorem.md` §4 drew for totality and equivalence.

**The same shape in classical learning theory.** Even before LLMs, PAC
learning was built on existential guarantees: "there exists a hypothesis in
the class that fits the data and generalizes," with sample-complexity bounds
as the witness to feasibility. The learner is the witness-extraction
procedure: it must output an actual hypothesis, not merely certify that one
exists. And the scope limits match the thread exactly: learnability holds for
*restricted* classes (finite or bounded VC dimension) and fails in general
(no uniform learner for all computable target functions). Learning theory
tells you when a witness must exist; questions about what a *particular
trained* model has learned are, once again, Rice territory.

## 6. The deepest layer: Tarski and Gödel

**Even "what the model means" cannot be pinned down from inside any fixed
formalism that is rich enough to capture it.**

A truth predicate over a model's internal states or behavior cannot be
defined in a language that can express its own provability
(`6_godel_tarski.md` §3): any such definition yields the liar contradiction.
And if one wants a consistent, effectively axiomatized theory that proves
*all* true statements about M's behavior, Gödel I says no: it will leave true
statements unprovable (`6_godel_tarski.md` §2). The undecidability of §3 and
§4 is a limit on *deciding*; this is a limit on *stating*. No formalism rich
enough to describe the model can define its own truth predicate or prove all
truths about it; interpretability explanations are, in the end, objects of an
external metatheory, evaluated by us, not certified from within.

## 7. What this means in practice

**Build on the distinction the thread keeps drawing: pointwise vs uniform.**

- **Keep finding witnesses.** The semi-decidable regime is productive
  forever: every circuit found, every feature that passes its checks, is a
  real, locally verifiable fact. Pointwise, interpretability works.
- **Never expect a completeness certificate.** "This model is safe" and
  "this explanation is complete" are exactly the uniform claims the theorems
  rule out. Design for local guarantees: test suites as semi-decision
  procedures, monitors that catch specific failures, and explanations scoped
  to the behavior they were checked against.
- **The escape hatch is restriction.** `4_rice_theorem.md` §3 marks where
  decidability returns: restricted classes of programs. The architectural
  version: bounded expressivity (limited depth, symbolic cores, verifiable
  components) buys back formal guarantees at the price of capability. That
  trade is not engineering conservatism; it is the content of the theorems.
- **Know which level you are on.** Σ₁ questions ("does a feature exist that
  fits this suite?") admit search and Markov-style extraction; Π₁ and beyond
  ("does it ever fail?", "is the circuit complete?") do not. The hierarchy
  table in `4_rice_theorem.md` §4 is a working map for this.

The thread's master pattern, at its most concrete: each shift of viewpoint
changes what is decidable. Interpretability lives in the regime where
pointwise witnesses are plentiful and uniform guarantees do not exist, and
the tools that work there are exactly the semi-decision procedures the
theorems predict.

## Where to go next

- `3_church_trakhtenbrot.md` §2 and §4: the undecidability results §4 applies,
  including the compactness argument for why finiteness is invisible to FOL.
- `4_rice_theorem.md` §1 and §5: the theorem, its scope (restricted classes),
  and the pointwise/uniform reading; §4 for the arithmetical hierarchy table.
- `5_markov_principle.md` §2 and §3: MP as witness extraction for decidable
  searches, and where it stops.
- `6_godel_tarski.md` §2 and §3: Gödel I and Tarski's undefinability, the
  "stating" layer of the limits.
- `2_mere_props.md` §4 and §5: truncation and the pointwise vs uniform gap, in
  their original setting.
- For the interpretability literature itself: T. Bricken et al., "Towards
  Monosemanticity: Decomposing Language Models Into Interpretable Features"
  (Anthropic, 2023); N. Elhage et al., "A Mathematical Framework for
  Transformer Circuits" (2021); P. Barceló et al., "Model Interpretability
  through the Lens of Computational Complexity", NeurIPS 2020 (the complexity
  results behind §5's hardness claims, e.g. NP-hard and #P-hard post-hoc
  explanation queries for deep architectures); M. Lazic, on the Ackermann-level
  complexity of vector addition system reachability (2023), for the
  non-elementary shadow around iterative vector computation.
