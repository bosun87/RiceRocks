Built a 2D space game RiceRocks that is inspired by the classic arcade game Asteroids using Python

This python program runs in: http://www.codeskulptor.org/

Simply paste the python code in the above link and press "run" button

Instructions for playing this game:

1. press "space" to fire the missiles
2. use "up" and "down" for acceleration and deceleration
3. use "left" and "right" for controlling the orientation of the plane


##########################################################################


D4C Regression Selection Policy
1. Objective
The goal is to select the smallest regression set that maintains acceptable confidence.

Avoiding false negatives is more important than minimizing test count.

When uncertain:

SHOULD_RUN is preferred over SKIP
high-risk uncertainty should become NEEDS_REVIEW
2. Evidence Priority
When evidence conflicts, use this order:

Actual PR diff and source code
Runtime / coverage evidence
Verified code dependency relationships
Understand-Anything knowledge graph
D4C Product Wiki
Test metadata
Lessons learned / golden cases
LLM inference
Source code defines actual implementation behavior.

The D4C Product Wiki provides product semantics, workflows, and intended behavior.

If source code and Wiki disagree, flag the discrepancy and classify conservatively.

3. Tier Definitions
MUST_RUN
Use when there is strong evidence that the PR can affect behavior exercised or observed by the test.

Typical reasons:

test directly exercises changed behavior
test executes the changed code path
changed shared/persisted state is consumed by the test
API/schema/config/interface contract used by the test changed
strong producer-consumer dependency exists
strong historical evidence exists
a high-risk boundary directly affects the tested workflow
SHOULD_RUN
Use when regression impact is credible but indirect or uncertain.

Typical reasons:

upstream/downstream workflow may be affected
shared component may propagate the change
product behavior overlaps the test scenario
an indirect dependency exists but impact is not fully proven
skipping would create meaningful uncertainty
SKIP
Use only after actively checking for impact and finding no credible relationship.

SKIP must not mean:

No obvious relationship was found.

Before assigning SKIP, verify that there is no meaningful connection through:

direct or indirect execution
shared state
persisted state
shared configuration
schema/API contracts
fallback/recovery behavior
product workflow
historical evidence
Do not SKIP solely because the test is in another folder, has a different name, or has no direct graph edge.

NEEDS_REVIEW
Use when:

evidence conflicts
graph/Wiki/test metadata is incomplete
a high-risk change has low confidence
an important dependency cannot be verified
Do not force a confident tier when evidence is insufficient.

4. High-Risk Changes
When a PR touches any of the following, widen the impact investigation before allowing tests to be skipped:

Python ↔ C++ bindings
shared or persisted state
schema / serialization
shared configuration
public APIs or contracts
fallback / recovery logic
common utilities or base classes
concurrency / threading
memory ownership
retry / timeout logic
cross-subsystem orchestration
High risk does not automatically mean every related test is MUST_RUN.

It means SKIP requires stronger evidence.

Refer to:

references/risk-patterns.md

for detailed investigation guidance.

5. Tier Upgrade / Downgrade Rules
Upgrade SKIP → SHOULD_RUN when a credible indirect impact path is discovered.

Upgrade SHOULD_RUN → MUST_RUN when direct behavioral, execution, state, contract, or strong historical evidence is established.

Downgrade MUST_RUN → SHOULD_RUN only with concrete evidence that direct impact does not exist.

Downgrade SHOULD_RUN → SKIP only after the suspected impact path has been explicitly investigated and disproven.

Absence of evidence is not sufficient for downgrade.

6. Mandatory Second-Pass Review
All initial classifications are provisional.

Before finalizing:

Challenge every SKIP
Try to find a hidden regression path through:

shared/persisted state
configuration
producer-consumer relationships
fallback/recovery
Python ↔ C++ boundaries
product contracts
relevant lessons learned
similar golden cases
If a credible impact path exists, upgrade the tier.

Challenge every MUST_RUN
Check whether:

the test truly exercises or observes changed behavior
the execution path is actually disjoint
the PR is implementation-only
classification was based only on feature/folder similarity
Downgrade only with evidence.

Final Principle
Regression selection should not optimize for:

the fewest tests possible.

It should optimize for:

the smallest test set that preserves regression confidence.

When credible risk remains unresolved, do not classify the test as SKIP.





