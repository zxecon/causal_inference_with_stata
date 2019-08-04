## Matching Methods Summary

>Since the 1970’s, work on matching methods has examined how to best choose treated and control subjects for comparison. Matching methods are gaining popularity in fields such as economics, epidemiology, medicine, and political science. 
--- Stuart (2010)

For causal inferences, matching methods have become more an more attracting aiming to reduce the imbalance of the treated and control groups. The basic idea in matching methods is to look for one or more observations in the control group for threated observations, based on a set of controls variables. While controlling for the changing factors, we can estimate the average treatment effects (ATE) and ATE on treated (ATT) directly. Generally speaking, the most widely used matching method is the propensity score matching, which is proposed by Paul Rosenbaum and Donald Rubin in 1983. To improve the accuracy of matching process, a variety of methods were developed from basic one to one match based on the propensity score, to matching with the nearest neighbor, radius and kernel, to the most recent machine learning matching methods like lasso, support vector machine. Each method has its pros and cons. 

### 1. Propensity Score Matching (PSM)
- One to One Matching (1:1)

  The One to One match is the basic and mostly used method in the PSM. The mechanism is first to calculate the probability of the observations to be treated based on a set of basic characteristics. Then, we can calculate a propensity score (pscore) based on this probability. In the final step, we match one treated observation with one in the control group by comparing their pscores. Thus, we can build up a new control group to mitigate the imbalance and generate the average treated effects.

- k-Nearest Neighbors Matching (kNN)

  k-nearest neighbors algorithm (k-NN) is a non-parametric method used for classification and regression. Nearest neighbor matching can be carried out on most statistics software through a simple command(Caliendo and Kopeinig(2005)). This is generally the most effective method for settings where the goal is to select individuals for follow-up(Stuart(2010)). Its most simple form is that the matching process selects one control observation for each treated individual with the smallest distance from the treated individual. Criticisms for this simple form are that it discards observations in the matching process and could lead to reduced power and increasing standard errors. The alternative way is the k-Nearest Neighbors Matching, which matches each treated unit to the k control units that are most similar, then average or weight over the potential outcomes of the control units. The matching process can be expressed as follows:

  $$
  S(P_{i}) = min_{j} ||P_{i} - P_{j}|| \quad i,j\in I_{0}
  $$

  where, $P_{i}$ and $P_{j}$ are the selected treeated and control observations respectively and $I_{0}$ is the dataset. The matching process can be either with replacement or non-replacement. However, the drawback of non-replacement is that the final estimate will depend on the initial ordering of the treated observations, which will lead to bias for observations with a lower rank. Thus, in my estimation, I use nearest neighbors matching with replacement.


- Radius Matching (RM)
  
   kNN matching faces the risk of inappropriate matches if the closest neighbor is too far away from the treated observation. To avoid this risk, we can impose a tolerance level on the maximum propensity score distance (caliper) as follows:
  
  $$
  ||P_{i} - P_{j}|| < \epsilon \quad i,j\in I_{0}
  $$

  Where $\epsilon$ is prespecified tolerance. Imposing a caliper works in the same direction as allowing for replacement. Bad matches are avoided, and hence the matching quality rises. However, if fewer matches can be performed, the variance of the estimates increases. Applying caliper matching means that those individuals from the comparison group is chosen as a matching partner for a treated individual that lies within the caliper (‘propensity range’) and is closest in terms of the propensity score. The possible drawback of this method is that it is difficult to know a priori what choice for the tolerance level is reasonable(Smith and Todd (2005)). It is the tradeoff between accuracy (small $\epsilon$) and generality (large $\epsilon$).  

  Dehejia and Wahba (2002) provide a variant of caliper matching, which is called radius matching. The basic idea of this variant is to use not only the nearest neighbor within each caliper but all of the comparison members within the caliper. A benefit of this approach is that it uses only as many comparison units as are available within the caliper and therefore allows for usage of extra (fewer) units when suitable matches are (not) available. Hence, it shares the attractive feature of oversampling mentioned above but avoids the risk of bad matches.

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

  King & Nielsen(2016) argues that accomplishes the opposite of its intended goal --- thus increasing imbalance, inefficiency, model dependence, and bias. Thus, an alternative way of matching - Mahalanobis distance matching was proposed. Mahalanobis matching bases on a distance (Mahalanobis distance) metric that measures the proximity between observations in the multivariate space of X. Statistian P.C.mahalanobis first proposed it in 1936. The idea is to use observations that are “close” in the distance, but not necessarily equal, as matches. The Mahalanobis distance can be measured as the following metric:

$$
    MD(X_{i},X_{j}) = \sqrt{(X_{i}-X_{j})^{'} \Sigma^{-1} (X_{i}-X_{j})}
$$

where $X_{i}$ ,$X_{j}$ are vectors of covariates from each observation, $\Sigma$ is the covariance matrix of X. We want to minimize the Mahalanobis distance. The more similar that two observations are, the smaller Mahalanobis distance will be, which means that the distributions of covariates are balanced and we can get better matching results.

---
References:

Frye Bobbie, James Bartlett, 2017.“Performing The PSM Analysis: An Applied Example.” Journal of Applied Research in the Community College, vol. 24, no. 1, p. 43;

Marco Caliendo, Sabine Kopeinig, 2005. "Some Practical Guidance for the Implementation of Propensity Score Matching", IZA Discussion Paper No. 1588;

Prasanta Chandra Mahalanobis, 1927. “*Analysis of race mixture in Bengal”, Journal and Proceedings of the Asiatic Society of Bengal, 23:301–333;

Martin Huber, Michael Lechner, Andreas Steinmayr, 2012. "Radius matching on the propensity score with bias adjustment: finite sample behaviour, tuning parameters and software implementation", Discussion Paper no. 2012-26;

Gary King, Richard Nielsen. 2019. "Why Propensity Scores Should Not Be Used for Matching.” Political Analysis;

Paul R. Rosenbaum and Donald B. Rubin, 1983. "The Central Role of the Propensity Score in Observational Studies for Causal Effects", Biometrika 70, 1, 41-55;


Paul R. Rosenbaum and Donald B. Rubin, 1985. "Constructing a Control Group Using Multivariate Matched Sampling Methods that Incorporate the Propensity Score", The American Statistician 39(1), 33-38;


Donald B. Rubin, 1974. "Estimating Causal Effects of Treatments in Randomised and Non-Randomised Studies", Journal of Educational Psychology 66, 688-701;


Donald B. Rubin, 1980. "Bias Reduction Using Mahalanobis-Metric Matching", Biometrics 36, 293-298;

Elizabeth A. Stuart, 2010."Matching methods for causal inference: A review and a look forward", Stat Sci., 25(1): 1–21.



