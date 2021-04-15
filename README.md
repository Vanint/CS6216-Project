# How Well Do Self-Supervised GNNs Transfer to Knowledge Graphs?
Ailin Deng, Yifan Zhang

CS6216 Project

National University of Singapore


## Overview
Graph representation learning has emerged as a powerful technique for real-world knowledge graph-based problems.  One of the arts on graph representation learning is using graph neural networks, which however require  a large number of labeled data for training and thus may be impractical for real-world applications. To handle this, contrastive self-supervised  learning leverages unlabeled graph data to train graph neural networks (GNNs) via contrastive learning and is attracting increasing attention. However, although there are several contrastive self-supervised  learning studies on the pre-training of GNNs, few have explored their applications to knowledge graph learning tasks. Therefore, in this repository, we empirically explore whether better self-supervised GNNs transfer better in Knowledge Graph-based tasks.

### Graph Contrastive Learning for Pre-training
We use graph contrastive learning framework [GraphCL](https://github.com/Shen-Lab/GraphCL) as basic graph contrastive learning method for empirical studies.

![](./Framework.png) 
 
In GraphCL, there are four kinds of graph-level augmentation techniques: node dropping, edge perturbation, attribute masking, and subgraph.

![](./Augmentation.png)

### TransE for Transfer Leanring to Knowledge Graph Tasks
After pre-training graph neural networks on a graph dataset, we then transfer the pre-trained model to solve a downstream link prediction task on knowledge graphs. Formally, a knowledge graph can be represented by a directed labeled graph $\mathcal{G}=(\mathcal{V},\mathcal{E},\mathcal{R})$. Given a training set $S$ of relationship triplet, \ie (subject, relation, object), shortly $(s,r,o)$. Here, two nodes $s,o\in \mathcal{V}$, and a relationship edge $r\in \mathcal{E}$. 

For each entity, we use a graph neural network (initialized by the GraphCL pre-training parameters) to extract their features. For edge edge, we  randomly initialize their representations. To transfer the model, we follow the idea of [TransE](https://proceedings.neurips.cc/paper/2013/file/1cecc7a77928ca8133fa24680a88d2f9-Paper.pdf) that the functional relation introduced by labeled edges represents a translation of their embeddings. That is, when $(s,r,0)$ holds, we enforce $s+r\approx o$ (\ie $o$ should be a nearest neighbor of $s+r$); if $(s,r,0)$ does not hold, $s+r$ should be far away from $o$. Following\cite{bordes2013translating}, we resort to an energy-based framework, where the energy of a triplet is represented by $d(s+r,o)$ for some dissimilarity measure $d$, \eg $\ell_1$ or $\ell_2$-norm.
 
To train the graph neural network and the embeddings of edges, we minimize the TransE loss over the training set:
\begin{align}\label{eq2}
    \mathcal{L}_{KG} \small{=} \sum_{(s,r,o)\in S}\sum_{(s',r,o')\in S'} [\gamma+d(s+r,o)-d(s'+r,o')]_{+}, \nonumber
\end{align}
where $\gamma>0$ is a margin hyper-parameter, and $[x]_+$ denotes the positive value of $x$. Moreover, the set $S'$ of corrupted triplets are represented by 
\begin{align}
    S'=\{((s',r,o)|s'\in\mathcal{V})\} \cup \{((s,r,o')|o'\in\mathcal{V})\}. \nonumber
\end{align}
The corrupted set $S'$ consists of training triplets with either the head or tail nodes replaced by a random entity (but not both at the same time). Such a TransE loss helps learn lower values of the energy for training triplets than those for corrupted triplets. In other words,  we encourage the dissimilarity value of training triplets to be low  and that of corrupted triplets to be high.

In addition to this loss, we also enforce an additional $\ell_2$-norm constraint on the node features to prevent the training process to naively minimize $\mathcal{L}$ by artificially increasing node feature norms.


## Experiment Results

*  Effectiveness of Self-supervised Pre-training.

*  Effect of Data Augmentations.

*  Compareness with Supervised Pre-training.


## Citation

If you would like to reference this report in you research, please cite our paper.

```
@inproceedings{nus-cs6216-report,
 author = {Deng, Ailin and Zhang, Yifan},
 booktitle = {NUS CS6216},  
 title = {How Well Do Self-Supervised GNNs Transfer to Knowledge Graphs?}, 
 year = {2021}
}
```

