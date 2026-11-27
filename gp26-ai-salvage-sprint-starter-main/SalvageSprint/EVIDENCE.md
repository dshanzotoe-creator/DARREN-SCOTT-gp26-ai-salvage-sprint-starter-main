# Week 1 evidence

Keep this short. Bullet points, a small table and one diagram or code trace are enough. This is your checkpoint record, not an extra report. Replace the prompts as you work.

Name: Darren Scott
Exact Unity version: 6000.3.26f1
Checkpoint commit / repository: 
Current stage and anything unfinished: Stage 3


## One complete trace

Use one actual situation. Include the observation data, derived facts, selected task and reason, movement request/result, and how feedback affects a later decision. Identify the responsible code locations. A tiny state diagram can accompany this trace.

I observed that "Observartion.CurrentTargetId" holds the ID of the pickup the rival is currently going for. I derived a fact that "DecisionPolicy.Infer" looks for 
an eligible candidate with that ID. If it finds one, it keeps it and sets "AgentFacts.RetainedTarget" is false. "Choose" returns "Collect" for the selected ID. The
reason shows whether the target was retained or newly selected.



## Scenario record

| Scenario | Predicted result | Observed result and short evidence / remaining issue |
|---|---|---|
| Normal loop and restart |Target 4 is being pursued and target 5 should be ignored. |The 5th target was ignored and target 4 was pursued. |
| Carrying cargo | | |
| Hidden / remembered pickup | ||
| Target taken | | |
| Unreachable target | | |
| No usable pickup | | |

Supplied test result, with any added tests identified: The states transitioned properly Explore - Collect - Deliver - Collect. All tests from the previous stages 
and stage 3 passed.

Live walkthrough checked by / recording link if used:

## One correction and one independent check

The original weakness and why it happened: The starter reselected the nearest pickup every time, so a closer one could pull the rival off a useful target.
I added a check that keeps the current target while it's still eligible.

My counterexample or experiment: prediction, observed result, and one limitation: 

## Collaboration and help

People I discussed or playtested with:
Code, sources or substantive tools/AI assistance I used, and how I verified them:
If unfinished: what works, exact reproduction steps, and the help I need:
