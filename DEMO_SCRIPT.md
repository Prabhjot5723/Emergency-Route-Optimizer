# Suggested 6-8 Minute Project Demonstration

## 1. Introduce the problem (45 seconds)

Explain that emergency responders need a quick way to compare routes when roads have different estimated travel times or a road segment becomes unavailable. The project is a decision-support prototype, not a real dispatch authority.

## 2. Show the DSA console engine (2 minutes)

1. Run `EmergencyRouteApp` in IntelliJ.
2. Choose menu option 3 to display the sample graph and directed road segments.
3. Choose menu option 1 to calculate routes from Central Hospital to Emergency Site.
4. Explain that Dijkstra finds the lowest estimated-time path and Yen's algorithm produces distinct loopless alternatives.
5. Use menu option 4 to close a directional road, then calculate again to show that the planner avoids it.
6. Optionally use menu option 5 to change a traffic factor and show that route costs are recalculated.

## 3. Show the backend (1 minute)

Run `EmergencyRouteBackendApplication` in IntelliJ. Open `/api/health` and explain that it is a simple readiness endpoint. Open the real route endpoint using the tested sample coordinates. Explain that the web route request is handled by OSRM using its standard driving profile; it is not the in-house Dijkstra/Yen graph calculation.

## 4. Show the web dashboard (2 minutes)

1. Open `http://localhost:8080/`.
2. Search and select an origin and incident destination.
3. Choose one or more response teams.
4. Explain that these are incident-planning selections only and share one origin/destination in the prototype.
5. Calculate the route and inspect geometry, distance, estimated duration, and alternatives.
6. Change the base map style and use the Dispatch Workspace theme switch.

## 5. Show testing and limitations (1-2 minutes)

Run `scripts/verify.sh` and show the 31 passing checks. If Maven is available, run `mvn test` and `mvn package`. Finish by naming the most important limitations: standard driving profile, no live traffic/verified closures, no separate vehicle dispatch, and the in-house DSA engine still uses a fictional graph.

## Closing statement

The project demonstrates an explainable graph-based routing algorithm and a web integration prototype. It establishes a foundation for research into real emergency routing, but it has not been validated or approved for operational emergency use.
