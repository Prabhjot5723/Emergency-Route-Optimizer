# Test Plan and Evidence Summary

## A. Dependency-free automated verification

Run from the project root:

```bash
./scripts/verify.sh
```

The suite compiles production Java sources with `javac --release 17` and checks the following behaviors.

| ID | Test area | Expected result |
|---|---|---|
| T01 | Sample graph size | 8 locations |
| T02 | Directed road count | 24 segments |
| T03 | Route existence | At least one route for HOSP to SITE |
| T04 | K limit | No more than requested alternatives |
| T05 | Ordering | Routes sorted by estimated time |
| T06 | Uniqueness | No duplicate route signatures |
| T07 | Endpoints | First and last locations match request |
| T08 | Node-edge structure | Nodes = edges + 1 |
| T09 | Looplessness | No repeated location in a route |
| T10 | Closure handling | Closed directed edge never appears |
| T11 | Re-routing after closure | A remaining path is found if one exists |
| T12 | Traffic update | Affected edge ETA increases |
| T13 | Route ranking update | A slower edge can change the preferred route |
| T14 | Same origin/destination | Zero ETA, no road legs |
| T15 | Disconnected graph | Empty route list |
| T16 | Unknown location | IllegalArgumentException |
| T17 | K = 1 | At most one route |
| T18 | K = 0 | Rejected |
| T19 | K > 10 | Rejected |
| T20 | Input normalization | Whitespace and case normalized |
| T21 | Locale robustness | IDs behave under Turkish default locale |
| T22 | Directionality | Closing A-to-B does not close B-to-A |
| T23 | Travel-time formula | Exact computed value for known inputs |
| T24 | Invalid distance | Zero distance rejected |
| T25 | Invalid traffic at construction | Factor below one rejected |
| T26 | Invalid traffic update | Factor above allowed cap rejected |
| T27 | Duplicate location | Rejected |
| T28 | Unknown road endpoint | Rejected |
| T29 | Duplicate road ID | Rejected |
| T30 | Route list copy | Input list mutation does not alter result |
| T31 | Route immutability | Returned route list is unmodifiable |

**Latest recorded run in the validation environment:** 31 checks passed. The run used OpenJDK 21 to compile with `--release 17`.

## B. JUnit / Maven tests

Run:

```bash
mvn test
```

The updated `RouteOptimizerTest.java` contains 13 test methods for route ordering, closure filtering, alternative uniqueness, traffic re-ranking, invalid endpoints, same-node routes, route-count validation, travel-time arithmetic, invalid road weights, directional closures, duplicate graph data, locale-independent identifiers, and defensive copying.

The documentation preparation environment did not have Maven installed, so the updated JUnit suite was not executed in that environment. Run it in IntelliJ or a Maven-enabled terminal before submitting and save the green test summary.

## C. Manual web/API acceptance checks

| ID | Action | Expected result | Status before final local re-test |
|---|---|---|---|
| W01 | Open `/api/health` | JSON status `UP` | Previously observed working by user |
| W02 | Request sample `/api/routes` | OSRM `code: Ok`, route distance/duration/geometry | Previously observed working by user |
| W03 | Search `/api/geocode?q=Patiala` | JSON array or clear JSON error | Must re-test after latest geocoding fix |
| W04 | Search origin/destination in UI | Search results and selected coordinates shown | Must re-test locally |
| W05 | Request route in UI | Route geometry displayed and metrics populated | Previously observed route endpoint; final UI re-test required |
| W06 | Select multiple response teams | Multiple teams visibly selected | Implemented in UI; final local re-test required |
| W07 | Toggle Dispatch Workspace theme | Only sidebar theme changes | Latest HTML is intended to scope theme to sidebar; final visual re-test required |
| W08 | Switch map style / zoom | Map tiles and attribution remain visible | Provider coverage/network dependent; final local re-test required |

## D. Evidence to collect for submission

Capture screenshots of: (1) console sample routes, (2) road-network menu, (3) road closure and recalculated route, (4) JUnit/Maven green summary, (5) backend health JSON, (6) real-road API response, (7) web dashboard with route, and (8) multiple team selection / workspace theme.
