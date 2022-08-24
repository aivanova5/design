# Simulation Design 

## File List 

* config.csv 
* controls.py 
* network.glm 
* loads.glm
* output.glm
* postprocess.py
* output/ 
  - billing.csv 
  - powerdump.csv
  - voltagedump.csv
  - currentdump.csv



## Simulation Flow 

```mermaid 
flowchart LR 

  classDef active stroke-width:4px;
  
  config--> loads --> network-->output
  config --> controls --> network
  controls --> loads
  

```
