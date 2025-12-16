---
sidebar_position: 15
---

# Survey Analysis


> _An airline has conducted a comprehensive passenger satisfaction survey. The goal is to identify customer needs and pain points, and to share insights through clear, easy-to-understand visualizations—ultimately improving business performance. As the analyst, create a report that supports data-driven decision-making._

Survey analysis has become an essential skill for all business professionals—not just data scientists.
In this tutorial, we’ll move beyond superficial analysis that simply counts the number of complaints, and aim for deeper understanding.

Specifically, we’ll look at:
 - How are dissatisfaction items related, and which “negative experiences” cascade into others? (Factor analysis)  
  If you were an executive, you’d want to improve the customer experience quickly and holistically. That means you shouldn’t just list issues as independent items—you should understand their underlying structure and how they relate. For example, if many customers are dissatisfied with `Check-in service`, `On-board service`, and `Inflight service`, those may share a common root cause (e.g., service quality), and revising service processes and staff training could improve overall satisfaction.

 - Which dissatisfaction factors matter most for repeat intent and overall satisfaction? (Sensitivity analysis)  
  Some issues are severe enough to make customers never want to fly with you again, while others are tolerable. Which problems should be prioritized for resolution? You need to identify them as quickly as possible.

 - What pain points are unique to specific customer segments, and what issues are shared across all segments? (Correspondence analysis)  
  Even if a segment is small, you can’t ignore it if it represents high-LTV customers (e.g., loyal customers). What unique frustrations do business-class loyal customers have on business trips? This is one of the key things you’d want to understand as an executive.

These three perspectives are difficult to obtain through simple aggregation and visualization alone, but they are crucial for strategy and decision-making.
Now, let’s analyze the data with Allye.


### Data

This guide walks through a practical survey-analysis workflow in Allye using the sample [Airline Passenger Satisfaction](https://www.kaggle.com/datasets/teejmahal20/airline-passenger-satisfaction/data). You can also download the CSV file from [here]().
Below is a table summarizing the key variables and their meanings:


| Column Name                        | Description                                                                                                  | Example Values                               |
|-------------------------------------|--------------------------------------------------------------------------------------------------------------|----------------------------------------------|
| Gender                             | Gender of the passengers                                                                                    | Female, Male                                 |
| Customer Type                      | The customer type                                                                                           | Loyal customer, disloyal customer            |
| Age                                | The actual age of the passengers                                                                            | 21, 35, 52                                   |
| Type of Travel                     | Purpose of the flight of the passengers                                                                     | Personal Travel, Business Travel             |
| Class                              | Travel class in the plane of the passengers                                                                 | Business, Eco, Eco Plus                      |
| Flight distance                    | The flight distance of this journey                                                                         |                                     |
| Inflight wifi service              | Satisfaction level of the inflight wifi service (0: Not Applicable, 1-5: Satisfaction score)                | 0, 1, 2, 3, 4, 5                             |
| Departure/Arrival time convenient  | Satisfaction level of Departure/Arrival time convenient                                                     | 1, 2, 3, 4, 5                                |
| Ease of Online booking             | Satisfaction level of online booking                                                                        | 1, 2, 3, 4, 5                                |
| Gate location                      | Satisfaction level of Gate location                                                                         | 1, 2, 3, 4, 5                                |
| Food and drink                     | Satisfaction level of Food and drink                                                                        | 1, 2, 3, 4, 5                                |
| Online boarding                    | Satisfaction level of online boarding                                                                       | 1, 2, 3, 4, 5                                |
| Seat comfort                       | Satisfaction level of Seat comfort                                                                          | 1, 2, 3, 4, 5                                |
| Inflight entertainment             | Satisfaction level of inflight entertainment                                                                | 1, 2, 3, 4, 5                                |
| On-board service                   | Satisfaction level of On-board service                                                                      | 1, 2, 3, 4, 5                                |
| Leg room service                   | Satisfaction level of Leg room service                                                                      | 1, 2, 3, 4, 5                                |
| Baggage handling                   | Satisfaction level of baggage handling                                                                      | 1, 2, 3, 4, 5                                |
| Check-in service                   | Satisfaction level of Check-in service                                                                      | 1, 2, 3, 4, 5                                |
| Inflight service                   | Satisfaction level of inflight service                                                                      | 1, 2, 3, 4, 5                                |
| Cleanliness                        | Satisfaction level of Cleanliness                                                                           | 1, 2, 3, 4, 5                                |
| Departure Delay in Minutes         | Minutes delayed when departure                                                                              |                                    |
| Arrival Delay in Minutes           | Minutes delayed when Arrival                                                                                |                                   |
| Satisfaction                       | Airline satisfaction level                                                                                  | Satisfaction, neutral or dissatisfaction     |


## 1. Understand the overall structure



## 2. Identify the Dissatisfaction Factor


## 3. Dissatisfaction - Common / Unique Factors



## 4. Conclusion & Proposal
