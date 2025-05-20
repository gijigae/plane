```mermaid
graph TD
    subgraph Frontend
        WebApp["WebApp (Next.js web/)"]
        AdminApp["AdminApp (Next.js admin/)"]
    end

    subgraph Backend
        APIServer["APIServer (Django apiserver/)"]
        Hocuspocus["Hocuspocus (Realtime Collaboration live/)"]
        CeleryWorkers["Celery Workers (Background Tasks)"]
        RabbitMQ["RabbitMQ (Message Broker)"]
    end

    subgraph "Data Storage"
        Database["Database (PostgreSQL)"]
        Cache["Cache (Redis)"]
        FileStorage["S3/Minio (File Storage)"]
    end

    SharedPackages["SharedPackages (packages/)"]

    WebApp -- "API Calls (REST/GraphQL)" --> APIServer
    AdminApp -- "API Calls (REST/GraphQL)" --> APIServer
    WebApp -- "Direct File Uploads (Optional)" --> FileStorage
    
    APIServer -- "Stores/Retrieves Data" --> Database
    APIServer -- "Caches Data" --> Cache
    APIServer -- "Manages Files" --> FileStorage
    APIServer -- "Publishes Tasks" --> RabbitMQ
    RabbitMQ -- "Delivers Tasks" --> CeleryWorkers
    CeleryWorkers -- "Process Tasks" --> APIServer
    CeleryWorkers -- "Updates Data (via APIServer or directly)" --> Database
    CeleryWorkers -- "Uses Cache" --> Cache
    CeleryWorkers -- "Accesses/Stores Files" --> FileStorage
    
    APIServer -- "Real-time Sync" --> Hocuspocus
    WebApp -- "Real-time Collaboration" --> Hocuspocus
    
    WebApp -- "Uses Utilities/Types/UI" --> SharedPackages
    AdminApp -- "Uses Utilities/Types/UI" --> SharedPackages
    APIServer -- "Uses Utilities/Types" --> SharedPackages
```
