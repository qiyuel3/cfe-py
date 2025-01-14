# Python version of the NWM Conceptual Functional Equivalent (CFE) model

This README file is adapted from the one in Jonathan Frame's repository https://github.com/jmframe/cfe.git under the py_cfe folder

This directory contains a Python version of the same CFE model as written in C contained in the `../src/` directory. This version is intended for learning and quick prototyping model development, but should also be fully functional within the Nextgen Framework.

# Files
## environment.yml: 
This is an environment file with the required Python libraries needed to run the model with BMI. Create the environment with this command: `conda env create -f environment.yml`, then activate it with `conda activate bmi_cfe` 
## cfe.py
This is the main model code. The input to this model is a CFE State, which can be either a Python class, or a dictionary. The only requirement is that the object contains the entire model running state (Not just state variables), which concists of the following:  
 - Forcings
    - timestep_rainfall_input_m
    - potential_et_m_per_s
 - Parameters
    - soil_params
    - K_nash
    - etc.
 - Volume trackers
    - volin
    - vol_to_gw
    - volout
    - etc.
 - Fluxes
    - flux_Qout_m
    - flux_giuh_runoff_m
    - flux_nash_lateral_runoff_m
    - flux_from_deep_gw_to_chan_m
    - total_discharge
 - Etc.  
 The model code takes the running state and calculates all the fluxes and corresponding state changes. The running state contains the single timestep changes, nothing is "returned" from the function. All the processing and interpretation of the model should take place in the driving code. An example of driving code is below.
 
 ## bmi_cfe.py
This is the code for the Basic Model Interface (BMI) that is used to call the cfe and interact with other models via the Framework, or driving code. This code contains all the required BMI functions to run the CFE, including 
 - initialize: Perform startup tasks for the model.
 - update: Advance model state by one time step. Calls the function `run_cfe` from `cfe.py`
 - update_until: Advance model state until the given time.
 - finalize: Perform tear-down tasks for the model.
 - get_value: Get a copy of values of a given variable.
 - set_value: Set the values of a given variable.
 - etc.  
 These functions need to be called by a framework or driving code, an example of which is below.

 ## run_cfe_bmi.ipynb
 This file includes an example run and a unit test run for CFE. The Jupyter notebook is good for visualizing the results. Notice that there are blocks of code that call all the functions listed above. These are the main BMI functions that allow us to control and run the model. This example requires a configuration file, which BMI uses to set the specifics of the model, including how to use Forcings. More on the configuration file below.
 
 ## cat_58_config_cfe.json
 This file has all the information to configure the model for a specific basin. The forcing file can be specified to run the a comparison with the origional model code (i.e. cat58_01Dec2015.csv), and there should be a corresponding file with the output from the test (i.e. test_compare.csv). In general the model should be run getting forcing from the driver using the set_value function. Some of the values in the config file will come from the NWM parameters, and some will be calibrated. Some values are basin specific, and need to be set to get the correct results for the basin, for instance the catchment_area_km2 is needed to convert the runoff to a volume flux, rather than a depth.
