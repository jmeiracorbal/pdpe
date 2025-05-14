## MongoDB + Mongo Express

```bash
docker compose -f docker-compose.yml -f docker-compose.override.mongodb.yml --profile dev up
```

This stack includes:

- mongo: with authentication using `.env` credentials.
- mongo-express: browser-based DB interface.
- web: PHP + Apache.

Access URLs:

- http://localhost:19874 -> The dashboard (if you use the 19874 port as first).
- http://localhost:8081 -> Mongo Express.

Login: Use `MONGO_EXPRESS_USER` and `MONGO_EXPRESS_PASSWORD` from `.env` file

You can create new override files for other technologies (Redis, PostgreSQL…) following the same pattern.

The stacks must be under php base environment (check the actual version). If your stack achieves with this condition, you can send a pull-request to add to the available stacks.