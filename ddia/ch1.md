# Chapter 1: Trade-Offs in Data Systems Architecture

Different *main* challenges based on the type of application:
* **Data-intensive**: data management: moving/storing huge amounts of data
    * Ex) Social media apps, data analytics, online shopping transactions
* **Compute-intensive**: parallelizing expensive computations
    * CPU/GPU
    * Ex) Weather simulations, graphics/rendering, scientific computing

Applications can be both data-intensive and compute-intensive
* Ex. machine learning training

Standard building blocks in a *data-intensive* application:
* **Databases**: Storing data for other applications to read
* **Caching**: Save results of expensive computations for quick re-use
* **Search indexes**: Search data by keywords
* **Stream processing**: Handle data/events in real-time
* **Batch processing**: Compute metrics of accumulated data periodically

## Operational Versus Analytical Systems

* **Operational**: data infrastructure, backend services that *create* new data based on application users
    * Store/modify data in databases
* **Analytical**: read-only copy of operational data that was transformed to be optimized for data analytics
