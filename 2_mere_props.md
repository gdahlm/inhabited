# The Logic Inside Sets, and Why Mere Propositions Are Subsingletons

A gentle follow-up to `1_homotopy_intro.md`. Classical logic says every
proposition is either true or false. Homotopy type theory says propositions
are spaces with points and paths. Where do the two views disagree, and where
do they secretly agree?

**They agree exactly on mere propositions, and those are precisely the
subsingletons.** A mere proposition is a type with at most one point; a
subsingleton is a set with at most one element. Two names, one property, and
it marks the boundary where homotopical logic collapses back into classical
behavior.

**They diverge on uniformity, not shape.** Pointwise, every subsingleton
really is either empty or a singleton, so classical logic seems to "just
work". But the internal logic of sets asks for *uniform* witnesses: one
construction that works for all propositions at once. Under that demand,
excluded middle turns out to be a weak choice principle in disguise
(Diaconescu-Goodman-Myhill), and the "world's simplest axiom of choice" fails
outright inside Set (Fourman and Ščedrov 1982).

This note builds the comparison piece by piece: §1 sets up the two
viewpoints; §2 describes what truth looks like from inside sets; §3 shows how
classical principles become choice principles there; §4 steps outside sets,
to types whose proofs can differ; §5 lands on the mere props = subsingletons
identification and why it matters. §6-8 collect a side-by-side table, the
practical payoff, and a summary.

## 1. Two viewpoints: truth from the outside vs. the inside

Nothing is wrong with classical logic; it just puts the truth values
*outside* the world being described. In everyday (classical) mathematics:

- "2 + 2 = 4" is either true or false. We don't carry proofs around as
  objects; we just use the fact.
- Truth values form a two-element set {T, F}, and every proposition picks one
  of the two.
- Two things follow that we never think about: **excluded middle** (P or
  not-P), and **proof irrelevance** (if one person hands you a proof of P and
  another hands you a "different" proof, there is no sense in which they
  differ). There is only one truth value to be.

The truth values are not part of the world being described; they are the
stage lighting. The internal logic of set theory takes the opposite stance:
propositions live *inside* the world, as objects of it. This note compares
the two.

## 2. The internal logic of sets: truth from the inside

**Inside set theory, propositions are subsingletons and predicates are
injections; proof irrelevance holds for a visible structural reason.**

- **Subsingletons represent the internal propositions.** A subsingleton is a
  set S with at most one element: every two elements a, b of S are equal.
- **Families of subsingletons represent the internal predicates.** A family
  of sets indexed by A is given by the fibers of a function f : B -> A, so a
  family of *subsingletons* is exactly an injection f : B into A.
- **The empty set represents false**, and **singletons represent true**. A
  singleton is a pointed subsingleton: a set S with a specified element a
  such that every other element b equals a.
- Dually, a family of *singletons* is exactly a bijection f : A ~ B.

So "P is true" internally means "the subsingleton P has an element", and a
true proposition has exactly one element. That is why proof irrelevance holds
for internal propositions, for a visible structural reason: any two proofs
are literally the same element.

## 3. Where classical principles become choice principles

**Externally everything works pointwise; internally, the uniform version
fails or hides a choice principle.**

The naive story breaks here. From our metatheory, every subsingleton really
is either empty or a singleton, so classical logic seems to "just work". The
*internal* logic, however, asks for *uniform* witnesses: one construction
that works for all propositions at once.

- **Excluded middle is not a free logical truth.** By the
  Diaconescu-Goodman-Myhill theorem, PEM holds if and only if every
  surjection with domain 2 splits (equivalently, 2 is projective). The proof
  is constructive: given any truth value p, quotient 2 = {0, 1} by
  identifying 0 ~ 1 exactly when p holds; the resulting surjection 2 -> A
  splits if and only if p is decidable. So "excluded middle" and "a very weak
  form of choice" are two names for one principle. In Set it happens to hold
  (a surjection from a two-element set splits by inspection, no full choice
  needed), so the first-order internal logic of Set is classical. But it is
  classical for a *choice-theoretic* reason, not a logical one.
- **One level up, even weaker principles fail.** Quantifying over *all* sets
  X : U (not just truth values), the "world's simplest axiom of choice" (WSC)
  says: every merely nonempty set is nonempty, i.e. ∥X∥ implies that X has a
  specific element, uniformly in X. This is not valid in the internal
  language of Set (Fourman and Ščedrov 1982): externalized over an index set,
  WSC is just the axiom of choice itself, so it holds internally exactly when
  the metatheory has choice. Pointwise, each X really is empty or nonempty;
  what fails is the *uniform* operation over all X.

The moral: ordinary classical mathematics is the *external* (metatheoretic)
logic of sets. The internal logic agrees with it at the level of truth values
(Set is a Boolean topos, equivalently 2 is projective), but already at the
level of families of sets it stops being classical.

## 4. Stepping outside sets: when proofs can differ

**HoTT types can carry higher structure; the internal logic of sets is what
you get when you can see points but not paths.**

In homotopy type theory, types can have higher structure: points, paths
between points, even paths between paths. Then "P is true" means "the space P
has a point", and a space may have *many* points connected in interesting
ways.

A concrete contrast (the code that computes it is in `1_homotopy_intro.md`
§3):

- On the circle S¹, the identity type `base = base` is the space of loops at
  the basepoint. It has many inhabitants: one for each winding number,
  classified by ℤ. Two different loops are genuinely different points. So
  `base = base` on S¹ is **not** a mere proposition.
- On Nat (which *is* a set), the identity type `m = n` has at most one
  inhabitant: if two natural numbers are equal, there is exactly one way for
  that to be so. No loops, no winding, nothing extra.

The full homotopical world sees more than Set does.

## 5. Mere propositions: the boundary where the two logics agree

**P is a mere proposition exactly when P is a subsingleton. That is a naming
fact, not a deep theorem.**

Definition in plain words: a type P is a **mere proposition** if any two of
its inhabitants are connected by a path, equivalently, if there is no way to
have two distinct inhabitants. In short: **P has at most one point.**

That last sentence is exactly what "subsingleton" means:

> P is a mere proposition ⟺ P is a subsingleton ⟺ for any p, q : P there is
> a path from p to q.

Two names, one property. "Mere prop" emphasizes the logical side (P behaves
like an ordinary true/false statement); "subsingleton" emphasizes the shape
side (P has at most one point).

Why "mere"? Because P may be nonempty and still be mere: all of its points
are glued together into one. There is existence, but no choice of *which*;
no structure left to inspect.

**Where sets sit in this picture.** A type is a **set** (0-truncated) exactly
when all of its identity types are mere props: any two proofs of an equality
are themselves equal. Set contains only sets, so **every proposition of Set
is a mere prop**, i.e. a subsingleton (the structural reason is in §2).
Therefore, at the propositional level: **the internal propositions of sets
are exactly the mere props.** This identification tells you what propositions
*are*; it does not by itself grant any classical principle about them. Which
principles hold is a separate question, and §3 shows it has a subtle answer.

## 6. A worked contrast: the circle again

| Statement | World | What it looks like |
|---|---|---|
| "S¹ is inhabited" | full HoTT | a mere prop (∥S¹∥): true, with at most one way to be true up to path, even though S¹ itself has a whole circle's worth of points |
| `base = base` on S¹ | full HoTT | **not** a mere prop: its inhabitants are the winding numbers ℤ |
| "2 + 2 = 4" in Nat | Set or HoTT | a mere prop either way: Nat is a set, so at most one proof |
| "P or not P" (P a truth value) | Set | holds, but not for free: by Diaconescu-Goodman-Myhill it is equivalent to "every surjection from 2 splits", a weak choice principle that Set happens to satisfy |
| WSC: ∥X∥ → X inhabited, uniformly in X : U | Set | fails internally; externalized, it is the axiom of choice itself (Fourman and Ščedrov 1982) |
| "P or not P" | full HoTT | not derivable in general, not even for mere props; it is an extra axiom there |

Read these rows together and the picture sharpens: "P is empty or a
singleton" is true *pointwise* about each subsingleton, but the *uniform*
statement over all propositions, let alone all sets, is a choice principle in
disguise. Internal logic is the logic of uniform witnesses; external logic
inspects each case by hand. That gap, not the shape of propositions, is where
classical and internal logic actually diverge.

## 7. Why bother marking mere props?

- **Proof irrelevance for free.** Once you know P is a mere prop, any two
  proofs of P are equal, so you may swap them without thinking. This is what
  ordinary mathematics assumes about *all* propositions; in HoTT you get it
  exactly where you have certified it.
- **Truncation: forgetting structure.** Given any type A, its
  **propositional truncation** ∥A∥ is the closest mere prop to A: "there
  exists an element of A", without remembering *which* one. ∥S¹∥ remembers
  that the circle has a point and forgets the entire loop space.
- **A hygiene marker.** Writing `P : hProp` tells readers and type-checkers
  alike: this statement behaves classically; no higher structure is hiding
  here.

## 8. Summary in one paragraph

Ordinary logic treats truth values as external bits. The internal logic of
sets reifies those bits *as sets*: propositions are subsingletons (empty or
singleton), predicates are families of them (injections). At that level the
internal logic of sets is classical, but Diaconescu-Goodman-Myhill shows even
excluded middle is a weak choice principle in disguise, and one level up
(families of arbitrary sets) the classical principles fail outright: WSC does
not hold internally (Fourman and Ščedrov). Homotopy type theory generalizes
propositions to spaces, where proofs can carry structure (the loops on S¹).
Mere propositions are exactly the types with at most one point (the
subsingletons), and they form the precise boundary where homotopical logic
collapses back into classical behavior.

## Where to go next

- The HoTT book: Chapter 3 (identity types) and Chapter 4 (propositional
  truncation, hProps).
- nLab entries: "proposition", "subsingleton", "truncation",
  "Diaconescu-Goodman-Myhill theorem".
- R. Diaconescu, "Axiom of choice and complementation", Proc. AMS 51
  (1975), 176-178.
- N. D. Goodman and J. Myhill, "Choice Implies Excluded Middle", ZML 24
  (1978), 461.
- M. P. Fourman and A. Ščedrov, "The world's simplest axiom of choice
  fails", Manuscripta mathematica 38 (1982), 325-332.
- Back to `1_homotopy_intro.md` §2 for the general bridge between types and
  spaces.
