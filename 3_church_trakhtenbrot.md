# Undecidability at the Core: Church, Trakhtenbrot, and Deciding Truth

A follow-up to `2_mere_props.md`. That document asked what a logic can *see*
from the inside (inhabitation, internal propositions, choice principles).
This one asks what a logic can *compute*: given an arbitrary first-order
sentence, can we decide whether it is true in every structure? The answers,
due to Church (1936) and Trakhtenbrot (1951), are no, and for the finite
world, no in a strictly stronger sense.

## 1. Setup: validity, models, and two kinds of algorithmic power

First-order logic (FOL) sentences are evaluated in **structures** (models):
a set of elements with relations, functions, and constants interpreted. A
sentence φ is **valid** if it is true in *every* structure:

- `∀x. x = x` is valid.
- `∀x. ∃y. y ≠ x` is not; the one-element structure refutes it.

Two notions of algorithmic tractability, in increasing strength:

- **Recursively enumerable (r.e.), semi-decidable**: an algorithm that
  eventually answers "yes" for every valid φ, but may run forever on inputs
  that are not valid.
- **Decidable**: an algorithm that always halts and answers correctly,
  yes or no.

**Gödel's completeness theorem (1930)** ties validity to proof: φ is valid
if and only if φ has a formal FOL proof. Proofs are mechanically checkable
and can be systematically enumerated, so this yields an r.e. procedure for
validity: enumerate all proofs until one of φ appears. If φ is valid you
will find it; otherwise you search forever.

Hilbert's **Entscheidungsproblem** asked whether the semi-decision can be
upgraded to a full decision procedure.

## 2. Church's theorem (1936): validity is not decidable

**Theorem (Church).** No algorithm decides, for an arbitrary first-order
sentence φ, whether φ is valid.

So the set of valid FOL sentences is r.e. (completeness) but not recursive
(Church). The Entscheidungsproblem has a negative answer; Church's and
Turing's 1936 results are two faces of the same barrier.

**Proof sketch (encoding computation).** The intuitive picture: given any
Turing machine M, build a first-order sentence that says "there exists an
accepting computation history of M on the blank tape", a sequence of
configurations starting from the initial state, each step following M's
transition rule, ending in an accept state. If M accepts, the real history
witnesses the sentence; if M does not accept, no such history exists, so the
negated sentence is valid. Deciding validity would then decide non-halting,
which is impossible.

Making this fully rigorous takes care: an arbitrary structure could try to
*fake* a long computation (non-standard models have no respect for standard
finiteness). The classical proofs therefore encode machines into **finitely
presented monoids**: from M one builds a finite *universal* theory T_M (the
axioms of a monoid presentation whose multiplication encodes M's transition
function) and a sentence σ_M (a word equation), such that

> M accepts ⟺ every model of T_M satisfies σ_M ⟺ (T_M → σ_M) is valid.

The universal (∀-only) form of T_M is what keeps fake computations out, and
it reduces logical consequence to plain validity. Either way the shape of
the argument is the same: **halting many-one reduces to validity**, so
validity is undecidable (in fact r.e.-complete, as hard as the halting
problem itself).

## 3. Trakhtenbrot's theorem (1951): the finite world is worse

Now restrict attention: φ is **finitely valid** if it is true in every
*finite* structure. More sentences are finitely valid than valid; for
example, "every nonempty linear order has a largest element" holds in all
finite linear orders but fails in (ℕ, ≤).

**Theorem (Trakhtenbrot).** The set of finitely valid sentences is **not
recursively enumerable**. There is no semi-decision procedure at all: no
algorithm can enumerate the finitely valid sentences, nor their complement.

**Proof sketch.** Use the computation-history idea again, now with the
saving observation that *finite* models cannot fake computations (every
element of a finite structure sits at some standard distance from every
other):

1. From M, build ψ_M saying "there is an accepting computation history of
   M". Then M accepts ⟺ ψ_M has a **finite** model (the accepting history is
   finite, and the substructure it generates is a finite model). So the
   halting problem reduces to "has a finite model", the complement of
   finite validity.
2. If finite validity were r.e., then "has a finite model" would be
   co-r.e.
3. Then both the halting problem and its complement would be r.e., making
   the halting problem decidable. Contradiction.

**Why finiteness is the enemy: compactness.** A standard fact (compactness
plus Löwenheim-Skolem):

