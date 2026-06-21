## 1. Community Choice & Justification

- Chosen Community: $r/formula1$ (Reddit Formula 1 Community).
- Justification: The Formula 1 fanbase sits at a fascinating intersection of high-engineering data analysis and intense emotional fandom. This environment creates a dramatic variance in discourse quality. On race weekends, the platform is flooded with a mixture of telemetry-backed strategic reviews, knee-jerk emotional reactions to crashes or penalties, and unverified speculative rumors about driver contracts or team politics. Classifying this community's text allows us to separate structural analytical value from noise, providing an automated tool to evaluate the depth of fan engagement.

## 2. Label Taxonomy & Definitions

To ensure high annotator agreement and clean model signals, we define three mutually exclusive and exhaustive labels:

### Label 1: technical_analysis

- Definition: The post makes a structured, objective argument regarding car performance, team strategy, aerodynamics, tire degradation, or telemetry data, backed by specific verifiable facts, timing loops, or historical reference points.
- Example 1: "Ferrari’s hard-tire stint during lap 24-40 showed a 0.4s drop-off per lap compared to McLaren, proving their suspension geometry is still chewing through the rears in high-speed corners."
- Example 2: "If you look at the speed trap data from Sector 2, Red Bull was running a significantly higher downforce wing configuration, sacrificing 5 km/h on the main straight to protect their tires."

### Label 2: hot_take

- Definition: A bold, highly opinionated, or speculative claim about a driver's skill, team future, or sport politics, stated confidently but entirely lacking objective data, mathematical evidence, or technical justification.
- Example 1: "Hamilton is completely washed and Ferrari is going to experience immediate buyer's remorse by race three next season."
- Example 2: "The FIA intentionally delayed the safety car car deployment just to manipulate the podium positions and create artificial drama for TV ratings."

### Label 3: emotional_reaction

- Definition: An immediate emotional outburst, celebration, or vent regarding a live track event, crash, driver radio message, or race result, containing little to no analytical argumentation or forward-looking speculation.
- Example 1: "OH MY GOD WHAT A MOVE BY NORRIS OUTSIDE OF TURN 4!!! GET IN THERE!!!"
- Example 2: "Unbelievable. Another absolute disaster-class strategy from the pit wall. I can't take this pain anymore, my Sunday is completely ruined."

## 3. Boundary Stress-Testing & Edge Cases

### The Hardest Anticipated Edge Case (The "Hybrid" Post)

- Uncertain Post Scenario: A post combines heavy opinionated anger with a single decorative or cherry-picked piece of evidence.
- Example: "Lando Norris has zero championship mentality. He has literally lost the lead on Lap 1 from pole position four separate times this season. He is a total fraud who chokes under the slightest pressure."
- The Conflict: This sits directly on the boundary between hot_take (due to the aggressive, subjective framing: "zero championship mentality", "total fraud") and technical_analysis (due to referencing a specific, verifiable statistic: "lost the lead on Lap 1 from pole four times").

### Explicit Decision Rule

The Decision Rule: To be labeled as technical_analysis, the text must use its data to build an objective explanatory argument or mechanical hypothesis. If a specific stat is merely weaponized to validate an emotional insults or subjective character judgments, it will be classified as a hot_take.

Because the example above uses a real stat purely as decorative backup for a sweeping subjective claim ("total fraud"), it is officially classified as a hot_take.

## 4. Data Collection & Evaluation Plan

### Data Collection Plan

- Source: Publicly available posts and comments from $r/formula1$ collected across historical race threads and post-race discussion boards.
- Annotation Process: Text will be extracted into a CSV containing a text and label column. I will manually review and label a minimum of 200 items to ensure a balanced distribution where no single label exceeds 70% of the dataset, aiming for a target distribution of roughly 30% Analysis, 35% Hot Takes, and 35% Reactions.

### Evaluation Metrics & Justification

- Metrics Selected: Macro F1-Score, Per-Class Precision/Recall, and Overall Accuracy.
- Justification: While overall accuracy gives a quick snapshot, it can mask systematic failures on minority classes. In a community dataset, technical_analysis posts will naturally be scarcer than emotional_reaction bursts. Precision ensures that when the model flags something as a technical analysis, it is genuinely high-value text. Recall ensures we aren't missing valid analytical threads. The Macro F1-Score will serve as our ultimate balancer across the three distinct categories.

### Definite Performance Threshold ("Good Enough")

- Concrete Success Threshold: The fine-tuned model must achieve an Overall Accuracy of $\ge 75\%$ on the locked test set, and no individual class may drop below an F1-Score of 0.65.
- Reasoning: Because human textual expression contains natural sarcasm and contextual ambiguity, an accuracy above 75% indicates the model has successfully generalized structural community rules rather than memorizing random keywords.

## 5. AI Tool Plan

- Intended Use Case: Label Stress-Testing & Annotation Assistance.
- Execution Strategy: I will prompt an external LLM to act as a adversarial annotator. I will feed it my precise label definitions and ask it to generate 15 borderline, ambiguous phrases that intentionally blur the lines between hot_take and technical_analysis. This will allow me to refine my decision rules before I begin manually tagging my 200-row CSV, drastically minimizing human labeling drift.
