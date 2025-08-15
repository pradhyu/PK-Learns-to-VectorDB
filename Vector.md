✦ Based on the code in visualize.py, the chromaviz library uses a two-step process to reduce the dimensionality of the vectors to 3D:

   1. PCA (Principal Component Analysis): It first uses PCA to reduce the dimensionality to 50 components.
   2. t-SNE (t-distributed Stochastic Neighbor Embedding): Then, it uses t-SNE to further reduce the 50 components down to 3 dimensions for visualization.

  The code also calculates a 3-component PCA result (pca_result_3), but the final visualization uses the t-SNE results.

  Therefore, the primary algorithm used is t-SNE on top of PCA. The library does not appear to support other dimensionality reduction algorithms out of the
   box.

╭────────────────────────────────────────────────────────────────────────────╮
│  > how does it know which dimention in vector is more important to keep ?  │
╰────────────────────────────────────────────────────────────────────────────╯

✦ That's an excellent question. The process of deciding which dimensions are "more important" is at the core of how dimensionality reduction algorithms
  like PCA and t-SNE work. Here's a breakdown of how chromaviz does it, based on the code:

  1. PCA: Finding the Directions of Maximum Variance

  The first step in the chromaviz pipeline is PCA. PCA doesn't actually "keep" or "discard" the original dimensions of your vectors. Instead, it creates
  new dimensions called "principal components".

  Here's the key idea:

   * Variance as Importance: PCA assumes that the most "important" dimensions are the ones with the most variance. In other words, it looks for the
     directions in your high-dimensional space where the data points are most spread out.
   * Principal Components: The first principal component is the direction of highest variance. The second principal component is the direction of the next
     highest variance, with the condition that it must be orthogonal (at a right angle) to the first. This continues for all subsequent components.
   * Dimensionality Reduction: By the time chromaviz gets to the 50th principal component, it has captured the 50 most significant directions of variance
     in the data. The remaining, less variant dimensions are discarded, as they are considered to contain less information (or more noise).

  In the visualize.py code, this is what's happening here:

   1 pca_50 = PCA(n_components=50)
   2 pca_result_50 = pca_50.fit_transform(df)

  2. t-SNE: Preserving Local Neighborhoods

  The second step, t-SNE, has a different goal. It's not about capturing variance, but about preserving the local structure of the data. It tries to
  arrange the data in the 3D space so that points that were close to each other in the high-dimensional space are still close to each other in the 3D
  space.

  Here's how it works:

   * Similarity Scores: t-SNE calculates a similarity score between every pair of points in the high-dimensional space. Points that are close together get
     a high similarity score, and points that are far apart get a low similarity score.
   * Random Arrangement: It then randomly scatters the points in the 3D space and calculates similarity scores for them in this new, lower-dimensional
     space.
   * Minimizing Divergence: The core of t-SNE is an iterative process that tries to minimize the difference (the "divergence") between the similarity
     scores in the high-dimensional space and the 3D space. It shuffles the points around in 3D until the arrangement best represents the original
     neighborhoods.

  This is what's happening in this part of the code:

   1 tsne = TSNE(n_components=3, verbose=0, perplexity=40, n_iter=300)
   2 tsne_pca_results = tsne.fit_transform(pca_result_50)

  In Summary:

   * PCA identifies the most important dimensions by finding the directions of maximum variance.
   * t-SNE identifies the most important relationships by preserving the local structure and similarity of the data points.

  By using PCA first, chromaviz speeds up the computationally expensive t-SNE step and can help to reduce noise in the data, which often leads to better
  visualizations.


