# model/

Training, evaluation, and ONNX export.

- Isolation Forest baseline (unsupervised, trained on benign traffic only)
- XGBoost classifier (supervised, trained on labeled augmented data)
- `artifacts/` — exported `.onnx` model files (gitignored; regenerate via
  the training scripts, or download from a release asset once available)
