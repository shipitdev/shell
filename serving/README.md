# serving/

Inference layer. Two stages:

1. FastAPI service loading the ONNX model via `onnxruntime`, exposing
   `/score`, wired into NGINX via `auth_request` — the guaranteed-working
   "inline WAF" architecture.
2. (Stretch goal) OpenResty + LuaJIT FFI direct embedding of the ONNX
   Runtime C API for lower-latency inline scoring, skipping the subrequest
   hop entirely.