> φ has an infinite model if and only if, for every n, φ has a model of
> size at least n.

First-order logic cannot tell finite from infinite: **no single first-order
sentence is true in exactly the finite structures** (if σ were, then ¬σ
would have an infinite model but no finite one, contradicting the fact
above). The class "finite structures" is invisible from inside FOL, and the
validity problem for that externally defined class is strictly harder than
the full one.

## 4. A summary of what is decidable

| Question | Status |
|---|---|
| Validity in propositional logic | Decidable (truth tables; PSPACE-complete) |
| Validity in FOL, all structures | r.e.-complete: undecidable, but semi-decidable (Gödel + Church) |
| Satisfiability in FOL ("φ has some model") | co-r.e.-complete: undecidable, and not even semi-decidable in the positive direction |
| Finite validity (all finite structures) | **Not r.e. at all** (Trakhtenbrot) |
| "φ has an infinite model" | Σ¹₁-complete: complete for the first level of the analytical hierarchy (Kreisel) |

The last row says how bad "not r.e." can get: the set of sentences with an
infinite model is as complicated as possible at the first level of the
analytical hierarchy. In particular it is not *arithmetical*: no oracle for
the halting problem, or any finite iteration of it, can decide these
questions. Undecidability here is not a mild deficiency; at this level it
is maximal in a precise sense.

## 5. How this relates to our thread

**Inhabitation is undecidable.** Under Curry-Howard, "φ is valid" means
"the proof type of φ is inhabited". Church's theorem says there is no
algorithm that decides whether an arbitrary first-order proposition has a
proof. In the vocabulary of `1_homotopy_intro.md`:
*inhabitation of proof types is already undecidable at the level of plain
first-order logic.* Every later result in this family (undecidability of
type inhabitation for ML with recursion, for System F, Rice's theorem on
program behavior) inherits its hardness from exactly this core.

**The internal/external pattern continues.** `2_mere_props.md` showed that the
internal logic of Set loses principles relative to the external logic (WSC
fails internally; LEM holds only as a weak choice principle in disguise, by
Diaconescu-Goodman-Myhill). Church and Trakhtenbrot add two more data
points to the same pattern:

- First-order logic itself (the internal logic of any topos) has no
  decision procedure for its own valid sentences.
- Restricting models to the externally defined class of *finite* structures
  (a restriction FOL itself cannot express) makes the validity problem
  strictly worse: from "r.e. but undecidable" to "not r.e."

Each shift of perspective (external meta-logic ↔ internal logic; all
structures ↔ finite structures) changes what is decidable. Undecidability
is not an artifact of an overly rich system; it is present in the bare
first-order core.

**Mere propositions do not save you.** The 0-level of logic (propositional
logic, where truth values are fixed bits and everything is "mere") is
decidable by truth tables. Undecidability enters exactly at the first-order
model quantifier: "∀M. M ⊨ φ" ranges over all structures, and that
unbounded universal quantification is what pushes the problem out of reach.
Restricting to mere props keeps the *content* tame; it does not tame the
*model quantifier*.

## 6. Summary in one paragraph

Gödel's completeness theorem made validity semi-decidable: valid sentences
are exactly the provable ones, and proofs can be enumerated. Church showed
the semi-decision cannot be upgraded to a decision: computations encode into
first-order structures, so deciding validity would decide the halting
problem. Trakhtenbrot went further: restricting to finite structures
destroys even enumerability, because "has a finite model" directly encodes
halting (finite models cannot fake computations) while compactness makes
finiteness invisible to FOL itself. In the language of our thread:
inhabitation of proof types is undecidable at the core, and every shift
between internal and external viewpoints changes exactly which questions
remain decidable.

## Where to go next

- A. Church, "An unsolvable problem of elementary number theory", American
  Journal of Mathematics 58 (1936), 345-363.
- A. M. Turing, "On computable numbers, with an application to the
  Entscheidungsproblem", Proc. London Math. Soc. 2(43) (1936), 284-309.
- B. Trakhtenbrot, "Über die Unmöglichkeit einer Normalformentheorie für
  die endlichen Relationenbereiche" (1951).
- For the finite-model side in detail: H. Ebbinghaus and T. Flum, "Finite
  Model Theory", Springer, 1995.
- nLab entries: "Entscheidungsproblem", "analytical hierarchy",
  "recursively enumerable set".
- Back to `2_mere_props.md` for the internal/external contrast this document
  extends.
