---
title: "Process Mining Demo"
excerpt: "A showcase of various process mining methodologies."
toc: true
toc_label: "Table of Contents"
toc_sticky: true
---

# Mining Business Processes: Use Case on Purchasing Business Processes
Dataset source: [Disco-Demo-Logs](https://fluxicon.com/disco/files/Disco-Demo-Logs.zip)<br>

Process mining is a subset of the data mining field that bridge the fields of data science and process management to support the analysis of operational processes based on event data (e.g. event logs). The goal of process mining is to turn event data into insights and actions. The events found in event data typically refer to activities executed by resources at particular times and for particular cases. Event data are collected everywhere: in logistics, manufacturing, finance, healthcare, e-learning, egovernment, and many other domains. Therefore, process mining can also be applied in almost every domains. Several industries, in particular, have applied process mining extensively: Energy & Materials (e.g., oil & gas); Industrials (e.g., construction & engineering); Consumer Goods (e.g., food, automobiles & parts, personal products); Consumers Services (e.g., media, leisure, education); Healthcare (e.g., healthcare providers, medical research); Utilities (e.g., telecommunications, electricity); Financial (e.g., banking); and Technology (e.g., software and services). The application of process mining in those industries are well-compiled in the literature review by Zerbino et al.<br>

Process mining provides the means to discover the model of real processes based on the actual event data, detect deviations of real processes compared to its standard operating procedures/business as usual state, analyze bottlenecks of the real processes, calculate waste from such bottleneck events, etc (Wil, 2018). However, process mining tends to be **backward-looking**, meaning that process mining alone shouldn't be used to explore the impact of different process design alternatives or different scenarios. To explore them, it is better to utilize simulation, especially Discrete Event Simulation (DES), based on the model of real processes that has been discovered by process mining.<br>

Many software programs have been created for process mining. Some require paid license (e.g. [Disco by Fluxicon](https://fluxicon.com/disco/)) while some are open-source (e.g., [PM4Py by Fraunhofer Institute for Applied Information Technology](https://pm4py.fit.fraunhofer.de/) and [ProM By Process Mining Group and TU/e](http://www.promtools.org/doku.php)). For more flexibility on showcasing the use case, the open-source Python library PM4Py is used as the main process mining software.

## Data Import
To emphasize the showcase of process mining algorithms on this use case, the CRoss Industry Standard Process for Data Mining (CRISP-DM) is **not used for this use case**. However, in general, process mining projects still follow CRISP-DM.<br>
<center><img src="https://www.datascience-pm.com/wp-content/uploads/2021/02/CRISP-DM.png" alt="The flow of CRISP-DM"/></center><br><br>
First, the event data or event log is imported.


```python
# from CSV or Excel
import pandas as pd

raw_df = pd.read_csv(r"C:\Users\Torivor\Documents\Learning activities\Projects\Datasets for practice\Process mining examples\Disco-Demo-Logs\Demo Logs\PurchasingExample.csv")
raw_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Case ID</th>
      <th>Start Timestamp</th>
      <th>Complete Timestamp</th>
      <th>Activity</th>
      <th>Resource</th>
      <th>Role</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>339</td>
      <td>2011/02/16 14:31:00.000</td>
      <td>2011/02/16 15:23:00.000</td>
      <td>Create Purchase Requisition</td>
      <td>Nico Ojenbeer</td>
      <td>Requester</td>
    </tr>
    <tr>
      <th>1</th>
      <td>339</td>
      <td>2011/02/17 09:34:00.000</td>
      <td>2011/02/17 09:40:00.000</td>
      <td>Analyze Purchase Requisition</td>
      <td>Maris Freeman</td>
      <td>Requester Manager</td>
    </tr>
    <tr>
      <th>2</th>
      <td>339</td>
      <td>2011/02/17 21:29:00.000</td>
      <td>2011/02/17 21:52:00.000</td>
      <td>Amend Purchase Requisition</td>
      <td>Elvira Lores</td>
      <td>Requester</td>
    </tr>
    <tr>
      <th>3</th>
      <td>339</td>
      <td>2011/02/18 17:24:00.000</td>
      <td>2011/02/18 17:30:00.000</td>
      <td>Analyze Purchase Requisition</td>
      <td>Heinz Gutschmidt</td>
      <td>Requester Manager</td>
    </tr>
    <tr>
      <th>4</th>
      <td>339</td>
      <td>2011/02/18 17:36:00.000</td>
      <td>2011/02/18 17:38:00.000</td>
      <td>Create Request for Quotation Requester Manager</td>
      <td>Francis Odell</td>
      <td>Requester Manager</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9114</th>
      <td>1358</td>
      <td>2011/10/04 13:22:00.000</td>
      <td>2011/10/04 13:23:00.000</td>
      <td>Approve Purchase Order for payment</td>
      <td>Magdalena Predutta</td>
      <td>Purchasing Agent</td>
    </tr>
    <tr>
      <th>9115</th>
      <td>1358</td>
      <td>2011/10/11 06:41:00.000</td>
      <td>2011/10/11 06:41:00.000</td>
      <td>Send invoice</td>
      <td>Esmeralda Clay</td>
      <td>Supplier</td>
    </tr>
    <tr>
      <th>9116</th>
      <td>1358</td>
      <td>2011/10/11 20:53:00.000</td>
      <td>2011/10/11 20:57:00.000</td>
      <td>Release Supplier's Invoice</td>
      <td>Pedro Alvares</td>
      <td>Financial Manager</td>
    </tr>
    <tr>
      <th>9117</th>
      <td>1358</td>
      <td>2011/10/11 20:57:00.000</td>
      <td>2011/10/11 20:57:00.000</td>
      <td>Authorize Supplier's Invoice payment</td>
      <td>Pedro Alvares</td>
      <td>Financial Manager</td>
    </tr>
    <tr>
      <th>9118</th>
      <td>1358</td>
      <td>2011/10/11 21:52:00.000</td>
      <td>2011/10/11 22:02:00.000</td>
      <td>Pay invoice</td>
      <td>Pedro Alvares</td>
      <td>Financial Manager</td>
    </tr>
  </tbody>
</table>
<p>9119 rows × 6 columns</p>
</div>



### Raw Data Feature Engineering
One of the main setback on a purchasing business process is the dispute that often happens between the purchasing team and financial managers or purchasing agents. Therefore, cases with disputes should be labelled so they can be further analyzed and prevented.


```python
import numpy as np

'''
In process mining, the data that is generally used is an event log where each event in the log is part of a case, represents an activity in a specific duration or point in time.
Therefore, an event log can be seen as a collection of cases and a case can be seen as a trace/sequence of events.
Source: http://www.processmining.org/event-data.html
'''
raw_df['dispute existance - event level'] = ['dispute' if 'dispute' in value else 'no dispute' for value in raw_df['Activity'].values]

cases_with_disputes = list(raw_df[raw_df['dispute existance - event level'] == 'dispute'].loc[:, 'Case ID'].values)
cases_with_disputes_dict = dict(zip(cases_with_disputes, ['dispute' for case in cases_with_disputes]))

cases_without_disputes = list(raw_df[raw_df['dispute existance - event level'] == 'no dispute'].loc[:, 'Case ID'].values)
cases_without_disputes_dict = dict(zip(cases_without_disputes, ['no dispute' for case in cases_without_disputes]))

from collections import ChainMap

mapping_dict = ChainMap(cases_with_disputes_dict, cases_without_disputes_dict)

raw_df['dispute existance - case level'] = raw_df['Case ID'].map(mapping_dict)
```


```python
# Create feature to consider the day when each activity is completed
'''
The .strftime() property of datetime datatype returns full weekday name from the given datetime.
'''
raw_df['Day of week Completed'] = pd.to_datetime(raw_df['Complete Timestamp'],
                                         infer_datetime_format = True).dt.strftime('%A')
display(raw_df.head(5))
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Case ID</th>
      <th>Start Timestamp</th>
      <th>Complete Timestamp</th>
      <th>Activity</th>
      <th>Resource</th>
      <th>Role</th>
      <th>dispute existance - event level</th>
      <th>dispute existance - case level</th>
      <th>Day of week Completed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>339</td>
      <td>2011/02/16 14:31:00.000</td>
      <td>2011/02/16 15:23:00.000</td>
      <td>Create Purchase Requisition</td>
      <td>Nico Ojenbeer</td>
      <td>Requester</td>
      <td>no dispute</td>
      <td>no dispute</td>
      <td>Wednesday</td>
    </tr>
    <tr>
      <th>1</th>
      <td>339</td>
      <td>2011/02/17 09:34:00.000</td>
      <td>2011/02/17 09:40:00.000</td>
      <td>Analyze Purchase Requisition</td>
      <td>Maris Freeman</td>
      <td>Requester Manager</td>
      <td>no dispute</td>
      <td>no dispute</td>
      <td>Thursday</td>
    </tr>
    <tr>
      <th>2</th>
      <td>339</td>
      <td>2011/02/17 21:29:00.000</td>
      <td>2011/02/17 21:52:00.000</td>
      <td>Amend Purchase Requisition</td>
      <td>Elvira Lores</td>
      <td>Requester</td>
      <td>no dispute</td>
      <td>no dispute</td>
      <td>Thursday</td>
    </tr>
    <tr>
      <th>3</th>
      <td>339</td>
      <td>2011/02/18 17:24:00.000</td>
      <td>2011/02/18 17:30:00.000</td>
      <td>Analyze Purchase Requisition</td>
      <td>Heinz Gutschmidt</td>
      <td>Requester Manager</td>
      <td>no dispute</td>
      <td>no dispute</td>
      <td>Friday</td>
    </tr>
    <tr>
      <th>4</th>
      <td>339</td>
      <td>2011/02/18 17:36:00.000</td>
      <td>2011/02/18 17:38:00.000</td>
      <td>Create Request for Quotation Requester Manager</td>
      <td>Francis Odell</td>
      <td>Requester Manager</td>
      <td>no dispute</td>
      <td>no dispute</td>
      <td>Friday</td>
    </tr>
  </tbody>
</table>
</div>


### Converting from event data to event log format


```python
import pm4py

# format the raw dataframe with pm4py attributes to ensure accurate interpretation of the dataframe by pm4py modules.
formatted_df = pm4py.format_dataframe(raw_df, case_id='Case ID', activity_key='Activity', start_timestamp_key='Start Timestamp', timestamp_key = 'Complete Timestamp')

# convert the formatted dataframe into an event log, ensure that the dataframe has been
## formatted with pm4py attributes to ensure accurate interpretation of the dataframe by pm4py modules.
raw_event_log = pm4py.convert_to_event_log(formatted_df)

display(raw_event_log)
```


    [{'attributes': {'concept:name': '1'}, 'events': [{'Case ID': 1, 'Start Timestamp': Timestamp('2011-01-01 00:00:00+0000', tz='UTC'), 'Complete Timestamp': Timestamp('2011-01-01 00:37:00+0000', tz='UTC'), 'Activity': 'Create Purchase Requisition', 'Resource': 'Kim Passa', 'Role': 'Requester', 'dispute existance - event level': 'no dispute', 'dispute existance - case level': 'no dispute', 'Day of week Completed': 'Saturday', 'concept:name': 'Create Purchase Requisition', 'time:timestamp': Timestamp('2011-01-01 00:37:00+0000', tz='UTC'), '@@index': 0, 'start_timestamp': Timestamp('2011-01-01 00:00:00+0000', tz='UTC')}, '..', {'Case ID': 1, 'Start Timestamp': Timestamp('2011-01-04 15:22:00+0000', tz='UTC'), 'Complete Timestamp': Timestamp('2011-01-04 15:31:00+0000', tz='UTC'), 'Activity': 'Pay invoice', 'Resource': 'Pedro Alvares', 'Role': 'Financial Manager', 'dispute existance - event level': 'no dispute', 'dispute existance - case level': 'no dispute', 'Day of week Completed': 'Tuesday', 'concept:name': 'Pay invoice', 'time:timestamp': Timestamp('2011-01-04 15:31:00+0000', tz='UTC'), '@@index': 16, 'start_timestamp': Timestamp('2011-01-04 15:22:00+0000', tz='UTC')}]}, '....', {'attributes': {'concept:name': '997'}, 'events': [{'Case ID': 997, 'Start Timestamp': Timestamp('2011-05-25 04:20:00+0000', tz='UTC'), 'Complete Timestamp': Timestamp('2011-05-25 05:04:00+0000', tz='UTC'), 'Activity': 'Create Purchase Requisition', 'Resource': 'Kim Passa', 'Role': 'Requester', 'dispute existance - event level': 'no dispute', 'dispute existance - case level': 'no dispute', 'Day of week Completed': 'Wednesday', 'concept:name': 'Create Purchase Requisition', 'time:timestamp': Timestamp('2011-05-25 05:04:00+0000', tz='UTC'), '@@index': 9115, 'start_timestamp': Timestamp('2011-05-25 04:20:00+0000', tz='UTC')}, '..', {'Case ID': 997, 'Start Timestamp': Timestamp('2011-05-29 10:18:00+0000', tz='UTC'), 'Complete Timestamp': Timestamp('2011-05-29 10:44:00+0000', tz='UTC'), 'Activity': 'Analyze Request for Quotation', 'Resource': 'Magdalena Predutta', 'Role': 'Purchasing Agent', 'dispute existance - event level': 'no dispute', 'dispute existance - case level': 'no dispute', 'Day of week Completed': 'Sunday', 'concept:name': 'Analyze Request for Quotation', 'time:timestamp': Timestamp('2011-05-29 10:44:00+0000', tz='UTC'), '@@index': 9118, 'start_timestamp': Timestamp('2011-05-29 10:18:00+0000', tz='UTC')}]}]


### Integrating Engineered Features into the event log

The raw event log can be refined to be more informational by calculating some useful features such as the \*cycle time and lead time if the event log is an interval log (has a start and end timestamp).<br><br>
*Cycle time definition: time or duration it takes to complete a given activity or task.<br>
*Lead time definition: time it takes to complete the whole process, from the moment an order is created until the arrival/delivery of the product/service.


```python
from pm4py.objects.log.util import interval_lifecycle
from pm4py.util import constants

'''
To store the calculated cycle time and lead time, PM4PY assigns the values to the following attributes =
@@approx_bh_partial_cycle_time: Incremental cycle time associated to the event (the cycle time of the last event is the cycle time of the instance)
@@approx_bh_partial_lead_time: Incremental lead time associated to the event
@@approx_bh_overall_wasted_time: Difference between the partial lead time and the partial cycle time values
@@approx_bh_this_wasted_time: Wasted time ONLY with regards to the activity described by the ‘interval’ event
@@approx_bh_ratio_cycle_lead_time: Measures the incremental Flow Rate (between 0 and 1).
'''
refined_event_log = interval_lifecycle.assign_lead_cycle_time(raw_event_log, parameters={
    constants.PARAMETER_CONSTANT_START_TIMESTAMP_KEY: "start_timestamp",
    constants.PARAMETER_CONSTANT_TIMESTAMP_KEY: "time:timestamp"})
```

After calculating its cycle time and lead time, the features are extracted along with other additional information of the process based on the event log.


```python
# Manual feature selection
from pm4py.algo.transformation.log_to_features import algorithm as log_to_features

'''
str_ev_attr = String attributes at the event level: these are hot-encoded into features that may assume value 0 or value 1.
str_tr_attr = String attributes at the trace level: these are hot-encoded into features that may assume value 0 or value 1.
num_ev_attr = Numeric attributes at the event level: these are encoded by including the last value of the attribute among the events of the trace.
num_tr_attr = Numeric attributes at trace level: these are encoded by including the numerical value.
str_evsucc_attr = Successions related to the string attributes values at the event level: for example, if we have a trace [A,B,C], it might be important to include not only the presence of the single values A, B and C as features; but also the presence of the directly-follows couples (A,B) and (B,C).
'''

# Extracting additional information (exc. cycle time and lead time) about:
## The resource that executes each process for each case
## If a process execution contains, or not, a directly-follows path between different resources.
## The duration of each case.
## The workload of each resource.

from pm4py.algo.transformation.log_to_features.variants.trace_based import Parameters as log_to_features_trb_parameters

feature_parameters = {"str_ev_attr": ["Day of week Completed"],
                      "str_tr_attr": ["Role"],
                      "num_ev_attr": ["@@approx_bh_partial_cycle_time", "@@approx_bh_partial_lead_time",  "@@approx_bh_overall_wasted_time", "@@approx_bh_this_wasted_time", "@approx_bh_ratio_cycle_lead_time"],
                      "num_tr_attr": [],
                      "str_evsucc_attr": [],
                      log_to_features_trb_parameters.ENABLE_CASE_DURATION: True,
                      log_to_features_trb_parameters.RESOURCE_KEY: "Resource",
                      log_to_features_trb_parameters.ENABLE_RESOURCE_WORKLOAD: True}
feature_data, feature_names = log_to_features.apply(refined_event_log, parameters=feature_parameters)
print(len(feature_names),
      np.array(feature_data).shape,
      sep = '\n')
```

    41
    (608, 41)
    

## Business-Oriented Questions Answered by Process Mining Use Cases
Process Mining can answer a lot of business-oriented questions (Milani et al., 2022). For example, it can improve process transparency while improving its goals.

### Improving process TRANSPARENCY
1. Process Model Discovery<br>
    1.1 What process is being analyzed?

2. Social Network Mining<br>
    2.1 What is the performance of each resource that is involved in the process?

3. Goal Mining<br>
    3.1 What are the process goals?<br>
    3.2 How to improve the process so it can better achieve its goals??

#### 1. Process Model Discovery

##### 1.1 What process is being analyzed?

To answer this business question, lets first visualize the **Petri Net** of the process. To produce a Petri Net, first make a Heuristics Net of the process using the Heuristics Miner. Heuristics Miner is an algorithm that acts on the [**Directly-Follows Graph**](https://pm4py.fit.fraunhofer.de/documentation#item-3-4), providing way to handle with noise and to find common constructs (dependency between two activities, AND). The output of the Heuristics Miner is an Heuristics Net, an object that contains the activities and the relationships between said activities.<br>

After producing the Heuristics Net, it can then be then converted into a Petri net. Petri nets are one of the most common formalism to express a process model. A Petri net is a directed bipartite graph, in which **the nodes represent transitions and places**. **Arcs are connecting places to transitions and transitions to places**, and have **an associated weight**. In other words, the Petri Net symbols are "places" (circles) that represent system states (e.g. activities) and "transitions" (rectangles) that represent the process proceeding on taking further actions without any additional system states (hence its denoted by its 0s performance metric), both connected by arrows known as "arcs."


```python
import pm4py
# producing the Heuristics Net from an event log, then convert into a Petri net.
net, initial_marking, final_marking = pm4py.discover_petri_net_heuristics(refined_event_log)
pm4py.write_pnml(petri_net=net, initial_marking=initial_marking, final_marking=final_marking, file_path="raw_petri_net.pnml")

from pm4py.visualization.petri_net import visualizer as pn_visualizer
parameters = {pn_visualizer.Variants.PERFORMANCE.value.Parameters.FORMAT: "png"}

'''
pn_visualizer.Variants.PERFORMANCE: This indicates that the model should be decorated according to performance (aggregated by mean) information obtained by applying replay.
'''
gviz_PERFORMANCE = pn_visualizer.apply(net, initial_marking, final_marking, parameters=parameters, variant=pn_visualizer.Variants.PERFORMANCE, log=refined_event_log)

# display the petri net decorated with the specified variant
pn_visualizer.view(gviz_PERFORMANCE)
pn_visualizer.save(gviz_PERFORMANCE, "inductive_PERFORMANCE.png")
```


    replaying log with TBR, completed variants ::   0%|          | 0/98 [00:00<?, ?it/s]



    
<img src="{{site.baseurl}}/assets/portfolio_assets/Process Mining Demo/images/output_18_1.png">
    


Conclusion:<br>
Several disputes happened during the process which forced the process to loop awhile before continuing forward.


```python
'''
pn_visualizer.Variants.FREQUENCY: This indicates that the model should be decorated according to frequency information obtained by applying replay.
'''
gviz_FREQUENCY = pn_visualizer.apply(net, initial_marking, final_marking, parameters=parameters, variant=pn_visualizer.Variants.FREQUENCY, log=refined_event_log)

# display the petri net decorated with the specified variant
pn_visualizer.view(gviz_FREQUENCY)
pn_visualizer.save(gviz_FREQUENCY, "inductive_FREQUENCY.png")
```


    replaying log with TBR, completed variants ::   0%|          | 0/98 [00:00<?, ?it/s]



    
<img src="{{site.baseurl}}/assets/portfolio_assets/Process Mining Demo/images/output_20_1.png">
    


By decorating the Petri net with frequency information, it is also found that the disputes that happen throughout the process are unexpected and are relatively rare occurances. This can be inferred from the relatively low proportion of frequency between the activity of dispute with supplier purchasing agent and the previous state's activity (deliver goods services).


```python
import pm4py

tree = pm4py.discover_process_tree_inductive(refined_event_log)

bpmn_graph = pm4py.convert_to_bpmn(tree)
pm4py.write_bpmn(bpmn_graph, "raw_bpmn.bpmn")
```

#### 2. Social Network Mining

##### 2.1 What is the performance of each resource that is involved in the process?

**Methodology used: resources profiling**<br>
Source = https://pm4py.fit.fraunhofer.de/documentation#item-11-7<br>
Journal article = https://sci-hub.se/https://doi.org/10.1145/3041218


```python
# Get list of activities
import pm4py

activities_list = list(pm4py.get_event_attribute_values(refined_event_log, "Activity").keys())
```


```python
# Get list of resources
import pm4py

resources_list = list(pm4py.get_event_attribute_values(refined_event_log, "Resource").keys())
```


```python
# Get minmax of all of the event log
import pm4py

Complete_timestamp_list = list(pm4py.get_event_attribute_values(refined_event_log, "Complete Timestamp").keys())
Complete_timestamp_list = [i.tz_convert(None) for i in Complete_timestamp_list]

Start_timestamp_list = list(pm4py.get_event_attribute_values(refined_event_log, "Start Timestamp").keys())
Start_timestamp_list = [i.tz_convert(None) for i in Start_timestamp_list]
```


```python
import os
from pm4py.algo.organizational_mining.resource_profiles import algorithm
from pm4py.util import constants
import pm4py

res_name_container = []
activity_name_container = []
indicator_name_container = []
indicator_val_container = []

for res_name in resources_list:
    # Metric RBI 1.1: Number of distinct activities done by a resource in a given time interval [t1, t2)
    res_name_container.append(res_name)
    activity_name_container.append("overall")
    indicator_name_container.append("RBI 1.1")
    indicator_val_container.append(algorithm.distinct_activities(refined_event_log, min(Start_timestamp_list), max(Complete_timestamp_list), res_name, parameters = {constants.PARAMETER_CONSTANT_RESOURCE_KEY: "Resource"}))
    
    # Metric RBI 2.1: The number of activity instances completed by a given resource during a given time slot.
    res_name_container.append(res_name)
    activity_name_container.append("overall")
    indicator_name_container.append("RBI 2.1")
    indicator_val_container.append(algorithm.activity_completions(refined_event_log, min(Start_timestamp_list), max(Complete_timestamp_list), res_name, parameters = {constants.PARAMETER_CONSTANT_RESOURCE_KEY: "Resource"}))
    
    # Metric RBI 2.2: The number of cases completed during a given time slot in which a given resource was involved.
    res_name_container.append(res_name)
    activity_name_container.append("overall")
    indicator_name_container.append("RBI 2.2")
    indicator_val_container.append(algorithm.case_completions(refined_event_log, min(Start_timestamp_list), max(Complete_timestamp_list), res_name, parameters = {constants.PARAMETER_CONSTANT_RESOURCE_KEY: "Resource"}))
    
    # Metric RBI 2.3: The fraction of cases completed during a given time slot in which a given resource was involved
    # with respect to the total number of cases completed during the time slot.
    res_name_container.append(res_name)
    activity_name_container.append("overall")
    indicator_name_container.append("RBI 2.3")
    indicator_val_container.append(algorithm.fraction_case_completions(refined_event_log, min(Start_timestamp_list), max(Complete_timestamp_list), res_name, parameters = {constants.PARAMETER_CONSTANT_RESOURCE_KEY: "Resource"}))
    
    # Metric RBI 2.4: The average number of activities started by a given resource but not completed at a moment in time.
    res_name_container.append(res_name)
    activity_name_container.append("overall")
    indicator_name_container.append("RBI 2.4")
    indicator_val_container.append(algorithm.average_workload(refined_event_log, min(Start_timestamp_list), max(Complete_timestamp_list), res_name, parameters = {constants.PARAMETER_CONSTANT_RESOURCE_KEY: "Resource"}))
    
    # Metric RBI 3.1: The fraction of active time during which a given resource is involved in more than one activity
    # with respect to the resource's active time.
    res_name_container.append(res_name)
    activity_name_container.append("overall")
    indicator_name_container.append("RBI 3.1")
    indicator_val_container.append(algorithm.multitasking(refined_event_log, min(Start_timestamp_list), max(Complete_timestamp_list), res_name, parameters = {constants.PARAMETER_CONSTANT_RESOURCE_KEY: "Resource"}))
    
    # Metric RBI 4.4: The average duration of cases completed during a given time slot in which a given resource was involved.
    res_name_container.append(res_name)
    activity_name_container.append("overall")
    indicator_name_container.append("RBI 4.4")
    indicator_val_container.append(algorithm.average_case_duration(refined_event_log, min(Start_timestamp_list), max(Complete_timestamp_list), res_name, parameters = {constants.PARAMETER_CONSTANT_RESOURCE_KEY: "Resource"}))
    
    for activity_name in activities_list:
        from statistics import StatisticsError
        
        # Metric RBI 1.3: Fraction of completions of a given activity ("a"), by a given resource ("r"),
        # during a given time slot, [t1, t2), with respect to the total number of activity completions by resource r during [t1, t2)
        res_name_container.append(res_name)
        activity_name_container.append(activity_name)
        indicator_name_container.append("RBI 1.3")
        try:
            indicator_val_container.append(algorithm.activity_frequency(refined_event_log, min(Start_timestamp_list), max(Complete_timestamp_list), res_name, activity_name, parameters = {constants.PARAMETER_CONSTANT_RESOURCE_KEY: "Resource"}))
        except StatisticsError:
            indicator_val_container.append(res_name + " haven't done any " + activity_name + " in the given time period")
        
        # Metric RBI 4.3: The average duration of instances of a given activity ("a") completed during a given time slot by a given resource ("r").
        ## This is also often referred to as average Cycle Time, which is the average of the time or duration it takes to complete one task.
        res_name_container.append(res_name)
        activity_name_container.append(activity_name)
        indicator_name_container.append("RBI 4.3")
        try:
            indicator_val_container.append(algorithm.average_duration_activity(refined_event_log, min(Start_timestamp_list), max(Complete_timestamp_list), res_name, activity_name, parameters = {constants.PARAMETER_CONSTANT_RESOURCE_KEY: "Resource"}))
        except StatisticsError:
            indicator_val_container.append("This resource hasn't done any " + activity_name + " in the given time period")
```


```python
resource_profile_df = pd.DataFrame(data = {
    'res_name': res_name_container,
    'activity_name': activity_name_container,
    'indicator_name': indicator_name_container,
    'indicator_val': indicator_val_container})

display(resource_profile_df[resource_profile_df['res_name'] == resources_list[0]])
print('resource_profile_df shape:', resource_profile_df.shape)
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>res_name</th>
      <th>activity_name</th>
      <th>indicator_name</th>
      <th>indicator_val</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Kim Passa</td>
      <td>overall</td>
      <td>RBI 1.1</td>
      <td>7</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Kim Passa</td>
      <td>overall</td>
      <td>RBI 2.1</td>
      <td>192</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Kim Passa</td>
      <td>overall</td>
      <td>RBI 2.2</td>
      <td>163</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Kim Passa</td>
      <td>overall</td>
      <td>RBI 2.3</td>
      <td>0.268534</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Kim Passa</td>
      <td>overall</td>
      <td>RBI 2.4</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Kim Passa</td>
      <td>overall</td>
      <td>RBI 3.1</td>
      <td>0.307257</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Kim Passa</td>
      <td>overall</td>
      <td>RBI 4.4</td>
      <td>2680810.97561</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Kim Passa</td>
      <td>Create Purchase Requisition</td>
      <td>RBI 1.3</td>
      <td>0.21875</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Kim Passa</td>
      <td>Create Purchase Requisition</td>
      <td>RBI 4.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Kim Passa</td>
      <td>Create Request for Quotation Requester</td>
      <td>RBI 1.3</td>
      <td>0.072917</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Kim Passa</td>
      <td>Create Request for Quotation Requester</td>
      <td>RBI 4.3</td>
      <td>15231.428571</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Kim Passa</td>
      <td>Analyze Request for Quotation</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Kim Passa</td>
      <td>Analyze Request for Quotation</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Analyze Request ...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Kim Passa</td>
      <td>Send Request for Quotation to Supplier</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Kim Passa</td>
      <td>Send Request for Quotation to Supplier</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Send Request for...</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Kim Passa</td>
      <td>Create Quotation comparison Map</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Kim Passa</td>
      <td>Create Quotation comparison Map</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Create Quotation...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Kim Passa</td>
      <td>Analyze Quotation comparison Map</td>
      <td>RBI 1.3</td>
      <td>0.151042</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Kim Passa</td>
      <td>Analyze Quotation comparison Map</td>
      <td>RBI 4.3</td>
      <td>25228.965517</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Kim Passa</td>
      <td>Choose best option</td>
      <td>RBI 1.3</td>
      <td>0.182292</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Kim Passa</td>
      <td>Choose best option</td>
      <td>RBI 4.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Kim Passa</td>
      <td>Settle conditions with supplier</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Kim Passa</td>
      <td>Settle conditions with supplier</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Settle condition...</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Kim Passa</td>
      <td>Create Purchase Order</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Kim Passa</td>
      <td>Create Purchase Order</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Create Purchase ...</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Kim Passa</td>
      <td>Confirm Purchase Order</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Kim Passa</td>
      <td>Confirm Purchase Order</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Confirm Purchase...</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Kim Passa</td>
      <td>Deliver Goods Services</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Kim Passa</td>
      <td>Deliver Goods Services</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Deliver Goods Se...</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Kim Passa</td>
      <td>Release Purchase Order</td>
      <td>RBI 1.3</td>
      <td>0.15625</td>
    </tr>
    <tr>
      <th>30</th>
      <td>Kim Passa</td>
      <td>Release Purchase Order</td>
      <td>RBI 4.3</td>
      <td>27170.0</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Kim Passa</td>
      <td>Approve Purchase Order for payment</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Kim Passa</td>
      <td>Approve Purchase Order for payment</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Approve Purchase...</td>
    </tr>
    <tr>
      <th>33</th>
      <td>Kim Passa</td>
      <td>Send invoice</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>34</th>
      <td>Kim Passa</td>
      <td>Send invoice</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Send invoice in ...</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Kim Passa</td>
      <td>Release Supplier's Invoice</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Kim Passa</td>
      <td>Release Supplier's Invoice</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Release Supplier...</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Kim Passa</td>
      <td>Authorize Supplier's Invoice payment</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Kim Passa</td>
      <td>Authorize Supplier's Invoice payment</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Authorize Suppli...</td>
    </tr>
    <tr>
      <th>39</th>
      <td>Kim Passa</td>
      <td>Pay invoice</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>40</th>
      <td>Kim Passa</td>
      <td>Pay invoice</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Pay invoice in t...</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Kim Passa</td>
      <td>Settle dispute with supplier Financial Manager</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>42</th>
      <td>Kim Passa</td>
      <td>Settle dispute with supplier Financial Manager</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Settle dispute w...</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Kim Passa</td>
      <td>Analyze Purchase Requisition</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Kim Passa</td>
      <td>Analyze Purchase Requisition</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Analyze Purchase...</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Kim Passa</td>
      <td>Create Request for Quotation Requester Manager</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Kim Passa</td>
      <td>Create Request for Quotation Requester Manager</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Create Request f...</td>
    </tr>
    <tr>
      <th>47</th>
      <td>Kim Passa</td>
      <td>Amend Request for Quotation Requester</td>
      <td>RBI 1.3</td>
      <td>0.208333</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Kim Passa</td>
      <td>Amend Request for Quotation Requester</td>
      <td>RBI 4.3</td>
      <td>3604.5</td>
    </tr>
    <tr>
      <th>49</th>
      <td>Kim Passa</td>
      <td>Amend Request for Quotation Requester Manager</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>50</th>
      <td>Kim Passa</td>
      <td>Amend Request for Quotation Requester Manager</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Amend Request fo...</td>
    </tr>
    <tr>
      <th>51</th>
      <td>Kim Passa</td>
      <td>Settle dispute with supplier Purchasing Agent</td>
      <td>RBI 1.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>52</th>
      <td>Kim Passa</td>
      <td>Settle dispute with supplier Purchasing Agent</td>
      <td>RBI 4.3</td>
      <td>This resource hasn't done any Settle dispute w...</td>
    </tr>
    <tr>
      <th>53</th>
      <td>Kim Passa</td>
      <td>Amend Purchase Requisition</td>
      <td>RBI 1.3</td>
      <td>0.010417</td>
    </tr>
    <tr>
      <th>54</th>
      <td>Kim Passa</td>
      <td>Amend Purchase Requisition</td>
      <td>RBI 4.3</td>
      <td>44220.0</td>
    </tr>
  </tbody>
</table>
</div>


    resource_profile_df shape: (1485, 4)
    


```python
resource_profile_df_data_dictionary = {
    'RBI 1.1': "Number of distinct activities done by a resource in a given time interval [t1, t2)",
    'RBI 1.3': "Fraction of completions of a given activity a, by a given resource r, during a given time slot, [t1, t2), with respect to the total number of activity completions by resource r during [t1, t2)",
    'RBI 2.1': "The number of activity instances completed by a given resource during a given time slot.",
    'RBI 2.2': "The number of cases completed during a given time slot in which a given resource was involved.",
    'RBI 2.3': "The fraction of cases completed during a given time slot in which a given resource was involved with respect to the total number of cases completed during the time slot.",
    'RBI 2.4': "The average number of activities started by a given resource but not completed at a moment in time.",
    'RBI 3.1': "The fraction of active time during which a given resource is involved in more than one activity with respect to the resource's active time.",
    'RBI 4.3': "The average duration of instances of a given activity completed during a given time slot by a given resource. This is also often referred to as average Cycle Time, which is the average of the time or duration it takes to complete one task.",
    'RBI 4.4': "The average duration of cases completed during a given time slot in which a given resource was involved.",
}
```

Example graph of the value of the RBI 4.3 indicator for the activity "Create Request for Quotation Requester".<br>
The RBI 4.3 indicator is the average duration of instances of a given activity completed during a given time slot by a given resource. This is also often referred to as **average Cycle Time**, which is the average of the time or duration it takes to complete a given activity or task.


```python
import seaborn as sns

'''
A request for quote (RFQ) is a solicitation for goods or services in which a company invites vendors to submit price quotes and bid on the job.
'''

filtered_resource_profile_df = resource_profile_df[(resource_profile_df['activity_name'] == 'Create Request for Quotation Requester')
                                                   & (resource_profile_df['indicator_name'] == 'RBI 4.3')
                                                   & [True if type(value) != str else False for value in resource_profile_df['indicator_val'].values]]

ax = sns.boxplot(data=filtered_resource_profile_df,
                 x="indicator_val")
ax = sns.swarmplot(data=filtered_resource_profile_df,
                   x="indicator_val",
                   color=".25")
```


    
<img src="{{site.baseurl}}/assets/portfolio_assets/Process Mining Demo/images/output_33_0.png">
    



```python
import numpy as np

Q1, Q3 = np.percentile(filtered_resource_profile_df['indicator_val'], [25,75])

# Get names of resources whose indicator values exceed the third percentile (75th percentile) compared to all other resources.
filtered_resource_profile_df[filtered_resource_profile_df['indicator_val'] >= Q3]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>res_name</th>
      <th>activity_name</th>
      <th>indicator_name</th>
      <th>indicator_val</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>395</th>
      <td>Elvira Lores</td>
      <td>Create Request for Quotation Requester</td>
      <td>RBI 4.3</td>
      <td>16726.153846</td>
    </tr>
    <tr>
      <th>670</th>
      <td>Anna Kaufmann</td>
      <td>Create Request for Quotation Requester</td>
      <td>RBI 4.3</td>
      <td>16669.411765</td>
    </tr>
    <tr>
      <th>1055</th>
      <td>Miu Hanwan</td>
      <td>Create Request for Quotation Requester</td>
      <td>RBI 4.3</td>
      <td>16320.0</td>
    </tr>
    <tr>
      <th>1275</th>
      <td>Alberto Duport</td>
      <td>Create Request for Quotation Requester</td>
      <td>RBI 4.3</td>
      <td>17051.25</td>
    </tr>
  </tbody>
</table>
</div>



#### 3. Goal Mining

##### 3.1 What are the process goals?
The goal of the process is to reduce the amount of disputes that happen throughout the process since these disputes require a lot of time to settle, which means an opportunity cost for the company.

##### 3.2 How to improve the process so it can better achieve its goals??


```python
import pm4py
variants = pm4py.get_variants_as_tuples(refined_event_log)
list_of_variant_sequences = list(variants.keys())
```


```python
list_of_filtered_variant_sequences = []
list_of_unfit_variant_sequences = []

for variant_sequence in list_of_variant_sequences:
    for sequence in variant_sequence:
        if "dispute" in sequence.lower():
            list_of_unfit_variant_sequences.append(variant_sequence)
        else:
            list_of_filtered_variant_sequences.append(variant_sequence)

list_of_filtered_variant_sequences = list(set(list_of_filtered_variant_sequences))
list_of_unfit_variant_sequences = list(set(list_of_unfit_variant_sequences))

for unfit_variant_sequences in list_of_unfit_variant_sequences:
    list_of_filtered_variant_sequences.remove(unfit_variant_sequences)
```


```python
from pm4py.algo.filtering.log.variants import variants_filter

'''
pm4py.algo.filtering.log.variants.variants_filter.Parameters.POSITIVE: 'positive' means keeping the admitted variants.
'''
parameters = {pm4py.algo.filtering.log.variants.variants_filter.Parameters.POSITIVE: 'positive'}
filtered_event_log = variants_filter.apply(refined_event_log, admitted_variants = list_of_filtered_variant_sequences, parameters = parameters)

import pm4py
filtered_net, filtered_initial_marking, filtered_final_marking = pm4py.discover_petri_net_inductive(filtered_event_log)
```


```python
from pm4py.algo.conformance.tokenreplay import algorithm as token_based_replay
parameters_tbr = {token_based_replay.Variants.TOKEN_REPLAY.value.Parameters.DISABLE_VARIANTS: True, token_based_replay.Variants.TOKEN_REPLAY.value.Parameters.ENABLE_PLTR_FITNESS: True}
replayed_traces, place_fitness, trans_fitness, unwanted_activities = token_based_replay.apply(refined_event_log, filtered_net,
                                                                                              filtered_initial_marking,
                                                                                              filtered_final_marking,
                                                                                              parameters=parameters_tbr)
```


    replaying log with TBR, completed variants ::   0%|          | 0/608 [00:00<?, ?it/s]


To know how to improve the process so it can better achieve its goals, the existing problems or bottlenecks that have been a burden for the entire process must first be identified. To identify them, a method called throughput analysis is used. Throughput analysis identifies bottlenecks on existing process flow by calculating the **throughput rate** of each activity and identify **which of them is underfed** (inefficient or unfit execution) or **which case contain reworks** (inefficient or unfit activities).<br><br>

Definition of throughput according to [Investopedia](https://www.investopedia.com/terms/t/throughput.asp):
The idea of throughput rate, also known as the flow rate, is referred to as the amount of a product or service that can be produced or the amount of activity that can be done within a specified period of time. The guiding ideology of measuring throughput rate is that a chain is only as strong as its weakest link. Therefore, the goal for business managers is to find ways to minimize how the weakest links affect a company's performance and to maximize throughput for the product's end users. Once throughput is maximized by removing inefficiencies, allowing inputs and outputs to flow in the most ideal manner, a company can reach revenue maximization.<br><br>

**Throughput analysis (unfit execution)**<br>
To perform throughput analysis on the transitions that were executed unfit, and then print on the console the result, the following code could be used:


```python
from pm4py.algo.conformance.tokenreplay.diagnostics import duration_diagnostics

'''
Provide some conformance diagnostics related to transitions that are executed in a unfit manner.
This returns: for each problematic transition, diagnostics about case duration.
This can be used to infer whether unfit executions lead to much higher throughput times or not.
'''
trans_diagnostics = duration_diagnostics.diagnose_from_trans_fitness(refined_event_log, trans_fitness)
for trans in trans_diagnostics:
    print(trans, trans_diagnostics[trans])
```

    (991a32da-27de-431b-a696-3a9d1b87b20c, 'Release Purchase Order') {'n_fit': 389, 'n_underfed': 24, 'fit_median_time': 1183560.0, 'underfed_median_time': 1289640.0, 'relative_throughput': 1.0896279022609754}
    

**Throughput analysis (unfit activities)**<br>
To perform throughput analysis on the process executions containing activities that are not in the model, and then print the result on the screen, the following code could be used:


```python
from pm4py.algo.conformance.tokenreplay.diagnostics import duration_diagnostics
act_diagnostics = duration_diagnostics.diagnose_from_notexisting_activities(refined_event_log, unwanted_activities)
for act in act_diagnostics:
    print(act, act_diagnostics[act])
```

    Settle dispute with supplier Financial Manager {'n_containing': 80, 'n_fit': 505, 'fit_median_time': 996540.0, 'containing_median_time': 1220250.0, 'relative_throughput': 1.224486724065266}
    Settle dispute with supplier Purchasing Agent {'n_containing': 24, 'n_fit': 505, 'fit_median_time': 996540.0, 'containing_median_time': 1289640.0, 'relative_throughput': 1.2941176470588236}
    

**Root Cause Analysis**<br>
The output of root cause analysis in the diagnostics context is a decision tree that permits to understand the causes of a deviation. In the following examples, for each deviation, a different decision tree is built and visualized.


```python
# build decision trees, prepare decision tree parameters
import itertools

string_attributes = list(set(list(itertools.chain(*[feature_parameters.get(i) for i in ['str_ev_attr', 'str_tr_attr', 'str_evsucc_attr']]))))
numeric_attributes = list(set(list(itertools.chain(*[feature_parameters.get(i) for i in ['num_ev_attr', 'num_tr_attr']]))))
dt_parameters = {"string_attributes": string_attributes, "numeric_attributes": numeric_attributes}
```

**Root Cause Analysis (unfit execution)**<br>
To perform root cause analysis on the transitions that are executed in an unfit way, the following code could be used:


```python
from pm4py.algo.conformance.tokenreplay.diagnostics import root_cause_analysis
trans_root_cause = root_cause_analysis.diagnose_from_trans_fitness(refined_event_log, trans_fitness,
                                                                   parameters=dt_parameters)
```


```python
from pm4py.visualization.decisiontree import visualizer as dt_vis

'''
'classes': ['fit', 'underfed']
'''
for trans in trans_root_cause:
    trans_clf = trans_root_cause[trans]["clf"]
    trans_feature_names = trans_root_cause[trans]["feature_names"]
    trans_classes = trans_root_cause[trans]["classes"]
    # visualization could be called
    gviz = dt_vis.apply(trans_clf, trans_feature_names, trans_classes)
    dt_vis.view(gviz)
```


    
<img src="{{site.baseurl}}/assets/portfolio_assets/Process Mining Demo/images/output_49_0.png">
    


**Root Cause Analysis (unfit activities)**<br>
To perform root cause analysis on activities that are executed but are not in the process model, the following code could be used:


```python
from pm4py.objects.log.util.get_class_representation import get_class_representation_by_str_ev_attr_value_presence
'''
dispute == True
'''
target, classes = get_class_representation_by_str_ev_attr_value_presence(refined_event_log, str_attr_name = 'dispute existance - case level', str_attr_value = 'dispute')

print(len(classes),
      np.array(target).shape,
      sep = '\n')
```

    2
    (608,)
    


```python
from sklearn import tree
'''
max_depth=7 is the default value for the max_depth parameter in most of PM4PY's sklearn decision tree implementation.
random_state=0 to ensure reproducibility of the model.
'''
act_clf = tree.DecisionTreeClassifier(max_depth=7,
                                      random_state=0)
act_clf.fit(feature_data, target)

from pm4py.visualization.decisiontree import visualizer as dectree_visualizer
gviz = dectree_visualizer.apply(act_clf, feature_names, classes)
dectree_visualizer.view(gviz)
```


    
<img src="{{site.baseurl}}/assets/portfolio_assets/Process Mining Demo/images/output_52_0.png">
    


Decision Trees (DTs) are a non-parametric supervised learning method used for classification and regression. The goal is to create a model that predicts the value of a target variable by learning simple decision rules inferred from the data features.<br>

In concept, decision trees are similar with graphs. Both have nodes and arcs. Nodes in decision trees are called leaves (leaf in singular) and arcs are called branches. Each internal or source leaf node represents a "test" on an attribute (e.g. whether a coin flip comes up heads or tails) and each destination leaf node represents a class label (decision taken after computing all attributes) while each branch represents the outcome of the test.<br>

As a general rule, **one should look for leaf nodes with the lowest Gini impurity score and the largest sample size of the target class**. These leaf nodes can be reliably referred to as decision rules which might be the actual root causes of the problem or symptoms of the problem. Therefore, these leaf nodes should be studied further so the solution for the problem and its symptoms can be identified. The estimation on whether the solution will work or not should be done using Discrete Event Simulation (DES) with the base model constructed from the model of real processes that has been discovered by process mining.

## Reference
- Zerbino, P., Stefanini, A., & Aloini, D. (2021). Process Science in Action: A Literature Review on Process Mining in Business Management. Technological Forecasting and Social Change, 172, 121021. [doi:10.1016/j.techfore.2021.121021](https://sci-hub.se/https://doi.org/10.1016/j.techfore.2021.121021)
- Wil, A. (2018, July 9–12). PROCESS MINING AND SIMULATION: A MATCH MADE IN HEAVEN! [Keynote paper]. SpringSim-SCSC, Bordeaux, France. DOI:[10.22360/summersim.2018.scsc.005](https://dl.acm.org/doi/10.5555/3275382.3275386)
- Milani, F., Fredrik & Lashkevich, Katsiaryna & Maggi, Fabrizio & Di Francescomarino, Chiara. (2022). Process Mining: A Guide for Practitioners. 10.1007/978-3-031-05760-1_16.
