# planning.md — TakeMeter Project Specification

**Community Focus:** r/formula1 (Reddit Formula 1 Community)

---

## 1. Community Choice & Analytical Justification

- **Chosen Community:** `r/formula1`
- **Context & Fit:** The Formula 1 fan ecosystem represents an ideal target for textual classification due to a highly polarized mix of engineering-centric analysis and volatile fandom. Discourse quality shifts radically within identical threads. Users simultaneously process complex aerodynamic regulations, live telemetry streams, financial budget caps, and high-intensity sporting drama. This creates a highly varied text environment ranging from rigorous, data-driven methodology to reactionary, knee-jerk emotional venting. Classifying this data allows us to separate structural, analytical insights from pure social noise.

---

## 2. Label Taxonomy & Granular Definitions

To achieve high mutual exclusivity and clean data boundaries, the discourse is divided into three distinct structural categories:

### A. `technical_analysis`

- **Definition:** The post or comment makes a structured, objective argument regarding car mechanical performance, team racing strategy, aerodynamic design, tire degradation, or track-side telemetry data, backed explicitly by verifiable technical facts, lap-time loops, or historical reference points.
- **Example 1:** "Ferrari’s hard-tire stint during laps 24-40 showed a distinct 0.4s drop-off per lap compared to McLaren, proving their updated suspension geometry is still chewing through the rear tires in high-speed corners."
- **Example 2:** "If you look at the speed trap data from Sector 2, Red Bull ran a significantly higher downforce wing configuration, sacrificing 5 km/h on the main straight to protect their traction profile."

### B. `hot_take`

- **Definition:** A bold, highly opinionated, speculative, or controversial claim about a driver's baseline skill, team management, or sport politics, stated with high confidence but entirely lacking objective telemetry, mathematical evidence, or technical documentation.
- **Example 1:** "Hamilton is completely washed and Ferrari is going to experience immediate buyer's remorse by race three next season."
- **Example 2:** "The FIA intentionally delayed the safety car deployment during that crash just to manipulate the podium positions and engineer artificial drama for TV ratings."

### C. `emotional_reaction`

- **Definition:** An immediate emotional outburst, celebration, or vent regarding a live on-track event, crash, team radio message, or race result, containing zero analytical arguments, structural hypotheses, or data.
- **Example 1:** "OH MY GOD WHAT A MOVE BY NORRIS OUTSIDE OF TURN 4!!! GET IN THERE!!!"
- **Example 2:** "Unbelievable. Another absolute disaster-class strategy from the pit wall. I can't take this pain anymore, my Sunday is completely ruined."

---

## 3. Boundary Stress-Testing & Hybrid Edge Cases

- **The Hardest Edge Case:** The "Hybrid" Post. This occurs when an author packages an aggressive, highly opinionated character attack or conspiracy inside a single, real, or cherry-picked technical statistic.
- _Borderline Example:_ "Lando Norris has zero championship mentality. He has literally lost the lead on Lap 1 from pole position four separate times this season. He is a total fraud who chokes under the slightest pressure."
- **The Conflict:** This sits perfectly on the boundary between `hot_take` (due to subjective insult framing: "zero championship mentality", "total fraud") and `technical_analysis` (due to referencing a verifiable stat: "lost the lead on Lap 1 four times").
- **Explicit Decision Rule:** To be classified as `technical_analysis`, the text must use its data to build an objective explanatory argument or mechanical hypothesis. If a statistic is merely weaponized to validate an emotional insult or subjective character judgment, it defaults to a `hot_take`. Because the example above uses data as decoration for a sweeping subjective claim, it is officially classified as a **`hot_take`**.

---

## 4. Data Collection & Imbalance Plan

- **Collection Strategy:** Public comments will be pulled directly from `r/formula1` post-race discussion threads and weekend live-text archives.
- **Target Distribution:** A minimum of 200 rows will be gathered, aiming for an initial target split of 30% `technical_analysis`, 35% `hot_take`, and 35% `emotional_reaction`.
- **Imbalance Mitigation:** If `technical_analysis` is significantly underrepresented (<20% of the total dataset) after 200 items, I will run targeted collection queries focusing on technical mid-week analytical threads (e.g., threads containing terms like "telemetry", "degradation", "FP2 long runs", or "wind tunnel") rather than scanning standard post-race chaos threads.

---

## 5. Evaluation Metrics & Deep Justification

- **Selected Metrics:** Overall Accuracy, Per-Class Precision, Per-Class Recall, and Macro F1-Score.
- **Justification:** Overall Accuracy alone is insufficient because it masking systematic failures on minority classes. In a community forum, raw emotional reactions naturally outnumber dense engineering evaluations.
    - **Precision** is critical for `technical_analysis` because a user utilizing an automated tool expects that items flagged as "Analysis" are genuinely substantive, filtering out false positives.
    - **Recall** ensures we don't drop true analytical contributions.
    - The **Macro F1-Score** will serve as the primary engineering metric to verify the model balances precision and recall equally across all three classes without tilting toward the majority class.

---

## 6. Concrete Definition of Success

- **Deployment Threshold:** The fine-tuned classifier will be considered "good enough" for real-world deployment if it meets both of the following objective, quantitative criteria:
    1. **Overall Accuracy** on the locked test set must be $\ge 75\%$.
    2. **No individual class F1-Score** may fall below $0.65$.
- **Reasoning:** Human textual expression includes natural sarcasm and linguistic noise. Hitting an overall accuracy of $\ge 75\%$ demonstrates that the model has successfully generalized structural patterns instead of memorizing keyword variations.

---

## 7. AI Tool Plan

### A. Label Stress-Testing

- **Strategy:** I will feed these complete definitions into an LLM and instruct it to generate 10 highly ambiguous, borderline phrases that deliberately attempt to break the boundary between `hot_take` and `technical_analysis`. I will manually sort those 10 responses. If any response exposes a logical loophole in my current decision boundary, I will update the rule set before starting manual dataset tagging.

### B. Annotation Assistance

- **Strategy:** I will use an LLM to pre-label a batch of raw text rows via zero-shot prompt categorization to speed up initial sorting. However, to guarantee total data integrity and track machine influence, I will maintain an explicit tracking column in the master spreadsheet: `is_ai_prelabeled` (True/False). Every single row will be manually reviewed, verified, and modified by me. Any corrections made to the LLM's initial labels will be logged.

### C. Failure Analysis

- **Strategy:** Once training completes, I will extract all test examples where the fine-tuned model's predictions diverge from the ground-truth labels. I will feed this error corpus into an LLM, asking it to look for hidden semantic features or structural patterns (e.g., "The model consistently flags high-length posts containing engineering terminology as Analysis even if they are heavily opinionated hot takes"). I will then manually audit the flagged rows to verify the pattern myself before documenting it in the final report.
