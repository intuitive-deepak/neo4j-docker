graph TD;
    User["User"] -->|Login with Username & Password| AuthAPI["FastAPI Auth (/token)"];
    AuthAPI -->|JWT Token Issued| User;
    User -->|Send Authenticated API Request (Bearer Token)| API["FastAPI Protected API"];

    API -->|Verify JWT| AuthMiddleware["JWT Middleware"];
    AuthMiddleware -->|Allow Request| TaskQueue["Send Task to Celery Queue"];
    
    TaskQueue --> RedisBroker["Redis (Message Broker)"];
    RedisBroker --> CeleryWorker["Celery Worker"];
    CeleryWorker --> RedisResults["Store Results in Redis"];

    User -->|Check Task Status| APIResults["API Fetches Results"];
    APIResults --> RedisResults;
    RedisResults --> User["Return Task Result"];

    subgraph Kubernetes Deployment
      API;
      CeleryWorker;
      RedisBroker;
    end

