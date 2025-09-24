# Assignment 2: Document Similarity using MapReduce

**Name:** Sai Phanindra Kothuri

**Student ID:** 801420700

## Approach and Implementation

### Mapper Design
The `DocumentSimilarityMapper` class processes each document by taking a line of text as input. For each document, it extracts the filename to use as a unique ID. It then tokenizes the document's content, converts each word to lowercase, and stores them in a HashSet to keep only the unique words. Finally, it emits a key-value pair where the key is the document's filename and the value is a single, comma-separated string of all its unique words. This process efficiently transforms raw text into a structured format, preparing the data for the Reducer to easily compare word sets and calculate the Jaccard Similarity between document pairs.

### Reducer Design
The `DocumentSimilarityReducer` class calculates the Jaccard Similarity for every possible pair of documents. It receives a document's filename and its unique word set from the mapper. The reducer maintains a map to store the word sets of all documents it has processed. For each new document it receives, it compares it against every document already in the map. For each pair, it computes the **intersection** (common words) and **union** (total unique words) of their word sets. The Jaccard Similarity is then calculated by dividing the size of the intersection by the size of the union. Finally, it emits the pair of document names and their corresponding similarity score as its final output.

### Overall Data Flow
[Describe how data flows from the initial input files, through the Mapper, shuffle/sort phase, and the Reducer to produce the final output.]

---

## Setup and Execution

### ` Note: The below commands are the ones used for the Hands-on. You need to edit these commands appropriately towards your Assignment to avoid errors. `

### 1. **Start the Hadoop Cluster**

Run the following command to start the Hadoop cluster:

```bash
docker compose up -d
```

### 2. **Build the Code**

Build the code using Maven:

```bash
mvn clean install
```

### 4. **Copying the JAR to Docker Container**

Copy the JAR file to the Hadoop ResourceManager container:

```bash
docker cp /workspaces/Assignment2-Document-Similarity-using-MapReduce/target/DocumentSimilarity-0.0.1-SNAPSHOT.jar resourcemanager:/opt/hadoop-3.2.1/share/hadoop/mapreduce/
```

### 5. **Move Dataset to Docker Container**

Copy the dataset to the Hadoop ResourceManager container:

```bash
docker cp shared_folder/input_files/ resourcemanager:/opt/hadoop-3.2.1/share/hadoop/mapreduce/
```

### 6. **Connect to Docker Container**

Access the Hadoop ResourceManager container:

```bash
docker exec -it resourcemanager /bin/bash
```

Navigate to the Hadoop directory:

```bash
cd /opt/hadoop-3.2.1/share/hadoop/mapreduce/
```

### 7. **Set Up HDFS**

Create a folder in HDFS for the input dataset:

```bash
hadoop fs -mkdir -p /input/dataset
```

Copy the input dataset to the HDFS folder:

```bash
hadoop fs -put ./input_files /input/dataset
```

### 8. **Execute the MapReduce Job**

Run your MapReduce job using the following command: Here I got an error saying output already exists so I changed it to output1 instead as destination folder

```bash
hadoop jar DocumentSimilarity-0.0.1-SNAPSHOT.jar com.example.controller.DocumentSimilarityDriver /input/dataset/input_files /output
```

### 9. **View the Output**

To view the output of your MapReduce job, use:

```bash
hadoop fs -cat /output/*
```

### 10. **Copy Output from HDFS to Local OS**

To copy the output from HDFS to your local machine:

1. Use the following command to copy from HDFS:
    ```bash
    hdfs dfs -get /output /opt/hadoop-3.2.1/share/hadoop/mapreduce/
    ```

2. use Docker to copy from the container to your local machine:
   ```bash
   exit 
   ```
    ```bash
    docker cp resourcemanager:/opt/hadoop-3.2.1/share/hadoop/mapreduce/output1/ shared-folder/output/
    ```
---

## Challenges and Solutions

[Describe any challenges you faced during this assignment. This could be related to the algorithm design (e.g., how to generate pairs), implementation details (e.g., data structures, debugging in Hadoop), or environmental issues. Explain how you overcame these challenges.]

---
## Sample Input

**Input from `small_dataset.txt`**
```
Document1 This is a sample document containing words
Document2 Another document that also has words
Document3 Sample text with different words
```
## Sample Output

**Output from `small_dataset.txt`**
```
"Document1, Document2 Similarity: 0.56"
"Document1, Document3 Similarity: 0.42"
"Document2, Document3 Similarity: 0.50"
```
## Obtained Output: (Place your obtained output here.)
