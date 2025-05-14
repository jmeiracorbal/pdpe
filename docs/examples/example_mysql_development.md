**MySQL + phpMyAdmin**

```bash
docker compose -f docker-compose.yml -f docker-compose.override.mysql.yml --profile dev up
```

This stack includes:

- mysql: preconfigured with credentials from `.env`.
- phpmyadmin: available only if profile dev is specified.
- web: main PHP + Apache environment.

Access URLs:

- http://localhost:19874 -> The dashboard (if you use the 19874 port as first).
- http://localhost:8080 -> phpMyAdmin UI.