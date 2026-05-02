# db/

Postgres schema migrations and the Python DB access layer.

- `migrations/` — SQL files run on container init (mounted into
  `docker-entrypoint-initdb.d` by docker-compose)
- `client.py` — connection helper used by other modules
