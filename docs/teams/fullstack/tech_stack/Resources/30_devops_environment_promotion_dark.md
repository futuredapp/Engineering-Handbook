```mermaid
graph LR
    Dev([develop branch<br/>Dev Environment]):::dev --> Staging([staging branch<br/>Staging Environment]):::staging
    Staging --> Prod([main branch<br/>Production]):::prod

    classDef dev fill:#22c55e,stroke:#15803d,color:#fff
    classDef staging fill:#f59e0b,stroke:#b45309,color:#fff
    classDef prod fill:#e11d48,stroke:#9f1239,color:#fff
```