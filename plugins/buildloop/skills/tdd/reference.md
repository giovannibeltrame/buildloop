# TDD craft reference

The discipline behind each turn of the loop the `tdd` skill drives. SKILL.md names
the rule; this file is the why and the worked example. Code is illustrative — apply
the principle in your stack's idiom.

## The core loop

```
RED → GREEN → REFACTOR → RED → ...
```

## The Three Laws

1. **No production code** without a failing test.
2. **No more test code** than is sufficient to fail (a compile error counts as failing).
3. **No more production code** than is sufficient to pass the one failing test.

The laws are what keep the loop honest: they forbid running ahead of the test that
justifies the code.

## RED — write the failing test

Describe the behavior you want, then watch it fail. A good test:

- uses domain language, not technical jargon;
- describes WHAT the code does, not HOW;
- is a concrete example, not an abstract statement.

```
// BAD — abstract
it('can add numbers', ...)

// GOOD — concrete example
it('adding 2 + 3 returns 5', ...)
```

## GREEN — simplest code that passes

Two strategies:

1. **Fake It** — return a hardcoded value. The simplest thing that turns the bar green.
2. **Obvious Implementation** — write the real logic when the solution is genuinely clear.

**Prefer Fake It when unsure.** Let the next test force the fake to generalize. Jumping
to a clever implementation skips the tests that would have pinned its edges.

## Triangulation

Each new test sculpts the solution toward the general case. Think of degrees of
freedom: a fake satisfies one example; a second example removes a degree of freedom and
forces a variable; a third forces the real rule. Add examples until the implementation
has nowhere left to cheat.

## Transformation Priority Premise

Going RED → GREEN, prefer the simpler transformation. Higher in the table = simpler;
reach for the lowest row only when a test demands it.

| Priority | Transformation |
|---|---|
| 1 | {} → nil |
| 2 | nil → constant |
| 3 | constant → variable |
| 4 | unconditional → conditional |
| 5 | scalar → collection |
| 6 | statement → recursion |
| 7 | value → mutated value |

## REFACTOR — where design happens

With every test green, improve the code without changing behavior. Look for:

- duplication — but wait for the **Rule of Three**;
- long methods to extract;
- poor names to sharpen;
- tangled conditionals to simplify.

### Rule of Three

Extract duplication only on the **third** occurrence.

```
// #1 — leave it
// #2 — note it, leave it
// #3 — now extract
```

A wrong abstraction costs more than the duplication it replaces; wait for the pattern to
prove itself.

## Arrange–Act–Assert

Structure every test in three beats:

```
it('applies a 10% discount to the order total', () => {
  // ARRANGE — set up the world
  const order = new Order([{ price: 100 }])
  const discount = new PercentDiscount(10)

  // ACT — execute the behavior
  const total = order.total(discount)

  // ASSERT — verify the outcome
  expect(total).toBe(90)
})
```

**Writing backwards** often helps: write the ASSERT first (what do you want to be
true?), then the ACT that produces it, then the ARRANGE it needs.

## Test naming

- Behavior-driven names in domain language.
- One concrete example per test — easy to read, easy to debug when it fails.
- Don't leak implementation detail into the name.

```
// BAD — implementation-focused
it('sets the data property to 1', ...)

// GOOD — behavior in domain language
it('recognizes "mom" as a palindrome', ...)
```

## Classic vs Mockist

- **Classic** (Detroit) — real dependencies. Higher confidence, slower. Best for pure
  logic and integration.
- **Mockist** (London) — mock collaborators. Faster, more isolated. Best where the unit
  has infrastructure dependencies (DB, network, clock).

**Start Classic.** Reach for mocks only at infrastructure seams. This lines up with the
bdd layer tags: `[unit]` and `[integration]` lean Classic; mocks appear where a scenario
crosses an infra boundary.

## Common mistakes

1. Writing code before the test.
2. Writing more test than is needed to fail.
3. Writing more code than is needed to pass.
4. Skipping refactor — that is where the design lives.
5. Testing implementation instead of behavior.
6. Abstract test names instead of concrete examples.
7. Extracting duplication before the Rule of Three.
