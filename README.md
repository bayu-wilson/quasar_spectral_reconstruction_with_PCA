# Reconstrucing astronomical spectra using Principal Component Analysis (PCA)

In my paper, [Wilson et al. 2021](https://arxiv.org/abs/2106.04837), we used absorption features in quasar spectra to study the evolution of thermal properties of the intergalactic medium over cosmic time. In order to quantify how strong an absorption feature is, one must know or predict what the un-absorbed spectrum looks like. The unabsorbed spectrum is often called the continuum spectrum. It is called this because the distribution of emission lines from the quasar blend together to make a continuous spectrum. In order to predict this, the XQ-100 Legacy Survey team ([Berg et al. 2016](https://arxiv.org/abs/1609.05968)) used a by-hand fit to the continuum using a cubic spline. See Figure 1 for an example of an XQ-100 quasar and its by-hand continuum fit. This has been shown to work well but it can be difficult to do this consistently. One thing that makes this especially hard is that hydrogen gas causes strong Lyman-series absorption blueward (leftward) of the Lyman-alpha line in Figure 1. 

<p align="center">
    <img src="https://github.com/user-attachments/assets/00c84271-284f-4297-b7ed-b041fbabe8dd" alt="Description" width="600"/>
</p>
<p align="center">Figures 1. A typical quasar spectrum from the XQ-100 Legacy Survey. The blue line is the by-hand continuum fit. The red line is the redshifted Lyman-alpha line. As light from the quasar travels through the universe, it will absorb at the Lyman-alpha resonant wavelength (1216 Angstroms) as well as other Lyman-series lines. During this journey, the entire spectrum will redshift (shift rightwards) due to cosmic expansion. This creates, for the Lyman-alpha line, the Lyman-alpha forest of absorption lines. This and the other Lyman-series forests can be seen in the blue/left side of the spectrum.</p>

Ideally there'd be an automatic and robust way to predict quasar continua, even blueward of the Lyman-alpha line (the Lyman-series forests hereafter). One idea is to look for a correlation between fluxes in the red and blue part of the spectrum. Then one would generate a model that can use the red, relatively unabsorbed part of the spectrum to predict the blue, more strongly absorbed side. Machine learning techniques like principle component analysis (PCA) are well suited for this kind of problem because it can reduce the dimensionality of the problem (related to the large number of wavelength bins) while still preserving the most important information in its principle components.

In this project I will mostly be referring to [Pâris et al. 2011](https://arxiv.org/abs/1104.2024) section 2.3. 

### Objective
The purpose of this project is to reconstruct the continuum quasar spectra blueward of the Ly$\alpha$ line. Note that is not meant to be a rigorous calculation. This is to be treated as an exercise to review and understand this application of PCA.

### Procedure
This procedure hits on the main ideas but not every detail.

1) Gather XQ-100 quasar spectra from [this](https://github.com/bayu-wilson/lyb_pk) github repo. There are N=100 quasars with redshifts between $z=3.0$ and $z=4.2$.
2) Fit the continuum of the entire spectrum by-eye to get the true continuum, $q(\lambda)_i$ for the $i$'th quasar. This has already been done and the data files are available.
3) From this continuum, a covariance matrix can be calculated for pairs of spectral pixels. This would contain information about how the red-side pixels correlate to the blue-side.
4) The principle components are found by decomposing the covariance matrix $V$ into a product $V = P^{-1}\Lambda P$ where $P$'s columns are the eigenvectors/principap components and $\Lambda$ is a diagonal matrix containing the eigenvalues. This is just a method to get the eigenvectors and eigenvalues of the covariance matrix. Recall that the eigenvectors point in directions where the data has maximal variance.
5) For each quasar's whole red+blue continuum, the distribution of weights for each principal component is found. Then a mock spectra can be found by adding the weighted principal components to the mean quasar continuum.
6) We just found the principal component spectra (PCS) for the combined red+blue continua. We will repeat this procedure again for just the red-side. Then we are left with $m$ eigenvectors for each of the $N$ quasars which are stacked horizonatlly to make matrices C and D for the redside and combined continua (shape $N$ by $m$ for each).
7) These matrices related by a projection matrix $X$ which has already been found in [Pâris et al. 2011](https://arxiv.org/abs/1104.2024).
8) Finally we apply the projection matrix onto the matrix $C$ (eigenvectors of redside) in order to predict the eigenvectors of the combined spectrum. Then this is used to predict the combined spectrum.

<p align="center">
    <img src="https://github.com/user-attachments/assets/f4136597-7d72-4d48-b3bd-b17d583e1256" alt="Description" width="600"/>
</p>
<p align="center">Figures 2. Left panel shows the hand-picked continuum (red) and the PCA continuum (black) prediction. The continuum ratio, hand-picked over PCA, is shown in the right panel. Clearly the models don't align too well as they differ significantly in some regions.  </p>









