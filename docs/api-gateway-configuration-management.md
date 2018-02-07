# API Gateway Configuration Model  
This document captures the intended _Configuration Model_ that will be loaded into the ServiceNow CMDB to manage the API Gateway environments across development, test and production.

```
┌───────────────────────┐  
│ Integration Framework │----------------------------> [Multiple CI's] representing the instance of the  
│ [Business Service]    │                              service i.e. development, test and production.
└───┬───────────────────┘                              Example: Development Integration Framework.
    │ Consumes::Consumed By
    │ ┌───────────────────────┐  
    └─┤ API Gateway           │-----------------------> [Single CI] representing the Gateway in the
      │ [Application Service] │                         environment.
      └───┬───────────────────┘                         Example: Development Gateway
          │  ↑ PROVISIONED AS API GATEWAY INITIALIZED IN AWS ACCOUNT
──────────┼────────────────────────────────────────────────────────────────────────────────────────────────────────
          │  Used By::Depends On
          │ ┌───────────────────────┐  
          └─┤ API                   │------------------> [Multiple CI's] representing the API's hosted in the
            │ [Technical Service]   │                    Gateway.
            └───┬─┬─────────────────┘                    Example: Development Automation API
                │ │  ↑ PROVISIONED BY API DEPLOYMENT
                │ │    (Not including Front and Back-End Integration Relationships)
────────────────┼─┼────────────────────────────────────────────────────────────────────────────────────────────────
                │ │  ↓ PROVISIONED BY APPLICATION DEPLOYMENT ACTIVITY
                │ │    (Including Front and Back-End Integration Relationships)
                │ │  Connected By::Connects To
                │ │ ┌───────────────────────┐  
                │ └─┤ Front-End Integration │-----------> [Multiple CI's] representing the Application
                │   │                       ├─┐           Services that depend on this API.
                │   └───────────────────────┘ │           Example: Development ServiceNow
                │                             │            
                │ Connected By::Connects To   │ Consumes::Consumed By To           
                │   ┌───────────────────────┐ │
                │   │                       ├─┘
                └───┤ Back-End Integration  │-----------> [Multiple CI's] representing the Application
                    └───────────────────────┘             Services that integrates to this API.
                                                          Example: Development Raffia #1
```
