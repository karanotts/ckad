# Health Probes

```readinessProbe:``` - "Are you ready yet?" - checks if newly started app is ready to accept incoming requests.

```livenessProbe:``` - "Do you still work?" - checks if a running app is still accepting incoming requests.

```startupProbe:``` - "Are you started? Can I start readiness probe?" - checks if app started (applies particularly to legacy apps that may take a long time to start up) before firing up readiness probe


- HTTP GET: - sends a HTTP GET request to endpoint exposed by app, looking for 200-299 response code indicating "OK". Other codes indicate failure.
```
    httpGet:
      path: /api/status
      port: 8080
```

- TCP Socket: - tries to open connection to a port, if connection can be established it's a success, otherwise it's a failure.
```
    tcpSocket:
      port: 3306
```

- EXEC: - executes a custom command inside the container and checks the exit code for success or failure. 
```
    exec:
      command:
        - cat
        - /app/status
```

Additional settings:

```initialDelaySeconds: 10``` - deyal in seconds before first check is executed

```periodSeconds: 5``` - interval between checks

```failureThreshold: 5``` - number of failures before probe is marked as ultimately failed

```successThreshold: 3``` - number of successes before probe is marked as ultimately succeeded