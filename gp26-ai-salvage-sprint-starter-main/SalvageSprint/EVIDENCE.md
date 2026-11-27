# Week 1 evidence

Keep this short. Bullet points, a small table and one diagram or code trace are enough. This is your checkpoint record, not an extra report. Replace the prompts as you work.

Name: Darren Scott
Exact Unity version: 6000.3.26f1
Checkpoint commit / repository: 
Current stage and anything unfinished: Stage 4


## One complete trace

Use one actual situation. Include the observation data, derived facts, selected task and reason, movement request/result, and how feedback affects a later decision. Identify the responsible code locations. A tiny state diagram can accompany this trace.

Originally I observed that the rival refused to move due to the pickup that is supposed to be "blocked" was still the closest and that there was nothing making
the rival AI ignore the blocked pickup. Each candidate (pickup) has a "Blocked" flag, which marks a pickup the rival recently failed to reach. Before implementation
of the rival moving towards the next nearest candidate while a closer candidate is blocked, DeciosionPolicy.Infer "IsEligible" boolean I built, so a blocked 
candidate was neither kept as the current target nor chosen as a new one. The decision was made to make it so that if another eligible pickup exists, "Choose"
returns "Collect" for it. If none does, it returns Explore.


## Scenario record

| Scenario | Predicted result | Observed result and short evidence / remaining issue |
|---|---|---|
| Normal loop and restart | | |
| Carrying cargo | | |
| Hidden / remembered pickup | | |
| Target taken | | |
| Unreachable target |The current target is blocked and another eligible pickup exists. The rival should switch to the other one.| The pickup outside of the walkalbe area of the AI was ignored and the AI went for the next closest one. |
| No usable pickup | | |

Supplied test result, with any added tests identified: All supplied tests for stage 4 and all previous tests passed (22 tests). 

Live walkthrough checked by / recording link if used:

## One correction and one independent check

The original weakness and why it happened: The rival could stay committed to a pickup it couldn't reach, because eligibility ignored "Blocked".
I added that check to "IsEligible".

My counterexample or experiment: prediction, observed result, and one limitation: There is currently one limitation; the block is temporary.
The rival can retry the pickup later, but that doesn't mean the route has become valid.

## Collaboration and help

People I discussed or playtested with:
Code, sources or substantive tools/AI assistance I used, and how I verified them:
If unfinished: what works, exact reproduction steps, and the help I need:
