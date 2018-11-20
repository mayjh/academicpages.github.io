---
title: 'Unboxing the machine learning black box in healthcare applications'
date: 2018-11-20
permalink: /posts/2018/11/interpretable-machine-learning/
tags:
  - interpretable machine learning
  - local interpretable model-agnostic explanations
  - Shapley
---

Machine learning models are being quickly adopted in various industry applications because of their outstanding performance compared to traditional models such as linear regression. However, the black-box nature of many machine learning models casts doubts on their usability in practice, especially in the healthcare field. As noted by Lipton (2017), “the doctor just won't accept that!” When health or even life is at stake, it is hard to trust a black-box algorithm that does not provide sufficient explanations for its decisions.

Because of the call for interpretations, there is a surge of interest in interpretable machine learning, a field which attempts to unbox the machine learning black box. The goal of such effort is not to understand detailed inner workings of the models--the feasibility of which is under debate, but to understand them enough for downstream tasks (Doshi-Velez & Kim, 2017a, 2017b).

The benefits of interpretable machine learning are multi-folds (Doshi-Velez & Kim, 2017a, 2017b; Du, Liu, & Hu, 2018; Molnar, 2018). For developers and researchers of the models, the working mechanisms help them to improve the model in cases of unexpected failure, to ward against potential discriminations in the algorithms (Dastin, 2018), and to increase system safety. For end-users such as doctors, it can reduce the discomfort with machine learning and establish trust. Also the explanations can be used as basis for actionable recommendations.

There are different techniques to interpret the models. We can build interpretable models by choosing simple algorithms such as linear regression or enforcing interpretability constraints in the modeling process (e.g., Freitas, 2014). A pitfall of such techniques is that they might compromise the model performance. To retain model performance, we can interpret the models in a post-hoc fashion. For example, using local interpretable model-agnostic explanations (LIME), we can fit a local surrogate model that is easy to interpret (Ribeiro, Singh, & Guestrin, 2016). Another tool, the Shapley value, determines the contribution of each feature to a specific prediction based on game theory (Lundberg & Lee, 2017; Štrumbelj & Kononenko, 2014).

These techniques have been actively developed and maintained in various software packages. For example, in Python, there are tools such as skater (Kramer, Choudhary, DataScience.com Team, & contributors, 2018) and lime (Ribeiro, 2018). In R, there are packages such as lime (Pedersen & Benesty, 2018), DALEX (Biecek, 2018), and iml (Molnar, Casalicchio, & Bischl, 2018).


References
========

Biecek P. (2018). DALEX: Descriptive mAchine Learning EXplanations. R package version 0.2.4. Retrieved from https://cran.r-project.org/web/packages/DALEX/index.html
Dastin, J. (2018, October 9). Amazon scraps secret AI recruiting tool that showed bias against women. Reuters, p. 1. Retrieved from https://www.reuters.com
Doshi-Velez, F., & Kim, B. (2017a August). Interpretable machine learning. Talk presented at the International Conference on Machine Learning, Sydney, Australia.
Doshi-Velez, F., & Kim, B. (2017b). Towards a rigorous science of interpretable machine learning. arXiv preprint arXiv:1702.08608.
Du, M., Liu, N., & Hu, X. (2018). Techniques for Interpretable Machine Learning. arXiv preprint arXiv:1808.00033.
Freitas, A. A. (2014). Comprehensible classification models: a position paper. ACM SIGKDD explorations newsletter, 15(1), 1-10.
Kramer A., Choudhary P., DataScience.com Team, & contributors. (2018). skater. Python package version 1.1.2. Retrieved from https://pypi.org/project/skater/
Lipton, Z. C. (2017). The Doctor Just Won't Accept That!. arXiv preprint arXiv:1711.08037.
Lundberg, S. M., & Lee, S. I. (2017). A unified approach to interpreting model predictions. In Advances in Neural Information Processing Systems (pp. 4765-4774).
Molnar, C. (2018). Interpretable Machine Learning. A Guide for Making Black Box Models Explainable.
Molnar, C., Casalicchio, G., & Bischl, B. (2018). iml: An R package for Interpretable Machine Learning. The Journal of Open Source Software, 3, 786.
Pedersen T. L. & Benesty M. (2018) lime: Local Interpretable Model-Agnostic Explanations. R package version 0.4.0. Retrieved from ttps://cran.r-project.org/web/packages/lime/index.html
Ribeiro, M. T. (2018) lime. Python package version 0.1.1.32. Retrieved from https://pypi.org/project/lime/
Ribeiro, M. T., Singh, S., & Guestrin, C. (2016, August). Why should i trust you?: Explaining the predictions of any classifier. In Proceedings of the 22nd ACM SIGKDD international conference on knowledge discovery and data mining (pp. 1135-1144). ACM.
Štrumbelj, E., & Kononenko, I. (2014). Explaining prediction models and individual predictions with feature contributions. Knowledge and information systems, 41(3), 647-665.
