# Session-level traffic statistics and models

This page contains the coefficients obtained on the work ***Characterizing and Modeling Session-Level Mobile Traffic Demands from Large-Scale Measurements***, presented at ACM IMC 2023. We also present a example on how to generate samples. We present models for 30 different services observed at the production network of a large mobile operator in France, with all values representing transport layer session-level statitics. 

DOI link: https://doi.org/10.1145/3618257.3624825

<img src="images/paper_front.png" width="50%" height="100%"/>

In case of doubts, please contact andre.zanella@imdea.org

In case you use this data in your study, please cite our work using the following: 
```bibtex
@inproceedings{10.1145/3618257.3624825, 
  title={Characterizing and Modeling Session-Level Mobile Traffic Demands from Large-Scale Measurements}, 
  author={Zanella, André Felipe and Bazco-Nogueras, Antonio and Ziemlicki, Cezary and Fiore, Marco}, 
  year={2023},
  publisher = {Association for Computing Machinery},
  address = {New York, NY, USA},
  doi={10.1145/3618257.3624825},
  url= = {https://doi.org/10.1145/3618257.3624825},
  booktitle = {Proceedings of the 23rd ACM Internet Measurement Conference},
  location = {Montreal, QC, Canada},
  series = {IMC '23}
}
```

## Models included

Here's a brief description on what's included on this page. Please note this work was developed in Python 3.9 and most of the statistical work was done using Scipy and Pomegranate libraries. We strongly suggest using those for better compatibility. All of those are used on `sample_generator.ipynb` file, as an usage example generating samples on $N$ antennas and $D$ days. 

### 1. Share of sessions and traffic across apps:
For every app, what was the percentage of sessions and traffic

### 2. Session arrival rate distribution
Considering all antennas included on our study, we present the average of each decile ranked their traffic load, with $decile=0.1$ having the lowest load and $decile=1.0$ the highest. Since for a day, the arrival rate had a multi-modal distribution represented by peak and non-peak hours, we split results for those two distributions. 

Please note peak hours is represented by a **Gaussian distribution** (with $\mu$ represented by column *loc* and $\sigma$ represented by *scale*), while non-peak hours is represented by a **Pareto distribution** (with its shape parameter being represented by column *alpha* and the scale that represents the shift on loads being the *scale* column; on this case the *loc* column will always be zero). 

In case you're not using Scipy to generate those distributions (both with `scipy.stats.norm` and `scipy.stats.pareto`), you may have to double check the correct way to convert its *loc* and *scale* parameters appropriately. 

### 3. Transport layer session-level traffic probability distributions

For every application, we present the probability distribution function modeled as a general mixture model (composed purely of **Gaussian distributions**). We highly using `pomegranate.NormalDistribution` for each individual Gaussian, and `pomegranate.GeneralMixtureModel` to create the mixture. 

Please note all applications will have columns columns *u_g* representing the mean $\mu$ and *s_g* representing the std deviation $\sigma$. When assembling the mixture, you must declare its weight as 1. In case the application has additional peaks composing the mixture model, its $\mu$ and $\sigma$ will be described by $[u_1,u_2,u_3]$ and $[s_1,s_2,s_3]$, respectively, while each component weight will be $[w_1,w_2,w_3]$. 

### 4. Relation between traffic and duration for transport-layer sessions

We model for each app the relation between the traffic consumed by a specific transport layer session and its expected duration. For a service $s$, its traffic $v_{s}$ will have duration $d$ modeled as a power-law:

$\tilde{v}_s(d)=\alpha_s\cdot d^{\beta_s}$

Note that during our example workflow, we actually generate first the traffic value and later obtain the duration by the inverse function of this powerlaw (calculated by `func_invpowerlaw` on the example). On the data files, coefficient $\alpha$ is represented by column *a*, while $\beta$ is represented by column *b*. 

## Known bugs
There are some cases where the traffic from a sampled session may be located at the extreme of its right tail, and on some apps this may lead to sessions lasting an incorrectly long ammount (i.e. days). We recomend filtering out all sessions above a certain duration limit (i.e. 1h). This is a short-comming on the way the data is collected and how we choose to draw the relation between session traffic and duration. 

## Acknowledgements
This work was supported by BANYAN project, which received funding from the European Union’s Horizon 2020 research and innovation program under grant agreement no. 860239; by NetSense, grant no. 2019-T1/TIC-16037 funded by Comunidad de Madrid; by the research project CoCo5G (Traffic Collection, Contextual Analysis, Data-driven Optimization for 5G), grant no. ANR-22-CE25-0016, funded by the French National Research Agency (ANR); and by the Regional Government of Madrid through the grant 2020-T2/TIC-20710 for Talent Attraction. 
