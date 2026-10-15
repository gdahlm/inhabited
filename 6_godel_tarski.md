# Truth Beyond Proof: Gödel's Incompleteness and Tarski's Undefinability

`3_church_trakhtenbrot.md` located undecidability at the core of first-order
logic; `4_rice_theorem.md` showed that every nontrivial semantic property of
programs is one reduction away from halting; `5_markov_principle.md` drew
the line where witness extraction reaches. This note adds the missing
piece: the limit on what a formal system can say about its *own* truths.

**One mechanism, two theorems.** Arithmetic is strong enough to encode its
own syntax: "y is a proof of x" becomes an arithmetical (Σ₁) relation.
Point the diagonal construction at that provability predicate and you get
**Gödel's first incompleteness theorem (1931)**: every consistent,
effectively axiomatized theory containing enough arithmetic has true
sentences it cannot prove. Point it at a putative truth predicate instead
and you get **Tarski's undefinability of truth**: no arithmetical formula
can define the truth of arithmetic sentences. Provability is arithmetical;
truth is not. The gap between the two is exactly incompleteness.

Roadmap: §1 builds the encoding (syntax as numbers, proof checking as a
decidable relation, the diagonal lemma). §2 states and proves Gödel I.
§3 states and proves Tarski's undefinability and shows that the two
theorems are one fact. §4 adds Gödel II (a theory cannot prove its own
consistency) and essential undecidability. §5 completes the thread's
internal/external pattern.

## 1. The encoding: arithmetic talking about its own proofs

**The whole machinery is a coding exercise. Formulas are numbers, proofs
are numbers, "y is a proof of x" is a decidable (hence arithmetical)
relation, and self-reference is just clever coding.**

- **Gödel numbering.** A formula is a finite sequence of symbols; a
  sequence of natural numbers can be coded as a single natural number
  (prime factorization: the i-th symbol becomes the exponent of the i-th
  prime). Coding and decoding are both computable.
- **Representability.** Every decidable relation on numbers has an
  arithmetical formula representing it (Robinson arithmetic Q suffices),
  and every r.e. relation is represented by a Σ₁ formula. Whatever a
  finite computation can check, arithmetic can state.
- **The proof predicate.** For a theory T with effectively listable axioms
  (e.g. Peano arithmetic, PA), "y is a T-proof of x" is decidable: y codes
  a finite list of lines, and each line is checked to be an axiom or to
  follow from earlier lines by a rule. So Prov_T(y, x) is decidable, and
  **"x is provable in T"** = ∃y Prov_T(y, x) **is Σ₁**. This is doc 3's
  r.e. procedure (enumerate proofs), now *inside* arithmetic: the object
  language can state its own provability.
- **The diagonal lemma.** For every arithmetical formula F(x) with one free
  variable, there is a sentence G such that T proves G ↔ F(⌜G⌝), where
  ⌜G⌝ is the Gödel number of G. Self-reference is not magic: it is the same
  move as a quine, a program that prints its own source code. The code
  below shows both halves of the machinery.

```python
# The machinery behind both theorems: make syntax into numbers.
#
# If formulas, proofs, and the relation "y is a proof of x" are all
# arithmetical, then arithmetic can talk about its own proofs, and the
# diagonal sentence "I am not provable" becomes a well-formed statement.

def ith_prime(i):
    # i-th prime (0-indexed) by trial division; illustration, not optimization
    count, n = 0, 1
    while count <= i:
        n += 1
        if all(n % d for d in range(2, int(n ** 0.5) + 1)):
            count += 1
    return n

def code(seq):
    # Goedel numbering by prime factorization: (s_0, s_1, ...) becomes
    # 2^(s_0+1) * 3^(s_1+1) * 5^(s_2+1) * ... . The +1 keeps trailing
    # zeros from vanishing.
    n = 1
    for i, s in enumerate(seq):
        n *= ith_prime(i) ** (s + 1)
    return n

def decode(n):
    seq, i = [], 0
    while n > 1:
        p = ith_prime(i)
        e = 0
        while n % p == 0:
            n //= p
            e += 1
        seq.append(e - 1)
        i += 1
    return seq

for seq in ([], [0], [3, 0, 2], [7, 1, 9, 4]):
    assert decode(code(seq)) == seq   # coding is reversible

# The self-reference half of the story, concretely: a quine. A program
# that prints its own source looks paradoxical, but it is just coding:
# it carries an encoded copy of itself and prints that out. The diagonal
# lemma is the theorem that this trick works *inside* arithmetic: for
# every arithmetical formula F(x) there is a sentence G such that (a
# strong enough theory) proves  G <-> F(godel(G)),  where godel(G) is the
# number coding G's symbol sequence.

s = 's=%r;print(s%%s)'; print(s%s)
```

Output: `s='s=%r;print(s%%s)';print(s%s)` (the program prints its own
source).

## 2. Gödel's first incompleteness theorem (1931)

