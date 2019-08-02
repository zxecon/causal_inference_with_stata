### Propensity Score Matching

In the statistical analysis of observational data, propensity score matching (PSM) is a statistical matching technique that attempts to estimate the effect of a treatment, policy, or other intervention by accounting for the covariates that predict receiving the treatment. PSM attempts to reduce the bias due to confounding variables that could be found in an estimate of the treatment effect obtained from simply comparing outcomes among units that received the treatment versus those that did not. Paul Rosenbaum and Donald Rubin introduced the technique in 1983.

#### Coding in Stata and Explaination
The codes here is written in Stata and following these procedures: calculating propensiy score; matching; balance test; estimating treatment effect; further analysis.

Here, we first need to see how to calculate the propensity sscore for each observation. In Stata, there are two ways to estimate: probit and logit model. 
```stata
probit [dependent var] [independent var]
```
where the denpendent variable is a binary variable that euqals one if the observations is treated and zero otherwise; the independent vars nests the control variables for the chracteristics of the observations. For instance, if we want to evaluate the effect of a self-opt program in high school on test outcomes, the dependt var equals one for all who opt in this program, and we may want to add factors that could affect their decisions (treatment) in the independent vars(family income level, parental eduation, numbers of siblings, health conditions etc.). Next, based on the probability we have calculated, we can get the pscore:
```stata
predict pscore, p
```

Next is the matching process. The command in Stats used for psm is psmatch2 and I have put the commance documentation in the referecnens.
```stata
ssc install psmatch2, replace
psmatch2 d, pscore(pscore) noreplacement
```
This provides a 1:1 match for your sample. Further more, you can see how your data becomes after the matching process:
```stata
*before matching
#delimit
twoway (kdensity _pscore if _treated==1) (kdensity _pscore if _treated==0, lpattern(dash)), 
legend( label( 1 "treated") label(  2 "control" ) ) xtitle("propensity score");
#delimit cr
*after matching
gen match=_n1 
replace match=_id if match==. 
duplicates tag match, gen(dup) 
#delimit
twoway (kdensity _pscore if _treated==1) (kdensity _pscore if _treated==0 & dup>0, lpattern(dash)),
legend( label( 1 "treated") label( 2 "control" ) ) xtitle("propensity score");
#delimit cr
```

Next step is to test the balance of the matched sample and analyze the averge treament effects:
``` stata
pstest [independent var], both
psmatch2 [dependent var], pscore(pscore) outcome([outcome var]) 
```
It provides detailed outcomes for average treatment effect on your target outcome variables.

Last but not least, sometimes researcher do further analysis by combing difference-in-differences with propensity score match. The coding is to conduct the DID process in the matched sample. 

References: [The Central Role of the Propensity Score in Observational Studies for Causal Effects](https://www.jstor.org/stable/2335942?seq=1#metadata_info_tab_contents);
[psm](https://en.wikipedia.org/wiki/Propensity_score_matching), 
[psmatch2](http://repec.org/bocode/p/psmatch2.html), 
[倾向评分匹配](https://baike.baidu.com/item/%E5%80%BE%E5%90%91%E8%AF%84%E5%88%86%E5%8C%B9%E9%85%8D)

