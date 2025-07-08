# scene_to_product_fitness
To build an AI-powered system that matches products to a scene image, we’ll design a solution that analyzes the scene, identifies its context (eg., a gaming setup), and recommends products from a catalog that naturally fit the scene. The system will use a combination of computer vision and semantic matching, with considerations for scalable deployment. Below is a detailed implementation, including a minimal demo and deployment strategy.

# Solution Approach
1. Product Catalog Processing: Use a pre-trained vision model (eg., CLIP from OpenAI) to extract features for product descriptions and images. CLIP can encode both images and text, allowing us to understand the product holistically. 
Store these features in a vector database (eg., FAISS) for efficient similarity search later.
2. Scene Analysis: Extract semantic features from the input scene image using CLIP to understand the scene's context (eg. Gaming Setup)
3. Matching: Compute similarity scores between the scene’s features and product features to rank products by relevance. 
4. Output: The script outputs a JSON object with the top-k products, ranked by similarity score. In the demo, the output shows top 3 products as k is set to 3. This can be changed by the user in the main function.


# Scalable Deployment
1.	Batch Ingestion: 
      o	Process: The products processesing part of the code runs in batches, generating embeddings for images and descriptions. In a production system, we can use a distributed system (eg., Apache Spark) to process large catalogs in parallel.
      o	Storage: Store embeddings in a vector database like FAISS or Pinecone. FAISS is used here for simplicity and efficiency.
      o	Update Handling: Periodically re-run batch ingestion for new or updated products, updating the FAISS index incrementally.

2.	Stateless Matching Service: 
      o	API: The Flask app provides a /match endpoint that accepts a scene image and returns top-K matches. It’s stateless, relying on the FAISS index for data. By not maintaining client session state on the server, the Stateless APIs enable easier scaling,              simplified development and testing, and improved caching capabilities.
      o	Scaling: Deploy on a cloud platform (eg., AWS ECS, Kubernetes) with auto-scaling to handle traffic spikes. Use a load balancer to distribute requests.
      o	Caching: Cache frequently queried scene embeddings in Redis to reduce redundant CLIP computations.

3.	Infrastructure:
      o	Compute: Use GPU instances (eg., AWS EC2 G4) for CLIP inference during batch ingestion and real-time matching.
      o	Storage: Store product metadata in a relational database (e.g., PostgreSQL) and embeddings in a vector database (eg. FAISS or Milvus).
      o	Monitoring: Use Prometheus and Grafana to monitor API latency, error rates, and FAISS query performance.

# Way Forward
1. Descrition of the scene as input (eg. "Gaming setup" or "Living room") could enhance model's performance by making it context rich and mapping the scene's embedding more appropriately with the products that could fit the scene
2. The adaptibility and performance of the engine could be improved further if we could create a dataset of few scenes with a few positive and negative products that could fit the scene. If such a dataset could be obtained, the features arising from the models would understand the exact context of fitness of a product in a given scene
3. Further, embeddings can be improved by using a larger model
 
