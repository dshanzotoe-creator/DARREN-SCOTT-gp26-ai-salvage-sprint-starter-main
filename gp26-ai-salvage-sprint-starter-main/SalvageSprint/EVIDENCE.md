# Week 1 evidence

Keep this short. Bullet points, a small table and one diagram or code trace are enough. This is your checkpoint record, not an extra report. Replace the prompts as you work.

Name: Darren Scott
Exact Unity version: 6000.3.26f1
Checkpoint commit / repository: https://github.com/dshanzotoe-creator/DARREN-SCOTT-gp26-ai-salvage-sprint-starter-main
Current stage and anything unfinished: Stage 5

## Stage checkpoints

| Stage | Commit | Commit message | Date |
|---|---|---|---|
| Baseline | e07b7bd | Initialize project | 26 Nov 16:26 |
| Stage 1 | 704e46b (trace), 87633b2 (initial prediction, later reverted in d410c19) | Update Evidence.md / Evidence Stage 1 Commit | 26 Nov 16:27 / 18:17 |
| Stage 2 | 3c97320 (code), 003b863 (code and evidence) | Implement stage 2 / Stage 2 and Stage 2 Evidence completed. | 26 Nov 17:42 / 19:09 |
| Stage 3 | 9db3fad | Stage 3 complet and implement | 27 Nov 01:01 |
| Stage 4 | 82eec22 | Stage 4 and Stage 4 evidence complete and implement. | 27 Nov 03:33 |

---

## Stage 1

### Initial prediction

Three observations the rival needs, including one it might not have: The distance from the scrap, if the scrap is visible, and memory of previous scraps.

Seeing a pickup across a gap proves / does not prove reachability because: Seeing a pickup across a gap doesn't automatically prove that there is no reachability because there could be
multiple different paths around the gap. This has to do with the ability of the AI having proper pathfinding.

One question about NPC behaviour: A question I have always wanted an answer to regarding video game AI, is how "reputation" works and affects NPCs. Take skyrim for example, your reputation can vary in different regions around the map, this changes the NPCS favorability/likeability towards the player. What is the core structure for this type of AI?

### Trace (original starter behaviour)

One situation I put the "Rival AI" in, was moving a wall from it's original position and blocking the cube with ID "11".
This resulted in the rival throwing a "Route Failure Error". From my understanding, the Rival AI only cares about what is considered the
"closest" scrap by distance. The AI disregards any other observation data such as: whether the scrap is actually visible, remembered, or blocked.
RivalBrain.Update() gathers observations through PickupSensor.Capture(), then uses DecisionPolicy.Infer() and DecisionPolicy.Choose() to derive facts
and select a task, target and reason. When cube 11 is selected for collection, Navigator.Request() sends its position to NavMeshAgent.SetDestination() to
request a route. Navigator.Poll() checks the result using NavigationFeedback.Classify(), and an incomplete route or more than five seconds without sufficient
progress can produce TravelStatus.Failed. RivalBrain.Update() responds by recording a six-second block for cube 11 in failedUntil,
which IsBlocked() makes available to subsequent observations. This feeds the failure back into later decisions.

---

## Stage 2

### Trace

I observed the candidates booleans, this includes: Available, Visible, Remembered, DistanceSquared. The dervied fact I got from the DecisionPolicy was the "Infer"
method. Originally Infer skips any candidate that is not available and checks which is the closest. Now. with current changes, it checks what it originally checked
and checks whether the candidate is visible or remembered. The "decision maker" of DecisionPolicy is the "Choose" method. Choose returns "Collect" with that ID, or
"Explore" if none qualifies; both Collect and Explore are "tasks" (states) to telling what the AI should do. The feedback given is the choosen ID is passed to
Navigator.Request.

### Scenario result

| Scenario | Predicted result | Observed result and short evidence / remaining issue |
|---|---|---|
| Hidden / remembered pickup | Ignores unknown hidden pickups. | The rival went down to the first visible candidate rather than the "hidden" pickup behind the wall, then preceded to patrol/search for any other pickups. |

### Supplied test result

All Stage 2 and Regression tests passed. The Overall count is 17 passes and 5 fails (the total count of tests
was increased in the newset repo).

### Correction

The original weakness and why it happened: The starter picked the nearest candidate with Available && DistanceSquared < distance.
It never checked Visible or Remembered, so a nearby unknown hidden pickup could win just by being closer.

What I changed and why it addresses the cause: I added a rule so a pickup is only eligible if it is available and either visible or remembered.
Distance is then only used to compare eligible pickups. I created a lamnda expression boolean named "IsEligible" that checks if the supplied candidate
meets the proper conditions.

---

## Stage 3

### Trace

