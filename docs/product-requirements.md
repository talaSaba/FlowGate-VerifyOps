# FlowGate Product Requirements

## 1. Purpose

FlowGate is a Linux networking application that receives client
connections, evaluates configured traffic rules, and either forwards
or blocks the traffic.

This document defines the initial behavior of the FlowGate core.
The requirements will be implemented incrementally in later phases.

## 2. Terminology

- Client: The application that connects to FlowGate.
- Backend: The server to which allowed traffic is forwarded.
- Rule: A configuration entry describing when traffic should be
  allowed or dropped.
- Default action: The action used when no configured rule matches.
- Payload: The application data transmitted by the client.

## 3. Rule Requirements

### FG-RULE-001 — Valid Rule

FlowGate shall accept a rule containing:

- a non-empty rule identifier;
- a valid IPv4 source address;
- a destination port between 1 and 65535;
- an action of ALLOW or DROP;
- an integer priority.

#### Acceptance criteria

1. A valid ALLOW rule is accepted.
2. A valid DROP rule is accepted.
3. Port 1 is accepted.
4. Port 65535 is accepted.
5. An empty rule identifier is rejected.
6. An invalid IPv4 address is rejected.
7. A port below 1 is rejected.
8. A port above 65535 is rejected.
9. An unsupported action is rejected.

**********

### FG-RULE-002 — Invalid Rule State Protection

When FlowGate rejects an invalid rule, the existing rule table shall
remain unchanged.

#### Acceptance criteria

1. The invalid rule is not stored.
2. Previously stored valid rules remain available.
3. A clear validation error is reported.
4. No existing rule is modified.

**********

### FG-RULE-003 — Unique Rule Identifier

FlowGate shall not allow two active rules with the same identifier.

#### Acceptance criteria

1. The first valid rule is stored.
2. A second rule with the same identifier is rejected.
3. The original rule remains unchanged after rejection.
4. A conflict error is reported.

**********

### FG-RULE-004 — Rule Priority

When more than one rule matches a connection, FlowGate shall apply
the matching rule with the highest numerical priority.

#### Acceptance criteria

1. A priority-100 rule is selected over a priority-10 rule.
2. The result is independent of insertion order.
3. Equal-priority behavior is documented and deterministic.

**********

### FG-RULE-005 — Default Action

When no configured rule matches a connection, FlowGate shall apply
the configured default action.

The initial default action shall be DROP.

#### Acceptance criteria

1. Traffic without a matching rule is blocked.
2. The decision is recorded in the logs.
3. The dropped-connection counter is incremented.

## 4. Proxy Requirements

### FG-PROXY-001 — Allowed Traffic Forwarding

FlowGate shall forward traffic permitted by an ALLOW rule to the
configured backend.

#### Acceptance criteria

1. The client can connect through FlowGate.
2. The backend receives the complete payload.
3. The payload is not modified.
4. The client receives the backend response.
5. The accepted-connection counter is incremented.

**********

### FG-PROXY-002 — Dropped Traffic

FlowGate shall prevent traffic matched by a DROP rule from reaching
the backend.

#### Acceptance criteria

1. The client does not communicate with the backend.
2. The backend receives no client payload.
3. The dropped-connection counter is incremented.
4. The selected rule identifier appears in the logs.

**********

### FG-PROXY-003 — Backend Unavailable

When the configured backend is unavailable, FlowGate shall report
the failure without crashing.

#### Acceptance criteria

1. FlowGate remains alive after the failed connection.
2. The client receives a defined failure result.
3. The backend-failure counter is incremented.
4. The error is recorded in the logs.
5. A later connection can succeed after the backend returns.

## 5. Lifecycle Requirements

### FG-LIFE-001 — Startup

FlowGate shall either enter the READY state or exit with a nonzero
status and a useful error message.

#### Acceptance criteria

1. Valid configuration leads to READY.
2. Invalid configuration does not lead to READY.
3. Startup failure returns a nonzero exit code.
4. Startup success returns or maintains a successful state.

**********

### FG-LIFE-002 — Graceful Shutdown

FlowGate shall close its resources during a requested shutdown.

#### Acceptance criteria

1. The listening port is released.
2. The process exits.
3. Shutdown is recorded in the logs.
4. FlowGate can be started again using the same port.

## 6. Logging and Statistics Requirements

### FG-LOG-001 — Structured Logging

FlowGate shall record structured events for startup, shutdown,
allowed connections, dropped connections, and backend failures.

Each event shall include:

- timestamp;
- severity;
- event name;
- relevant connection or rule information.

**********

### FG-STAT-001 — Connection Counters

FlowGate shall maintain counters for:

- accepted connections;
- dropped connections;
- backend connection failures;
- bytes forwarded from client to backend;
- bytes forwarded from backend to client.

Counters shall never become negative.