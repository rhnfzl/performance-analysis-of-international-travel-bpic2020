# Performance Analysis of International Travel Declarations at the Eindhoven University of Technology

#### Part of Assignment of Advance Process Mining (2AMI20) at TU/e


## Objective

A process-mining expert should be able to reproduce the work and apply it to event-data. However, the main audience is the Finance and Control department, understand, and accept the analysis. Technical details about generating subsets and discovering models is intended for the process-mining experts.


## Process and Data Description

### Process Description

In many organizations, staff members travel for work. They travel to customers, to conferences, or to project meetings. Sometimes these travels are expensive. As an employee you do not have to pay for your own travel expenses. The company takes care of them.

* For international trips, employees do need to ask permission first. They obtain this permission by submitting a travel-permit. The employee should not make any travel arrangements before her supervisor has approved this travel permit.

To get the costs for travel reimbursed, an employee files a claim. This can be done as soon as the employee actually paid the costs. For example, travelers pay for flights or conference registration fees in advance. Or the reimbursement claim can be filed within two months after the trip. For example travelers pay for a hotel and food costs during their trip.

The process happens through the following documents. Various people fill in, approve, and process each document

* For an international trip the following happens:

1. Employees fill in a travel permit document with an estimated start and end date, the estimated costs, and various other details. Because the employees estimate the start and end date when submitting the permit request, the actual start and end dates can be different.
2. After a travel permit is approved, but before the trip starts, employees can ask for a reimbursement of prepaid travel costs with another document. This can be done as soon as costs are actually paid. Employees can submit several reimbursement requests for the same trip.
3. If the permit is approved, the employee may travel. The TMS does not record the actual travel dates in the data but they should usually be close to the given dates.
4. After the end of a trip, an employee receives several reminders to submit an international declaration document to pay for costs that occurred during the trip or that have not yet been reimbursed. Typically, employees submit just one international travel declaration, but sometimes they submit multiple declarations. The declaration has to be submitted within two months after the trip.
5.  If the approval flow has a positive result, payment is requested of and made by the university with another document.


### Event Data

Data is collected from the reimbursement process at the TU/e in five separate files. The files contain data from 2017 and 2018. In 2017, only two departments used the new TMS, whereas in 2018 the whole TU/e used the new TMS.

The data is split into travel permits and several request types, namely domestic declarations, international declarations, prepaid travel costs, and requests for payment. The latter refers to expenses which should not be related to trips, such as representation costs, hardware purchased for work, etc. The documents refer to each other through case attributes explained below.

The data is anonymized in such a way that no TU/e internal IDs are visible in the final dataset, i.e., all identifiers are freshly generated. Furthermore, the amounts mentioned in the data are not exact amounts. However, adding declarations referring to the same travel permit and then comparing them to the original budget or estimate is still possible. Furthermore, on large enough samples of the data, the summed and averaged amounts should be roughly correct.

Employees cannot be identified in the data. Instead, for all steps, the role of the person that executed the step is recorded. The resource recorded in the data is either “SYSTEM”, “STAFF MEMBER”, or “UNKNOWN”. On occasion, the data is “MISSING”.


