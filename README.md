# Risk Process Monitoring Demo

This repository contains a Neo4j-based demo for monitoring banking risk computation processes. It demonstrates how to use graph databases to visualize job dependencies and perform critical path analysis (CPA) to enhance operational efficiency and risk management.

## Overview

In banking, risk computation processes involve complex workflows with interdependent jobs running on multiple processors. This demo showcases how Neo4j can provide a 360° view of these processes, predict job durations, and identify critical paths to ensure timely completion and proactive risk mitigation.

### Key Features
- **Graph Visualization**: Visualize job dependencies (`DEPENDS_ON`), queue states (`WAITS`), and processor workloads.

![model](https://github.com/user-attachments/assets/bf909950-12f4-4907-89ae-3dbcbce7093e)

- **Critical Path Analysis (CPA)**: Compute the critical path and ETA for processes to identify bottlenecks.

## Prerequisites

- **Neo4j Server** or **Neo4j Aura** (version 5.10 or later).

## Setup

Ingest graph from [**script**](./ingest.cypher)

To show the graph model :
```cypher
CALL db.schema.visualization()
```

## Query

- Show a cuputing process
```cypher
MATCH (n:Process {process_id:"Proc1"})<-[i:IS_INSTANCE_OF]-(j:Job)
OPTIONAL MATCH path = (j)-[:DEPENDS_ON]->*()
RETURN path, n, i
```

- Show a processor queue
```cypher
MATCH path = (n:Processor {processor_id: "P3"} )-[:QUEUE_HEAD]->()
(()<-[:WAITS]-())*
()<-[:QUEUE_TAIL]-(n)
RETURN path
```

- Show work still to do to complete a process
```cypher
MATCH (n:Process {process_id:"Proc3"})<-[i:IS_INSTANCE_OF]-(j:Job)
OPTIONAL MATCH path = (j)-[:DEPENDS_ON|WAITS]->*(x WHERE x.status <> "Completed")
RETURN path, n, i
```

- Critical path analysis of a process
```cypher
MATCH (n:Process {process_id:"Proc3"})<-[i:IS_INSTANCE_OF]-(j:Job WHERE j.status <> "Completed")
OPTIONAL MATCH path = (j)(()-[:DEPENDS_ON|WAITS]->(jobs))*(x WHERE x.status <> "Completed")
// the *duration* property in this context means *expected_duration* because tasks are not completed yet
WITH n, i, path, apoc.coll.sum([job IN [j]+jobs | job.duration * (1.0-job.completion_progress)]) AS total_duration
ORDER BY total_duration DESC LIMIT 1
RETURN n, i, path, total_duration
```
CPA at scale can be done with GDS longest path as described [**here**](https://medium.com/me/stats/post/792cc8a04ff1).

## Explore

### Run search phrases

- Show me a graph
- Show process $procid
- Show process dependency for $procid
- Show queue for processor $processor_id
- Show not completed work for $procid

### Run scene action

Select one or several **Process** nodes and right-click run scene action / CPA

