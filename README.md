
# Thought Process Behind Each Step

THOUGHT PROCESS BEHIND EACH STEP



1. Importing and Merging the Datasets

Why: We were given two Excel files representing battery test logs. To analyze the full experiment, we needed to combine them.

Challenge: Both files had Test_Time(s) starting from zero, causing discontinuities.

Fix: We parsed Test_Time(s) into seconds, and offset the second file's time to start right after the first one ended.



2. Parsing Test_Time(s) Correctly

Why: To compute capacity transferred (delta_c), we needed precise time differences.

Challenge: Time was in mixed formats like '00:00:01' and '1-11:15:39' (i.e., D-HH:MM:SS).

Fix: Wrote a custom parser function to convert all formats to total seconds, ensuring consistency across datasets.



3. Separating Charge and Discharge Phases

Why: Charging and discharging behave differently and need to be analyzed separately.

Approach: Used Current(A) > 0 for charging and < 0 for discharging.

Fix: Ensured that the discharge current was used as absolute value when calculating capacity, since discharge current is negative.



4. Per-Cycle Capacity Calculation using Right Riemann Sum

Why: As per the assignment, we used the textbook formula.

Challenge: Some delta_t values were unrealistically large (e.g., 76,000 seconds), inflating capacity.

Fix: Filtered out rows where delta_t > 100 seconds and capped extreme currents (< 7 A), keeping only physically meaningful entries.



5. Visualizing Charge and Discharge Capacity

Why: Plots help interpret battery performance and verify correctness.

Challenge: Initial plots showed 100+ Ah capacity — physically impossible.

Fix: After cleaning delta_t and rebasing time, capacity dropped to realistic values (~2.3 Ah initially, degrading over cycles), confirming our fix.



6. State of Health (SoH) Calculation

Why: SoH is a critical metric to quantify battery degradation.

Approach: Defined SoH as the ratio of capacity at each cycle to the max initial capacity.

Challenge: SoH initially showed negative and >100% values due to bad delta_c values.

Fix: Computed SoH after fully cleaning and smoothing capacity data — SoH now starts at 100% and gradually declines as expected.



7. Polynomial Regression to Model Capacity Degradation

Why: To visualize and extrapolate the degradation pattern over cycles.

Approach: Fit a 2nd-degree polynomial on Charge_Capacity_RR vs Cycle_Index.

Outcome: A smooth curve accurately capturing early-cycle degradation and tapering, which can help forecast battery life.



### Final Thoughts:
Biggest Challenge: Data cleaning — especially fixing time resets and removing unrealistic current spikes — was essential to make the analysis physically meaningful.

Lesson Learned: Even with good formulas and models, real-world data needs a lot of cleaning before applying any statistical or ML methods.

