# FlowGate Verification Strategy

## 1. Objective

The objective is to verify that FlowGate satisfies its documented
functional, reliability, performance, and operational requirements.

Verification will be developed together with the product rather than
being added only after implementation is complete.

## 2. Verification Levels

### 2.1 Unit Verification

Unit tests verify individual C++ components without starting the
complete network product.

Initial components:

- RuleValidator
- RuleEngine
- Statistics
- ConfigurationStore

Examples:

- reject an invalid IPv4 address;
- select the highest-priority rule;
- preserve the rule table after invalid input;
- calculate counters correctly.

Primary tool:

- GoogleTest

### 2.2 Component Verification

Component tests verify several C++ classes working together.

Examples:

- RuleValidator and RuleEngine integration;
- RuleEngine and ProxyServer decision flow;
- configuration loading into the rule table.

### 2.3 API and Functional Verification

Functional tests verify behavior through public interfaces rather
than calling internal methods directly.

Examples:

- add a rule;
- remove a rule;
- query statistics;
- check product health.

Primary tools:

- Python
- PyTest

### 2.4 Integration Verification

Integration tests verify communication between:

- the client;
- FlowGate;
- the backend;
- the control interface;
- configuration storage.

Example:

A rule is created through the control interface, FlowGate loads it,
and real traffic behavior changes accordingly.

### 2.5 System Verification

System tests verify the complete user-visible topology:

Client → FlowGate → Backend

Examples:

- allowed traffic reaches the backend;
- dropped traffic does not reach the backend;
- FlowGate survives backend failure;
- FlowGate recovers after the backend restarts.

### 2.6 Performance Verification

Performance tests will measure:

- connections per second;
- throughput;
- latency;
- CPU usage;
- memory usage;
- behavior as the number of rules increases.

Performance testing will be added only after correctness testing is
stable.

### 2.7 Recovery Verification

Recovery tests will intentionally create failures such as:

- backend termination;
- FlowGate restart;
- network interruption;
- corrupted configuration;
- temporary packet loss.

The tests shall verify detection, logging, safe behavior, and
recovery.

## 3. Test Types

The project shall include:

- positive tests;
- negative tests;
- boundary tests;
- regression tests;
- concurrency tests;
- performance tests;
- recovery tests.

## 4. Automation Principle

Tests should be repeatable and produce the same result when executed
under the same documented conditions.

Manual exploration may be used while learning or debugging, but
accepted regression scenarios should eventually be automated.

## 5. Failure Evidence

When an automated test fails, the framework should eventually
collect:

- test name;
- product version;
- configuration;
- FlowGate logs;
- backend logs;
- process state;
- network state;
- expected result;
- actual result.

## 6. Initial Quality Goals

Before the first portfolio release:

- all critical functional tests pass;
- no known critical defect remains open;
- every critical requirement has an automated test;
- every fixed defect has a regression test;
- the project builds from a clean checkout.