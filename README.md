# Emergency Route Optimizer

**Academic prototype | Java 17+ | Graph algorithms | Spring Boot | Leaflet**

The Emergency Route Optimizer is an educational prototype that demonstrates graph-based route optimization and a web-based route-planning interface. It has two distinct routing paths, and they must not be confused:

1. **In-house DSA engine:** Dijkstra's algorithm and Yen's K-shortest loopless paths operate on the project's deterministic fictional road graph (8 locations and 24 directed road segments).
2. **Real-road web demonstration:** the Spring Boot API sends geographic coordinates to OSRM's standard `driving` profile and returns the provider's route geometry, distance, duration, and any alternatives returned. The current web route endpoint does **not** run the in-house Dijkstra/Yen implementation against a locally imported real-world road graph.

The web interface also supports user-triggered place search through Nominatim, browser GPS permission, map display through Leaflet, response-team selection, incident planning fields, route comparison, and a Dispatch Workspace theme toggle. Multiple response teams can be selected for one incident, but the prototype currently uses one shared origin/destination and one shared route request; it does not create separate vehicle missions or send dispatch messages.

> **Safety boundary:** This is an academic prototype, not a certified emergency-dispatch or navigation system. It does not provide validated emergency-vehicle access rules, live traffic, verified closures, traffic-signal priority, or operational guarantees. Do not use it to direct an actual emergency response.

## Implemented capabilities

### DSA / console engine

- Directed weighted graph stored with adjacency lists.
- Named locations and independently addressable directional road segments.
- Explainable estimated road time from distance, nominal speed, traffic multiplier, and fixed delay.
- Dijkstra shortest-time route search.
- Yen's algorithm for up to 10 distinct loopless alternatives (the demonstration normally requests a small number).
- Open/closed road status; closed segments are excluded from later searches.
- Interactive traffic-multiplier updates and bidirectional-road addition.
- Validation for unknown IDs, invalid route counts, invalid road attributes, and unreachable destinations.

### Web / backend demonstration

- Spring Boot REST backend with `GET /api/health`.
- `GET /api/routes?originLat=...&originLon=...&destinationLat=...&destinationLon=...` calls OSRM using the standard driving profile and requests GeoJSON route geometry and alternatives.
- `GET /api/geocode?q=...` performs an explicit place search using Nominatim; the interface is designed around user-triggered searches rather than per-keystroke autocomplete.
- Leaflet map with configurable street, humanitarian, terrain, satellite, and cloudless map layers as available in the installed `index.html`.
- Origin/destination place selection, GPS permission request, route display, route alternatives, and distance/duration cards.
- Multi-team selection (Ambulance, Fire & Rescue, Police) and incident planning fields are currently interface-level planning features, not dispatch integrations.
- Light/dark theme scoped to the Dispatch Workspace; map tiles and map controls remain independently styled.
- Optional `BrowserLauncher` opens the local page when the backend has started, using the computer's default browser.

## Architecture

```text
                   Browser / Dispatch Workspace
       HTML + CSS + JavaScript + Leaflet map and controls
                    |             |            |
            /api/health     /api/geocode    /api/routes
                    |             |            |
                    +-------------+------------+
                                  |
                         Spring Boot backend
                                  |
                   +--------------+---------------+
                   |                              |
          Nominatim integration             OSRM integration
          place name -> coordinates         coordinates -> route
                                                   |
                                      distance / duration / GeoJSON

     Separate academic algorithm path (not used by the OSRM endpoint):
     RoadNetwork -> RouteOptimizer -> Dijkstra + Yen alternatives
          fictional graph with 8 vertices / 24 directed segments
```

## Main source map

```text
emergency-route-optimizer/
├── pom.xml
├── README.md
├── report.tex
├── report.pdf
├── src/
│   ├── main/
│   │   ├── java/com/emergencyroute/
│   │   │   ├── EmergencyRouteApp.java
│   │   │   ├── EmergencyRouteBackendApplication.java   # Spring Boot entry point (added in working project)
│   │   │   ├── BrowserLauncher.java                    # optional local browser launcher
│   │   │   ├── Location.java
│   │   │   ├── Road.java
│   │   │   ├── RoadNetwork.java
│   │   │   ├── Route.java
│   │   │   ├── RouteOptimizer.java
│   │   │   ├── SampleNetwork.java
│   │   │   ├── api/
│   │   │   │   ├── HealthController.java
│   │   │   │   ├── RouteController.java
│   │   │   │   └── GeocodingController.java
│   │   │   └── integration/
│   │   │       ├── OsrmRoutingService.java
│   │   │       └── NominatimGeocodingService.java
│   │   └── resources/static/index.html
│   └── test/java/com/emergencyroute/RouteOptimizerTest.java
├── tools/VerificationSuite.java
├── scripts/verify.sh
├── sample-output/
└── docs/
```

Some backend and web files were added after the original project archive was created. The tree above describes the intended current working project; verify the files are present in your local IntelliJ project before creating the final archive.

## Build, run, and test

### Automated verification without Maven

With JDK 17 or newer and Bash:

```bash
./scripts/verify.sh
```

This compiles the production Java files using `javac --release 17` and executes the dependency-free regression suite. The current expanded suite contains **31 checks**.

### Maven / JUnit

From the folder containing `pom.xml`:

```bash
mvn test
mvn package
```

Run `com.emergencyroute.EmergencyRouteApp` for the original console demonstration. Run `com.emergencyroute.EmergencyRouteBackendApplication` from IntelliJ IDEA for the web backend. When it is ready, open:

- `http://localhost:8080/` - dispatch interface
- `http://localhost:8080/api/health` - backend health
- `http://localhost:8080/api/geocode?q=Patiala` - place-search smoke test

