# SeniorEngineer_Opinov8

2 - Legacy Platform Review & Proposed Enhancement

 Some technical limitations of a monolithic architecture are:
    - Hard to scale parts independently making it a less than flexible environment and less future prone in case there's a need to add/decouple features
    - It's harder to maintain in the sense that the frontend and backend are tightly integrated which means a change in one will require changes to the other. This can be time consuming and costly.

Technical enhancement
    - One could be a security uplift, where we could create a different authentication feature. Usually monoliths use a session based authentication which is tightly coupled with the app. Creating an auth based API using JWT for example, allows it the app to call it instead of authenticating directly. The design of this API implements secure token standards, expiration and scopes and allows to use it in a different and more modern architecture like API-first or microservices.

3 - Future-State Architecture 
    I would recommend a API-FIrst approach for a new solution architecture.
        -  The advantages of it is that the application could use diferent services throughout, allowing for more flexibility in what to add/decouple from the app. This provides better maintainability, flexibility and enables for a more agile approach amking it quicker to adapt to new changes in the market
        - CI/CD to keep track of changes, allowing for quick fixes and more frequent releases.
        - Observability to monitor performance, errors and incidents that occur. This provides a better response to problems that may arise and diagnose performance issues 