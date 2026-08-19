# LLM05:2026 Data and Model Poisoning

Data and model poisoning is the manipulation of training data, fine-tuning data, or model
parameters to introduce vulnerabilities, backdoors, or biases, corrupting the model's behavior,
security, or accuracy without necessarily being detected by standard evaluation metrics.

Both notebooks here demonstrate the underlying mechanics on classical scikit-learn classifiers
(logistic regression over synthetic 2D/3D blobs) rather than an actual LLM fine-tuning run. The
point is to make the boundary-shifting effect of poisoned training data visible and measurable, the same principle applies whether the model being retrained is a linear classifier or a neural
network trained on text.

## Notebooks

### `Label_Flipping_Attack.ipynb`

Corrupts training **labels only**; features are left untouched. Two variants:

- **Random flipping** (`flip_labels`): picks a `poison_percentage` fraction of *all* training
  indices via a seeded RNG and inverts each selected label. Sweeps 0% → 50% and plots accuracy and
  decision-boundary drift at each rate.
- **Targeted flipping** (`targeted_flip_labels`): restricts the index pool to one chosen class
  first, then flips a fraction of just that subset to a different class. Meant to bias the model
  against one class specifically rather than degrading it broadly.

### `Clean_Label_Attack.ipynb`

Corrupts training **features only**; the labels of the poisoned points are never touched (hence
"clean label"). Goal: force one specific target point to be misclassified after retraining,
without relabeling anything.

1. Trains a baseline `OneVsRestClassifier(LogisticRegression)` on a synthetic 3-class dataset.
2. Picks an attack target: the correctly-classified point closest to the decision boundary between
   its own class and a neighboring class, using the score-difference function
   `f(x) = (w_a - w_b)·x + (b_a - b_b)`.
3. Finds the `n_neighbors` nearest points belonging to the *other* class and nudges each one by a
   fixed step `epsilon_cross` along the unit-normalized vector between the two classes' weight
   vectors, pushing them toward the target's class in feature space while keeping their original
   label.
4. Retrains on the perturbed data. The mismatch between each perturbed point's (shifted) features
   and its (unchanged) label is what drags the boundary over the target during retraining.

## Running

Dependencies (no `requirements.txt` in this repo, install directly):

```
pip install numpy matplotlib scikit-learn
```

Then open either notebook with Jupyter/JupyterLab and run all cells top to bottom. Both are
self-contained and regenerate their own synthetic data with a fixed seed (`SEED = 1337`).

## Gotchas

- **Label flipping isn't a gradual slope.** In the random-flip sweep, test accuracy stays flat
  (~0.99) all the way from 0% to 40% flipped, then collapses to ~0.48 at 50%. On linearly
  separable data, logistic regression is largely indifferent to label noise until the flip rate
  approaches the point where "majority label per region" itself flips. Accuracy alone can hide a
  poisoning attack right up until a cliff edge.
- **The targeted-flip cell only prints overall accuracy**, not a per-class breakdown
  (`classification_report`). The whole point of the targeted variant is that it hurts one class's
  recall while leaving aggregate accuracy comparatively intact, but the notebook as written won't
  show you that unless you add the per-class metric yourself; reading only the printed accuracy
  line understates the damage.
- **Clean-label attack strength is hand-tuned** via `n_neighbors` (how many points get perturbed)
  and `epsilon_cross` (how far each is pushed). Too small and the target's prediction won't flip;
  too large and the perturbed points become visible outliers and/or overall accuracy drops
  noticeably. The demo values (5 neighbors, `epsilon_cross=0.25`) are tuned to flip the target
  while moving overall test accuracy by only ~0.002.
- **Exact printed values are seed/version-dependent.** The specific target point index and
  accuracy figures quoted in each notebook's "Results" cell come from one run with `SEED = 1337`;
  re-running with a different scikit-learn/numpy version can shift `make_blobs` output enough to
  change the exact index or numbers, even though the qualitative effect (target flips, accuracy
  barely moves) should reproduce.
