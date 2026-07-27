https://www.udacity.com/

To perform A/B testing in AI, Machine Learning, and Data Science, you need a strong foundation in Probability and Inferential Statistics. You do not need deep calculus, but you must understand how data behaves under uncertainty. [1, 2] 
Here is the exact mathematical roadmap required, broken down by core concepts.
------------------------------
## 1. Descriptive Statistics & Distributions
Before comparing groups, you must understand how data spreads.

* Measures of Central Tendency & Dispersion: Mean (μ), variance (σ²), and standard deviation (σ).
* Probability Density Functions (PDF): Understanding how continuous data spreads out.
* The Normal Distribution: The classic bell curve. Most continuous metrics (like time spent or revenue) follow this shape when sample sizes are large. [3, 4] 
* The Binomial Distribution: Used for categorical conversions (e.g., a user either clicks (1) or does not click (0)).
* Central Limit Theorem (CLT): The foundational math rule stating that if your sample size is large enough (n > 30), the distribution of the sample means will be normal, no matter what the underlying distribution looks like.

------------------------------
## 2. Hypothesis Testing Foundations
This is the core mathematical framework used to prove if your new ML model actually worked.

* Null Hypothesis (H₀): The assumption that your new ML model makes no difference compared to the old one.
* Alternative Hypothesis ($H_a$): The claim you want to prove (e.g., the new ML model increases user engagement).
* Type I Error (α): A false positive. The math says your new ML model is better, but it actually isn't. Usually capped mathematically at 5% (α = 0.05).
* Type II Error (β): A false negative. Failing to realize your new ML model is better. Usually capped at 20% (β = 0.20). [5] 

------------------------------
## 3. Power Analysis (Sample Size Math)
You must mathematically calculate your sample size before starting the test to ensure your data is reliable.

* Statistical Power (1 - β): The probability of correctly rejecting the null hypothesis when it is false (usually targeted at 80%).
* Minimum Detectable Effect (MDE): The smallest change in your metric that you care about detecting (e.g., a 2% lift in revenue).
* Sample Size Formula: For a standard two-tailed continuous test, the required sample size (n) per group uses the math:
$$n \approx \frac{2 \sigma^2 (Z_{1-\alpha/2} + Z_{1-\beta})^2}{\text{MDE}^2}$$ 
(Where Z represents critical values from the standard normal distribution scale). [6] 

------------------------------
## 4. Statistical Significance Tests
Once the data is collected, you apply mathematical tests to check the p-value (the probability that your results happened by pure random luck).

* Two-Sample T-test / Z-test: Used to compare the means of two independent groups for continuous data (e.g., average watch time per user).
* Chi-Square (χ²) Test of Independence: Used to compare categorical conversion proportions between two groups.
* Welch’s T-test: Used when your two groups have unequal sample sizes or unequal variances (highly common in real-world data science). [7] 

------------------------------
## 5. Advanced Data Science Math (Optional but highly valued)
If you want to move beyond traditional frequentist statistics:

* Bayes' Theorem: Used in Bayesian A/B testing to calculate the actual probability that Variant B is better than Variant A, updating your beliefs dynamically as data rolls in. [8] 
* Multi-Armed Bandit Math: Algorithms like Thompson Sampling use probability distributions (like the Beta distribution) to dynamically balance exploration (testing options) and exploitation (driving traffic to the current winner).

------------------------------
Would you like a quick Python example using scipy.stats to see how these equations are calculated programmatically, or would you like to dive deeper into one of these specific mathematical concepts?

[1] [https://www.collegevine.com](https://www.collegevine.com/faq/154724/what-are-the-most-important-math-courses-for-data-science-majors)
[2] [https://data-flair.training](https://data-flair.training/blogs/skills-needed-to-become-a-data-scientist/)
[3] [https://www.open.edu](https://www.open.edu/openlearncreate/mod/oucontent/view.php?id=57313&printable=1)
[4] [https://sathee.iitk.ac.in](https://sathee.iitk.ac.in/sathee-cuet/student-corner/article/general-test/generaltest/numerical-ability-statistics/)
[5] [https://www.vaia.com](https://www.vaia.com/en-us/textbooks/math/statistics-informed-decisions-using-data-5-edition/chapter-10/problem-13-ready-for-college-the-act-is-a-college-entrance-e/)
[6] [https://www.vaia.com](https://www.vaia.com/en-us/textbooks/math/elementary-statistics-11-edition/chapter-8/problem-49-a-high-tech-company-wants-to-estimate-the-mean-nu/)
[7] [https://www.stratascratch.com](https://www.stratascratch.com/blog/a-comprehensive-statistics-cheat-sheet-for-data-science-interviews)
[8] [https://testbook.com](https://testbook.com/maths-formulas/bayes-theorem-formula)
