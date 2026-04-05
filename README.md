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
```

## MaxMind GeoIP Data Usage

The project uses the GeoLite2 City database from MaxMind to extract geographic coordinates from IP addresses.

### Steps

1. Download the GeoLite2-City.mmdb file from MaxMind  
2. Store it locally or in Google Drive  
3. Load the database in Python using the geoip2 library  

### Example

```python
import geoip2.database

reader = geoip2.database.Reader('GeoLite2-City.mmdb')

response = reader.city(ip_address)
latitude = response.location.latitude
longitude = response.location.longitude
```
This process enables mapping IP addresses to geographic coordinates, which are used for graph construction and clustering.

## Neo4j Integration

The system can integrate with Neo4j to persist and query the network graph. Neo4j is a graph database that represents data as nodes and relationships, making it suitable for modeling peer-to-peer networks.

### In this project -
Nodes represent IP addresses with attributes such as latitude, longitude, and cluster identifier
Relationships represent connections between nodes and may store attributes such as distance
### Connection Setup
```python
from neo4j import GraphDatabase

uri = "bolt://localhost:7687"
username = "neo4j"
password = "your_password"

driver = GraphDatabase.driver(uri, auth=(username, password))
```

### Creating Nodes
```python
def create_node(tx, ip, lat, lon, cluster):
    tx.run(
        "MERGE (n:IPAddress {ip: $ip}) "
        "SET n.latitude = $lat, n.longitude = $lon, n.cluster = $cluster",
        ip=ip, lat=lat, lon=lon, cluster=cluster
    )
```
### Creating Relationships
```python
def create_edge(tx, ip1, ip2, distance):
    tx.run(
        "MATCH (a:IPAddress {ip: $ip1}) "
        "MATCH (b:IPAddress {ip: $ip2}) "
        "MERGE (a)-[r:CONNECTS]->(b) "
        "SET r.distance = $distance",
        ip1=ip1, ip2=ip2, distance=distance
    )
```

### Storing NetworkX Graph in Neo4j
```python
with driver.session() as session:
    for node in G.nodes(data=True):
        ip = node[0]
        lat, lon = node[1]['pos']
        cluster = node[1].get('cluster', 0)
        session.write_transaction(create_node, ip, lat, lon, cluster)

    for u, v, data in G.edges(data=True):
        distance = data.get('distance', 0)
        session.write_transaction(create_edge, u, v, distance)
```
### Retrieving Graph from Neo4j
```python
def fetch_graph(tx):
    query = """
    MATCH (n:IPAddress)-[r:CONNECTS]->(m:IPAddress)
    RETURN n.ip, n.latitude, n.longitude,
           m.ip, m.latitude, m.longitude,
           r.distance
    """
    return list(tx.run(query))
```
### Reconstructing NetworkX Graph
```python
import networkx as nx

G = nx.Graph()

with driver.session() as session:
    results = session.read_transaction(fetch_graph)

    for record in results:
        ip1 = record[0]
        lat1 = record[1]
        lon1 = record[2]
        ip2 = record[3]
        lat2 = record[4]
        lon2 = record[5]
        distance = record[6]

        G.add_node(ip1, pos=(lat1, lon1))
        G.add_node(ip2, pos=(lat2, lon2))
        G.add_edge(ip1, ip2, distance=distance)
```

### Workflow
Construct the graph using NetworkX
Store nodes and relationships in Neo4j
Query graph data when required
Reconstruct the graph in NetworkX
Perform clustering and analysis

This process enables persistent storage, efficient querying, and scalable graph management.

### How to Run
Install all required libraries
Prepare the dataset containing IP addresses (CSV format)
Download and place the GeoLite2-City.mmdb file
(Optional) Install and start Neo4j database and configure credentials
Update file paths for dataset and GeoIP database
Execute the Python script

The system will read IP data, extract geolocation, construct the graph, and perform clustering and analysis. If Neo4j is enabled, the graph can be stored and queried from the database.

### Dataset
CSV file containing IP addresses
GeoLite2 database for geolocation

### Key Concepts
Graph Theory
Community Detection
Spectral Clustering
Network Simulation

### Notes
Requires GeoLite2 database from MaxMind
Neo4j integration is optional but useful for persistence
Large datasets require higher memory and computation resources

### License

This project is intended for academic and research purposes only.
