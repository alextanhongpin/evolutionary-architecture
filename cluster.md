
## Why an odd number of cluster members?

The odd number of nodes prevents split-brain problem, whereby the multiple leaders are being elected due to even number of votes [^1]. For a cluster of `n` nodes, `n/2+1` votes are required in order to reach a consensus.



References
- https://etcd.io/docs/v3.5/faq/#why-an-odd-number-of-cluster-members


[^1]: https://stackoverflow.com/questions/58823341/why-is-it-recommended-to-create-clusters-with-odd-number-of-nodes#:~:text=The%20odd%20number%20of%20nodes,the%20node%20with%20majority%20votes.
