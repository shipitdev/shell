# sandbox/

Dockerized "catch-all" mock HTTP listener. Accepts any method/URI/payload,
captures the raw request, and writes it into the `raw_traffic` table.
Used as the replay target for both malicious PoCs and benign traffic
generators.
