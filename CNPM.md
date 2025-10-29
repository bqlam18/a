sequenceDiagram
  %% Optional: autonumber to number each message
  autonumber

  %% Define participants/actors
  actor User
  participant Web as Web App
  participant API
  participant DB

  %% Message flow
  User->>Web: Clicks "Sign in"
  activate Web
  Web->>API: POST /login (email, password)
  activate API
  API->>DB: Validate credentials
  DB-->>API: User record
  API-->>Web: 200 OK (session)
  deactivate API
  Web-->>User: Redirect to dashboard
  deactivate Web
