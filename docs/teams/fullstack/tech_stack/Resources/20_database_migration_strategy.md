```mermaid
graph LR
    Change([Schema Change<br/>in Code]):::code --> Migration{{Generate<br/>Migration File}}:::migration
    Migration --> Review[[Code Review<br/>in PR]]:::review
    Review --> CI[[CI Runs<br/>Migration Test]]:::ci
    CI --> Deploy([Deploy:<br/>Run Migration]):::deploy
    Deploy --> App([Start<br/>Application]):::success

    classDef code fill:#8b5cf6,stroke:#5b21b6,color:#fff
    classDef migration fill:#3b82f6,stroke:#1e40af,color:#fff
    classDef review fill:#f59e0b,stroke:#b45309,color:#fff
    classDef ci fill:#06b6d4,stroke:#0e7490,color:#fff
    classDef deploy fill:#e11d48,stroke:#9f1239,color:#fff
    classDef success fill:#22c55e,stroke:#15803d,color:#fff
```