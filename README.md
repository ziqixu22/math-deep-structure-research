# Math Department Deep Structure Research

A collaborative undergraduate research project exploring whether latent intellectual structure in a mathematics department can be recovered from publication, citation, journal, and classification relationships.

## 1. Goal and Research Motivation

Academic departments are formally organized into areas, but actual research relationships can cut across those boundaries. The project asks a structural question:

> Can collaboration and research-similarity data reveal meaningful groups of faculty and relationships between those groups?

The repository explores this through graph-based similarity matrices, clustering, tree construction, spectral methods, SVD-based representations, and non-Euclidean tree-space ideas.

The goal is not simply to produce a cluster label. It is to compare multiple mathematical representations of the same underlying research network and understand which structures remain stable across methods.

## 2. Data Representation

The code in `Clustering/` works with pairwise and feature-level information including:

- joint publications
- directed/joint citation information
- common journals
- common references
- collaboration-distance matrices
- classification-code matrices

For a collection of $n$ faculty members, pairwise relationships can be represented by a similarity matrix

$$
S\in\mathbb{R}^{n\times n},
$$

where $S_{ij}$ measures the strength of the relationship between faculty members $i$ and $j$.

One transformation implemented in `spectral.ipynb` converts a similarity count $s_{ij}$ into a distance-like quantity using

$$
d_{ij}=\frac{1}{r^{s_{ij}}},\qquad r>1.
$$

The notebook uses $r=1.05$ by default. Larger similarity counts therefore imply smaller distances.

## 3. Why Multiple Relationship Measures?

No single signal fully describes intellectual similarity. Joint publication captures direct collaboration, while common references or journals can capture thematic similarity even when two researchers have never coauthored a paper.

The project therefore constructs several matrices and studies them separately and in combined form. This is useful because a clustering result that appears only under one highly specific definition of similarity may be less convincing than a pattern that survives several representations.

## 4. Spectral Clustering

The repository explicitly uses `sklearn.cluster.SpectralClustering` with a **precomputed affinity matrix**.

For affinity matrix $W$, define the degree matrix

$$
D_{ii}=\sum_j W_{ij}.
$$

A standard graph Laplacian is

$$
L=D-W.
$$

A normalized form is

$$
L_{\mathrm{sym}}=I-D^{-1/2}WD^{-1/2}.
$$

Spectral clustering uses eigenvectors associated with the low-frequency structure of the graph to embed the nodes into a lower-dimensional space, followed by clustering in that embedding.

This method is appropriate here because faculty relationships form a graph and the groups do not need to be linearly separable in the original feature space.

The exploratory notebook contains an experiment with

```python
SpectralClustering(n_clusters=20, affinity="precomputed")
```

on the joint-publication similarity matrix.

### Important diagnostic

The notebook reports a warning that the graph is **not fully connected**. That matters: disconnected components can dominate the spectral embedding and make cluster interpretation sensitive to graph construction. For an interview, this is an important limitation to mention rather than hide.

## 5. SVD-Based Structure

The project also explores sparse-matrix representations and partial singular value decomposition.

For data matrix

$$
X\in\mathbb{R}^{n\times p},
$$

SVD decomposes the matrix as

$$
X=U\Sigma V^\top.
$$

Keeping only the largest $k$ singular values gives the rank-$k$ approximation

$$
X_k=U_k\Sigma_kV_k^\top.
$$

This can expose dominant latent directions in high-dimensional citation or classification data while reducing noise and dimensionality. It is particularly useful when the original representation is sparse.

## 6. Hierarchical Tree Construction

`TreeConstructor.py` contains modified BioPython implementations of UPGMA and Neighbor Joining.

### UPGMA

UPGMA repeatedly merges the closest pair of clusters. After merging clusters $A$ and $B$, the implementation updates the distance to another cluster $C$ using an arithmetic average:

$$
d(A\cup B,C)=\frac{d(A,C)+d(B,C)}{2}.
$$

