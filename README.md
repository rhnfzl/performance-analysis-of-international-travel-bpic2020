# Performance Analysis of International Travel Declarations at the Eindhoven University of Technology

#### Part of Assignment of Advance Process Mining (2AMI20) at TU/e

## Objective

A process-mining expert should be able to reproduce the work and apply it to event-data. However, the main audience is the Finance and Control department, understand, and accept the analysis. Technical details about generating subsets and discovering models is intended for the process-mining experts.
---
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
3. Assess the quality of the subset and the model. Justify your assessment based on evidence you gathered from the current model. Assessment should go beyond just listing fitness and precision. A model that have been generated with a process-mining algorithm might have good fitness or precision, but that does not make it necessarily valid or even suitable to answer your research question.
4. Conclude how can the model be improved or the underlying subset.

If the discovered model is improved manually adapting it, how would that model be different? Why would this manually adapted model be better than the original model?

Arguments should be built around at least four of your discovered models and two of their underlying subsets. Conclude it with a justified actionable recommendation in the conclusion.

Explore other algorithms and configurations. Or look into the possibility to manually adapt your discovered models to improve their quality by using another process modeling tool. The quality of a model goes beyond technical merits such as precision and fitness. Make sure that your models are valid and meaningful in light of the research question choosen.


---
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

