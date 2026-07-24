Prescriptive Soft-Sensor for Distillation Slippage

<u><b>PROPRIETARY NOTICE: </b></u>
This repository contains the architectural overview, methodology, and validation results of the Soft Sensor. Due to the proprietary nature of the industrial deployment, operational LIMS and DCS datasets are strictly confidential and are not available for public distribution.

<b>Executive Summary</b> <br>
In industrial petrochemical operations, real-time quality control of distillation is severely constrained by analytical latency. Traditional manual laboratory analyzes (LIMS) introduce feedback dead-times of 4 to 12 hours, forcing reactive plant operations. Furthermore, physical online Gas Chromatographs (GCs) are highly susceptible to mechanical wear, sample line blockages, and severe calibration drift.
This project details the development of a high-fidelity Digital Twin designed to predict C4 slippage in a heavy naphtha bottoms stream. By subordinating machine learning to the laws of chemical engineering, this architecture successfully replaced a faulty hardware analyzer, achieving a dynamic deployment R2 of 0.819.

<b>Scalability & Hardware Independence</b><br>
Crucially, this digital twin was deliberately designed to exclude any inputs from physical online analyzers. This bypasses hardware failure modes and makes the architecture highly scalable, allowing it to be deployed on any fractionation column without the prerequisite of cost-intensive physical sensors.

<b>The Challenge:</b> Hardware Failure & Data Scarcity
1. Hardware Degradation: The physical online analyzer had lost absolute calibration, resulting in a baseline tracking accuracy of -2.991 R2 (rendering it completely unviable for plant control).
2. Data Constraints: Due to historian logging gaps, the continuous/workable operational window available for model training was strictly limited to nine months. Standard data-heavy AI models fail under these constraints as they lack the sheer volume of data required to autonomously learn physical plant rules.

<b>Methodology:</b><br> The Physics-Informed Approach
Standard data science models fail in chemical plants due to "chemical blindness" and time-travel leakage. This architecture overcomes these hurdles through strict domain-knowledge integration:
1. Thermodynamic Feature EngineeringRather than feeding raw temperatures and pressures into the AI, the data was mathematically transformed into fundamental vapor-liquid equilibrium (VLE) proxies:
- Pressure Compensated Temperature (PCT): Neutralizing overhead condenser pressure swings to reveal the true internal energy state.
- Concentration Wave (Delta-T): Tracking the temperature gradient across the column to identify hydraulic pinching and mass-transfer collapse.
2. Residence Time Alignment
  Statistical cross-correlation often hallucinates false "phase delays" based on operator shift patterns. This model rejected statistical correlation in favor of physical unit hydraulics by enforcing a strict residence time alignment for fluid travel from the upstream cracking heaters down to the distillation bottoms.

<b>The Hybrid AI Architecture</b><br>
To resolve the severe multicollinearity of DCS sensor arrays and prevent extrapolation failure, a two-stage hybrid engine was deployed:
1. The Linear Thermodynamic Anchor: Projects the high-dimensional sensor matrix into a low-dimensional space, acting as a highly stable, linear mass and energy balance calculator.
2. The Non-Linear Corrector: A heavily regularized machine learning model trained strictly on the residual errors of the linear anchor to map complex, non-linear thermodynamic drift.
3. The Dynamic Feedback Loop: A real-time bias updating mechanism dynamically calibrates the engine against physical laboratory results, absorbing long-term unmeasured equipment fouling and catalyst degradation.

<b>Validation & Results</b><br>
The architecture was evaluated using a strict chronological walk-forward split to prevent target interpolation leakage.
- Physical Hardware Analyzer R2: -2.991
- Soft Sensor Static R2 (No Lab Update): 0.645
- Soft Sensor Dynamic R2 (Deployment Ready): 0.819

<img width="2969" height="1464" alt="image" src="https://raw.githubusercontent.com/iaryankamboj/soft-sensor-distillation/refs/heads/main/2_final_architecture.png" />
<i>(Visualizing the replacement of the faulty hardware analyzer with the high-fidelity Digital Twin)</i><br>

<br><b>Prescriptive Diagnostics (Explainable AI)</b>
To ensure algorithmic transparency for board operators, explainable AI protocols were integrated. Instead of acting as a "Black Box," the sensor generates real-time Waterfall plots, diagnosing the exact root cause of an impending C4 spike (e.g., distinguishing between a localized reboiler failure vs. an upstream feed-surge flooding event).

<img width="2969" height="1464" alt="image" src="https://github.com/iaryankamboj/soft-sensor-distillation/blob/main/3_waterfall_upset_plot.png?raw=true" />

Root Cause Diagnosis (The Destabilizing Factors):
- Cold Stripping Section (pct_strp_ctrl = -1.245): The primary culprit behind the upset. The pressure-compensated temperature at the bottom of the column dropped severely (1.25 standard deviations below the mean). Lacking sufficient thermal energy to vaporize the light ends, the C4 molecules remained trapped in the liquid phase. This localized cooling event was the strongest factor driving the prediction upward toward a spike (indicated by the large red +0.04 impact).
- Column Pinching (column_delta_T = -1.246): The temperature gradient across the entire column collapsed simultaneously. Hydraulically, this indicates a loss of internal vapor-liquid mass transfer efficiency (column pinching), which compounded the slippage (red +0.03 impact).

Mitigating Factors:
- Upstream Reflux (dp_reflux_sv = -0.385): Interestingly, the upstream Depropanizer reflux was running slightly below its historical average. The AI identified this as a mitigating factor (blue -0.05 impact) that actually prevented the spike from being significantly worse. Had the upstream unit been surging or flooding into the Debutanizer feed during this low-temperature event, the resulting bottoms contamination would have been catastrophic.

Operational Value: <br>
In a live control room, this granular level of explainability fundamentally shifts the operating paradigm. Rather than receiving a generic high-impurity alarm, board operators are instantly provided with the mathematical root cause. In this specific scenario, the operator is explicitly directed to investigate localized reboiler duty and hydraulic tray efficiency, rather than wasting critical response time hunting for upstream cracking or feedstock disturbances.

<br>

<i> Architected and Developed by Aryan Kamboj (June-July 2026) </i>
