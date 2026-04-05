# Blockchain P2P Network Connectivity Analysis

## Overview
This project implements a large-scale IP-based network graph simulation using real-world geographic data. It constructs a dynamic peer-to-peer (P2P) network graph where nodes represent IP addresses and edges represent connectivity based on geographic proximity.

The system integrates geolocation, graph processing, and clustering techniques to model and analyze realistic distributed network behavior.

---

## Features
- IP geolocation using MaxMind GeoLite2 database  
- Dynamic graph construction using NetworkX  
- Community detection and clustering  
- Incremental node addition with adaptive connectivity  
- Optional Neo4j integration for persistent graph storage  
- Graph visualization and analysis  

---

## Required Libraries

The project uses the following Python libraries:

- networkx – Graph creation and analysis  
- numpy – Numerical computations  
- pandas – Data handling and CSV processing  
- matplotlib – Visualization  
- geoip2 – IP geolocation using MaxMind database  
- community (python-louvain) – Community detection  
- tabulate – Table formatting  
- heapq – Priority queue operations  
- collections (deque) – Efficient queue handling  
- math – Mathematical computations  
- random – Random sampling  
- csv – File handling  
- neo4j – Python driver for Neo4j database  

Install dependencies using:

```bash
pip install geoip2 networkx matplotlib pandas numpy scikit-learn python-louvain tabulate neo4j