**Every consistent, effectively axiomatized theory T in the language of
arithmetic, containing enough of Q, has a sentence G_T such that T ⊬ G_T
and T ⊬ ¬G_T; if T is Σ₁-sound, then G_T is true in ℕ.**

- **The sentence.** Take F(x) = ¬Prov_T(x) and diagonalize: there is a G
  with T ⊢ G ↔ ¬Prov_T(⌜G⌝). In words: "I am not provable in T."
- **Why T ⊬ G (consistency suffices).** If T proved G, then by the
  biconditional T would prove ¬Prov_T(⌜G⌝). But a proof of G then exists
  (the one we assumed), and Q can verify any given proof line by line, so
  T also proves Prov_T(⌜G⌝). Contradiction.
- **Why T ⊬ ¬G.** The original argument needed ω-consistency (stronger
  than consistency: T may not prove ∃x P(x) while proving ¬P(n) for every
  numeral n). Rosser (1936) replaced G with a sentence R saying "every
  proof of me is matched by a smaller proof of my negation"; for R, mere
  consistency gives T ⊬ R and T ⊬ ¬R.
- **Why G is true (a mild soundness condition).** If T is Σ₁-sound
  (every Σ₁ sentence it proves is true in ℕ), then since no proof of G
  exists, Prov_T(⌜G⌝) is false in ℕ; the biconditional holds in ℕ as well
  (it is provable, hence true); so ℕ ⊨ G. Consistency gives unprovability;
  adding Σ₁-soundness turns that unprovable sentence into a *true* one.
- **Not a paradox.** Unlike the liar sentence, G_T is perfectly
  well-behaved: it is true, its truth is visible from outside, and the
  biconditional causes no contradiction because "provability" is weaker
  than "truth." The system simply cannot prove G_T without thereby proving
  its own unprovability.

## 3. Tarski's undefinability of truth

**Diagonalize on truth itself and you get a contradiction: no formula of
arithmetic can be a truth predicate for arithmetic. Gödel I and Tarski are
one fact, "provability is arithmetical, truth is not," seen from two
sides.**

- **What a truth predicate would be.** A formula Tr(x) of arithmetic
  satisfying the T-schema for every sentence φ: ℕ ⊨ φ iff ℕ ⊨ Tr(⌜φ⌝).
- **The argument.** Tr(x) is an arithmetical formula with one free
  variable, so the diagonal lemma applies: there is a sentence L with
  T ⊢ L ↔ ¬Tr(⌜L⌝) ("I am not true"). If Tr satisfied the T-schema, then
  ℕ ⊨ L ⟺ ℕ ⊨ ¬Tr(⌜L⌝) ⟺ ¬ℕ ⊨ L. Contradiction. No such Tr exists.
- **Semantic ascent.** Truth for a language L can only be defined in a
  strictly richer metalanguage. Each level of ascent defines the previous
  level's truth and not its own; there is no level at which a language can
  define its own truth.
- **One fact, two faces.** Gödel I diagonalizes on provability: since
  Prov_T is arithmetical, the fixed point exists *and is consistent with
  everything* (it just comes out true and unprovable). Tarski diagonalizes
  on truth: the fixed point is a liar, so no such predicate can exist. And
  the two theorems imply each other in the standard setting: if a
  consistent, sound, effectively axiomatized T were *complete* (for every
  sentence φ, T ⊢ φ or T ⊢ ¬φ), then true arithmetic would be decidable:
  provability is r.e. (enumerate proofs), refutability is r.e. (enumerate
  proofs of negations), consistency ensures both never fire at once, and
  completeness ensures one always fires. But Tarski says true arithmetic
  is not even arithmetical. Completeness of such a T is impossible; that
  impossibility *is* Gödel I.

## 4. Gödel II and essential undecidability

**A theory cannot prove its own consistency, and no consistent r.e.
extension of Q can ever be completed.**

- **Theorem (Gödel II, 1931).** If T is a consistent, effectively
  axiomatized theory containing enough of Q, then T ⊬ Con(T), where
  Con(T) is the Π₁ sentence "no proof of 0 = 1 exists in T."
- **Proof sketch.** Four steps, all formalizable in Q:
  1. T proves Prov_T(⌜G⌝) → Prov_T(⌜0=1⌝): from any proof of G, a few
     checkable extra steps (modus ponens with G → ¬Prov_T(⌜G⌝), then the
     contradiction between that unprovability claim and the proof's own
     existence) yield a proof of 0 = 1, and Q can formalize this
     transformation. So T ⊢ Con(T) → ¬Prov_T(⌜G⌝).
  2. By the fixed point, T ⊢ Con(T) → G.
  3. If T ⊢ Con(T), then T ⊢ G.
  4. But §2 showed T consistent ⇒ T ⊬ G. Contradiction; hence T ⊬ Con(T).
