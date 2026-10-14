# Homotopy Theory and Inhabited Types: A Basic Introduction

Two fields that look unrelated ask the same kind of question: type theory
asks *does this type have a value?*, homotopy theory asks *what is the shape
of this space?*. The **homotopical interpretation** answers both at once:
types are spaces, values are points, and proofs of equality are paths. In
that picture, "inhabited" (non-empty) is the coarsest question a type can be
asked, and homotopy theory is the study of everything *above* that bit of
information.

This note builds the bridge from both ends: §1 develops the type-theory side,
§2 states the dictionary, §3 develops the topology side, and §4 puts
"inhabited" in its place in the hierarchy. The two simplest computations are
worked out in annotated code where they make their points (§1 and §3).

## 1. Inhabited types (the type-theory side)

In type theory, truth is existence: "P is true" literally means "the type P
is inhabited", and for simple inductive types that question is decidable by
inspection of the constructors.

A type is **inhabited** if at least one value of that type exists.

| Type | Inhabited? | Inhabitants |
|---|---|---|
| `Bool` | yes | `true`, `false` |
| `Nat` | yes | `0`, `S 0`, `S (S 0)`, … |
| `Empty` (a.k.a. `⊥`, `False`) | **no** | none exist; its very non-existence is the point |

Under the Curry-Howard correspondence, types are propositions and values are
proofs:

| Type theory | Logic |
|---|---|
| a type `P` | a proposition *P* |
| an inhabitant of `P` | a proof of *P* |
| function `A → B` | implication *A ⇒ B* |
| product `A × B` | conjunction *A ∧ B* |
| sum `A + B` | disjunction *A ∨ B* |
| empty type | contradiction / false |

So "*P* is true" literally means "*P* is inhabited." Inhabitation is the
type-theoretic form of **existence**.

For simple inductive (algebraic data) types, inhabitedness is decidable by
inspection: a type is inhabited iff some constructor can be built without
already having a value of the type itself (a nullary constructor, or one
whose arguments all come from types we already know are inhabited).
(`Nat = Z | S Nat` is inhabited via `Z`; a type like `T = C T` is not.)

**The computation.** The test is one pass over the constructors (O(k) for k
constructors): each constructor is summarized by a pair `(arity, recursive)`,
how many arguments it takes and whether any of them is the type being defined.

```python
def is_inhabited(constructors: list[tuple[int, bool]]) -> bool:
    # A constructor is *usable* if it can be built without already having a
    # value of the type itself:
    #   arity == 0     -> nullary constructor (e.g. Z : Nat)
    #   not recursive  -> every argument comes from a type we already know
    #                     is inhabited (e.g. C : Nat -> Wrap)
    return any(arity == 0 or not recursive for arity, recursive in constructors)

examples = {
    "Bool   = True | False": [(0, False), (0, False)],  # inhabited
    "Empty  = <no constructors>": [],                   # UNINHABITED
    "Nat    = Z | S Nat": [(0, False), (1, True)],      # inhabited via Z
    "T      = C T": [(1, True)],                        # UNINHABITED
    "Wrap   = C Nat": [(1, False)],                     # inhabited via Nat
}
for name, ctors in examples.items():
    verdict = "inhabited" if is_inhabited(ctors) else "UNINHABITED"
    print(f"{name:30} -> {verdict}")
```

The interesting rows are the two failures. `Empty` has no constructors at all,
so the `any` over an empty list is false. `T = C T` has a constructor, but
using it requires a value of `T` first: the rule can never get started, and
the type is uninhabited. Two caveats: mutually recursive types need the same
test run on the whole family at once, and coinductive types invert the
answer (`Stream = A x Stream` has no finite inductive term, yet is inhabited
by an infinite stream whenever `A` is).

## 2. The bridge: types as spaces

The bridge is a dictionary between the two languages, and it makes
"inhabited" mean "non-empty".

- a **type** = a space; its values are the **points**
- the **identity type** `a =_A b` = the space of *paths* from `a` to `b`
- a function `f : A → B` = a continuous map
- "inhabited" = "non-empty"; "any two points joined by a path" = *connected*,
  the higher-dimensional analogue of being inhabited

**Mere propositions ((-1)-truncation).** Most everyday propositions don't
care *which* proof you have; there is at most one. Such types are called
**(-1)-truncated** (mere props, h-level 1): any two inhabitants are connected
by a unique path. Classical logic lives inside type theory as the world of
mere propositions; full type theory sees more structure.

**The truncation hierarchy.** Above mere propositions:

- **0-truncated (h-level 2): sets**: paths between points are mere
  propositions; e.g., `Bool` and `Nat` (discrete spaces).
- **1-truncated (h-level 3): groupoids**: identity types form groupoid-like
  structures; e.g., `Circle`, whose loop space is ℤ.

