# Markov's Principle: When a Search Cannot Fail, It Succeeds

`1_homotopy_intro.md` built the bridge between types and spaces;
`2_mere_props.md` showed that classical principles are choice principles in
disguise; `3_church_trakhtenbrot.md` and `4_rice_theorem.md` located
undecidability at the core, for sentences and programs alike. This document
ties them together through a single principle, **Markov's principle (MP)**.

**MP is the smallest slice of classical reasoning that computation actually
needs.** For a decidable predicate P on ℕ, "the search cannot be shown to
fail" (¬¬∃n. P(n)) already implies "the search succeeds" (∃n. P(n)), with
the witness extractable. It sits exactly at the boundary between "a truth
value" and "a witness": the Russian school accepts it on computational
grounds, Brouwer's school rejects it, and intuitionistic arithmetic cannot
settle it either way.

The argument runs in five steps: §1 gives constructive math in one page
(proofs as constructions, and why double negation and excluded middle are no
longer free). §2 states MP and three facts about it. §3 reads it
computationally: semi-decidability plus impossibility of failure yields an
actual result, and where the principle stops. §4 contrasts computation with
declarative frameworks that throw witnesses away (Codd's relational model).
§5 positions MP exactly between unique choice and WSC among the choice
principles.

## 1. Constructive math in one page

**Proofs are constructions, and each connective says what shape a proof must
have; the two classical habits that break are double-negation elimination
and excluded middle.**

Classical math treats propositions as true or false, with proofs as
certificates we can ignore. Constructive math (the BHK reading, after
Brouwer, Heyting, Kolmogorov) takes **proofs to be constructions**, and the
meaning of each connective is what a proof must compute:

| Connective | What a proof must be |
|---|---|
| `A ∧ B` | a pair: a proof of A and a proof of B |
| `A ∨ B` | a tag saying which side, plus a proof of that side |
| `∃n. P(n)` | a specific n, together with a proof of P(n) |
| `A → B` | a function turning proofs of A into proofs of B |
| `¬A` | the same as `A → ⊥`: any proof of A yields absurdity |

Two classical habits now break.

**Double negation is not the identity.** `¬¬A` says "every attempt to refute
A fails." That is a statement about refutations, not a construction of A.
Classically we read it as "A is true"; constructively it is strictly weaker.
Knowing that a search cannot be shown to fail does not hand you the answer;
it only rules out one kind of bad news.

**Excluded middle is not a law of logic.** `P ∨ ¬P` demands an actual
decision procedure for P. For P = "machine M halts," no such procedure
exists (see `3_church_trakhtenbrot.md`), so LEM cannot be proved in general.
And per `2_mere_props.md`, even where LEM does hold (inside Set), it holds for
a choice-theoretic reason: by Diaconescu-Goodman-Myhill, LEM is equivalent
to "every surjection from 2 splits," a weak form of choice. Classical logic
is not the default setting; it is what you get when certain choice
principles are available.

In the vocabulary of `1_homotopy_intro.md`: classically, every proposition
behaves as a mere prop (a bit), and the content of proofs is discarded;
constructively, proofs are first-class points, and the connectives tell you
what shape they must have. The constructive position is not that classical
truths are false, but that they carry no witness: a classical proof of
`∃n. P(n)` may contain no n at all.

## 2. Markov's principle

**MP.** Let P : ℕ → Prop be decidable (for each n we can check P(n) or
¬P(n)). Then

> ¬¬∃n. P(n) implies ∃n. P(n).

In words: if a decidable search on the natural numbers cannot be shown to
fail, then it succeeds, and the witness can be extracted. MP was introduced
by Andrey Markov (Jr.), of the Russian school, in his work on infinite
sequences of digits in the late 1920s.

Three facts about MP:

- **Trivial classically.** Since ¬¬A ≡ A, MP is just an instance of
  double-negation elimination.
- **Not derivable constructively.** MP is independent of intuitionistic
  arithmetic (HA): neither provable nor refutable there (see Troelstra and
  van Dalen). In particular, the Brouwer school rejects it.
- **Where the rejection comes from.** Brouwer's continuity principle
  (formalized today as Kripke's schema) says that any fact about an infinite
  binary sequence α is decided by finitely much of α. Let P(n) be "α takes
  value 1 at position n," which is decidable. Then "α contains a 1" cannot
  be proved (the all-zero sequence is still possible), but its double
  negation can: if α were all zeros, that fact would be decided by some
  finite initial segment, and extending that segment with a single 1
  contradicts it. So Brouwer's system proves ¬¬∃n. P(n) without ∃n. P(n):
  MP fails there. The Russian school accepts MP, on the grounds of §3 below.

MP is double-negation elimination restricted to **Σ₁ propositions**, i.e.
statements of the form "∃n. P(n)" with decidable P (the first level of the
arithmetical hierarchy). It is the smallest classical fragment that talks
about actual searches.

## 3. The computation reading: a search that cannot fail, succeeds

**MP's content is internal: externally a decidable search either halts or
diverges anyway, but given a proof h : ¬¬∃n. P(n) as an object of the
theory, MP is what licenses extracting the witness n.**

For decidable P there is an actual algorithm: check P(0), P(1), P(2), … in
order. It halts exactly when some n satisfies P(n), and then outputs the
least such n. Externally (in the classical metatheory), for each concrete P
this search either halts or diverges, so MP looks trivial here; its content
is internal. Given a *proof* h : ¬¬∃n. P(n) as an object of the theory, MP
asserts that a witness n can be obtained from h. HA alone cannot perform
this extraction; MP licenses it. This is precisely the Russian school's
justification: set a computer working, deciding P(0), P(1), P(2), …, until
it finds n; and "impossible that it continues forever" is exactly ¬¬∃n.
P(n). Other constructivists object that no argument establishes the machine
will stop, only that its not stopping is impossible: that objection is
exactly what MP settles in favor of the Russian school.

This is the same shape as the results in `3_church_trakhtenbrot.md`:

- **Semi-decidability + impossibility of failure = actual result.** Take
  P(p) = "p codes a valid first-order proof of φ." Proof checking is
  decidable, so this is a genuine Σ₁ predicate, and by Gödel's completeness
  theorem "there is a proof of φ" is equivalent to "φ is valid." MP says:
  if ¬¬(φ is valid), i.e. validity cannot be refuted, then there is an
  actual proof of φ, and the proof enumeration from §1 of
  `3_church_trakhtenbrot.md` will find it.
- **The halting problem is the canonical instance.** The standard
  equivalent form of MP: "if a Turing machine does not run forever, then it
  halts." Here P(n) = "M halts within n steps," decidable; the witness is a
  halting time (and hence a full accepting computation history).
- **Termination is the everyday face.** "This loop cannot run forever" is a
  ¬¬-statement about a decidable event; MP is the license to conclude "it
  terminates, and here is the output." In practice, proof assistants usually
  ask for a direct termination argument (a decreasing measure, structural
  recursion), which is the constructive way to avoid needing MP at all.
- **Witnesses are what computation outputs.** The annotated code in
  `1_homotopy_intro.md` embodies this: it computes winding numbers as
  first-class data, not merely "a loop exists". A terminating search is a
  proof with the witness attached.

Note where MP stops: it needs an actual search, i.e. a decidable predicate
on ℕ. Trakhtenbrot's finite validity lies on the other side of that line:
"φ has no finite model" is a universal statement over sizes n (Π₁ in the
arithmetical hierarchy), not an existential one, and finite validity is not
even semi-decidable, so there is no enumeration for MP to operate on. Some
undecidabilities are beyond the reach of any witness-extraction principle.

## 4. The real-world contrast: declarative semantics throw away witnesses

**The same witness discipline separates computation from the declarative
frameworks built on it: a finished program carries its output, while
Codd's relational model evaluates existentials as bare truth values and
discards the witness row.**

Computation is inherently constructive: a program that finishes has produced
its output, and an existential claim backed by a finished search carries its
witness. But many of the frameworks we actually use are not constructive at
the semantic level. The clearest example is **Codd's relational model**:

- A relation is a *set* of tuples; a query in relational calculus is a
  first-order formula over that set.
- In this semantics, "∃t. φ(t)" is evaluated as an external truth value:
  yes or no. The tuple t is not carried along. SQL's `EXISTS (…)` literally
  returns a Boolean and discards the witness row.
- So when a database says "this query result is nonempty," it has asserted
  `∥X∥`, mere inhabitation in the vocabulary of `2_mere_props.md`, without
  producing an element of X. The step from "the set is nonempty" to "here is
  a specific row" (a `SELECT … LIMIT 1`, or the first row a scan happens to
  hit) is exactly the choice step that the declarative semantics never
  formalizes; which row you get depends on implementation details, precisely
  the "witness is arbitrary" situation that choice principles study.

The same pattern appears in classical mathematics itself: "there exists a
fixed point," proved by a compactness argument, gives no fixed point; "the
equation has a solution," proved by a counting argument, gives no solution.
The witness-extraction gap is not a defect of any particular system; it is
the precise distance between a truth value and a construction, and MP is one
of the standard principles for closing it in specific, computable
situations.

## 5. Exactly where MP sits: between unique choice and WSC

**MP sits between unique choice (provably free, since uniqueness removes the
need for choice) and WSC (which fails internally in Set): for decidable
subtypes of ℕ, MP implies the corresponding WSC fragment, because ¬¬p is
strictly weaker input than p.**

`2_mere_props.md` introduced WSC ("every merely inhabited set is inhabited,"
∥X∥ → X uniformly in X : U), which fails internally in Set (Fourman and
Ščedrov) and externalizes to AC. How does MP relate? Precisely, as follows.

**Fact 1: truncation and double negation are different inputs.** For any
type A there is a canonical map ∥A∥ → ¬¬A (mere inhabitation implies "cannot
be shown empty"), but not conversely in general: the Brouwer counterexample
in §2 is exactly a case where ¬¬∃n. P(n) is provable while ∃n. P(n) is not,
so "cannot be shown empty" is strictly weaker information than "merely
inhabited."

**Fact 2: MP implies the corresponding WSC fragment.** For decidable P, let
p_P be the mere proposition "∃n. P(n)" (that is, ∥{n | P(n)}∥).

- The WSC instance for the subtype {n | P(n)} says: p_P → witness.
- MP says: ¬¬p_P → witness.

Since p_P always implies ¬¬p_P (and not conversely), MP takes strictly
weaker input and gives the same output: **MP implies the WSC fragment for
decidable subtypes of ℕ.** The converse is not a logical consequence, and
the Brouwer setting separates them: there, "if α contains a 1, we can find
one" is provable (by the same continuity argument, the witness must lie in
the finite segment that decides the fact), while the passage from ¬¬∃ to ∃
is exactly what fails. The parenthetical is a sketch of the standard
continuity argument, not a formal proof: the principle used is that a
proof of a decidable property of a choice sequence is decided by some
finite initial segment (Kripke's schema), and for "contains a 1" that
segment must itself contain the witness. For the rigorous version, see
Troelstra and van Dalen, cited below.

**Fact 3: unique choice is free, and MP is what's left over.** Choice
principles stratify by the structure of what you are choosing from:

| What you're choosing from | Input | Principle | Status |
|---|---|---|---|
| A mere proposition P (subsingleton) | ∥P∥ | None: ∥P∥ ≃ P, since P has at most one point | Provably free |
| A family of singletons (all fibers mere props) | each fiber merely inhabited | unique choice | provable in HoTT, no axiom needed (HoTT book, Ch. 4) |
| A decidable search {n \| P(n)} on ℕ | ¬¬"∃n. P(n)" | Markov's principle | independent of HA; rejected by Brouwer, accepted by the Russian school |
| An arbitrary type X : U | ∥X∥ | WSC | fails internally in Set (Fourman-Ščedrov); externalizes to AC |

Read top to bottom: as the chosen-from object gains structure (from "at most
one point" to "arbitrarily many points"), the free principles run out and
you need more and more. At the singleton level, uniqueness does all the
work, so choice is provable: a merely inhabited mere prop is inhabited,
because there is at most one thing to be. At the Σ₁ level, decidability
gives you an actual search, and MP is exactly the principle that says a
search that cannot be shown to fail must have succeeded. At the fully
general level, you are back at WSC / AC, which Set's internal logic refuses
unless the metatheory has choice.

Also note what even full WSC does not give: it eliminates truncations
(∥X∥ → X), but it does not eliminate double negations (¬¬A → A). The two
operations, "forget which point" and "negate twice," coincide only in the
classical setting, and keeping them separate is another instance of the
internal/external discipline that runs through this thread.

## 6. Summary in one paragraph

Constructive math reads proofs as constructions: an existence proof must
carry its witness, so double negation and excluded middle are no longer
free. Markov's principle is the smallest slice of classical reasoning that
survives this scrutiny: for a decidable predicate P on ℕ, "the search cannot
be shown to fail" (¬¬∃n. P(n)) already implies "the search succeeds"
(∃n. P(n)), with the witness extractable. This is exactly what computation
does all the time: semi-decidability plus an impossibility-of-failure
argument yields an actual result, and a terminating program outputs its
witness, while declarative frameworks like Codd's relational model evaluate
existentials as bare truth values and throw the witness away. Precisely
positioned among choice principles, MP sits between unique choice (provable,
since uniqueness removes the need for choice) and WSC (which fails internally
in Set and externalizes to AC): for decidable subtypes of ℕ, MP implies the
corresponding WSC fragment because ¬¬p is weaker input than p, while the
reverse direction would require double-negation elimination. The moral this
document contributes: classical principles are stratified choice principles,
computation is the regime where witnesses are mandatory, and undecidability
marks the boundary of how far any witness-extraction can be automated.

## Where to go next

- A. S. Troelstra and D. van Dalen, *Constructivism in Mathematics*, Vol. 1
  (North-Holland, 1988): the standard reference; MP is treated in the
  chapters on intuitionistic arithmetic (its independence from HA, and
  Brouwer's counterexample via continuity / Kripke's schema).
- D. Bridges and F. Richman, *Varieties of Constructive Mathematics*
  (Springer, 1987): a compact comparison of the schools, including where
  MP is accepted and rejected.
- E. F. Codd, "A relational model of data for large shared data banks",
  Communications of the ACM 13 (1970), 377-387: the original statement of
  the relational model whose semantics §4 analyzes.
- The HoTT book, Chapter 4: truncations, hProps, and the discussion of
  choice principles (unique choice is provable; full AC is an axiom).
- nLab entries: "Markov's principle" (with weak and analytic variants),
  "double negation", "propositional truncation".
- Back to `2_mere_props.md` §3 for WSC and the internal/external distinction
  this document extends.
- Back to `3_church_trakhtenbrot.md` §1 and §5 for the semi-decidability
  results that MP operates on.
- `1_homotopy_intro.md`: the annotated code in §1 (decidable inhabitation by
  inspection) and §3 (winding numbers as first-class data, witnesses you
  can hold).
