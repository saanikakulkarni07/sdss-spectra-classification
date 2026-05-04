# 04 — XGBoost Experiments

## 2026-05-03

### Max depth tuning

Increased `max_depth` from 6 to 7 in the XGBoost classifier. Test accuracy went from **97.82%** to **97.88%**.

This makes sense — deeper trees can capture more complex decision boundaries, which helps resolve the trickier QSO/GALAXY overlap cases. The improvement is small (~0.06%) which suggests we're near the point of diminishing returns. Going much deeper risks overfitting (the model memorizes training noise instead of learning real patterns).

At `max_depth=8`, accuracy drops back to **97.83%** — worse than depth=7 and roughly back to depth=6 territory. This confirms depth=7 is the sweet spot: deep enough to capture the hard boundary cases, shallow enough to avoid overfitting to training noise.

**Takeaway:** `max_depth=7` is optimal for this dataset. The fact that depth=8 *hurts* means the model starts fitting noise rather than signal — classic bias-variance tradeoff in action.

---

### What is the bias-variance tradeoff?

Every model's prediction error can be decomposed into three parts:

**Error = Bias² + Variance + Irreducible Noise**

- **Bias** — error from oversimplifying. A too-simple model (e.g., shallow trees) misses real patterns. It *underfits*: consistently wrong in the same direction.
- **Variance** — error from being too sensitive to the training data. A too-complex model (e.g., very deep trees) memorizes noise and performs differently on new data. It *overfits*: great on training data, worse on test data.
- **Irreducible noise** — inherent randomness in the data you can't fix (e.g., QSOs that genuinely look like galaxies in photometry alone).

The tradeoff: as you increase model complexity (deeper trees, more parameters), bias goes down but variance goes up. There's a sweet spot in the middle where total error is minimized.

**What we saw:**
- `max_depth=6` — slightly too simple (higher bias), misses some boundary cases → 97.82%
- `max_depth=7` — just right, captures real patterns without memorizing noise → 97.88%
- `max_depth=8` — too complex (higher variance), starts fitting noise → 97.83%

This is why you always validate on held-out data — training accuracy will *always* improve with more depth, but test accuracy reveals when you've crossed into overfitting territory.
