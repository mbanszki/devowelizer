<!-- TABLE OF CONTENTS -->
## Table of Contents

* [About the Project](#about-the-project)
  * [Built With](#built-with)
* [Test Strategies](#testing-strategies)
    * [API Testing](#api-testing-strategies)
        * [Testing of Components and Flow for API Messaging](#testing-of-components-and-flow-for-api-messaging)
        * [API Contract Version and Documentation Testing](#api-contract-version-and-documentation-testing)
        * [API Logging](#api-logging)
        * [API Monitoring](#api-monitoring)
        * [Items Exluded due to example](#items-exluded-due-to-example)
    * [Data Driven Testing](#data-driven-testing)
    * [Scaling and Concurrency Testing](#scaling-and-concurrency-testing)
    * [Reliability Testing](#reliability-testing)
    * [Performance Testing](#performance-testing)
* [Test Files](#test-files)
    * [basic.csv](#basic)
    * [basic_lowercase.csv](#basic_lowercase)
    * [basic_uppercase.csv](#basic_uppercase)
    * [basic_numbers.csv](#basic_numbers.csv)
    * [character_types_encoded_utf8.csv](#character_types_encoded_utf8)
    * [character_types_unicode.csv](#character_types_unicode)
    * [example_text.csv](#example_text)
    * [http_standards.csv](#http_standards)
    * [special_characters.csv](#special_characters)
    * [special_vowels.csv](#special_vowels)
* [Deployment Files](#deployment-files)
    * [docker-compose.yml](#docker-compose)
    * [docker-compose.postman.yml](#docker-compose.postman)
    * [docker-compose.postman_concurrency.yml](#docker-compose.postman_concurrency)
    * [docker-compose.postman_reliability.yml](#docker-compose.postman_reliability)
* [Execution Details](#execution-details)
    * [HTML reporting](#html-reporting)
    * [Execution Logs](#execution-logs)

## About The Project
This project is an example on implementing API testing and achieving testing strategies

### Objectives
* Integrate Postman API testing
* Implement a data-driven approach
* Support local usage for dev / testing
* Support scalable and flexible usage in CI
* Support reporting and notifications

### Built With
* [Postman](https://www.postman.com/)
* [Postman-Newman](https://learning.postman.com/docs/running-collections/using-newman-cli/command-line-integration-with-newman/)
* [Docker](https://www.docker.com/)
* [Jenkins](https://www.jenkins.io/)
* [Slack](https://slack.com/)

Note: JENKINS and SLACK are included via `Jenkinsfile`. The implementaion is demonstrated, usage is not.

## Testing Strategies

### API Testing Strategies

#### Testing of Components and Flow for API Messaging

API communication is designed to handle HTTP REQUESTS, process them through an algorithm and respond with an HTTP RESPONSE.

Components of an HTTP REQUESTS that may affect processing:
* URL (including query parameters and values)
* REQUEST TYPE
* HEADERS (security token, target environment, CORS, testing-mode-on etc.)
* DATA or BODY CONTENT (content-type, length, format)

Components of an HTTP RESPONSE that may affect the result, assertions:
* HEADERS (processing-node, cache-hit or miss, CORS, etc)
* DATA or BODY CONTENT (content-type, length, format)
* RESPONSE CODE

Communication Medium or HTTP messaging flow aspects. What is between the sender and receiver?:
* Proxies and reverse-proxies
* Messaging queues
* API gateways / redirections

#### API Contract Version and Documentation Testing

APIs usually expose a form of structural and functional documentation that is used as a contract between services to very that the state or version of the API meets expectations.
API documentation usually exposes some part of the DATA-VALIDATION that can apply both before and during the processing of the REQUEST.

#### API Logging

APIs are usually designed to record communication and their details into LOG files which can be processed by other systems to give feedback on their health, usage.
E.g.: ELK, Sematext

#### API Monitoring

There are systems to continuously confirm the availability of a service. E.g.: Sentry
These are meant to alert and notify before an actual user experiences the down-time

#### APM

This is a more advanced form of monitoring which extends to the lowest levels of code. It provides the ability to follow up undesirable user experience all the way to exceptions in code.

#### Items Exluded due to example
1. There is no documentation available for the API. Any dynamic or static testing based on this is not applicable
2. There is only one resource and request type
3. GET request types don't have a BODY


### Data Driven Testing

As seen above, the basic approach to this simple API is using an INPUT vs OUTPUT test structure to assert capabilities.
* Understanding and working with this should be easy enough to engage all parties of software development, not just QA and DEV

The dynamic nature and simple syntax of files support multiple advanced forms of usage: 
* The files can be generated even from production usage logs
* A randomizer can generate this input and independent & identical implementation of the algorithm can be used to generate the expected output

Data Files included:

1. Quick scan and feedback of the most commonly expected input via `basic.csv`. 
2. A more extended scan meant to test the algorithm using different input-cases and types `basic_lowercase.csv`, `basic_uppercase.csv`, `basic_numbers.csv`
3. A scan for different types of special inputs to test the character-encoding capability via `character_types_unicode.csv`
4. A real life scenario where someone wants to process some text input `example_text.csv`
5. A test file trying the extent and rules of HTTP messaging `http_standards.csv`
6. A scan to make sure that historically or potentially error prone characters are safe to use `special_characters.csv`
7. A scan to check if there is support for languages other than English

### Scaling and Concurrency Testing

Having the ability to deploy multiple test containers can help us understand how the API handles concurrency and whether this has an effect on the output.

### Reliability Testing

Having the ability to set up a continuous stream of inputs over an extended time period can ensure that the API reliably returns the correct output.

### Performance Testing

Reports generated from the POSTMAN contain many useful details of the HTTP messaging between the test container and target. 

#### Test Suite Level Metrics and Trends

We can observe how the API behaves at a certain point of time compared to an established baseline
* `Total run duration: 2m 18.9s`
* `Total data received: 7.68KB`
* `Average response time: 359ms`

#### Test Level Metrics and Trends

* `Response Code:200 - OK`
* `Mean time per request:1119ms`
* `Mean size per request:24B`

Basic usage example:

1. deploy the DEVOWELIZER using docker
2. run tests via POSTMAN or the containerized implementation

For Docker, define test and environment using ".env" file or environment variables
```
TEST_SUITE=data-driven.json
TEST_DATA=http_standards.csv
TEST_ENV=docker-internal.json
TEST_ENV_URL=http://devowelizer:8080
ITERATION_COUNT=5
```

Execute
```
docker-compose -f docker-compose.postman.yml up
```

Review report in `/results` folder

### Test Files

#### basic
An example file representing the basic extend of the input

#### basic_lowercase
An example file applying input based testing strategies for lower-case characters

#### basic_uppercase
An example file applying input based testing strategies for upper-case characters

#### basic_numbers.csv
An example file applying input based testing strategies for numbers

#### character_types_encoded_utf8
An example file containing URL encoded UTF characters for input

#### character_types_unicode
An example file containing unicode characters for input

#### example_text
An example file containing text as input

#### http_standards
An example file containing checks for HTTP. URL character limit should be 2,048 characters where the input already exceeds this 

#### special_characters
An example file containing specific special characters 

#### special_vowels
An example file containing vowels from different languages

### Deployment Files

#### docker-compose
A basic compose file for the docker image to simplify deployment

#### docker-compose.postman
A basic example file for POSTMAN collection runner using NEWMAN

#### docker-compose.postman_concurrency
A basic example file for deploying multiple container executing tests and asserting concurrency.
Note
* can be easily improved by DOCKER-STACK or KUBERNETES where services are scaled via replication

#### docker-compose.postman_reliability
A basic example file that is intended to repeat the execution of one test N times to confirm stability and reliability in responses.

## Execution Details

### HTML reporting

Example reports can be found for all input data files in `/results`

### Execution Logs

Example Test Execution via NEWMAN
```
devowelizer-qa_1  | Iteration 4/4
devowelizer-qa_1  |
devowelizer-qa_1  | → Data Driven Request
devowelizer-qa_1  |   GET http://devowelizer:8080/¡¢£¤¥€¦§¨©ª«¬®¯°± [200 OK, 240B, 5s]
devowelizer-qa_1  |   ┌
devowelizer-qa_1  |   │ 'Request Data - ¡¢£¤¥€¦§¨©ª«¬®¯°±'
devowelizer-qa_1  |   │ 'Response Data - ¡¢£¤¥€¦§¨©ª«¬®¯°±'
devowelizer-qa_1  |   │ 'Expected Data - ¡¢£¤¥€¦§¨©ª«¬®¯°±'
devowelizer-qa_1  |   └
devowelizer-qa_1  |   ✓  Response code is 200
devowelizer-qa_1  |   ✓  Data Driven Test
```

Example CLI Report
```
devowelizer-qa_1  | ┌─────────────────────────┬──────────────────┬─────────────────┐
devowelizer-qa_1  | │                         │         executed │          failed │
devowelizer-qa_1  | ├─────────────────────────┼──────────────────┼─────────────────┤
devowelizer-qa_1  | │              iterations │                4 │               0 │
devowelizer-qa_1  | ├─────────────────────────┼──────────────────┼─────────────────┤
devowelizer-qa_1  | │                requests │                4 │               0 │
devowelizer-qa_1  | ├─────────────────────────┼──────────────────┼─────────────────┤
devowelizer-qa_1  | │            test-scripts │                8 │               0 │
devowelizer-qa_1  | ├─────────────────────────┼──────────────────┼─────────────────┤
devowelizer-qa_1  | │      prerequest-scripts │                4 │               0 │
devowelizer-qa_1  | ├─────────────────────────┼──────────────────┼─────────────────┤
devowelizer-qa_1  | │              assertions │                8 │               2 │
devowelizer-qa_1  | ├─────────────────────────┴──────────────────┴─────────────────┤
devowelizer-qa_1  | │ total run duration: 10.3s                                    │
devowelizer-qa_1  | ├──────────────────────────────────────────────────────────────┤
devowelizer-qa_1  | │ total data received: 77B (approx)                            │
devowelizer-qa_1  | ├──────────────────────────────────────────────────────────────┤
devowelizer-qa_1  | │ average response time: 2.5s [min: 78ms, max: 5s, s.d.: 2.4s] │
devowelizer-qa_1  | └──────────────────────────────────────────────────────────────┘
```
