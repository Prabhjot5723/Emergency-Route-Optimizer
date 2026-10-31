# Final Academic Submission Checklist

Project: Emergency Route Optimizer

## Evidence already available in the project package

- [x] Java 17-compatible DSA implementation.
- [x] Adjacency-list graph model with 8 fictional locations and 24 directed road segments.
- [x] Dijkstra shortest-time path algorithm.
- [x] Yen's loopless alternative-route algorithm.
- [x] Road closure and traffic-multiplier simulation.
- [x] Invalid input and unreachable destination handling.
- [x] Spring Boot backend and static web interface were added in the working IntelliJ project (verify all listed files exist locally).
- [x] OSRM route API was manually exercised by the developer; one observed Delhi response returned code `Ok`, 6.7559 km, 573.8 seconds, and GeoJSON geometry.
- [x] The dependency-free verification suite was run after expansion: 31/31 checks passed.
- [x] Report, README, demonstration script, test plan, and viva notes prepared.

## Must be checked on the student's final computer before submission

- [ ] Confirm the final local project contains `EmergencyRouteBackendApplication.java`, `HealthController.java`, `RouteController.java`, `OsrmRoutingService.java`, `GeocodingController.java`, `NominatimGeocodingService.java`, and `src/main/resources/static/index.html`.
- [ ] Run `mvn test` from the project root and save a screenshot of the green JUnit/Maven result.
- [ ] Run `mvn package` and confirm `BUILD SUCCESS`.
- [ ] Run `scripts/verify.sh` and retain the output showing 31 checks passed.
- [ ] Run `EmergencyRouteBackendApplication` and confirm Tomcat starts.
- [ ] Confirm `/api/health` returns JSON with `status: UP`.
- [ ] Confirm `/api/geocode?q=Patiala` returns a JSON array or a clear JSON error, never an unhandled HTML parse error.
- [ ] Search and select an origin and destination by name.
- [ ] Calculate a real-road route and check the geometry, distance, duration, and map display.
- [ ] Verify that the workspace theme switch does not recolor the map.
- [ ] Verify that more than one response team can be selected and that the UI explains the shared-route limitation.
- [ ] Capture screenshots of the dashboard, selected teams, search results, route results, and successful tests.
- [ ] Replace report cover placeholders (student name, roll number, course, institution, supervisor).
- [ ] Ensure the report date and institution formatting match submission requirements.
- [ ] Create a backup ZIP of the final local project after all tests pass.

## Academic honesty / system boundary

Do not state that the system dispatches real units, controls traffic lights, receives live traffic, or computes emergency-authorized routes. The current web route provider uses standard driving routing. Multiple response teams are selectable in the UI but are not separately dispatched. The in-house Dijkstra/Yen engine is tested against the fictional sample graph, not a full imported city road graph.
