# Rice's Theorem: No Nontrivial Property of What a Program Computes Is Decidable

The fourth doc in the sequence. `3_church_trakhtenbrot.md` located
undecidability at the core of first-order logic and noted, in passing, that
"Rice's theorem on program behavior" inherits its hardness from that core.
This note makes that link explicit: Rice's theorem (1953) is the
program-theoretic completion of the story, and it shows exactly which program
properties a witness-extraction principle can reach, the line
`5_markov_principle.md` draws next.

**Halting is not a special pathological case; it is one instance of a
general no-go theorem.** Any property of programs that depends only on the
function they compute (is "semantic") and is nontrivial (some computable
functions have it, some do not) is undecidable. The reduction from halting
to any such property is uniform and mechanical, so every one of them
inherits the full hardness of the halting problem.

Roadmap: §1 states the theorem and pins down what "semantic" means
(intension vs. extension). §2 proves it with a single gated-program
construction (with annotated code). §3 marks what the theorem does not
cover. §4 stratifies program properties in the arithmetical hierarchy and
shows where `5_markov_principle.md`'s witness extraction reaches. §5 closes
the thread's internal/external pattern.

## 1. Statement: what "semantic" means

**A program has two faces: its code (the intension) and the function it
computes (the extension). Rice's theorem says no algorithm can read off any
nontrivial fact about the extension from the intension.**

A program computes a partial function ℕ ⇀ ℕ: defined on some inputs,
diverging on others. Two programs are **extensionally equivalent** if they
compute the same function: different code, same function (e.g. `x + 1` and
`1 + x`).

- A property S of programs is **semantic** (extensional) if it depends only
  on the computed function: whenever two programs compute the same
  function, either both are in S or neither is. Equivalently, S is a union
  of equivalence classes of the "computes the same function" relation.
- S is **nontrivial** if some computable functions lie in S and some do
  not.

**Theorem (Rice, 1953).** Every nontrivial semantic property of programs is
undecidable: no algorithm decides, given arbitrary program code e, whether
the function computed by e lies in S.

The same content is often packaged as the **Myhill-Shepherdson theorem**:
any decidable property of programs that depends only on the computed
function must be trivial.

Examples of nontrivial semantic properties (all undecidable):

- "halts on input 0" (depends only on whether the function is defined at
  0)
- "is total" (defined on every input)
- "computes the zero function"
- "computes the same function as a fixed program p"

The contrast case: **syntactic** properties like "the source contains no
while loops" or "uses fewer than 100 variables" are decidable, and they are
not semantic: two extensionally equal programs can differ on them. The
theorem's scope is exactly the extensional properties.

## 2. Proof: one gated construction

**One uniform construction reduces halting to any nontrivial S: build a
program whose behavior is gated on the target computation.**

Fix a nontrivial S, and let ∅ denote the everywhere-divergent function.
There are two cases, and they use the same construction.

- **Case A: ∅ is not in S.** Pick some f_in ∈ S. Given e, effectively build
  g_e that, on input x, first simulates φ_e(e); if (and only if) it halts,
  compute f_in(x). Then:

  > e halts on its own input ⟺ g_e = f_in ∈ S; otherwise g_e = ∅ ∉ S.

  So halting many-one reduces to S.
- **Case B: ∅ is in S.** Pick f_out ∉ S and gate on it instead. Then:

  > e halts on its own input ⟺ g_e = f_out ∉ S, i.e. g_e lies in the
  > complement of S.

  So halting many-one reduces to the complement of S.

Either way, S is undecidable. The construction is uniform: from e one can
mechanically produce the code of g_e (this is the **s-m-n theorem** doing
its job), so the reduction is a genuine many-one reduction. When S itself
is r.e. (for example "halts on input 0"), it is r.e.-complete: as hard as
the halting problem itself.

The construction, as runnable code:

```python
# Rice's reduction, as a program.
#
# Formally, programs are natural-number indices e of partial computable
# functions phi_e : N -> N, and the s-m-n theorem gives an effective map
# e -> index(g_e). Here we pass the program object directly: the same
# construction, without the encoding.

def zero(x):
    return 0

def loops(i):
    while True:
        pass

def make_g(e_prog, e_input, target):
    # g_e(x): first run the target computation e_prog(e_input). If it
    # halts, behave like `target`; if it diverges, g_e diverges on every
    # input, because the simulation never returns.
    def g(x):
        e_prog(e_input)
        return target(x)
    return g

# S = "computes the zero function": nontrivial, and the everywhere
# divergent function is not in S (case A of the proof).

g_halts = make_g(lambda i: 0, 0, zero)   # target computation halts
print("g_halts(7) =", g_halts(7))        # -> 0 : g_e computes zero, so g_e is in S
assert g_halts(7) == 0

# make_g(loops, 0, zero)(7) would block forever: the simulation never
# returns, so that g_e is the everywhere divergent function, not in S.
```

## 3. What Rice does not cover

**The theorem's scope is exactly "arbitrary program + extensional
property"; step outside either leg and decidability can return.**

- **Syntactic (intensional) properties.** "Contains no while loops" is
  decidable, and it is not semantic: extensionally equal programs can
  differ on it. The theorem says nothing against such properties.