I observed that "Observartion.CurrentTargetId" holds the ID of the pickup the rival is currently going for. I derived a fact that "DecisionPolicy.Infer" looks for
an eligible candidate with that ID. If it finds one, it keeps it and sets "AgentFacts.RetainedTarget" is true. "Choose" returns "Collect" for the selected ID. The
reason shows whether the target was retained or newly selected.

### Scenario result

| Scenario | Predicted result | Observed result and short evidence / remaining issue |
|---|---|---|
| Closer distraction (keep current target) | Target 4 is being pursued and target 5 should be ignored. | The 5th target was ignored and target 4 was pursued. |

### Supplied test result

The states transitioned properly Explore - Collect - Deliver - Collect. All tests from the previous stages
and stage 3 passed.

### Correction

The original weakness and why it happened: The starter reselected the nearest pickup every time, so a closer one could pull the rival off a useful target.
I added a check that keeps the current target while it's still eligible.

---

## Stage 4

### Trace

Originally I observed that the rival refused to move due to the pickup that is supposed to be "blocked" was still the closest and that there was nothing making
the rival AI ignore the blocked pickup. Each candidate (pickup) has a "Blocked" flag, which marks a pickup the rival recently failed to reach. Before implementation
of the rival moving towards the next nearest candidate while a closer candidate is blocked, DeciosionPolicy.Infer "IsEligible" boolean I built, so a blocked
candidate was neither kept as the current target nor chosen as a new one. The decision was made to make it so that if another eligible pickup exists, "Choose"
returns "Collect" for it. If none does, it returns Explore.

### Scenario result

| Scenario | Predicted result | Observed result and short evidence / remaining issue |
|---|---|---|
| Unreachable target | The current target is blocked and another eligible pickup exists. The rival should switch to the other one. | The pickup outside of the walkalbe area of the AI was ignored and the AI went for the next closest one. |

### Supplied test result

All supplied tests for stage 4 and all previous tests passed (22 tests).

### Correction

The original weakness and why it happened: The rival could stay committed to a pickup it couldn't reach, because eligibility ignored "Blocked".
I added that check to "IsEligible".

My counterexample or experiment: prediction, observed result, and one limitation: There is currently one limitation; the block is temporary.
The rival can retry the pickup later, but that doesn't mean the route has become valid.

---

## Stage 5 — Verify and preserve

### One complete trace

Situation: the rival's current (remembered) target was moved out of bounds (video, 3:13).

- Observation (PickupSensor.Capture): the pickup is still the current target, so it counts as Remembered.
- Facts (DecisionPolicy.Infer): the pickup is still Available, Remembered and not Blocked, so it stays eligible and RetainedTarget is true.
- Decision (DecisionPolicy.Choose): Collect that target, reason "Retain current eligible target".
- Movement (RivalBrain.Update -> Navigator.Request -> SetDestination): the rival still tried to reach the pickup.
- Feedback (Navigator.Poll and RivalBrain.IsBlocked): when a route is reported as failed, the code blocks that pickup for six seconds, so a later decision picks another target or explores.

State: Collect -> (route fails) -> another Collect target or Explore

### Scenario record

| Scenario | Predicted result | Observed result and short evidence / remaining issue |
|---|---|---|
| Normal loop and restart | Collects, delivers, continues, finishes and restarts. | Ran as expected (see video). |
| Carrying cargo | Delivers before pursuing more scrap. | Ran as expected (see video). |
| Hidden / remembered pickup | Ignores unknown hidden targets; valid memory may persist. | Ran as expected (see video). |
| Target taken | Releases the unavailable target and continues. | Ran as expected (see video). |
| Unreachable target | Uses failure feedback and temporarily avoids the target. | Ran as expected (see video). |
| No usable pickup | Explores without crashing or inventing pickup targets. | Ran as expected (see video). |

Supplied test result, with any added tests identified: All base tests ran as they were supposed to.

Live walkthrough checked by / recording link if used: https://www.youtube.com/watch?v=_glqMPmLaak (counterexample at 3:13)

### One correction and one independent check

The original weakness and why it happened: The rival could stay committed to a pickup it couldn't reach, because eligibility ignored "Blocked".

What I changed and why it addresses the cause: I added that check to "IsEligible", so a blocked pickup is neither kept as the current target nor chosen as a new one.

My counterexample or experiment: prediction, observed result, and one limitation:
- Test: if the current/remembered target goes out of bounds, will the rival still try to access it?
- Prediction: the AI will ignore the remembered scrap then move towards either a patrol point, or the next closest visible scrap.
- Observed: the AI still tried to access it (video, 3:13).
- Limitation: eligibility has no in-bounds or reachability check, so the rival only reacts after a route fails, and it can retry once the six-second block ends.

---

## Collaboration and help

People I discussed or playtested with:

Code, sources or substantive tools/AI assistance I used, and how I verified them:

If unfinished: what works, exact reproduction steps, and the help I need:
