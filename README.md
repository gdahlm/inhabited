# From Inhabitation to Unprovability: Six Annotated Theory Notes

Type theory and homotopy answer "existence is inhabitation"; the internal
logic of sets asks what a world can see from inside; Church and
Trakhtenbrot mark the boundary of what is computable at all, and Rice's
theorem extends it to every nontrivial property of what a program computes;
Markov's principle is the smallest classical principle that lets a search
which cannot fail actually succeed; Gödel and Tarski mark where a formal
system cannot even name its own truths.

This repo is six gentle markdown theory notes that build that thread in
order, with annotated code where computation makes the point.

## Reading the markdown docs

The six docs form one thread and build on each other. Read them in this
order:

1. **`1_homotopy_intro.md`**: types as spaces. Inhabited types, identity
   types as paths, π₁(S¹) = ℤ, higher inductive types, and the truncation
   hierarchy ((-1)-truncated mere props, 0-truncated sets, 1-truncated
   groupoids), with annotated code for inhabitation by inspection and
   winding numbers on a discrete S¹.
2. **`2_mere_props.md`**: the internal logic of sets. Propositions as
   subsingletons, predicates as injections, why excluded middle is a weak
   choice principle in disguise (Diaconescu-Goodman-Myhill), and where the
   internal logic stops being classical (WSC fails: Fourman and Ščedrov).
3. **`3_church_trakhtenbrot.md`**: undecidability at the core. Church 1936
   (validity is semi-decidable but not decidable) and Trakhtenbrot 1951
   (finite validity is not even enumerable), plus a table of what is and
   is not decidable.
4. **`4_rice_theorem.md`**: undecidability at the level of programs. Rice's
   theorem (1953): every nontrivial property of what a program computes is
   undecidable, one uniform reduction away from halting; plus where each
   level of program property sits relative to the witness-extraction
   principles in `5_markov_principle.md`.
5. **`5_markov_principle.md`**: when a search cannot fail, it succeeds.
   Constructive math in one page,
   Markov's principle ("a search that cannot fail succeeds"), the contrast
   between constructive computation and declarative semantics (Codd's
   relational model), and exactly where MP sits between unique choice and
   WSC.
6. **`6_godel_tarski.md`**: truth beyond proof. Gödel's incompleteness
   (consistent effectively axiomatized theories leave true sentences
   unprovable), Tarski's undefinability of truth, Gödel II (a theory cannot
   prove its own consistency), and how provability (Σ₁) vs. truth (beyond
   arithmetical) completes the thread's internal/external pattern.

Each doc ends with a "Where to go next" section (real references) and
cross-links to its neighbors, so you can also jump around once you know the
shape of the thread.
