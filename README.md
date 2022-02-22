# Kedro Lab

Kedro is an open-source Python framework for creating reproducible, maintainable and modular data science code.
It borrows concepts from software engineering and applies them to machine-learning code; applied concepts include
modularity, separation of concerns and versioning

--- 
python==3.8

### Install:
`pip install kedro`

#### Verify a successful installation
`kedro info`



--- 
### Kedro concepts:
+ **Node:** It is a wrapper for a Python function that names the inputs and outputs of that function. 
It is the building block of a pipeline. Nodes can be linked when the output of one node is the input of another.
+ **Pipeline:** A pipeline organises the dependencies and execution order of a collection of nodes, 
and connects inputs and outputs while keeping your code modular
+ **DataCatalog:** It is the registry of all data sources that the project can use. It maps the names
of node inputs and outputs as keys in a DataSet, which is a Kedro class that can be specialised for different types of
data storage. eg: Kedro uses a MemoryDataSet for data that is simply stored in-memory
+ **Runner:** is an object that runs the pipeline.

### Kedro Starters:
Kedro starters are used to create projects that contain code to run as-is, or to adapt and extend. They provide predefined
example code and configuration that can be reused.  
A Kedro starter is a Cookiecutter template that contains the boilerplate code for a Kedro project. You can create your
own starters for reuse within a project or team.  

+ `kedro new --starter=<path-to-starter>`

If you want to use a specific version of a starter, you can pass a `--checkout` argument to the command as follows:
 
+ `kedro new --starter=pyspark --checkout=0.1.0`

List templates: 
+ `kedro starter list`
---
### Create a new project:

+ `kedro new`
  + project_name   
  + repo_name  
  + python_package   
+ `kedro install`    
+ `kedro run`  

--- 

## Kedro project development workflow
+ Set up the project template 
+ Set up the data
+ Create the pipeline
+ Package the project

## Project 1:
### Set up the project template 
Create a new project:
+ `kedro new --starter=spaceflights`

Install dependencies
+ `pip install -r src/requirements.txt`

Configure the project  
eg. conf/local/credentials.yml

### Set up the data

Add your datasets to data
+ reviews  
`wget -O data/01_raw/reviews.csv https://kedro-org.github.io/kedro/reviews.csv`
+ companies  
`wget -O data/01_raw/companies.csv https://kedro-org.github.io/kedro/companies.csv`
+ shuttles  
`wget -O data/01_raw/shuttles.xlsx https://kedro-org.github.io/kedro/shuttles.xlsx`

Register the datasets
+ conf/base/catalog.yml

+ csv:
```
companies:type: 
  pandas.CSVDataSet  
  filepath: data/01_raw/companies.csv  

reviews:  
  type: pandas.CSVDataSet  
  filepath: data/01_raw/reviews.csv  
```

### Create the pipeline
Data processing pipeline  
Generate a new pipeline template  
`kedro pipeline create data_processing`

Add node functions   
`src/kedro_tutorial/pipelines/data_processing/nodes.py`

Assemble nodes into the data processing pipeline  
`src/kedro_tutorial/pipelines/data_processing/pipeline.py`

Update the project pipeline  
`src/kedro_tutorial/pipeline_registry.py`

Test the example  
`kedro run --node=preprocess_companies_node`  
complete:  
`kedro run`

Visualise the pipeline  
`kedro viz`
`kedro viz --autoreload`

Persist pre-processed data  
`conf/base/catalog.yml`
```
preprocessed_companies:
  type: pandas.ParquetDataSet
  filepath: data/02_intermediate/preprocessed_companies.pq

preprocessed_shuttles:
  type: pandas.ParquetDataSet
  filepath: data/02_intermediate/preprocessed_shuttles.pq
```

Create another pipeline
`kedro pipeline create data_science`


### Kedro runners
There are three different Kedro runners that can run the pipeline:
+ SequentialRunner - runs your nodes sequentially; once a node has completed its task then the next one
starts.
+ ParallelRunner - runs your nodes in parallel; independent nodes are able to run at the same time, which
is more efficient when there are independent branches in your pipeline and allows you to take advantage of
multiple CPU cores.
+ ThreadRunner - runs your nodes in parallel, similarly to ParallelRunner, but uses multithreading instead
of multiprocessing.

`kedro run --parallel`  
`kedro run --runner=ThreadRunner`  (with remote execution engines such as Spark and Dask)   
`kedro run --runner=module.path.to.my.runner`  

#### Slice a pipeline
`kedro run --pipeline=ds`

#### Share a pipeline
Save:  
`kedro viz --save-file my_shareable_pipeline.json`

Load:  
`kedro viz --load-file my_shareable_pipeline.json`




### Package the project


### Namespace
