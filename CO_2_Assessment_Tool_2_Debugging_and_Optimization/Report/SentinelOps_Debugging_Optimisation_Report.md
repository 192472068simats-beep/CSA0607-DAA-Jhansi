SIMATS ENGINEERING
SAVEETHA INSTITUTE OF MEDICAL AND TECHNICAL SCIENCES

DEPARTMENT OF COMPUTER SCIENCE AND ENGINEERING

SENTINELOPS SOC ALERT-TRIAGE MODULE

Defect Diagnosis, Systematic Logic Repair, Regression Testing, and Empirical Optimization


| Course Name & Code | CSA0606 — Design and Analysis of Algorithms |
| --- | --- |
| Course Outcome (CO) | CO2 — Assessment Tool 2: Debugging and Optimization |
| Assessment Type | Individual Algorithm Engineering Assignment |
| Student Name | MALLAPU VIDYADHAR |
| Registration Number | 192511467 |
| Academic Year | 2024–2025 (Third Year / B.E. CSE) |
| Date of Submission | November 2026 |
| Maximum Marks | 50 Marks |



# TABLE OF CONTENTS

`	ext
1. Executive Summary & Security Operations Center (SOC) Scenario
`

`	ext
2. Part A — Reproduce and Identify Algorithmic Defects
`

`	ext
3. Part B — Systematic Corrections & State Trace Forensics
`

`	ext
4. Part C — Optimized High-Throughput Alternatives
`

`	ext
5. Part D — Empirical Benchmarking & Complexity Analysis
`

`	ext
6. Part E — Automated Regression Test Suite
`

`	ext
7. Engineering Recommendations & Architectural Defense
`

`	ext
8. Viva-Voce Technical Defense Preparation
`

`	ext
9. Limitations & Hardware Environment
`

`	ext
10. Academic References
`


# 1. Executive Summary & SOC Scenario

Modern Security Operations Centers (SOCs) depend on real-time event correlation engines to process millions of incoming security telemetry records. The SentinelOps alert-triage pipeline executes four critical operations:
1. Priority-ordered alert sorting by incident severity.
2. Fast IP blocklist membership verification.
3. Cyber threat signature scanning across unstructured network payloads.
4. Geographic correlation of concurrent login events to detect credential stuffing.

The legacy codebase contained four catastrophic defects: unstable non-descending sorting, premature early-return in linear blocklist scans, boundary-truncating string matching, and inverted farthest-pair coordinate tracking. This report provides the definitive forensic diagnosis, minimal mathematical corrections, and high-performance algorithmic replacements.

Verified Execution Platform: Windows-11-10.0.26300-SP0 | Python 3.14.6.


# 2. Part A — Reproduce and Identify Algorithmic Defects

The original faulty implementation was executed against baseline test vectors without modification. All four routines failed silently, generating misleading outputs that would blind SOC analysts to active cyber attacks.


| Defect ID | Subsystem | Root Cause Analysis | Observed Operational Failure |
| --- | --- | --- | --- |
| DEF-01 | sort_alerts | Reverse scan terminated at j > 0 instead of j >= 0; non-strict comparison inverted stability. | Alerts dropped/misordered; equal severities shuffled; index 0 corrupt. |
| DEF-02 | is_blocked | Immediate return False on first mismatch inside loop. | False negative: only checks 1st entry; subsequent malicious IPs ignored. |
| DEF-03 | find_signature | Loop bound n-m instead of n-m+1; match confirmed at j == m-1 before comparing final char. | False positive at index 4 ('FAILFAIX'); missed legitimate match at end. |
| DEF-04 | closest_logins | Greedy comparator used > instead of <; returned farthest distance pair. | Returns maximum distance (104.4 km) instead of closest suspicious pair (1.41 km). |



# 3. Part B — Systematic Corrections & State Traces

Minimal surgical corrections were applied, strictly preserving the original algorithmic architectures while restoring provable mathematical correctness.


## Stable Descending Insertion Sort

`	ext
SORT-ALERTS(alerts)
  for i = 1 to length(alerts) - 1
    key = alerts[i]
    j = i - 1
    while j >= 0 and alerts[j].severity < key.severity
      alerts[j + 1] = alerts[j]
      j = j - 1
    alerts[j + 1] = key
  return alerts
`

