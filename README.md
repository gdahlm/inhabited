# From Inhabitation to Undecidability: Four Annotated Theory Notes

Four gentle markdown theory notes that build on each other, with annotated
code where computation makes the point. They explain what "existence"
means, what a logic can see from the inside, where computation breaks down,
and where witnesses come from.

## Reading the markdown docs

The four docs form one thread and build on each other. Read them in this
order:

1. **`homotopy_intro.md`**: types as spaces. Inhabited types, identity
   types as paths, π₁(S¹) = ℤ, higher inductive types, and the truncation
   hierarchy ((-1)-truncated mere props, 0-truncated sets, 1-truncated
   groupoids), with annotated code for inhabitation by inspection and
   winding numbers on a discrete S¹.
2. **`mere_props.md`**: the internal logic of sets. Propositions as
   subsingletons, predicates as injections, why excluded middle is a weak
   choice principle in disguise (Diaconescu-Goodman-Myhill), and where the
   internal logic stops being classical (WSC fails: Fourman and Ščedrov).
3. **`church_trakhtenbrot.md`**: undecidability at the core. Church 1936
   (validity is semi-decidable but not decidable) and Trakhtenbrot 1951
   (finite validity is not even enumerable), plus a table of what is and
   is not decidable.
4. **`markov_principle.md`**: the capstone. Constructive math in one page,
   Markov's principle ("a search that cannot fail succeeds"), the contrast
   between constructive computation and declarative semantics (Codd's
   relational model), and exactly where MP sits between unique choice and
   the axiom of choice.

Each doc ends with a "Where to go next" section (real references) and
cross-links to its neighbors, so you can also jump around once you know the
shape of the thread.

## The thread in one line

Type theory and homotopy answer "existence is inhabitation"; the internal
logic of sets asks what a world can see from inside; Church and
Trakhtenbrot mark the boundary of what is computable at all; Markov's
principle is the smallest classical principle that lets a search which
cannot fail actually succeed.
