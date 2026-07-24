Prescriptive Soft-Sensor for Distillation Slippage

PROPRIETARY NOTICE:
This repository contains the architectural overview, methodology, and validation results of the Soft Sensor. Due to the proprietary nature of the industrial deployment, operational LIMS and DCS datasets are strictly confidential and are not available for public distribution.

Executive Summary
In industrial petrochemical operations, real-time quality control of distillation is severely constrained by analytical latency. Traditional manual laboratory analyzes (LIMS) introduce feedback dead-times of 4 to 12 hours, forcing reactive plant operations. Furthermore, physical online Gas Chromatographs (GCs) are highly susceptible to mechanical wear, sample line blockages, and severe calibration drift.
This project details the development of a high-fidelity Digital Twin designed to predict C4 slippage in a heavy naphtha bottoms stream. By subordinating machine learning to the laws of chemical engineering, this architecture successfully replaced a faulty hardware analyzer, achieving a dynamic deployment R2 of 0.819.

Scalability & Hardware Independence
Crucially, this digital twin was deliberately designed to exclude any inputs from physical online analyzers. This bypasses hardware failure modes and makes the architecture highly scalable, allowing it to be deployed on any fractionation column without the prerequisite of cost-intensive physical sensors.

The Challenge: Hardware Failure & Data Scarcity
1. Hardware Degradation: The physical online analyzer had lost absolute calibration, resulting in a baseline tracking accuracy of -2.991 R2 (rendering it completely unviable for plant control).
2. Data Constraints: Due to historian logging gaps, the continuous operational window available for AI training was strictly limited to nine months. Standard data-heavy AI models fail under these constraints as they lack the sheer volume of data required to autonomously learn physical plant rules.

Methodology: The Physics-Informed Approach
Standard data science models fail in chemical plants due to "chemical blindness" and time-travel leakage. This architecture overcomes these hurdles through strict domain-knowledge integration:
1. Thermodynamic Feature EngineeringRather than feeding raw temperatures and pressures into the AI, the data was mathematically transformed into fundamental vapor-liquid equilibrium (VLE) proxies:
- Pressure Compensated Temperature (PCT): Neutralizing overhead condenser pressure swings to reveal the true internal energy state.
- Concentration Wave (Delta-T): Tracking the temperature gradient across the column to identify hydraulic pinching and mass-transfer collapse.
2. Residence Time Alignment
  Statistical cross-correlation often hallucinates false "phase delays" based on operator shift patterns. This model rejected statistical correlation in favor of physical unit hydraulics by enforcing a strict residence time alignment for fluid travel from the upstream cracking heaters down to the distillation bottoms.

The Hybrid AI Architecture
To resolve the severe multicollinearity of DCS sensor arrays and prevent extrapolation failure, a two-stage hybrid engine was deployed:
1. The Linear Thermodynamic Anchor: Projects the high-dimensional sensor matrix into a low-dimensional space, acting as a highly stable, linear mass and energy balance calculator.
2. The Non-Linear Corrector: A heavily regularized machine learning model trained strictly on the residual errors of the linear anchor to map complex, non-linear thermodynamic drift.
3. The Dynamic Feedback Loop: A real-time bias updating mechanism dynamically calibrates the engine against physical laboratory results, absorbing long-term unmeasured equipment fouling and catalyst degradation.

Validation & Results
The architecture was evaluated using a strict chronological walk-forward split to prevent target interpolation leakage.
- Physical Hardware Analyzer R2: -2.991
- Soft Sensor Static R2 (No Lab Update): 0.645
- Soft Sensor Dynamic R2 (Deployment Ready): 0.819

<img width="2969" height="1464" alt="image" src="https://raw.githubusercontent.com/iaryankamboj/soft-sensor-distillation/refs/heads/main/2_final_architecture.png" />
(Visualizing the replacement of the faulty hardware analyzer with the high-fidelity Digital Twin)
(Chronological Tracking Performance)

Prescriptive Diagnostics (Explainable AI)
To ensure algorithmic transparency for board operators, explainable AI protocols were integrated. Instead of acting as a "Black Box," the sensor generates real-time Waterfall plots, diagnosing the exact root cause of an impending C4 spike (e.g., distinguishing between a localized reboiler failure vs. an upstream feed-surge flooding event).

<img width="2969" height="1464" alt="image" src="https://github.com/iaryankamboj/soft-sensor-distillation/blob/main/4_shap_waterfall_upset.png?raw=true" />


Architected and Developed by Aryan Kamboj (June-July 2026)
