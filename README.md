
flowchart LR
  %% Client side
  subgraph CLIENT["Client / Browser"]
    direction TB
    User[User (Doctor / Nurse / Admin / Reception)]
    SPA[SPA (HTML, TailwindCSS, FontAwesome, Chart.js)]
    LocalCache[(localStorage / IndexedDB)]
    User --> SPA
    SPA --> LocalCache
  end

  %% Edge / Networking
  SPA -->|HTTPS / REST / GraphQL| APIGW[API Gateway / Reverse Proxy]
  SPA -->|Static assets| CDN[CDN / Static Hosting]

  %% Backend services
  subgraph BACKEND["Backend Services"]
    direction LR
    Auth[Auth Service\n(JWT / Sessions)]
    Patient[Patient Service]
    Inventory[Inventory Service]
    Visits[Visits Service]
    Employee[Employee Service]
    Reporting[Reporting & Analytics Service]
    Worker[Background Workers\n(Exports, Notifications, ETL)]
  end

  APIGW --> Auth
  APIGW --> Patient
  APIGW --> Inventory
  APIGW --> Visits
  APIGW --> Employee
  APIGW --> Reporting
  APIGW --> Worker

  %% Data plane
  subgraph DATA["Data & Storage"]
    direction TB
    DB[(Primary DB\nPostgres / MySQL)]
    Obj[(Object Storage\nS3 / MinIO)]
    Cache[(Redis / Memcached)]
    Archive[(Cold Storage / Backups)]
  end

  Auth --> DB
  Patient --> DB
  Inventory --> DB
  Visits --> DB
  Employee --> DB
  Reporting --> DB
  Worker --> DB
  Worker --> Obj
  Reporting --> Cache
  APIGW --> Cache

  %% Integrations
  subgraph INTEGRATIONS["Third-party Integrations"]
    Email[Email / SMTP]
    SMS[SMS Gateway]
    Billing[Payment / Billing]
    Lab[Lab Systems / HL7]
  end

  Worker --> Email
  Worker --> SMS
  Billing --> APIGW
  Lab --> APIGW

  %% Infra & Ops
  subgraph OPS["Operations"]
    CI[CI / CD (GitHub Actions, GitLab CI)]
    Monitoring[Monitoring & Logging\n(Prometheus, Grafana, ELK)]
    Secrets[Secrets Manager / Vault]
  end

  CI --> CDN
  CI --> BACKEND
  BACKEND --> Monitoring
  APIGW --> Monitoring
  BACKEND --> Secrets

  %% Notes / labels
  classDef infra fill:#f8f9fa,stroke:#ddd;
  class CDN,APIGW,DB,Obj,Cache,CI,Monitoring,Secrets infra;
```

Key points
- Frontend: Single Page App built with HTML/CSS (Tailwind), FontAwesome and Chart.js for charts. Can be hosted on a CDN (Vercel/Netlify/S3+CloudFront).
- API: API Gateway (REST or GraphQL) routes to microservices (Auth, Patient, Inventory, Visits, Employee, Reporting).
- Data: Primary relational DB for transactional data, object storage for exports/attachments, Redis for caching, scheduled backups to cold storage.
- Background processing: Workers handle long-running tasks (CSV/Excel exports, notifications, integrations).
- Integrations: Email/SMS gateways, external lab/billing systems.
- Ops: CI/CD pipelines deploy frontend and backend, monitoring and secret management for production safety.

You can paste this file into the repository as ARCHITECTURE.md. If you'd like, I can also:
- Convert this to PlantUML,
- Generate a PNG/SVG export of the diagram,
- Create a README section that references this diagram.