This creates a rooted hierarchical tree and provides an interpretable way to visualize nested similarity structure.

### Neighbor Joining

The Neighbor Joining implementation adjusts pairwise distances using each node's average distance to the remaining nodes. Conceptually, it selects the pair minimizing a corrected criterion

$$
Q(i,j)=d_{ij}-r_i-r_j,
$$

where the code computes

$$
r_i=\frac{\sum_j d_{ij}}{n-2}.
$$

After joining nodes $i$ and $j$, the distance from the new node $u$ to another node $k$ is updated as

$$
d_{uk}=\frac{d_{ik}+d_{jk}-d_{ij}}{2}.
$$

Compared with UPGMA, Neighbor Joining does not require the same ultrametric interpretation and can be useful when pairwise distances do not fit a constant-rate hierarchical model.

## 7. PATHTREES / Tree-Space Reference

The repository includes `PATHTREES-main/`, and a separate [`PATHTREES`](https://github.com/ziqixu22/PATHTREES) repository is retained as supporting material.

**PATHTREES is an external/reference implementation and is not presented as my original standalone software project.**

It was used to study how tree structures can be compared and explored in Billera-Holmes-Vogtmann (BHV) tree space. That perspective is relevant because different clustering or hierarchical procedures can produce different trees, and ordinary Euclidean averaging is not generally appropriate for tree-valued objects.

## 8. Evaluation Strategy

This project is unsupervised, so there is no single ground-truth accuracy metric in the repository. Evaluation is therefore primarily structural and comparative.

A rigorous interpretation should consider:

- **cluster stability** across different similarity definitions
- **connectivity of the underlying graph** before spectral embedding
- **agreement between clustering/tree-building methods**
- **sensitivity to the number of clusters**
- **interpretability** of recovered groups relative to known research relationships
- **dimensional structure** revealed by singular values in SVD experiments

The current notebooks are exploratory and do not contain a single locked benchmark table with metrics such as silhouette score or adjusted Rand index. This README therefore does not invent one.

## 9. Results and Interpretation

The repository successfully demonstrates that the same faculty network can be represented through multiple research signals and analyzed using several mathematical frameworks. The spectral notebook produces explicit faculty clusters from publication affinities, while the tree-construction code creates hierarchical representations from pairwise distances.

A key practical finding from the spectral experiment is that the collaboration graph is not fully connected. That means network construction is itself part of the modeling problem: sparse or disconnected relationships can strongly affect the embedding and the clusters that follow.

The broader conclusion is that "department structure" is not a single objectively observed label. It depends on how similarity is defined, and robust interpretation should compare structures across publication, citation, journal, and classification signals rather than rely on one matrix alone.

## 10. Repository Structure

```text
.
├── Barycenter Team/      # Barycenter / tree-space research work
├── Clustering/           # Spectral, hierarchical, SVD, and tree experiments
├── Spectral Team/        # Spectral-analysis research work
├── PATHTREES-main/       # External/reference implementation
├── README.md
└── research PDFs         # Reports and presentation material
```

Notable files in `Clustering/` include:

- `spectral.ipynb` — precomputed-affinity spectral clustering
- `svd_cluster.ipynb` — sparse matrix + partial SVD exploration
- `hierachical.ipynb` — hierarchical clustering experiments
- `ConsensusTrees.ipynb` — consensus-tree experiments
- `TreeConstructor.py` — UPGMA and Neighbor Joining tree construction

## 11. Skills Demonstrated

Python · NumPy · pandas · SciPy · scikit-learn · Network Analysis · Spectral Clustering · SVD · Hierarchical Clustering · Tree Algorithms · Mathematical Modeling · Research Collaboration

## 12. Research Context and Attribution

This is a collaborative research workspace. Different files may have been created by different team members or subteams, so the repository should not be interpreted as a single-author software package. External/reference code is identified as such; the repository is presented to document the research process, methods explored, and mathematical ideas used.