Bound extended to j >= 0; strict inequality alert['severity'] < key['severity'] guarantees stability.


## Sequential Blocklist Scan

`	ext
IS-BLOCKED(blocklist, ip)
  for each entry in blocklist from first to last
    if entry = ip
      return true
  return false
`

Scan traverses entire list; return False only after loop completes without any match.


## Brute-Force Signature Matching

`	ext
FIND-SIGNATURE(text, pattern)
  if pattern is empty
    return boundaries 0 through length(text)
  if length(pattern) > length(text)
    return empty list
  hits = empty list
  for start = 0 to length(text) - length(pattern)
    offset = 0
    while offset < length(pattern) and text[start + offset] = pattern[offset]
      offset = offset + 1
    if offset = length(pattern)
      append start to hits
  return hits
`

Alignment range updated to range(n - m + 1); match flag validated across all m characters.


## Closest Coordinate Pair

`	ext
CLOSEST-LOGINS(events)
  require at least two events
  bestSquared = infinity
  for i = 0 to n - 2
    for j = i + 1 to n - 1
      squared = squaredDistance(events[i], events[j])
      if squared < bestSquared, or equal with lexicographically smaller (i,j)
        save squared and pair (i,j)
  return pair, squareRoot(bestSquared)
`

Nested loop examines unique pairs (j > i); updates state when current_dist < min_dist.


# 4. Part C & D — Empirical Benchmarking & Complexity Analysis

Both corrected baseline algorithms and optimized production alternatives were benchmarked across scaled synthetic datasets under deterministic seeds.


## Sorting Benchmarks (Insertion vs Merge Sort)


| Dataset | n | Algorithm | Comparisons | Movements | Seconds | Stable |
| --- | --- | --- | --- | --- | --- | --- |
| nearly_sorted | 100 | insertion | 197 | 197 | 0.000035115 | True |
| nearly_sorted | 100 | stable_merge | 375 | 1400 | 0.000250318 | True |
| random | 100 | insertion | 2632 | 2637 | 0.000415696 | True |
| random | 100 | stable_merge | 562 | 1400 | 0.000235470 | True |
| reverse_sorted | 100 | insertion | 4942 | 5005 | 0.000850368 | True |
| reverse_sorted | 100 | stable_merge | 334 | 1400 | 0.000214681 | True |
| nearly_sorted | 1000 | insertion | 1010 | 1010 | 0.000252161 | True |
| nearly_sorted | 1000 | stable_merge | 5052 | 20000 | 0.003280714 | True |
| random | 1000 | insertion | 241765 | 241769 | 0.044766729 | True |
| random | 1000 | stable_merge | 8736 | 20000 | 0.003658970 | True |
| reverse_sorted | 1000 | insertion | 495309 | 495408 | 0.143309093 | True |
| reverse_sorted | 1000 | stable_merge | 5665 | 20000 | 0.005531274 | True |
| nearly_sorted | 5000 | insertion | 5000 | 5000 | 0.001090104 | True |
| nearly_sorted | 5000 | stable_merge | 33356 | 130000 | 0.027422946 | True |
| random | 5000 | insertion | 6126799 | 6126804 | 0.836288691 | True |
| random | 5000 | stable_merge | 56813 | 130000 | 0.013081768 | True |
| reverse_sorted | 5000 | insertion | 12376956 | 12377055 | 1.898640324 | True |
| reverse_sorted | 5000 | stable_merge | 34593 | 130000 | 0.012016761 | True |


Figure 1: Comparison of operations across Insertion and Merge Sort.


## Blocklist Search (Sequential vs Hash Set Index)


| Blocklist Size | Queries | Sequential Comps | Sequential Time (s) | Index Build Time (s) | Indexed Query Time (s) | Valid |
| --- | --- | --- | --- | --- | --- | --- |
| 100 | 500 | 37884 | 0.001123550 | 0.000013055 | 0.000037821 | True |
| 1000 | 500 | 380682 | 0.012014415 | 0.000044213 | 0.000036819 | True |
| 10000 | 500 | 3803143 | 0.121902678 | 0.000744009 | 0.000048590 | True |


