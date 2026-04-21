```mermaid
graph TD
    A[Distributed System] --> B[Communication]
    A --> C[Storage]
    A --> D[Computation]
    A --> E[Partial Failure]
    E --> F[Fault Tolerance]
    F --> G[Replication]
    G --> H[Consistency]
    H --> I[Tradeoffs]
    D --> J[Parallel Processing]
    J --> K[MapReduce]
    C --> L[GFS]
    L --> K
    K --> M[Map Tasks]
    K --> N[Reduce Tasks]
    M --> O[Shuffle]
    O --> N
```
