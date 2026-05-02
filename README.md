# SentinelPress

An inline, ML-driven Web Application Firewall (WAF) for WordPress.

## Why

Signature-based WAFs (regex rules) catch known attack strings but miss novel
or obfuscated variants — multi-pass URL encoding, alternative whitespace,
nested PHP wrappers, etc. SentinelPress instead scores incoming HTTP
requests with a model trained on structural and statistical features
(entropy, token distributions, character ratios), so it can flag payloads
that _look_ anomalous even if they don't match a known signature.

## Architecture

```
[ Incoming Client Request ]
            │
            ▼
   [ NGINX Engine ]
            │
   (inline scoring hook)
            │
            ▼
   [ SentinelPress ] ──► [ ONNX Anomaly Model ]
            │
            ├──◄── [ Decision: Allow / Block ]
            │
     ┌──────┴──────┐
     ▼             ▼
 [ 200 OK ]   [ 403 Forbidden ]
 (WordPress)   (WAF log entry)
```

Two independent pipelines:

```
A. DATA ENGINEERING (offline)
[PoC scripts] → [Docker sandbox] → [Capture listener] → [Postgres]
   → [Feature extraction + adversarial augmentation] → [Model training]

B. SERVING (online)
[Live traffic] → [NGINX] → [Scoring hook] → [ONNX model] → allow/block
```

The serving layer starts as an NGINX `auth_request` → FastAPI/ONNX scorer
(guaranteed to work, a legitimate production pattern on its own), with an
inline OpenResty + LuaJIT FFI path attempted as a stretch goal later in the
build. See PROJECT_PLAN.md for why it's sequenced this way.

## Repo layout

| Folder        | Purpose                                                                             |
| ------------- | ----------------------------------------------------------------------------------- |
| `aggregator/` | Scripts that pull/curate WordPress PoC exploits and replay them against the sandbox |
| `sandbox/`    | Dockerized mock HTTP listener that captures raw traffic into Postgres               |
| `db/`         | Schema migrations and DB access layer                                               |
| `features/`   | Heuristic + TF-IDF feature extraction, adversarial augmentation                     |
| `model/`      | Training, evaluation, ONNX export                                                   |
| `serving/`    | Inference service + NGINX integration                                               |
| `docs/`       | Diagrams, benchmark results, retrospective notes                                    |

## Quickstart

```bash
cp .env.example .env
docker compose up -d
```

(More to come as the pipeline is built — see PROJECT_PLAN.md for the
current stage.)

## Disclaimer

This project handles real exploit PoC code for research/training purposes
only, inside isolated Docker sandboxes. It is not intended to be pointed at
any system you don't own or have explicit authorization to test.