**Univalence, in one sentence.** The identity type of *types* is equivalent
to the type of equivalences: "two types are equal" means "they are mutually
convertible," exactly as two spaces are "the same up to deformation."

## 3. Basic homotopy theory (the topology side)

On the topology side the same questions have classical names, and the first
invariant that goes beyond "inhabited" is the fundamental group: for the
circle it is ℤ, computed by winding number.

**Deformation.** A **homotopy** between maps `f, g : X → Y` is a continuous
`H : X × [0,1] → Y` with `H(·, 0) = f` and `H(·, 1) = g`: a movie of
continuously deforming `f` into `g`.

**Homotopy equivalence.** Maps `f : X → Y`, `g : Y → X` with
`g ∘ f ≃ id_X` and `f ∘ g ≃ id_Y`. This is looser than homeomorphism: a
coffee mug and a doughnut are *homeomorphic*, but a disk is only
*homotopy equivalent* to a single point (you can squash it down; the
squashing has no continuous inverse).

**Why prefer homotopy?** Coarser relations produce more robust invariants,
and they are far easier to compute with. Homotopy theory studies spaces up
to deformation, not up to rigid shape.

**The fundamental group.** `π₁(X, x₀)` is the set of *loops* at a basepoint
`x₀`, up to homotopy that keeps the basepoint fixed. For the circle:

> **π₁(S¹) ≅ ℤ**: every loop winds around the circle some integer number of
> times (the winding number), and two loops are homotopic iff they wind the
> same number of times.

This is the first invariant that sees structure *beyond* a set of points:
S¹ is not just non-empty, its loop space is classified by ℤ.

**The computation.** Discretize S¹ into N positions (the group ℤ/N); a loop
based at 0 is a walk of ±1 steps that returns to 0, and its winding number is
the net displacement divided by N. One pass over the walk computes it (O(L)
for L steps).

```python
N = 360  # one full turn = N unit steps

def is_loop(steps: list[int]) -> bool:
    """A walk on Z/N is a loop iff it ends where it started."""
    pos = 0
    for s in steps:
        if s not in (-1, 1):
            raise ValueError("steps must be +-1")
        pos = (pos + s) % N
    return pos == 0

def winding_number(steps: list[int]) -> int:
    """Net displacement divided by one full turn; exact, since is_loop
    guarantees the net displacement is a multiple of N."""
    if not is_loop(steps):
        raise ValueError("not a loop: walk does not return to basepoint")
    return sum(steps) // N

loops = {
    "empty walk (trivial loop)": [],
    "one full turn": [1] * N,
    "turn + backtrack (homotopic to one turn)": [1] * (N + 180) + [-1] * 180,
    "two turns": [1] * (2 * N),
}
for name, steps in loops.items():
    print(f"{name:40} -> winding {winding_number(steps)}")

# Homotopy of loops == equality of winding numbers.
assert winding_number([1] * N) == winding_number([1] * (N + 180) + [-1] * 180)
assert winding_number([1] * (2 * N)) != winding_number([1] * N)
```

The output is the theorem in miniature:

    empty walk (trivial loop)                -> winding 0
    one full turn                            -> winding 1
    turn + backtrack (homotopic to one turn) -> winding 1
    two turns                                -> winding 2

The "turn + backtrack" walk goes a half-turn past the basepoint and returns;
it is homotopic to one full turn, and the code agrees: same winding number.
Two loops based at 0 are homotopic (deformable into each other, basepoint
fixed) iff they have the same winding number, so the loop space is not just
*inhabited* (the empty walk is a trivial loop): its connected components are
exactly ℤ.

**Higher inductive types.** Type theory can define spaces directly:

```
Circle : Type
  base : Circle
  loop : base =_{Circle} base
```

This is not a set of values; it *is* the circle, up to homotopy. Higher
inductive types (HITs) are how HoTT builds S¹, tori, suspensions, and more,
as first-class types.

## 4. How "inhabited" sits in the picture

"Inhabited" is the first and coarsest question; homotopy theory is the study
of everything *above* that bit of information.

| Level | Type theory asks | Topology asks | Example invariant |
|---|---|---|---|
| (-1)-truncated (h-level 1): mere proposition | is the type inhabited? | is the space non-empty? | yes/no |
| 0-truncated (h-level 2): set | are any two points connected by a path? | is the space path-connected? | yes/no |
| 1-truncated (h-level 3): groupoid | what are the loops, up to homotopy? | what is the fundamental group? | π₁(S¹) = ℤ |

Under the bridge: the empty type is the empty space, `Bool` is a two-point
discrete space, and `Circle` is S¹ itself.

## 5. Where to go next

- **The HoTT book** (free online): the standard reference for this bridge.
- **Egbert Rijke**, *Introduction to Homotopy Type Theory* (Cambridge
  University Press, 2024): a gentler on-ramp than the HoTT book.
- **nLab**: entries for "homotopy type theory", "fundamental group", and
  "higher inductive type".
