# CrowdNav

![Banner](https://raw.githubusercontent.com/Starofall/CrowdNav/master/banner.PNG)


### Description
CrowdNav is a simulation based on SUMO and TraCI that implements a custom router
that can be configured using kafka messages or local JSON config on the fly while the simulation is running.
Also runtime data is send to a kafka queue to allow stream processing and logger locally to CSV.

### Minimal Setup
* Download the CrowdNav code
* Run `python setup.py install` to download all dependencies 
* Install [SUMO](http://sumo.dlr.de) & set env var SUMO_HOME
   1. Download SUMO from http://prdownloads.sourceforge.net/sumo/sumo-src-0.32.0.zip?download
   2. Extract and note the full path to the directory
   3. Set SUMO_HOME to point to this directory
* Install [Kafka](https://kafka.apache.org/) (we recommend [this](https://hub.docker.com/r/spotify/kafka/) Docker image) and set kafkaHost in Config.py
* Run `python run.py`

### Getting Started Guide
A first guide on how to use (i.e. adapt, measure, optimize) CrowdNav with the [RTX tool](https://github.com/Starofall/RTX) is available at this [Wiki page](https://github.com/Starofall/RTX/wiki/RTX-&-CrowdNav-Getting-Started-Guide). 

### Operational Modes

* Normal mode (`python run.py`) with UI to Debug the application. Runs forever.
* Parallel mode (`python parallel.py n`) to let n processes of SUMO spawn for faster data generation.
  Stops after 10k ticks and reports values.
  
### Further customization

* Runtime variables are in the knobs.json file and will only be used if `kafkaUpdates = True
` is set to false in `Config.py`. Else the tool uses Kafka for value changes.
* To disable the UI in normal mode, change the `sumoUseGUI = True` value in `Config.py` to false.

### Notes

* To let the system stabalize, no message is sent to kafka or CSV in the first 1000 ticks .


### Further extension to add one output metric function Average feedback

Logic for a new metric function added in the "app/entity/Car.py" file. The feedback is assumed as the user satisfaction result. Based on the tripOverhead a corresponding trip feedback is calculated. For the experiment purpose we consider the values from 1 to 5. As the feedback is calculated totally based on the overhead, so the feedback calculation logic can be handled different way if there is significant changes in values for overheads. The piece of code showing the logic of feedback control can be shown below - 

 ```
 if tripOverhead==1:
                    tripFeedback=5
            if 1<tripOverhead<1.7:
                    tripFeedback=4
            if 1.7<=tripOverhead<=2.2:
                    tripFeedback=3
            if tripOverhead>2.2:
                    tripFeedback = random.randint(1,2)
 ```

The individual feedback from each sample is collected and added to count the average feedback. After all sampling, the final average feedback is added to the kafka messaging. Apart from Average trip overhead, the Average trip feedback consists of discrete or categorical values from the categories of 1 to 5. 5 is the best feedback while 1 is the worst. 
