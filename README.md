# Statistical Model Checking for robustness verification of different control algorithms

This repository contains the UPPAAL SMC model created for the paper "Application of Statistical Model Checking for Robustness Comparison of Power Electronics Controllers" presented at the conference PEDG 2024: [presentation](https://github.com/Sapica77/SMC_for_PES_control_robustness/blob/main/Presentation/Application%20of%20SMC%20for%20Robustness%20Comparison%20of%20Power%20Electronics%20Controllers.pdf)  and [paper](https://github.com/aipoweraau/SMC_PES/blob/main/SMC_Full_paper_.pdf)


# Abstract

Power electronic-based systems exhibit non-linear dynamics requiring simultaneous control of multiple control objectives. Therefore, controllers that can cope with those nonlinearities are expected to perform better than controllers requiring system linearization or nesting of the control objectives in a cascaded structure. However, the problem remains how to quantify their robustness and compare different non-linear controllers fairly. The conventional tools used for the robustness validation of linear controllers cannot directly be applied to different non-linear controllers. Therefore, this paper demonstrates an approach based on statistical model checking for performing controller comparisons. The performance and robustness of different controllers (linear, model predictive, and neural networks-based) were compared in the same stochastic environment. Using this approach, a statistical estimate can be obtained for how the controller performance will be affected under different scenarios.


# Quick start

To open the model it is required to download the UPPAAL toolbox from https://uppaal.org/downloads/. The software is free for academic use. UPPAAL is an integrated environment that supports the modeling, validation, and verification of real-time systems. The models are prepared as networks of hybrid timed automata, which enables connecting individual components. They can model both deterministic state-based behavior, non-linear behavior based on ordinary differential equations (ODEs), and stochastic behavior in the same models.

<p align="center">
  <img src="https://github.com/aipoweraau/SMC_PES/blob/main/Presentation/Timed_automaton.png"  width="750px"/>
</p>


# Model structure

The model schematic is illustrated below. The two-level voltage source converter (2L-VSC) with an LC filter at the output supplies the passive load. Three different controllers were modeled for this system: Linear controller [1], model predictive controller [2], and NN controller [3].

<p align="center">
  <img src="https://github.com/aipoweraau/SMC_PES/blob/main/Presentation/System_info.png"  width="750px"/>
</p>


The model contains 6 templates: 
- *TwoLevel_VSC_LC_with_R_load* (controller a) - which contains the model of the 2L-VSC and linear control algorithm proposed in [1]
  - The user can modify the linear controller gains in the *Init* location
- *TwoLevel_VSC_MPC_with_R_load* (controller b) - which contains the model of the 2L-VSC and model predictive algorithm proposed in [2]
- *TwoLevel_VSC_ANN_with_R_load* (controller c) - which contains the model of the 2L-VSC and neural networks algorithm proposed in [3]
  - The user can modify the weights and bias of the network and adjust the network size
- *PWM* - modulator used with the model using linear control method (TwoLevel_VSC_LC_with_R_load)
- *TwoStateLoadModel* - a simple two-location model of resistive and stochastic load
- *RMSD* - it calculates the root mean square difference of reference and measured load voltage signal

<p align="center">
  <img src="https://github.com/aipoweraau/SMC_PES/blob/main/Presentation/Model_templates.png"  width="300px"/>
</p>


In the *system declarations* section, the user can change the parameters of the converter system:
  - DC-link voltage, reference voltage amplitude, and frequency (Vdc, ref_amp, ref_freq)
  - LC filter parameters (L_filter, C_filter, R_filter)
  - PWM switching frequency (sw_freq)
  - Weighting factors in the MPC cost function (K<sub>1</sub>, K<sub>2</sub>)
  - Parameters of the load which include the value and max & min durations of the load condition before transition to high load and vice-versa (low_load, min_low_time, max_low_time, high_load,min_high_time, max_high_time

<p align="center">
  <img src="https://github.com/aipoweraau/SMC_PES/blob/main/Presentation/System_parameters.png"  width="750px"/>
</p>


# Run the model simulation

Using the *Verifier* tab it is possible to simulate the model using the prepared *simulate* queries and obtain the load voltage, load current, converter current, and reference voltage.
Example:

<p align="center">
  <img src="https://github.com/aipoweraau/SMC_PES/blob/main/Presentation/Example_simulator_output.png"  width="750px"/>
</p>

# Model use and verification queries 

The model is used to compare the robustness of the three controllers (linear, MPC, NN) during:
1) Load transients (the load will change stochastically during simulations)
2) Parameter miss-match (e.g. between the prediction model and physical model of the output filter)
3) Different operating conditions (e.g. reference change, lower DC-link voltage)

The parameters can be changed in *System declarations*. Verification queries are prepared to estimate each controller's maximum root mean squared difference (RMSD). The results can be viewed using the *Plot Composer* in the *Tools* menu.

# References

[1] F. de Bosio, L. A. de Souza Ribeiro, F. D. Freijedo, M. Pastorelli, and J. M. Guerrero, “Effect of state feedback coupling and system delays on the transient performance of stand-alone VSI with LC output filter,” IEEE Trans. Ind. Electron., vol. 63, no. 8, pp. 4909–4918, 2016.

[2] T. Dragicevic, “Model predictive control of power converters for robust and fast operation of AC microgrids,” IEEE Trans. Power Electron., vol. 33, no. 7, pp. 6304–6317, 2018.

[3] M. Novak and T. Dragicevic, “Supervised imitation learning of finite set model predictive control systems for power electronics,” IEEE Trans. Ind. Electron., vol. 68, no. 2, pp. 1717–1723, 2021

[4] M. Novak, I. Grobelna, U. Nyman, P. Szczesniak, and F. Blaabjerg, “Modular modeling and statistical validation for grid-connected FSMPC controlled matrix converters,” IEEE Trans. Ind. Electron., vol. 70, no. 9, pp. 8613–8623, 2023

