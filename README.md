# Risk Process Monitoring Demo

This repository contains a Neo4j-based demo for monitoring banking risk computation processes. It demonstrates how to use graph databases to visualize job dependencies and perform critical path analysis (CPA) to enhance operational efficiency and risk management.

## Overview

In banking, risk computation processes involve complex workflows with interdependent jobs running on multiple processors. This demo showcases how Neo4j can provide a 360° view of these processes, predict job durations, and identify critical paths to ensure timely completion and proactive risk mitigation.

### Key Features
- **Graph Visualization**: Visualize job dependencies (`DEPENDS_ON`), queue states (`WAITS`), and processor workloads.
- **Critical Path Analysis (CPA)**: Compute the critical path and ETA for processes to identify bottlenecks.

## Prerequisites

- **Neo4j Server** or **Neo4j Aura** (version 5.10 or later).
- Neo4j Graph Data Science (GDS) library installed.
- APOC library installed.

## Setup

Ingest graph from [**script**](./ingest.cypher)