- **Restricted classes of programs.** When the input is a restricted formal
  object rather than an arbitrary program, semantic questions can be
  decidable: "does this regular expression match any string?" is decidable
  (automaton emptiness); "does this loop-free arithmetic program compute
  zero?" is decidable by evaluation. The undecidability comes from the
  input being able to compute anything.
- **Undecidable does not mean not semi-decidable.** "Halts on input 0" is
  r.e. (simulate). Rice gives a lower bound: every nontrivial semantic
  property is at least as hard as halting. How much harder it can get is
  the subject of §4.

## 4. The hierarchy: where each property sits, and where MP reaches

**Program properties stratify in the arithmetical hierarchy; witness
extraction (`5_markov_principle.md`) reaches exactly the bottom level,
Σ₁.**

| Property of φ_e | Form | Level | Semi-decidable? |
|---|---|---|---|
| halts on input 0 | ∃s. decidable check | Σ₁ | yes: simulate |
| computes the zero function | ∀x ∃s. decidable check | Π₂ | no |
| is total | ∀x ∃s. decidable check | Π₂ | no |
| equivalent to a fixed program | ∀x [Π₁ in x] | Π₂ | no |

("Decidable check" means a bounded simulation, e.g. "φ_e(x) halts with
value 0 within s steps." For the last row: "both diverge, or both halt with
equal values" is Π₁ in x, since it is the negation of two Σ₁ conditions,
"exactly one halts" and "both halt with different values".)

Two readings of this table:

- **The Σ₁ row is exactly where Markov's principle operates.** "Halts on
  input 0" is ∃s of a decidable predicate, the canonical instance of MP in
  `5_markov_principle.md` §3 ("if a machine does not run forever, it
  halts"); the witness is a runtime bound s. One level up (Π₁, e.g. "does
  not halt on input 0") and two levels up (Π₂: totality, equivalence), no
  witness-extraction principle reaches: the same boundary doc 5 drew for
  Trakhtenbrot's finite validity.
- **The Rice-Shapiro theorem pins down exactly which semantic properties
  are semi-decidable:** for a class of functions whose finite fragments
  form an r.e. set, membership is semi-decidable exactly when it is
  determined by positive finite evidence, i.e. f lies in the class exactly
  when some finite fragment of its graph already forces it. "Halts on 0"
  qualifies (a value at 0 is the witness); "computes zero" does not (no
  finite fragment forces an infinite pattern).

## 5. How this closes the thread

**Pointwise classical, uniformly not: the `2_mere_props.md` pattern again.**
For each fixed program e and property S, "φ_e ∈ S" is a mere proposition
(subsingleton): either the computed function has S or it does not, at most
one truth value. Externally, each case can be settled by inspection. What
Rice rules out is the *uniform* decider over all e: exactly the gap between
pointwise and uniform that `2_mere_props.md` §3 identified for WSC.

**Intension vs. extension: another internal/external pair.** The code is
what an algorithm manipulates; the computed function is the semantic
content it is supposed to reflect. Rice says the reflection has no
nontrivial decidable shadow: from the intension, no algorithm can read off
any nontrivial fact about the extension.

**The reduction chain, complete.** Validity (Church,
`3_church_trakhtenbrot.md` §2) many-one reduces to halting, and halting
many-one reduces to every nontrivial semantic property (this note). The
forward reference in `3_church_trakhtenbrot.md` §5 now has its explicit
middle link: Rice's theorem inherits its hardness from the same first-order
core.

**The witness line.** Σ₁ properties are exactly the shape where MP-style
witness extraction applies; Π₁ and Π₂ properties are beyond any such
principle. Computation outputs witnesses, but no uniform procedure can even
decide whether a witness exists for any nontrivial semantic question.

## 6. Summary in one paragraph

Rice's theorem completes the undecidability story at the level of programs:
any property that depends only on the function a program computes, and is
nontrivial, is undecidable, because halting many-one reduces to it via a
single uniform construction (a program gated on the target computation).
The theorem's scope is exactly extensional properties of arbitrary
programs; syntactic properties and restricted classes of programs sit
outside it. Program properties stratify in the arithmetical hierarchy: Σ₁
properties like "halts on 0" are semi-decidable and are exactly where
Markov-principle-style witness extraction reaches, while Π₂ properties like
totality and equivalence are beyond any such principle. In the thread's
vocabulary: pointwise, each program's fate is a mere proposition, but no
uniform decider exists; the code (intension) carries no nontrivial
decidable shadow of the function it computes (extension); and the chain
validity → halting → every nontrivial semantic property shows that all
these undecidabilities inherit their hardness from the same first-order
core.

## Where to go next

- B. A. Rice, "Classes of recursively enumerable sets and their decision
  problems", Transactions of the American Mathematical Society 74 (1953),
  356-364.
- H. Rogers Jr., *Theory of Recursive Functions and Effective
  Computability* (MIT Press, 1967): the standard textbook; Rice's theorem
  and the arithmetical hierarchy in its chapter on undecidability
  theorems.
- Back to `3_church_trakhtenbrot.md` §2 for the reduction machinery and §5
  for the forward reference this note pays off.
- Back to `5_markov_principle.md` §3 for where witness extraction reaches
  and stops.
- Back to `2_mere_props.md` §3 for the pointwise vs. uniform distinction
  that the theorem instantiates again.