- **The flavor.** Assuming T is consistent, Con(T) is a true Π₁ sentence
  with no T-proof: another instance of "true but unprovable," now about
  the theory's own reliability.
- **Essential undecidability.** Apply §2 to any consistent r.e. extension
  of Q: it too has an unprovable (and, under soundness, true) sentence. No
  consistent r.e. extension of Q is complete; incompleteness cannot be
  patched away by adding axioms, as long as axiomatization stays effective
  and consistency is preserved. Even Q itself is essentially undecidable.

## 5. How this completes the thread

**The internal/external pattern reaches its sharpest form: from inside any
arithmetical theory, the set of its own truths is not merely hard to
derive; it cannot even be named.**

- **Internal/external.** `2_mere_props.md`: WSC fails internally although
  each instance holds pointwise externally. `3_church_trakhtenbrot.md`:
  finiteness is invisible from inside FOL. `4_rice_theorem.md`: the
  extension is invisible from the intension. Here: truth is invisible from
  inside any arithmetical theory, while provability is visible (Σ₁) and
  never complete. The external metatheory evaluates G_T without trouble;
  the internal system cannot even state "x is true."
- **The witness line.** A T-proof of φ is a witness for "T ⊢ φ";
  provability sits at Σ₁, exactly where MP-style witness extraction
  reaches (`5_markov_principle.md` §3, `4_rice_theorem.md` §4). Truth sits
  beyond the entire arithmetical hierarchy: no finite stack of
  halting-problem oracles, no witness-extraction principle. G_T is
  externally witnessed (evaluation in ℕ) and internally unwitnessed (no
  T-proof); Con(T) is a true Π₁ sentence with no T-proof, at precisely the
  level doc 5 said is out of reach.
- **The complexity landscape, extended.** Doc 3's table ended at Σ¹₁
  (Kreisel). Add two rows: {φ : T ⊢ φ} is Σ₁ for any r.e. theory T;
  Th(ℕ) (true arithmetic) is not arithmetical at all. The full landscape,
  in increasing hardness: decidable (proof checking, doc 3 §1) → Σ₁
  (provability, validity, halting) → the analytical hierarchy (model
  existence, Kreisel) → beyond arithmetical (truth).
- **The chain, complete.** Validity → halting → every nontrivial semantic
  property (`4_rice_theorem.md`). And now: arithmetic encodes its own
  syntax, so undecidability lives *inside* the object language, and the
  diagonal turns that self-reference into unprovability. The thread's
  master pattern holds at its extreme: each shift of viewpoint changes
  what can be said; the most extreme shift is from a theory to its
  metatheory, and there the gap is not a single principle but the entire
  truth set.

## 6. Summary in one paragraph

Arithmetic can encode its own syntax: formulas and proofs become numbers,
and "y is a proof of x" becomes a decidable, hence arithmetical (Σ₁),
relation. The diagonal lemma then lets arithmetic state self-referential
sentences. Pointed at the provability predicate, this gives Gödel I: every
consistent, effectively axiomatized theory strong enough for arithmetic has
a sentence G_T, "I am not provable in T," that it cannot prove and (under
mild soundness) is true in ℕ; pointed at a putative truth predicate, it
gives Tarski's undefinability: no arithmetical formula can satisfy the
T-schema, since its diagonal instance is a liar. Gödel II adds that such a
theory cannot even prove its own consistency, a true Π₁ sentence it must
leave unproven, and essential undecidability says no consistent r.e.
extension of Q can be completed. In the thread's vocabulary: provability is
arithmetical and sits exactly where witness extraction reaches; truth is
not arithmetical at all and lies beyond every oracle stack; and the
internal/external pattern reaches its sharpest form, with the set of a
theory's own truths not merely hard to derive but impossible to name from
inside.

## Where to go next

- K. Gödel, "Über formal unentscheidbare Sätze der Principia Mathematica
  und verwandter Systeme I", Monatshefte für Mathematik und Physik 38
  (1931), 173-198.
- J. Barkley Rosser, "Extensions of some results of Church and Gödel",
  Journal of Symbolic Logic 1 (1936), 87-91.
- A. Tarski, "The concept of truth in formalized languages" (originally in
  German: "Der Wahrheitsbegriff in den formalisierten Sprachen"); English
  translation in A. Tarski, Logic, Semantics, Metamathematics (Hackett,
  1984).
- P. Smith, *An Introduction to Gödel's Theorems* (Oxford University Press,
  2007): a modern, careful treatment of both theorems and their variants.
- Back to `3_church_trakhtenbrot.md` §1 for the r.e. proof enumeration that
  becomes the Σ₁ provability predicate, and §4 for the complexity table
  this note extends.
- Back to `4_rice_theorem.md` §5 for the reduction chain this note
  completes.
- Back to `2_mere_props.md` §3 for pointwise vs. uniform, now in its
  sharpest form.
- Back to `5_markov_principle.md` §3 for where witness extraction reaches
  and stops.
