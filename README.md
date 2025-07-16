# SeniorEngineer_Opinov8

Legacy Platform Review & Proposed Enhancement

 Some technical limitations of a monolithic architecture are:
    - Hard to scale parts independently making it a less than flexible environment and less future prone in case there's a need to add/decouple features
    - It's harder to maintain in the sense that the frontend and backend are tightly integrated which means a change in one will require changes to the other. This can be time consuming and costly.

Technical enhancement
    - One could be a security uplift, where we could create a different authentication feature. Usually monoliths use a session based authentication which is tightly coupled with the app. Creating an auth based API using JWT for example, allows it the app to call it instead of authenticating directly. The design of this API implements secure token standards, expiration and scopes and allows to use it in a different and more modern architecture like API-first or microservices.