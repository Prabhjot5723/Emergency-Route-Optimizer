# Viva / Demonstration Questions and Suggested Answers

### 1. What problem does the project address?
It demonstrates how a route-planning program can compare estimated travel times over a road network and provide alternative paths when road conditions change.

### 2. Why represent roads as a graph?
Intersections and named places can be represented as vertices, while directional road segments are edges. This makes graph algorithms applicable to route search.

### 3. Why is the graph directed?
Road accessibility can differ by direction. A two-way road is represented by two separately controlled directed edges, so one direction can be closed without closing the reverse direction.

### 4. Why use an adjacency list?
The graph is sparse relative to all possible vertex pairs. An adjacency list stores only existing outgoing roads and supports efficient neighbour iteration.

### 5. Why use Dijkstra's algorithm?
The project assigns non-negative estimated travel-time weights to road segments. Dijkstra finds the minimum total weight path under those assumptions.

### 6. Why use Yen's algorithm?
Dijkstra gives one shortest path. Yen's algorithm uses repeated shortest-path searches to produce additional distinct loopless paths in increasing cost order.

### 7. How is travel time estimated?
For a road, estimated minutes are `(distanceKm / speedKph) * 60 * trafficFactor + extraDelayMinutes`.

### 8. What happens when a road is closed?
The edge remains in the graph for display/state management, but the route search ignores it. A new route request is calculated over the remaining open directed edges.

### 9. Does the program find the safest route?
No. The current objective is minimum estimated travel time. Risk points are descriptive inputs and are not validated safety scores or part of the ranking objective.

### 10. What is the role of Spring Boot?
It provides HTTP endpoints for backend health, route requests, and explicit place searches. It allows the browser UI to communicate with Java services.

### 11. What is OSRM doing?
The web route endpoint sends coordinates to OSRM's standard `driving` profile and receives route distance, estimated duration, geometry and any alternatives returned by the service.

### 12. Are Dijkstra and Yen calculating the real Delhi route?
Not in the current integration. The in-house algorithms operate on the fictional eight-location sample graph. The web API delegates real-road route calculation to OSRM. Connecting the DSA engine to a real imported road graph is future work.

### 13. What does Nominatim do?
It converts a user-entered place/address query into candidate places and coordinates. The interface uses explicit searches, not per-keystroke autocomplete, and the backend limits/caches requests for the public low-volume service.

### 14. Does selecting Ambulance, Fire and Police dispatch those units?
No. The interface can select multiple teams for one incident, but it does not know each unit's location, generate separate missions, send notifications, or communicate with control rooms.

### 15. Does the system use live traffic?
No. OSRM's standard route response provides an estimated duration based on the routing data/profile available to the service. A live traffic provider and verified road-closure integration are future enhancements.

### 16. Can the project be used for real emergency dispatch now?
No. It is an academic prototype. Real deployment requires verified road data, vehicle access rules, time-stamped traffic/closure feeds, operational approvals, reliability/security engineering, and extensive safety validation.

### 17. What is the main future enhancement?
Import a validated city road graph with geographic coordinates and vehicle-specific restrictions, then adapt and benchmark the in-house routing engine against a trusted routing service.
