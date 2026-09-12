
# On Rectangularity in Distributionally Robust Asset Pricing

In this thesis, we reformulate robust superhedging as a robust Markov decision process (MDP) and compare rectangular and non-rectangular prices for a parametric volatility uncertainty model in order to quantify the gap. We assume we don't know the true distribution of the returns of the underlying and thus want to price against a whole set of possible models and charge enough to hedge under the worst one.

The non-dominated framework of Bouchard and Nutz (2015), which we adopt here, allows for ambiguity sets consisting of mutually singular probability measures and is therefore well suited to ambiguity sets defined through statistical distances such as Wasserstein balls.

However, one of the largest challenges in Wasserstein distributionally robust optimization is that computational tractability is typically recovered by imposing rectangularity on the ambiguity set as in Epstein and Schneider (2003) and the robust Markov decision processes (MDP) literature of Iyengar (2005) and Nilim and El Ghaoui (2005). Rectangularity allows robust MDP problems to be solved through dynamic programming, and Wiesemann et al. (2013) showed that without it the problem can be strongly NP-hard.

The numerical results show that the rectangularity gap can be large when uncertainty is coupled across time, meaning that rectangular approximations may substantially overstate the capital required for robust hedging. Overall, we show that rectangularity is not a harmless tractability assumption, but a modeling choice with direct pricing consequences.

We make two main contributions. First, we introduce and analyze the rectangularity gap, defined as the difference between the robust superhedging price under the rectangularized ambiguity set and the price under the original non-rectangular ambiguity set. We show that this gap is nonnegative and relate it to the loss of global consistency constraints across time. We also derive theoretical examples and structural results that identify when the gap can vanish and when it can become economically significant.

## Methods Compared

- **Discretized ambiguity set LP for nonrectangular price**
  For ```math m = 1,\dots, M ``` sigma values in the ambiguity set, solves:
  ```math
  \min_x \quad x
  ```
  
  subject to:
  ```math
  x + \sum_{t=0}^{T-1} h_t(\sigma,\text{history})\,\Delta S_{t+1}
  \ge \zeta
  ```
  
  for all paths ```math (\sigma^m, \omega) ```

- **Dynamic programming solution for rectangular price**
  Terminal condition:

  ```math
  V_T(s) = \mathbf{1}_{\{s \ge K\}}
  ```
  
  Backward step:
  
  ```math
  V_t(s) = \min_a \max_{\sigma \in \{\text{lo, hi}\}, \varepsilon} \left[ V_{t+1}(s + \mu + \sigma \varepsilon) - a(\mu + \sigma \varepsilon) \right]
  ```

- **Actor-critic algorithm for nonrectangular price (Algorithm 4.1 from Li, Kuhn, and Sutter (2026))**

    ```
    Require: K ∈ ℕ, step size η > 0, tolerance ε > 0
    1: Initialize π⁽⁰⁾(a|Z) = 1/|A| for all Z ∈ Z, a ∈ A; set k ← 0
    2: while k ≤ K−1 do
    3:     Critic: find θ⁽ᵏ⁾ ∈ Θ s.t. V_{π⁽ᵏ⁾}^{P^{θ⁽ᵏ⁾}}(Z₀) ≥ V*_{π⁽ᵏ⁾}(Z₀) − ε
    4:     Actor:  π⁽ᵏ⁺¹⁾ ← Proj_Π( π⁽ᵏ⁾ − η ∇_π V_{π⁽ᵏ⁾}^{P^{θ⁽ᵏ⁾}}(Z₀) )
    5:     k ← k + 1
    6: end while
    7: return π⁽ᵏ⁾
    ```
  
## Numerical results


**Digital call `1{S_T ≥ K}`.** The payoff is bounded, so the rectangular price saturates at 1 and the gap stabilizes.

![Digital call: robust superhedging price vs. horizon](Figures/horizon_digital.png)


![Digital call: robust superhedging price vs. half-interval width](Figures/radius_digital.png)

**European call `max(S_T − K, 0)`.** The payoff is unbounded and 1-Lipschitz, so both prices grow with the horizon and the gap widens without saturating.

![European call: robust superhedging price vs. horizon](Figures/horizon_call.png)

![Digital call: robust superhedging price vs. half-interval width](Figures/radius_call.png)

## Repository

```
sigma_interval_digital_call.ipynb    # digital call experiments 
sigma_interval_european_call.ipynb   # European call experiments 
figures/                             # result plots
```

Each notebook builds the path tree, solves the LP benchmark and rectangular DP exactly, runs the actor–critic loop, and reproduces the convergence and horizon/radius sweep figures.

```bash
pip install numpy scipy cvxpy matplotlib jupyter
jupyter lab
```


## References

- Bouchard and Nutz (2015), *Arbitrage and duality in nondominated discrete-time models.*
- Epstein and Schneider (2003), *Recursive multiple-priors.*
- Iyengar (2005), *Robust dynamic programming.*
- Nilim and El Ghaoui (2005), *Robust control of Markov decision processes with uncertain transition matrices.*
- Wiesemann, Kuhn and Rustem (2013), *Robust Markov decision processes.*
- Li, Kuhn, and Sutter (2026) *Policy gradient algorithms for robust MDPs with non-rectangular uncertainty sets.*