For this subquestion, it is interesting to look at any bottlenecks that occur in cases with rejected declarations. We have made use of the event log based on international travel declarations. The subset, as described in section [Declarations rejected at least once](/README.md#declarations-rejected-at-least-once), focuses on travel declarations that are submitted in 2018 and that have been rejected at least once.

To identify performance bottlenecks, we will use the Interactive Data-aware Heuristic miner to create a causal net. From the causal net, we convert it to a Petri net and then use the log and Petri net to use the plugin Replay a Log on Petri Net for Performance / Conformance Analysis to obtain Figure 1. Annotated in the top right corner are the fitness, precision and generalization of the model against the filtered data. The color of the transition show the average sojourn time, while the color of the places indicate average waiting time. The more red a place or transition is colored, the more time it takes for this transition to happen. The black transitions are τ -transitions, and do not have a sojourn time. The average sojourn time is indicated in between brackets after the name of the transition.

![Architecture](/img/RejectedTravelDeclarations-ProM-annotated.png)

<figcaption>

Figure 1: Petri net of cases with at least one rejected request

</figcaption>

The loop on the top left shows the process of the employee submitting a declaration and it being rejected by the administration. Following that rejection, the employee should also reject their submission before they can submit a new travel declaration. An interesting finding is that both transitions Rejected by Employee and Rejected by Administration stand out due to their large mean sojourn time (3.41 days and 2.11 days respectively). However, the mean is heavily impacted by large outliers. If we look at the median sojourn time (which does not suffer from this problem), we find that Rejected by Employee has a median sojourn time of 1.82 days and Rejected by Administration 10.9 minutes. This means that the distribution of sojourn times is heavily skewed by cases with large sojourn times. Since Rejected by Employee has a mean and median sojourn time that is much closer to each other, we can conclude that it is a major contributor to delays, while Rejected by Administration is not. This problem of the discrepancy between the median and mean sojourn times is interesting by itself and will be discussed in section [Bottlenecks based on manual vs. automatic verification steps](/README.md#bottlenecks-based-on-manual-vs-automatic-verification-steps). We will not discuss this here.

Another bottleneck seems to occur at the transition Approved by Budget Owner, but this is actually not true: the median sojourn time of this transition is only 20 hours. Note that the loop in the top left corner allows the model to have good generalization, but also good precision. The loop is very specific in its behavior as it only allows a certain sequence of activities to be repeated.

##### Sending reminders to the employee

We’ve noticed that employees are slow to reject their application. In a sense this does not pose an immediate problem to the university, since it’s the loss of the employee that the application takes so long. However, it is still beneficial to the university to keep the average duration of a travel declaration case low: it keeps the satisfaction of their employees high and it decreases the size of the backlog. If employees decide to resubmit their application all at the same time, this will cause avoidable strain on the staff that needs to verify the requests, if there is a backlog.

Hence, we would like to provide a few suggestions to reduce or perhaps even remove the bottleneck: the university ought to send reminders to employees who have not made a resubmission after the rejection of their travel declaration. To verify whether reminders actually improve the response time of employees, we took the same subset as described in section [Declarations rejected at least once](/README.md#declarations-rejected-at-least-once) and also included the End trip activity in the cases. We want to have this activity included as reminders are only send after the trip of the employee has ended.

Figure 2 shows part of a directly-follows graph that was generated. Arc A shows that it takes a long time to send reminders (after ending a trip). It is difficult to send reminders exactly two weeks after employees have finished their trip, because the end activity does not necessarily indicate the end of the trip of an employee.

![Architecture](/img/Internationaltraveldeclarationswithreminders-withlabels.png)

<figcaption>

Figure  2:  Part  of  a  directly-follows  graph  to  inspect  the  results  of  sending  reminders.   Theprimary  performance  metric  is  the  median  duration,  while  the  second  performance  metric  isthe mean duration

</figcaption>

However, after sending reminders, employees seem to respond within 7 days (median), this is similar performance to when they do not receive reminders (arc B). This may indicate that reminders seem to be somewhat effective. Note that sending reminders is fairly cheap: it’s often not much more than an automatic, scheduled e-mail. We would therefore suggest to reduce the time before sending a reminder to address the bad performance of arc A.

#### Bottlenecks based on roles

When looking at the process description, we noticed that the budget owner can be skipped in the process. This is also confirmed by the given data for all departments which had more than 10 cases. It is interesting to verify whether the budget owner is a bottleneck.

To verify that, we created a subset that focuses on the declaration approval and rejection by the budget owner and supervisor. The implementation of the subset is explained in the section [Declarations approved or rejected by Budget Owner or supervisor](/README.md#declarations-approved-or-rejected-by-budget-owner-or-supervisor). By focusing on just the process which involves the budget owner and the supervisor, we are able to determine whether a bottleneck is created by one of those two roles.

We created a Petri net using the Interactive Data-aware Heuristic Miner, for details see [Declaration process that are handled by budget owner and/or supervisor](/README.md#declaration-process-that-are-handled-by-budget-owner-andor-supervisor). The fitness, precision and generalisation of this model are checked against our subset using Replay a Log on a Petri Net for Conformance Analysis and Measure Precision/Generalization: trace fitness = 1, precision = 0.89 and generalization = 0.98.

This model shows high fitness and also high generalization. The precision is lower, so there is some behavior possible in the model which is not seen in the log. This can be explained due to our process having a loop. Since we’re confident that this model accurately represents the subset, we use it to determine the bottlenecks. The plugin Replay a Log on Petri Net for Performance/Conformance Analysis is used to obtain Figure 3. This model should be interpreted in the same manner as Figure 1.

Notice that the approval and declaration steps by the budget owner or supervisor take longer than the other steps. Furthermore, on average, the budget owner and supervisor take the same amount of time to approve a declaration. However, where the budget owner takes less time to reject a declaration, the supervisor takes more time to reject a declaration. Unfortunately, it’s unclear from this model whether the supervisor is slow due to the budget owner being slow (and preceding the supervisor) or if the supervisor themselves is slow. Therefore, we are interested at the sojourn time of the supervisor preceded by the budget owner. We use Disco for this because we have noticed that there can be a large difference between the mean and median of the sojourn time and the performance analysis only shows the average sojourn time.

![Architecture](/img/Performance_budget_owner_vs_supervisor.png)

<figcaption>

Figure 3: Petri net used for performance analysis on the log and model.

</figcaption>

Table 1 shows an overview of the performance of several activities for the different subsets. We can see that the mean for the approved cases does not differ significanlty (around 3 days regardless of the involvement of the budget owner), while it does for the rejected cases. However, the median duration between activities differs significantly between both the approved and rejected cases. When the budget owner is not involved, the median duration is diminished for the case of approval and enlarged for the case of rejection, however rejection occurs rarely.

To conclude, we have two possible paths a case can take (shown in Table 2). Passing via the budget owner adds a lot of time to a case, so this forms a bottleneck. A possibility to tackle this bottleneck could be to ‘skip’ this step in the process, which is often already done.

#### Bottlenecks based on manual vs. automatic verification steps

In this subsection, it is interesting to find out what is causing the skewness in the performance of the model, especially very large skewness in the sojourn time for Declaration APPROVED/REJECTED by ADMINISTRATION. Preparation of subset for this analysis has been discussed in Appendix [Declarations Approved or Rejected by Administrator only once](/README.md#declarations-approved-or-rejected-by-administrator-only-once).

We noticed a large difference between the mean and median duration of the sojourn time for Declaration APPROVED/REJECTED by ADMINISTRATION. The mean duration is 43.2 hrs, while the median duration is 7.5 minutes. We found these values using Disco. Furthermore, we found that there are 1253 traces that performed these events within one minute of submitting a request. Our hypothesis is that there is an automated process in place that determines whether a declaration can be approved or rejected. If the automated process cannot make a decision (edge cases), human intervention is required. This explains why this process can also take longer than a minute. If there is some automated process in place, this process will be deterministic. We will try to find out whether there is an automated process by finding data attributes that determine whether this decision is made within one minute, and thus by the automated process, or not.

We sub-divided the event log into two subsets under 1 minute and on and over 1 minute as discussed in Appendix subsection [Declarations Approved or Rejected by Administrator only once](/README.md#declarations-approved-or-rejected-by-administrator-only-once). This splitting allowed us to compare the two types of subsets directly and derive a Decision Tree to determine whether this event happened within one minute based on the labelling. The “Yes” label indicates completed within a minute and “No” indicates cases took more than a minute. This decision tree could help us to understand the automated process.

The first decision trees found were of no significant value even after hyperparameter optimization. However, each decision tree found using random forest made use of the following three decision variables: Permit OrganizationEntity, Permit RequestedBudget, and Amount with an accuracy of 65.71%, i.e. decision tree correctly predicts considering the said attributes. This decision tree has been discussed in [Decision Tree on International Travel with Organisation Entity](/README.md#decision-tree-on-international-travel-with-organisation-entity) from which we derive that Permit OrganizationEntity has no significance as the results were random for each organization unit (department) and the assumed automated process rule is the same for all of them.

Next, we took the PermitLogWithClassifier.xes log since it had the activities Declaration APPROVED/REJECTED by ADMINISTRATION and prepared the subset as discussed in Appendix [Declarations Approved or Rejected by Administrator only once](/README.md#declarations-approved-or-rejected-by-administrator-only-once) and joined it with the labelled subset of International Travel. After doing feature engineering (discussed in [Feature Engineering](/README.md#feature-engineering) ), a new attribute Overspent was found along with previous decision variables except for Permit OrganizationEntity. Consequently, we performed different classification techniques (discussed in section [Classification Techniques](/README.md#classification-techniques) ) and chose the decision tree technique since the accuracy was 68% in each technique.

The decision tree was built on same three departments discussed in [Decision Tree on International Travel with Organisation Entity](/README.md#decision-tree-on-international-travel-with-organisation-entity) namely: 65456, 65458 and 65455. Figure 4 shows the decision tree, where Permit RequestedBudget is at the root followed by Overspent, and the Amount at the last node. Here “Yes” indicates completed within a minute and “No” indicates cases took more than a minute. The discretized process of continuous attributes has been briefly discussed in Appendix subsection [Declarations Approved or Rejected by Administrator only once](/README.md#declarations-approved-or-rejected-by-administrator-only-once). Even after sampling and hyperparameter optimization (as discussed in [Hyperparameter Optimization & Sampling](/README.md#hyperparameter-optimization--sampling) ), we again did not get a significant decision tree (Figure 4), but it is more explainable and generalizable than the previous decision tree and has the accuracy of 69.52%. The conclusions we may want to draw from Figure 4 is that if the Permit RequestedBudget is within the 332.1 EUR and the Amount declared is within the budget, it will be auto approved most of the time.

![Architecture](/img/DecisionTreeFinal_.png)

<figcaption>

Figure 4: Decision Tree with attributes \texttt{Permit RequestedBudget

</figcaption>

We also approached this problem using the Multi-Perspective Process Explorer in the hope this would give us some decision variables that indicated why some approvals/rejection seem to happen automatically while others don’t. For this, we selected the traces that occurred in 2018 and that contained Declaration REJECTED/ACCEPTED by ADMINISTRATION from the International Declarations log. We only selected the traces that occurred at least 50 times. We created a Python script that creates a distinction between the activities we suppose are done automatically and manually. The decision threshold was set at one minute. This script takes the filtered event log as an input (CSV) and outputs the new event log in-place.

Subsequently, the output of the script is fed into ProM to create a Petri net using the Interactive Data-Aware Heuristics Miner. We found a trace fitness of 0.94 when we replayed the log, a precision of 0.93 and a generalization of 0.99. The non-perfect precision can be explained due to the existence of loops in the process. These measures are quite good, so we ran Multi-Perspective Process Explorer on this model and log. We used the ‘Data Discovery Model’ as MPE mode, and configured a decision tree. Unfortunately, we were not able to get decision variables visible on the arcs. We tried different configuration options, but it never seemed to show any decision variable. Hence, this approach did not give any insight about why some declarations are approved/rejected that fast. We decided to not include the model because it does not show any useful information.

Due to the low accuracy of the decision tree learned on the trace attributes, we can neither confirm nor reject our hypothesis as the decision tree is not entirely helpful in finding out the rules and confirm if there is an automated process. Unfortunately, the Interactive Data-Aware Heuristics Miner did not give any extra insight into this phenomenon. Further research could look into combinations of available attributes or discovering new attributes that are currently not available to us. Additionally, we recommend to find a better foundation to decide which activities are done automatically and which are done manually. We chose a threshold of one minute. However, to support this decision threshold, we need to consult domain experts. It could be that we set our threshold too low and therefore we were not able to find significant differences in behavior.

### Conclusion and Suggestions

This report looked at identifying bottlenecks in the submission process of international travel declarations. The performance is analyzed based on three different subquestions. Each question is aimed at diving into a specific part of the process to identify possible bottlenecks or discover peculiarities and possibly suggest improvements. The first subquestion looked into bottlenecks during cases that were rejected at least once. It was found that the employees seem to cause a bottleneck as they are slow to respond when their application is rejected. This may seem like an issue that is not relevant to the university (as it’s a loss for the employee, not the university), but it might have indirect, undesirable effects. To remedy this, it seems (somewhat) effective to send automatic reminders. Since reminders are cheap, the costs are negligible and the rewards might be non-zero.

The second subquestion looked into the difference in performance between different roles. We found that the budget owner may cause a significant bottleneck. We are unsure what the root cause of this problem is: one of our hypotheses is that this is caused by the handover of work between the budget owner and supervisor that creates additional delay. Due to space and time constraints, we have not looked further into this topic, but this is highly suggested in further research. To remedy this problem, one could remove the budget owner entirely (if possible), or reduce the number of cases that pass through the budget owner. Perhaps it is possible to automate some part of their verification process to reduce the work done by the budget owner.

The final and third subquestion looked into the rules deriving automated and manual handling of administrative approval and rejection. We found that the dataset does not have enough meaningful attributes which could have given a clear picture of the rule deriving the automated approval done within 1 minute by the administration. However, the derived model indicates an automated process running in the backend but not clearly confirms it. Hence, for future research, we suggest looking into what drives human intervention for the approval of an application (by the administration). One of the major shortcomings of the found model was that it wasn’t accurate. It might be the case that there are some hidden variables (variables that are not in our dataset) that are able to explain the divide. We can therefore offer no concrete recommendation, and instead suggest further research into this area.

Although the report was aimed at identifying bottlenecks in the process, we aimed to look for specific instances of bottlenecks, each of them tackled in a subquestion. With the answer to each subquestion, we hoped to find a suitable answer to solving some of the bottlenecks. These suggestions are limited by the lack of domain knowledge. To verify whether these suggestions are possible, domain experts need to be consulted. Our analysis for each of the subquestions is always done using a case-to-case analysis, which has limits regarding its identifying power. One of the interesting trends in the data is that the distribution of requests was not evenly spread over the year. Instead, we saw peaks before the holidays. We have not taken these peaks into account, but they could lead to dynamic bottlenecks. Focusing on specific cases will not reveal whether there is an interaction between cases; a large increase in incoming requests will put additional pressure on the staff and hence increase processing time, but you will not find this if you merely look at the activities at the case level. Future research should therefore take these inter-case dynamics into account.


---

### Subsets

#### Declarations rejected at least once

filename: ```Declarations rejected at least once.xes```

We decided to focus on the cases that occur (completely) in 2018, as 2017 was a pilot-year. There is very little data in 2019, so we discard that as well. Since we are interested in the cases that have rejections, we filter out cases that do not have a rejection (by employee). Finally, we are interested in cases that do not end in a rejection, so we filter out all cases that end on the activities Rejected by Employee, Rejected by Missing or Rejected by Supervisor. This is done with the following steps:

* We select all cases that are contained in 2018.
* We project the cases to only include activities that are related to the process of submitting travel declarations. These are:

Declaration SUBMITTED by Employee
Declaration REJECTED By Employee
Declaration APPROVED by ADMINISTRATION
Declaration REJECTED by ADMINISTRATION
Declaration APPROVED by BUDGET OWNER
Declaration REJECTED by BUDGET OWNER
Declaration APPROVED by SUPERVISOR
Declaration REJECTED by SUPERVISOR
Declaration FINAL APPROVED by SUPERVISOR
Declaration FINAL APPROVED by DIRECTOR
Declaration REJECTED by DIRECTOR

* We then look at the cases whose sequences of activities is shared by at least 65 cases, so we obtain a dataset that has roughly 10% of the variants. We do this because we are interested in mainstream behavior, not in exotic or exceptional behavior.
* Next, we keep only cases that have the activity Declaration Rejected by Employee at least once.
* Finally, we only keep the cases that end in an approved process, because we want to keep the scope of the research question on the approved declarations that have been rejected at least once. This means that cases are only allowed to end in:

Declaration FINAL APPROVED by SUPERVISOR
Declaration FINAL APPROVED by DIRECTOR
Declaration APPROVED by SUPERVISOR

After all these steps, we are left with 999 traces and with 4 variants.



#### Declarations approved or rejected by Budget Owner or supervisor

filename: ```Final Result after Filtering and Renaming.xes.gz```

* First we select all traces that are contained in 08-01-2018 until 21-12-2018.
* We use projection to filter in the events shown below (attribute type is concept:name). We chose these since we would like to focus on the process once the declaration is APPROVED by the ADMINISTRATION. The REJECT by ADMINISTRATION is removed since this does not tell us anything about the distribution of work of the BUDGET OWNER and the SUPERVISOR.

Decl SUBMITTED by EMPLOYEE
Decl APPROVED by ADMINISTRATION
Decl APPROVED by BUDGET OWNER
Decl REJECTED by BUDGET OWNER
Decl APPROVED by SUPERVISOR
Decl REJECTED by SUPERVISOR
Decl FINAL APPROVED by SUPERVISOR
Decl FINAL APPROVED by DIRECTOR
Decl REJECTED by DIRECTOR

* Next we select the traces that contain one of the following attributes (attribute type is concept:name) to ensure at least the supervisor or the budget owner has handled the specific case:

Decl APPROVED by BUDGET OWNER
Decl REJECTED by BUDGET OWNER
Decl APPROVED by SUPERVISOR
Decl REJECTED by SUPERVISOR
Decl FINAL APPROVED by SUPERVISOR

* Next we merge subsequent events, since the declaration can be rejected multiple times (by the administration), the EMPLOYEE submits several times. We remove the rejection by administration, so we merge the subsequent Declaration SUBMITTED by EMPLOYEE by taking the last event.

* We see that Declaration REJECTED by DIRECTOR only happens once, so we remove this trace. (select trace and then forbidding this event).

* We remove Declaration FINAL APPROVED by DIRECTOR (projection) (we do not need this information).

* We end up with Declaration APPROVED by SUPERVISOR as end activity, which is in essence the same as Declaration FINAL APPROVED by SUPERVISOR, we rename this activity to Declaration FINAL APPROVED by SUPERVISOR using the rename plugin of ProM. We rename the task Declaration APPROVED by SUPERVISOR into Declaration FINAL APPROVED by SUPERVISOR.

After all these steps we have 4476 traces and only 13 variants.


#### Declarations Approved or Rejected by Administrator only once

##### Subset International Travel Log

filename: ```InternationalDeclarations Adminstrator Approvals Rejections.xes```

Tool Used : Disco

* First we select all traces that are contained in 01 January 2018 00:00:05 until the 31 December 2018 23:59:59.
* Then we projected only on “Declaration SUBMITTED by EMPLOYEE”, “Declaration APPROVED by ADMINISTRATOR”, “Declaration REJECTED by ADMINISTRATOR”. We chose these since we would like to focus on the automated behaviour of the ADMINISTRATION.
* After applying the above filter and saving the event log to the system and importing back to the Disco lead us to a new attribute named as Variants. Using this filtered selected the variants with only one instance of Employee Submission followed by either Administrator Approval or Rejection with no repetition for the either of the activities:

Decl SUBMITTED by EMPLOYEE followed by Decl APPROVED by ADMINISTRATOR or
Decl SUBMITTED by EMPLOYEE followed by Decl REJECTED by ADMINISTRATOR

* After the above steps we have 3794 traces and 2 variants.


##### Sub-subset split over 1 Minute of International Travel Log

On and above 1 Minute sub-subset filename:
Subset InternationalDeclarations G1M.xes Less than 1 minute sub-subset filename:
Subset InternationalDeclarations L1M.xes Merged subset file:
Subset InternationalDeclarations 1M Classifier.csv
Tool Used : ProM

* Next we split the above subset into two sub-subsets using Select traces by directly/ eventually follows with the time restrictions of 1 minute on shorter and longer duration namely :

	– The first subset consists of cases that completed withing 1 minute (1253 Cases).
	– The second subset consists of cases which completed after 1 minute (2534 Cases).
	
* Extracted the respective sub-subsets in csv format and labelled them individually, “Yes” for less than a minute and “No” for on or more than a minute.
*  Both the respective csv files were merged for the further processing.

##### Joining Permit Log

filename: ```PermitLogWithClassifier.csv```

The log was subset following the same steps mentioned in subsection Subset International Travel Log.  After the processing we have 3155 traces and 2 variants.

Export the subset Permit Log event log (PL) to the csv file in RapidMiner and join it with the labelled International Travel Log (IL) with the following attributes:

	* IL.CASE ID with PL.dec_id_0
	* IL.Permit ID with PL.Case ID
	* IL.Activity with PL.Activity
	

##### Discretization of Continuous Attributes

The Amount, Permit RequestedBudget and Overspent Amount were continuous variables in the extracted csv file, it was discretized by entropy method which converted these three attributes into nominal attributes. The boundaries of the bins are chosen by such that the entropy is minimized in the induced partitions.



---

### Models

#### Declarations that have been rejected at least once

filename: ```Petri net of travel declarations with at least one rejection.cpn```

This model is based on the subset explained in section [Declarations rejected at least once](/README.md#declarations-rejected-at-least-once). We use the Interactive Data-aware Heuristic Miner to develop a Petri net, with the following parameters.

F requency: 0.1 Dependency: 0.9 Bindings: 0.1 Conditions: 0.5

Then, we export the Petri net and run the Replay a Log on Petri Net for Performance/Conformance Analysis. Accept the default settings, except for the last setting. We want to enable that move on models are assumed to be firing transitions as soon as they are enabled. You will then obtain Figure 5 as shown below. We’ve manually re-arranged the Petri net for ease of understanding.

![Architecture](/img/RejectedTravelDeclarations-ProM-annotated.png)

<figcaption>

Figure 5: Petri net with annotated colors for performance analysis. Note that the metrics in the top right corner are computed by a different plugin.

</figcaption>

#### Declaration process that are handled by budget owner and/or supervisor

filename: ```Supervisor vs Budget owner PN.cpn```

This model is based on the subset explained in section [Declarations approved or rejected by Budget Owner or supervisor](/README.md#declarations-approved-or-rejected-by-budget-owner-or-supervisor). We used the Interactive Data-aware Heuristic Miner to develop a causal net.

We used the following parameters

F requency: 0 Dependency: 0.9 Bindings: 0.1 Conditions: 0.5


So the causal net included 14/14 directly-follows relation occurring at least 0 times. This model again is converted to a Petri net using the plugin Convert Causal net (C-Net) to Petri net.

We ended up with the following model:


![Architecture](/img/Petrinet_Budgetowner_vs_Supervisor.png)

<figcaption>

Figure 6: Petri net, focused on the process of declaration acceptance/rejection by the budget owner/supervisor

</figcaption>


---

### Decision Tree

*Tool Used :* RapidMiner

#### Feature Engineering

Performed different feature engineering techniques, as shown in the below Table. It is quite evident from the weights that Amount is most significant in all the methods with respect to the weight. Permit RequstedBudget seems significant in two methods, i.e. Information Gain and Correlation Weight. Overspent is less significant comparatively but not neglectable; it consistently placed it self on the third most significant. Activity seems of no significance, and it gives the relative comparison for the rest attributes.

| attributes			   | Information Gain Weight | Correlation Weight | Relief Weight |
|--------------------------|-------------------------|--------------------|---------------|
| Permit RequestedBudget   | 0.021					 | 0.175			  | -0.395		  |
|Overspent				   | 0.003					 | 0.065			  | -0.268		  |
|Permit OrganizationEntity | 0.007					 | 0.223			  | -0.117		  |
|Activity				   | 0.000					 | 0.000			  | -0.115		  |
|Amount					   | 0.037					 | 0.223			  | 0.458		  |


####  Classification Techniques

Before building the decision tree, we wanted to test how other classification technique performs, we performed the Auto ML Model to test it out, as from Figure 7 it is clear that all the classification technique was performing almost equally in terms of accuracy.


![Architecture](/img/ClassificationTech.png)

<figcaption>

Figure 7: Accuracy of Different Classification Techniques

</figcaption>


####  Hyperparameter Optimization & Sampling

After selecting meaningful attributes after the feature engineering, the main aim for sampling was to keep the data balanced and the parameter optimized. So, performed up and downsampling with the Grid Parameter Optimization on the decision tree. Since the number of events for less than a minute was less than more than a minute, we performed downsampling for that case and the optimization; the accuracy came out to be around 65.78% whereas for upsampling the accuracy fell to 63.15%.

Since the accuracy was not high enough, 10-fold cross-validation technique was applied where we split the data into 70% for training and 30% for testing for each fold and performed the optimization, and the accuracy turned out to be 69.52%.

The optimization values for the decision tree we used were :

* criterion : gain ratio
* maximal depth : 15
* confidence : 0.1
* minimal gain : 0.001
* minimal leaf size : 4
* minimal size of split : 2
* number of prepruning alternatives : 3


####  Decision Tree on International Travel with Organisation Entity

Here we present the decision tree, which indicates that the process running in the every organization unit (department) is no different and the system running in the backend is the same for every organization unit. To demonstrate this, we used the top three departments with respect to there frequency, namely: 65456, 65458 and 65455, all together they constitute 45.97% of relative frequency from the entire event log. Figure 8 shows the decision tree, where “Permit OrganizationEntity” is at the root followed by “Amount”, and the ”Permit RequestedBudget” at the last node. Each leaf indicate the majority of the cases for the respective distribution with either “Yes” or “No”. The respective distribution ratio has been indicated for each leaf. The accuracy of the model is 65.71%.


![Architecture](/img/DecisionTree_L1M_.png)

<figcaption>

Figure 8: Decision Tree

</figcaption>

For e.g. from Figure 8 organization unit 65456 is followed by the Amount node which has the three splits for the respective range followed by Permit RequestedBudget. However, none of the leaves for this organization unit has completed the approval within a minute as the majority because the leaves’ distribution is not significant as indicated within the orange box for each leaf. Similarly, for organization unit 65455, there is one leaf that has the approval completed within a minute, but that is not significant, and the same goes for organization unit 65458.


Since the decision tree looks at the best split, not at the events’ order, we cannot guarantee that this is the correct order. We are essentially looking into the Automated process rules, and this decision tree helps our further work on finding the system order in terms that the Organization unit (Permit OrganizationEntity) does not play any role. So we can look in the more generalized model excluding Permit OrganizationEntity.













