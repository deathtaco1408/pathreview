## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/146

**Issue title:** PII scrubber fails to redact parenthesized US phone numbers

**Tier:** [x] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
The PII scrubber module in `safety/pii_scrubber.py` uses a regex pattern that
correctly matches dashed US phone formats like `555-123-4567`, but fails to
match the equally common parenthesized format `(555) 123-4567`. As a result,
`scrub()` leaves these numbers unredacted in text output, and `detect()`
incorrectly reports no PII present when a parenthesized number is the only
match. This is a gap in the safety layer meant to prevent sensitive personal
data from leaking through review output. A successful fix extends the phone
regex to cover the parenthesized format without breaking existing dashed-format
matching, and passes the four related unit tests already written for this bug.

**Scope reasoning ("Is this right for me?" checklist):**

*Part 1 — Understanding the issue*
The bug is in `safety/pii_scrubber.py`: the phone-number regex matches dashed
formats like `555-123-4567` but not parenthesized formats like `(555) 123-4567`,
so `scrub()` leaves those numbers unredacted and `detect()` reports no PII found.
Done looks like: both formats get redacted by `scrub()`, `detect()` correctly
flags parenthesized numbers as PII, and the four related tests in
`tests/unit/test_pii_scrubber.py` pass.

*Part 2 — Tier fit*
Labeled tier-1 and good-first-issue. This is my first contribution to a large
codebase, so a Tier 1 fix is the right starting point — it's a single-file
regex/logic change, not a cross-module feature.

*Part 3 — Codebase readiness*
[x] Confirm after opening the file: I've located `pii_scrubber.py` and read the
current phone-matching regex and the `scrub()`/`detect()` functions closely
enough to sketch a fix.
[x] Confirm after opening the file: I've read `tests/unit/test_pii_scrubber.py`,
specifically `test_us_phone_number_redaction`, `test_us_phone_formats`,
`test_detect_phone_pii`, and `test_phone_at_start_of_text`, to understand the
expected input/output shape.

*Part 4 — Scope and time*
No blockers or dependencies mentioned in the issue. PR #162 is linked but
claims are non-exclusive per the course guidelines, so I checked the comments/
ledger claims count and am comfortable proceeding. This is a small, well-scoped
regex fix — I estimate 3-4 hours, well within the Tier 1 range and the Week 8-9
window.

**Branch name:** fix/146-pii-phone-regex

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger

## Week 8 — Reproduction & solution planning

**Reproduction commit link:** 
https://github.com/deathtaco1408/pathreview/commit/9a10094

**Reproduction summary:**
Ran `PIIScrubber().scrub()` and `.detect()` against `(555) 123-4567` and
confirmed the parenthesized format passed through unredacted and undetected,
while the dashed format redacted correctly — confirming the bug was in the
regex's boundary handling, not the surrounding scrub/detect logic.

**PLAN.md link:**
https://github.com/deathtaco1408/pathreview/commit/07ddb06

**Walkthrough video (recommended):** 

**Blockers or open questions:**
None currently — the `street_address` false-positive bug found during
testing is unrelated to this issue and out of scope; may raise it as a
separate issue.