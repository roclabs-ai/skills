# Problem Diagnosis and Verified Fix

Applies when: the UI or program misbehaves and the cause must be located and fixed.

Execute the following instruction (verbatim):

> Analyze the current UI and runtime state, locate the code-level cause of the problem, re-run and verify after fixing, and check whether similar problems exist elsewhere.

Key points:

- Analyze the UI and runtime state first and find the root cause at the code level; never change code based on guesses.
- After the fix, you must re-run and verify that the problem is truly gone.
- Once the current problem is fixed, proactively scan the codebase for the same class of problem and fix those too.
