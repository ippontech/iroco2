# Definition of standards for testing

## Context and Problem Statement

As any application, we want IroCO2 to be reliable throughout time. To that end, tests should be written all along code writing, but those tests should not depend on the person who write code. Thus, definition of standards appears to be mandatory for two reasons : 
 - this will facilitate test writing, as well as test reviewing
 - this will ensure the application is resilient, and prevent any regression to be silently implemented

The frame we drew with standards to be respected is settled upon two lines of conduct : code coverage as an indicator, and test strategy as a mindset.

## 1. Code coverage

The definition of standards around coverage was mainly based on experience and knowledge of the Ippon Technology team in place at the moment of said definition. Nevertheless, online resources can easily be found if you wish to deepen your comprehension of these rules definition (see for example the article ["What is code coverage"](https://www.atlassian.com/continuous-delivery/software-testing/code-coverage))

### Objectives for code coverage by project

Since each side of IroCO2 application has its specificities, minimal coverage rate was established project by project. 

Please keep in mind, in particular for Back-end and Front-end projects, that coverage philosophy of IroCO2 team go beyond quantity of tests, thinking also about quality and pertinence of each test.

| Project perimeter | Source code opening | Long-term objective |
| ----------------- | ------------------- | ------------------- |
| Infrastructure    | 0%                  | 50%*                |
| Back-end          | 80%                 | 90%                 |
| Front-end         | 50%**               | 80%                 |

> \* : *indicative value which reflect the ambition to test main components with Terraform Test*
> 
> \*\* : *defined without clear idea of necessary time to catch up on that objective, so it can be considered as a minimal*

## 2. Test strategy

To be done.
