# Week 1 evidence

Keep this short. Bullet points, a small table and one diagram or code trace are enough. This is your checkpoint record, not an extra report. Replace the prompts as you work.

Name: Darren Scott
Exact Unity version: 6000.3.26f1
Checkpoint commit / repository: 
Current stage and anything unfinished: Stage 2 


## One complete trace

Use one actual situation. Include the observation data, derived facts, selected task and reason, movement request/result, and how feedback affects a later decision. Identify the responsible code locations. A tiny state diagram can accompany this trace.

I observed the candidates booleans, this includes: Available, Visible, Remembered, DistanceSquared. The dervied fact I got from the DecisionPolicy was the "Infer"
method. Originally Infer skips any candidate that is not available and checks which is the closest. Now. with current changes, it checks what it originally checked
and checks whether the candidate is visible or remembered. The "decision maker" of DecisionPolicy is the "Choose" method. Choose returns "Collect" with that ID, or
"Explore" if none qualifies; both Collect and Explore are "tasks" (states) to telling what the AI should do. The feedback given is the choosen ID is passed to
Navigator.Request.



## Scenario record

| Scenario | Predicted result | Observed result and short evidence / remaining issue |
|---|---|---|
| Normal loop and restart | | |
| Carrying cargo | | |
| Hidden / remembered pickup |Ignores unknown hidden pickups. |The rival went down to the first visible candidate rather than the "hidden" pickup behind the wall, then preceded to patrol/search for any other pickups.|
| Target taken | | |
| Unreachable target | | |
| No usable pickup | | |

Supplied test result, with any added tests identified: All Stage 2 and Regression tests passed. The Overall count is 17 passes and 5 fails (the total count of tests
was increased in the newset repo).

Live walkthrough checked by / recording link if used:

## One correction and one independent check

The original weakness and why it happened: The starter picked the nearest candidate with Available && DistanceSquared < distance. 
It never checked Visible or Remembered, so a nearby unknown hidden pickup could win just by being closer.

What I changed and why it addresses the cause: I added a rule so a pickup is only eligible if it is available and either visible or remembered. 
Distance is then only used to compare eligible pickups. I created a lamnda expression boolean named "IsEligible" that checks if the supplied candidate
meets the proper conditions.

My counterexample or experiment: prediction, observed result, and one limitation:

## Collaboration and help

People I discussed or playtested with:
Code, sources or substantive tools/AI assistance I used, and how I verified them:
If unfinished: what works, exact reproduction steps, and the help I need:
