Hybrid Thermodynamic-AI Soft Sensor architecture for fractional distillation columns.

Prescriptive Soft-Sensor for Distillation C4 Slippage
A Hybrid Thermodynamic-Artificial Intelligence Architecture

PROPRIETARY NOTICE:
This repository contains the architectural overview, methodology, and validation results of the Soft Sensor. Due to Non-Disclosure Agreements (NDA) and the proprietary nature of the live industrial deployment pipeline, the raw source code (.py) and operational LIMS/DCS datasets (.csv) are held in a private, locked repository and are not available for public distribution.

Executive Summary
In industrial petrochemical operations, real-time quality control of distillation is constrained by analytical latency. Traditional manual laboratory analyzes (LIMS) introduce feedback dead-times of 4 to 12 hours, while hardware Gas Chromatographs (GCs) suffer from mechanical wear and severe calibration drift.
This project details the development of a high-fidelity Soft Sensor designed to predict C4 in a Debutanizer column bottoms stream. By subordinating machine learning to the laws of chemical engineering, this architecture achieved a dynamic deployment R2 of 0.819, successfully shifting the operational paradigm from reactive to proactive control.

Methodology: The Physics-Informed Approach
Standard Data Science models fail in chemical plants due to "chemical blindness" and time-travel leakage. This architecture overcomes these hurdles through strict domain-knowledge integration:
1. Thermodynamic Feature Engineering
Rather than feeding raw temperatures and pressures into the AI, the data was mathematically transformed into fundamental vapor-liquid equilibrium (VLE) proxies:
	Pressure-Compensated Temperature (PCT): Neutralizing overhead condenser pressure swings.
	Concentration Wave (∆T): Tracking the temperature gradient across the column to identify hydraulic pinching and mass-transfer collapse.
2. Multi-Speed Time Warping (Fluid Dynamics)
Statistical cross-correlation often hallucinates false "phase delays" based on operator shift patterns. This model rejected statistical correlation in favor of physical unit hydraulics:
	Enforced a strict maximum 1.0-hour residence time for fluid travel from upstream fractionation units to the Debutanizer bottoms.
	Implemented Dynamic Volumetric Time-Warping, adjusting the historical lookback window in real-time based on active plant throughput (Q).

The Hybrid AI Architecture
To resolve the severe multicollinearity of DCS sensor arrays, a two-stage hybrid engine was deployed:
	The Linear Anchor (DPLS): Dynamic Partial Least Squares projects the high-dimensional sensor matrix into a low-dimensional space, acting as a highly stable, linear mass and energy balance calculator.
	The Non-Linear Corrector (XGBoost): An Extreme Gradient Boosting model, heavily constrained by L2 Ridge penalties and stochastic dropout, is trained strictly on the residual errors of the DPLS model to map complex, non-linear thermodynamic drift.
	The Decoupled EWMA Kalman Filter: A real-time Exponentially Weighted Moving Average feedback loop (Gain = 0.35) dynamically calibrates the engine against physical LIMS results to absorb long-term unmeasured equipment fouling.

Validation & Results
The architecture was evaluated using a strict chronological walk-forward split to prevent target interpolation leakage.
	Static R2 (Pure AI / No Lab): 0.645
	Dynamic R2 (Deployment / EWMA): 0.819

<img width="2969" height="1464" alt="image" src="https://raw.githubusercontent.com/iaryankamboj/soft-sensor-distillation/refs/heads/main/2_final_architecture.png" />

Prescriptive Diagnostics (SHAP)
To ensure algorithmic transparency for board operators, SHapley Additive exPlanations (SHAP) were integrated. Instead of acting as a "Black Box," the sensor generates real-time Waterfall plots, diagnosing the exact root cause of an impending C4 spike (e.g., distinguishing between a localized reboiler failure vs. an upstream feed-surge flooding event).

<img width="2969" height="1464" alt="image" src="https://github.com/iaryankamboj/soft-sensor-distillation/blob/main/4_shap_waterfall_upset.png?raw=true" />

Developed by Aryan Kamboj (June-July 2026)
