# Core Diagrams

## Self Serve Jurisdiction Switching 

### Logical State Diagram
```mermaid
graph TB
    A{Eligibilities already open?} -->|Yes| B{Switching to an EES jurisdiction?}
    B -->|Yes| C{Record compatible with their EES?}
    C -->|Yes| D[Switching can be automated]
    C -->|No| E{Jurisdiction allows non-EES flow?}
    E -->|Yes| F[Manual request process]
    E -->|No| G[Switching rejected]
    B -->|No| H[Manual request process]
    A -->|No| I{Switching to an EES jurisdiction?}
    I -->|Yes| J{Record compatible with their EES?}
    J -->|Yes| K[Switching can be automated -- open eligibilities]
    J -->|No| L{Jurisdiction allows non-EES flow?}
    L -->|Yes| M[Switching can be automated]
    L -->|No| N[Switching rejected]
    I -->|No| O[Switching can be automated]
```

### API FLow
```mermaid
sequenceDiagram
    participant Apps_UI as Apps UI
    participant Jurisdiction_Switching_API as Jurisdiction Switching API
    participant History_Logging as History Logging
    participant Email_Handler as Email Handler
    participant Zendesk_Handler as Zendesk Handler
    participant IJurisdictionSwitchRequested_Handler as IJurisdictionSwitchRequested Handler

    Apps_UI->>Jurisdiction_Switching_API: Calls Jurisdiction Switching API
    Jurisdiction_Switching_API->>Jurisdiction_Switching_API: Checks CanSwitch flag
    alt CanSwitch is true
        Jurisdiction_Switching_API->>IJurisdictionSwitchRequested_Handler: Publish JurisdictionSwitchRequested event
        IJurisdictionSwitchRequested_Handler->>IJurisdictionSwitchRequested_Handler: Switches user to new Jurisdiction
        IJurisdictionSwitchRequested_Handler->>Email_Handler: Sends event to Email Handler
    else CanSwitch is false
        Jurisdiction_Switching_API->>Zendesk_Handler: Publish event to Zendesk Handler
    end
    Jurisdiction_Switching_API->>History_Logging: Log jurisdiction switch or attempt