Figure 2: Sequential scanning vs Hash Set Index query latencies.


## Closest Login Coordinates (Brute-Force vs Divide-and-Conquer)


| n Events | Unique Pairs | Brute Calcs | D&C Calcs | Preprocessing Ops | Brute Time (s) | D&C Time (s) | Match |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1000 | 499500 | 499500 | 915 | 28058 | 0.095256083 | 0.008108986 | True |
| 2000 | 1999000 | 1999000 | 1854 | 62085 | 0.330356033 | 0.024963155 | True |
| 4000 | 7998000 | 7998000 | 3733 | 136132 | 1.598185661 | 0.037607256 | True |
| 8000 | 31996000 | 31996000 | 7417 | 296258 | 5.587078836 | 0.081627552 | True |


Figure 3: Quadratic pairwise calculations vs Divide-and-Conquer planar reduction.


## Complexity Architecture Summary


| Subsystem | Time Complexity | Space Complexity | Engineering Recommendation |
| --- | --- | --- | --- |
| Alert Insertion Sort | O(n) best; O(n^2) worst | O(1) in-place | Ideal for nearly-sorted incoming alert streams. |
| Stable Merge Sort | Theta(n log n) all cases | O(n) auxiliary | Optimal for large unorganized batch indexing. |
| Sequential IP Search | O(1) best; O(n) worst | O(1) auxiliary | Zero setup; fits small, volatile blocklists. |
| Set Index Hash Lookup | O(1) expected lookup | O(n) memory | Essential for high-throughput packet filtering. |
| Brute String Matching | O(m(n-m+1)) worst | O(1) auxiliary | Sufficient for short intrusion signatures. |
| D&C Closest Pair | Theta(n log n) | O(n) auxiliary | Massive calculation reduction on high-volume login batches. |



# 5. Part E — Automated Regression Test Suite


| Test Method | Result Status |
| --- | --- |
| test_brute_and_divide_conquer_closest_pair_agree | PASS |
| test_closest_pair_duplicates_ties_negative_and_two_events | PASS |
| test_closest_pair_rejects_small_inputs | PASS |
| test_corrected_closest_pair_supplied_and_known_answer | PASS |
| test_optimized_sorts_match_corrected_and_are_stable | PASS |
| test_optimized_string_comparison_counter | PASS |
| test_original_faulty_behaviour_is_preserved | PASS |
| test_sequential_search_boolean_convention_and_boundaries | PASS |
| test_sort_empty_single_sorted_reverse_and_stability | PASS |
| test_string_matching_supplied_and_all_boundaries | PASS |


The automated unit test suite executed 10 test cases across all subsystems; 100% of tests PASSED.


# 6. Viva-Voce Technical Defense Preparation

Q: Why did the original blocklist function fail for all entries after index 0?
A: Because the loop body executed 'else: return False' immediately upon comparing the first entry, aborting traversal before checking remaining items.

Q: Why must the string matching loop bound be n - m + 1?
A: Because Python range(start, stop) is right-exclusive. If stop is n - m, the final valid alignment at index n - m is never tested.

Q: How does strict inequality preserve sorting stability?
A: When an incoming key has identical severity to an existing item, strict inequality (key < current) prevents the existing item from being shifted, preserving arrival order.

Q: Why does the divide-and-conquer closest-pair strip only inspect 7 points?
A: Within a delta x 2*delta box, points in the same half cannot be closer than delta. Geometric packing proves at most 8 points can exist, meaning at most 7 neighbors can be within distance delta.


# 7. Academic References

`	ext
Cormen, T. H., Leiserson, C. E., Rivest, R. L., & Stein, C. (2022). Introduction to Algorithms (4th ed.). MIT Press.
`

`	ext
Scarfone, K., & Mell, P. (2007). Guide to Intrusion Detection and Prevention Systems (IDPS). NIST Special Publication 800-94.
`

`	ext
SIMATS Engineering Curriculum. CSA0606 — DAA Assessment Framework.
`
