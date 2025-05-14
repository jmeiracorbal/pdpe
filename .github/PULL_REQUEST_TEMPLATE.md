# Pull request – [Title of your change]

## Summary

Please briefly explain what this PR does. Explain broadly, and avoid vague descriptions.

>Add PostgreSQL support as an optional development stack using `docker-compose.override.postgres.yml`.

## Checklist

Make sure you have done the following before submitting;

- [ ] You have tested the code locally.
- [ ] You updated the `.env.example` file (if applicable).
- [ ] You updated the documentation (README, examples, etc.).
- [ ] The change is backwards compatible.
- [ ] The PR only addresses one issue or feature.

## Related issues references

If this PR is related with a existant issue:

Closes #[issue_number]
See also #[other_issues or links]

## Testing instructions

Provide clear steps test the PR:

Add a new stack with an override to use `postgres.yml`:

```bash
docker compose -f docker-compose.yml -f etc/docker/stacks/development/docker-compose.override.postgres.yml --profile dev up -d
```

## Files changed or added 

Specify relevant files/folders changed by the PR. 

- etc/docker/stacks/development `docker-compose.override.postgres.yml`.
- docs/examples/example_postgres_development.md 
- .env.example: include the postgres variables. 

## Review notes 

Include any background information that might assist to the reviewer grasp your justification, constraints or identified problems. 

Thanks for your help!