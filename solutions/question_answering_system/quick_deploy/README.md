# Quick Start


This project combines Milvus and BERT to build a question and answer system. This aims to provide a solution to achieve semantic similarity matching with Milvus combined with AI models.

## Data description

The dataset needed for this system is a CSV format file which needs to contain a column of questions and a column of answers. 

There is a sample data in the data directory.

## How to deploy the system

### 1. Start Milvus and MySQL

The system will use Milvus to store and search the feature vector data, and Mysql is used to store the correspondence between the ids returned by Milvus and the questions data set, then you need to start Milvus and Mysql first.

- **Start Milvus v1.1.0**

First, you are supposed to refer to the Install Milvus v1.1.0 for how to run Milvus docker.

```bash
$ wget -P /home/$USER/milvus/conf https://raw.githubusercontent.com/milvus-io/milvus/v1.1.0/core/conf/demo/server_config.yaml
$ sudo docker run -d --name milvus_cpu_1.1.0 \
-p 19530:19530 \
-p 19121:19121 \
-v /home/$USER/milvus/db:/var/lib/milvus/db \
-v /home/$USER/milvus/conf:/var/lib/milvus/conf \
-v /home/$USER/milvus/logs:/var/lib/milvus/logs \
-v /home/$USER/milvus/wal:/var/lib/milvus/wal \
milvusdb/milvus:1.1.0-cpu-d050721-5e559c
```

> Note the version of Milvus.

- **Start MySQL**

```bash
$ docker run -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
```

### 2. Start Server

The next step is to start the system server. It provides HTTP backend services, and there are two ways to start: running with Docker or source code.

#### 2.1 Run source code

- **Install the Python packages**

```bash
$ cd server
$ pip install -r requirements.txt
```

- **wget the model**

```bash
$ cd server/src/model
$ wget https://public.ukp.informatik.tu-darmstadt.de/reimers/sentence-transformers/v0.2/paraphrase-mpnet-base-v2.zip
$ unzip paraphrase-mpnet-base-v2.zip -d paraphrase-mpnet-base-v2/
```

- **Set configuration**

```bash
$ vim server/src/config.py
```

Please modify the parameters according to your own environment. Here listing some parameters that need to be set, for more information please refer to [config.py](./server/src/config.py).

| **Parameter**    | **Description**                                       | **Default setting** |
| ---------------- | ----------------------------------------------------- | ------------------- |
| MILVUS_HOST      | The IP address of Milvus, you can get it by ifconfig. | 127.0.0.1           |
| MILVUS_PORT      | Port of Milvus.                                       | 19530               |
| VECTOR_DIMENSION | Dimension of the vectors.                             | 768                 |
| MYSQL_HOST       | The IP address of Mysql.                              | 127.0.0.1           |
| MYSQL_PORT       | Port of Milvus.                                       | 3306                |
| DEFAULT_TABLE    | The milvus and mysql default collection name.         | milvus_qa           |
| MODEL_PATH       | The path of the model `paraphrase-mpnet-base-v2`      |                     |

- **Run the code** 

Then start the server with Fastapi. 

```bash
$ cd server/src
$ python main.py
```

- **API docs**

Vist `127.0.0.1:8000/docs` in your browser to use all the APIs.

![](pic/qa_api.png)



**/qa/load_data**

This API is used to import Q&A datasets into the system.

**/qa/search**

This API is used to get similar questions in the system.

**/qa/answer**

This API is used to get the answer to a given question in the system.

**/qa/count**

This API is used to get the number of the questions in the system.

**/qa/drop**

This API is used to delete a specified collection.