## Matching Methods Summary

For causal inferences, matching methods have become more and more attracting aiming to reduce the imbalance of the treated and control groups. The basic idea in matching methods is to look for one or more observations in the control group for treated observations, based on a set of controls variables. While controlling for the changing factors, we can estimate the average treatment effects (ATE) and ATE on treated (ATT) directly. Generally speaking, the most widely used matching method is the propensity score matching, which is proposed Paul Rosenbaum and Donald Rubin in 1983. To improve the accuracy of matching process, a variety of methods were developed from basic one to one match based on propensity score, to matching with nearest neighbor, radius and kernel, to the most recent machine learning matching methods like lasso, support vector matching. Each method has its own pros and cons. 

### 1. Propensity Score Matching (PSM)
- One to One Matching (1:1)

  The One to One match is the basic and mostly used method in the PSM. The mechnism is to first calculate the probability of the observations to be treated based on a set of basic chracteristics. Then, we can calculate a propensity socre (pscore) based on this probability. In the final step, we match one treated observation with one in the control group by comparing therir pscores. Thus, we can build up a new control group to mitigate the imbanlance and generate the average treated effects.

- k-Nearest Neighbors Matching (kNN)

  k-nearest neighbors algorithm (k-NN) is a non-parametric method used for classification and regression. Nearest neighbor matching can be carried out on most statistics software through a simple command(Caliendo and Kopeinig(2005)). It can use either a “greedy” algorithm, which goes through the potential matches and selects the closest unmatched option to match each time, or a more complicated, more sophisticated “optimal matching” which, through some involved calculations, minimizes global balance over all matches.

  $$
  S(P_{i}) = min_{j} ||P_{i} - P_{j}|| \quad i,j\in I_{0}
  $$

  where, $P_{i}$ and $P_{j}$ are the selected treeated and control observations respectively and $I_{0}$ is the dataset. The matching process can be wither with replacement or non-replacement. However, the drawback of non-replacement is that finalestimatewillusually depend on the initial ordering of the treated observations, which will lead to bias for observations with lower rank.

- Radius Matching (RM)
  
  kNN matching faces the risk of inappropriate matches, if the closest neighbour is too far away from the treated observation. To avoid this risk, we can impose a tolerance level on the maximum propensity score distance (caliper) as follows:
  
  $$
  ||P_{i} - P_{j}|| < \epsilon \quad i,j\in I_{0}
  $$

  where $\epsilon$ is the prespecified tolerance. Imposing a caliper works in the same direction as allowing for replacement. Bad matches are avoided and hence the matching quality rises. However, if fewer matches can be performed, the variance of the estimates increases. Applying caliper matching means that those individual from the comparison group is chosen as a matching partner for a treated individual that lies within the caliper (‘propensity range’) and is closest in terms of propensity score. Possible draw back of this method is that it is difficult to know a priori what choice for the tolerance level is reasonable(Smith and Todd (2005)). It is the tradeoff between accuracy (small $\epsilon$) and generality (large $\epsilon$). 

  Dehejia and Wahba (2002) provids a variant of caliper matching which is called radius matching. The basic idea of this variant is to use not only the nearest neighbour within each caliper but all of the comparison members within the caliper. A benefit of this approach is that it uses only as many comparison units as are available within the caliper and therefore allows for usage of extra (fewer) units when good matches are (not) available. Hence, it shares the attractive feature of oversampling mentioned above, but avoids the risk of bad matches.

The matching algorithms discussed above have in common that only a few observations from the comparison group are utilized in the process of  constructing the counterfactual outcome of a treated individual. Kernel matching (KM) and local linear matching (LLM) are non-parametric matching estimators that use weighted averages of all individuals in the control group to construct the counterfactual outcome. 

- Kernel Matching (KM) and Local Linear Regression Matching (LLR)
  
  Kernel matching Uses a weighted average of all observations within the common support region: the farther away the comparison unit is from the treated unit the lower the weight.
  
  The kernel estomator for $E(Y_{0i}|P(X_{i},D_{i}=0))$ is given by the following:

  $$
    \hat{E}(Y_{0i}|P(X_{i},D_{i}=0)) = \Sigma^{n_{0}}_{j=1,{D_{j}=0}} W_{j}(P(X_{i}))Y_{0j}
  $$

  where W is the weighting metric with kernel function (K) and bandwidth (h):

  $$
    W_{j}(P(X_{i})) = \frac{K(\frac{P(X_{i}-P(X_{k})}{h_{n}})}{ \Sigma^{n_{0}}_{k=1,{D_{k}=0}} K(\frac{P(X_{i}-P(X_{k})}{h_{n}})}
  $$

    Local linear is similar to the kernel estimator, but it includes a linear term in the weighting function, which helps to avoid bias.

### 2. Mahalanobis Matching (MD)

  King & Nielsen(2016) argues that accomplishes the opposite of its intended goal --- thus increasing imbalance, inefficiency, model dependence, and bias. Thus, an alternative way of matching - mahalanobis distance matching was proposed. Mahalanobis matching bases on a distance (mahalanobis distance) metric that measures the proximity between observations in the multivariate space of X. It was first proposed by Statistian P.C.mahalanobis in 1936. The idea is to use observations that are “close” in distance, but not necessarily equal, as matches. The mahalanobis distance can be measured as the following metric:

$$
    MD(X_{i},X_{j}) = \sqrt{(X_{i}-X_{j})^{'} \Sigma^{-1} (X_{i}-X_{j})}
$$

where $X_{i}$ ,$X_{j}$ are vectors of covariates from each observation, $\Sigma$ is the covariance matrix of X. We want to minimize the mahalanobis distance. The more similar that two observations are, the smaller mahalanobis distance will be, which means that the distributions of covariates are balanced and we can get better mataching results.

---
References:

Marco Caliendo, Sabine Kopeinig, 2005. "Some Practical Guidance for the Implementation of Propensity Score Matching", IZA Discussion Paper No. 1588;

Martin Huber, Michael Lechner, Andreas Steinmayr, 2012. "Radius matching on the propensity score with bias adjustment: finite sample behaviour, tuning parameters and software implementation", Discussion Paper no. 2012-26

Gary King, Richard Nielsen. 2019. "Why Propensity Scores Should Not Be Used for Matching.” Political Analysis.



