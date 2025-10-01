# Definition of standards for testing

## Context and Problem Statement

As any application, we want IroCO2 to be reliable throughout time. To that end, tests should be written all along code writing, but those tests should not depend on the person who write code. Thus, a definition of standards appears to be mandatory for two reasons : 
 - this will facilitate test writing, as well as test reviewing
 - this will ensure the application is resilient, and prevent any regression to be silently implemented

The frame we drew with standards to be respected is settled upon two lines of conduct : code coverage as an indicator, and test strategy as a mindset.

---

## 1. Code coverage

The definition of standards around coverage was mainly based on experience and knowledge of the Ippon Technology team in place at the moment of said definition. Nevertheless, online resources can easily be found if you wish to deepen your comprehension of these rules definition (see for example the article ["What is code coverage"](https://www.atlassian.com/continuous-delivery/software-testing/code-coverage)).

### Objectives for code coverage by perimeter

Since each side of IroCO2 application has its specificities, minimal coverage rate was established project by project. 

Please keep in mind, in particular for Back-end and Front-end projects, that coverage philosophy of IroCO2 team go beyond quantity of tests, thinking also about **quality** and **pertinence** of each test.

| Perimeter      | Objective |
| -------------- | --------- |
| Infrastructure | 50%*      |
| Back-end       | 90%       |
| Front-end      | 80%**     |

> \* : *indicative value which reflect the ambition to test main components with Terraform Test*
> 
> \*\* : *defined without clear idea of necessary time to catch up on that objective, so it can be considered as a minimal*

---

## 2. Test strategy

The main idea behind our test strategy is the Test Pyramid, whose base is made of Unit tests, End-to-end tests at the top, with Integration tests between (see this [pragmatic article](https://martinfowler.com/articles/practical-test-pyramid.html) of Martin Fowler). 

Still, the concrete strategy must match the reality of the project IroCO2, and moreover it seems necessary to define what we mean with unit tests, integration tests or end-to-end tests, for each perimeter.

---

### 2.a. Infrastructure

For infrastructure perimeter, unit testing and integration testing appear to be difficult. Thus, infrastructure code will mostly be tested with End-to-end tests, focusing on the top of the test pyramid :

#### UNIT testing

- Definition
> Testing infrastructure elements 
- Strategy
> Testing of main elements using for example Terraform Test

#### INTEGRATION testing

Not applicable

#### END-TO-END testing
-  Definition
> Testing the whole application.
- Strategy
> A few tests of use cases, to be executed manually at first, within an AWS sandbox running IroCO2 code. Automation shall be implemented in a second phase. 

---

### 2.b. Back-end

#### UNIT testing
-  Definition
> Testing the classes individually, mocking anything which could be outside its scope.
- Strategy
> Our ambition is to test every possible output of every method of every classe. **The coverage with unit tests should never decrease due to a new development**. When possible, it should rise with new unit tests for existing classes with missing test cases. 
> Also, an ambition of IroCO2 team is to produce back-end code with TDD.

#### INTEGRATION testing
-  Definition
> Testing the components in a more complex way. Some (but not all) external behavior are mocked.
- Strategy
> For a given use case, several scenarii should be tested, including errors handling.

#### END-TO-END testing
-  Definition
> Testing back-end from endpoints to databases and cloud services without mocking anything.
- Strategy
> Each endpoint should be tested through two test cases at least : one testing expected behavior, and one testing error handling

---

### 2.c. Front-end

#### UNIT testing
-  Definition
> Testing the components individually, mocking what does not depend on its scope.
- Strategy
> Our ambition is to test every UI component of the application. **The coverage with unit tests should never decrease due to a new development**. When possible, it should rise with new unit tests for existing components with missing test cases.
> Also, an ambition of IroCO2 team is to produce front-end code with TDD.

#### INTEGRATION testing
-  Definition
> Testing the components in a more complex way, navigating between components for example
- Strategy
> At the moment of the first definition of test strategy, this floor of the Front-end test pyramid is not a priority for IroCO2 team. Anyone is free to init the subject at any moment, beginning with replacing content of this field with the strategy for integration tests.

#### END-TO-END testing
-  Definition
> Testing the whole application.
- Strategy
> A few tests of use cases, to be executed manually at first, within an AWS sandbox running IroCO2 code. Automation shall be implemented in a second phase. 