The event data is also used in [BPIC-2020](https://icpmconference.org/2020/bpi-challenge/), it can be downloaed from [4TU website](https://data.4tu.nl/collections/_/5065541/1).


### Task, Deliverables

Select two subsets you find most relevant for further analysis of the travel related processes and Discover up to ten useful models using at least four different discovery algorithms. For each discovered model, follow the next four steps to document your assumptions, choices, expectations, and progress:

1. Describe the subset and the configuration you used to create it.
2. Evaluate the model: Does it help you to better understand the travel related processes?
3. Assess the quality of the subset and the model. Justify your assessment based on evidence you gathered from the current model.
Assessment should go beyond just listing fitness and precision. A model that have been generated with a process-mining algorithm might have good fitness or precision, but that does not make it necessarily valid or even suitable to answer your research question.
4. Conclude how can the model be improved or the underlying subset.

If the discovered model is improved manually adapting it, how would that model be different? Why would this manually adapted model be better than the original model?


Arguments should be built around at least four of your discovered models and two of their underlying subsets. Conclude it with a justified actionable recommendation in the conclusion.


Explore other algorithms and configurations. Or look into the possibility to manually adapt your discovered models to improve their quality by using another process modeling tool. The quality of a model goes beyond technical merits such as precision and fitness. Make sure that your models are valid and meaningful in light of the research question choosen.



## Report


### Introduction

In this report, we will be looking at the process of submitting travel declarations for international travels. We’ve received several event logs that describe this process. The submission process of international travel declarations are structured as follows:

* An employee has to submit a request (permit) to be able to make an international trip.
* After the request has been approved, the employee can travel at the designated time.
* Once the trip has ended and the employee has returned to the university, they are obliged to submit an international travel declaration.
* The employee should summarize all costs made during the trip and provide a document to the university stating what costs they would like to have reimbursed.
* After they have submitted this document (or multiple of them), the university will verify the document and approve or disapprove the request.
* Once the request is approved, the costs are reimbursed.
* If the request is rejected, the employee may alter their original request and resubmit; they can also waive away their request, and consequently, no costs are reimbursed.

The main goal of this research is to identify possible bottlenecks in the process of submitting and approving travel declarations. The process of submitting a request (permit) for international travel is not regarded in this report. The university would like to help their employees with reimbursing their costs (to keep their employees happy), while the employees would like to have a concise and speedy process, so they don’t have to wait too long for a reimbursement, perhaps with unforeseen financial and personal consequences. It’s nigh impossible to focus on the full process at once with such a broad research question; to simplify, we focus on three different subtopics regarding the performance of the system. Each of the subtopics is looked into with greater detail and answers are sought for the subtopic. We hope to provide recommendations for these subtopics that can be combined to improve the overall performance of the process.


#### Research questions

The first subtopic is related to the performance of cases where the travel declaration has been rejected at least once. We are interested in the performance penalties that are accompanied by the rejection of a travel declaration. What kind of delay is expected when a travel declaration is rejected and what (or who) is responsible for the largest part of the delay. Is there any way to improve the process of rejecting so that less time is lost during that stage?

The second subtopic is related to the performance of individuals: after the employee has submitted a travel declaration it will be verified by the university. Several individuals (with different roles) are responsible for reading and checking the contents of the request. We are interested in possible individual bottlenecks based on roles. Does a role take longer to process a request compared to another role?

Finally, one of the distinguishing patterns that we found in the data is that there is a large discrepancy between the median time between an employee submitting a request and the administration (of the employee) approving that request as supposed to the mean time. The median time is in the order of minutes, while the mean time is in the order of days. Based on these peculiarities, we will delve deeper into that specific subprocess to discover what the details of the implementation of the supporting system are. We suspect that there might be some automatic process involved. If we are able to solve what is causing the automation, we might be able to adapt the system that is doing the automation so that it can apply these steps on a larger part of the submitted travel declarations. This would in turn increase the processing significantly, as the difference between a few minutes and a few days is night-and-day.


### Data Analysis on Performance

#### Bottlenecks introduced by rejecting requests

For this subquestion, it is interesting to look at any bottlenecks that occur in cases with rejected declarations. We have made use of the event log based on international travel declarations. The subset, as described in section [```Declarations rejected at least once```](), focuses on travel declarations that are submitted in 2018 and that have been rejected at least once.


To identify performance bottlenecks, we will use the Interactive Data-aware Heuristic miner to create a causal net. From the causal net, we convert it to a Petri net and then use the log and Petri net to use the plugin Replay a Log on Petri Net for Performance / Conformance Analysis to obtain Figure 1. Annotated in the top right corner are the fitness, precision and generalization of the model against the filtered data. The color of the transition show the average sojourn time, while the color of the places indicate average waiting time. The more red a place or transition is colored, the more time it takes for this transition to happen. The black transitions are τ -transitions, and do not have a sojourn time. The average sojourn time is indicated in between brackets after the name of the transition.

![Architecture](/img/RejectedTravelDeclarations-ProM-annotated.png "Figure 1: Petri net of cases with at least one rejected request")

The loop on the top left shows the process of the employee submitting a declaration and it being rejected by the administration. Following that rejection, the employee should also reject their submission before they can submit a new travel declaration. An interesting finding is that both transitions Rejected by Employee and Rejected by Administration stand out due to their large mean sojourn time (3.41 days and 2.11 days respectively). However, the mean is heavily impacted by large outliers. If we look at the median sojourn time (which does not suffer from this problem), we find that Rejected by Employee has a median sojourn time of 1.82 days and Rejected by Administration 10.9 minutes. This means that the distribution of sojourn times is heavily skewed by cases with large sojourn times. Since Rejected by Employee has a mean and median sojourn time that is much closer to each other, we can conclude that it is a major contributor to delays, while Rejected by Administration is not. This problem of the discrepancy between the median and mean sojourn times is interesting by itself and will be discussed in section ```Bottlenecks based on manual vs. automatic verification steps```. We will not discuss this here.


Another bottleneck seems to occur at the transition Approved by Budget Owner, but this is actually not true: the median sojourn time of this transition is only 20 hours. Note that the loop in the top left corner allows the model to have good generalization, but also good precision. The loop is very specific in its behavior as it only allows a certain sequence of activities to be repeated.

##### Sending reminders to the employee

We’ve noticed that employees are slow to reject their application. In a sense this does not pose an immediate problem to the university, since it’s the loss of the employee that the application takes so long. However, it is still beneficial to the university to keep the average duration of a travel declaration case low: it keeps the satisfaction of their employees high and it decreases the size of the backlog. If employees decide to resubmit their application all at the same time, this will cause avoidable strain on the staff that needs to verify the requests, if there is a backlog.

Hence, we would like to provide a few suggestions to reduce or perhaps even remove the bottleneck: the university ought to send reminders to employees who have not made a resubmission after the rejection of their travel declaration. To verify whether reminders actually improve the response time of employees, we took the same subset as described in section 3.1.1 and also included the End trip activity in the cases. We want to have this activity included as reminders are only send after the trip of the employee has ended.

Figure 2 shows part of a directly-follows graph that was generated. Arc A shows that it takes a long time to send reminders (after ending a trip). It is difficult to send reminders exactly two weeks after employees have finished their trip, because the end activity does not necessarily indicate the end of the trip of an employee.


![Architecture](/img/Internationaltraveldeclarationswithreminders-withlabels.png "Figure  2:  Part  of  a  directly-follows  graph  to  inspect  the  results  of  sending  reminders.   Theprimary  performance  metric  is  the  median  duration,  while  the  second  performance  metric  isthe mean duration")


However, after sending reminders, employees seem to respond within 7 days (median), this is similar performance to when they do not receive reminders (arc B). This may indicate that reminders seem to be somewhat effective. Note that sending reminders is fairly cheap: it’s often not much more than an automatic, scheduled e-mail. We would therefore suggest to reduce the time before sending a reminder to address the bad performance of arc A.