The exact external-provider results can change over time and require internet access.

## Core model

A road network is represented as a directed weighted graph \(G=(V,E)\). Every directed road segment stores distance \(d\) in kilometres, nominal speed \(v\) in kilometres per hour, traffic multiplier \(m\geq 1\), extra delay \(a\) in minutes, a descriptive risk input, and an open/closed flag.

The estimated time of one segment is:

\[
 t(e)=\left(\frac{d_e}{v_e}\right)60m_e+a_e.
\]

The route optimizer minimizes the sum of edge-time estimates over open directed edges. The risk value is displayed descriptively and is **not** part of the ranking objective or a validated safety score.

## Algorithm summary

- **Dijkstra:** a priority queue selects the unsettled vertex with the lowest tentative total travel time. The implementation stores predecessor roads to reconstruct the selected path. With a binary heap, a conventional bound is \(O((|V|+|E|)\log |V|)\).
- **Yen's K-shortest loopless paths:** after the first shortest path, the algorithm generates spur paths by banning selected edges and root-prefix vertices, combines root and spur segments, deduplicates route signatures, and accepts the next lowest-cost candidate. Its cost depends on the number of alternatives requested and repeated shortest-path runs.

## Observed demonstration result

During development, the web endpoint was tested with approximate coordinates near India Gate and Red Fort in New Delhi. One observed OSRM response returned:

- Distance: 6,755.9 metres (approximately 6.76 km)
- Duration: 573.8 seconds (approximately 9.56 minutes)
- Response code: `Ok`
- Geometry: GeoJSON `LineString`
- Returned alternatives: one route in that particular response

This is a provider-generated example, not a fixed expected result, live-traffic prediction, or safety guarantee.

The fictional DSA sample graph has a separate deterministic result. Its three best sample routes are documented in `sample-output/demo-run.txt` and `report.pdf`.

## Test status and evidence

The dependency-free regression script was run after the academic test expansion and reported **31 checks passed**. The checks cover graph dimensions, route existence and ordering, alternative uniqueness, looplessness, endpoint correctness, closure handling, traffic updates, same-origin behavior, unreachable networks, invalid inputs, route-count validation, ID normalization, direction-specific closure handling, travel-time arithmetic, road validation, duplicate IDs, and route-result immutability.

The JUnit suite currently contains 13 test methods and is also expanded. Run `mvn test` in IntelliJ or a Maven-enabled terminal on the final local project and retain the green test report as submission evidence. The validation environment used for preparing this documentation did not have Maven installed, so the current JUnit execution was not independently rerun here.

## External data and usage notes

- **OSRM** supplies standard-driving routes based on its configured road graph. Requesting alternatives does not guarantee that multiple alternatives will be available.
- **Nominatim public service** is for low-volume, explicit search. The integration uses a descriptive User-Agent, caches successful responses, spaces request starts, and avoids autocomplete. Respect the public usage policy; production use should use a properly provisioned provider or self-hosted service.
- **OpenStreetMap** requires visible attribution. Its public tile service is best-effort and has no SLA; do not bulk download or prefetch tiles.
- **Map coverage** varies by location. Missing building footprints, roads, river names, or English labels reflect source-data and rendering coverage; the client cannot invent missing authoritative geographic data.
- **Satellite imagery** resolution and acquisition dates vary by provider and location. Zooming beyond native image detail cannot recover detail that is not present in the source imagery.

## Current limitations

1. The in-house Dijkstra/Yen engine still operates on the fictional sample graph; it is not yet connected to a locally imported real-world road graph.
2. The web route request delegates route calculation to OSRM's standard driving profile.
3. Emergency vehicle permissions, size/weight/height constraints, road restrictions, turn restrictions, live traffic, authoritative closures, construction, weather, and signal priority are not integrated.
4. Multiple response teams can be selected for one incident in the interface, but the prototype uses a shared origin/destination and route; it does not track separate vehicles, send dispatch orders, or communicate with emergency services.
5. The incident type, priority, and reference/notes are planning fields; they do not change route weights or invoke an operational dispatch workflow.
6. GPS is browser-mediated and permission-based. It is a location input, not a tracking or fleet-management service.
7. Public demo routing/geocoding/tile services can rate-limit, change, or become unavailable. The web demo requires internet access.
8. There is no authentication, role-based access, audit logging, high-availability deployment, incident database, telemetry, or safety certification.

## Future work

A real-world research phase would need a validated routable road graph and geospatial import pipeline; vehicle-specific restrictions; reliable, timestamped traffic and closure sources; live GPS tracking with consent; separate per-vehicle route computation; dispatcher override and audit trails; offline/fallback behavior; security and privacy review; and field validation with emergency-service stakeholders. The system must remain advisory until a formal safety case and operational approvals exist.

## References

1. E. W. Dijkstra, "A note on two problems in connexion with graphs," *Numerische Mathematik*, vol. 1, pp. 269-271, 1959. DOI: <https://doi.org/10.1007/BF01386390>.
2. J. Y. Yen, "Finding the K Shortest Loopless Paths in a Network," *Management Science*, vol. 17, no. 11, pp. 712-716, 1971. DOI: <https://doi.org/10.1287/mnsc.17.11.712>.
3. OSRM Project, HTTP API documentation: <https://project-osrm.org/docs/v26.4.0/http>.
4. OpenStreetMap Foundation, Nominatim Usage Policy: <https://operations.osmfoundation.org/policies/nominatim/>.
5. OpenStreetMap Foundation, Tile Usage Policy: <https://operations.osmfoundation.org/policies/tiles/>.
6. Leaflet, API Reference: <https://leafletjs.com/reference.html>.
7. OpenStreetMap, Copyright and Licence: <https://www.openstreetmap.org/copyright>.
