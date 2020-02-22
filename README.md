# MLflow-Basics

### How to create projects in MLflow

Step 1: Install MLflow by ```pip install MLflow.
                            ```
<br>

Step 2: Create a `conda.yaml` file to give all the requirements of the environment on which you want to work. For example

```
name: nameofenvironment
channels:
  - defaults
dependencies:
  - numpy=1.14.3
  - pandas=0.22.0
  - scikit-learn=0.19.1
  - pip:
    - mlflow
```

Step 3: Create a` MLproject` file in the root directory to specify your project. Here you can specify your environment, entry point and also many things for defining your project workflow. For example

```
name: projectname

conda_env: conda.yaml

entry_points:
  main:
    parameters:
      alpha: float
      l1_ratio: {type: float, default: 0.1}
    command: "python train.py {alpha} {l1_ratio}"

```

### That's it for creating the project. Now lets see how we can run the project and visualize logs defined by us, using MLflow UI.

* Run `MLflow run .` command inside your project folder. It will create the environment specified by you and executes the steps defined by you in your `MLproject` file.

* Run `MLflow ui` to see all the logs of parameters and hyperparameters, versions, metrics and artifacts(e.g. model.pkl).

### Now your model is ready to serve. Here is how you can serve your model.

* Run the following command. It will create the model server.

```
mlflow models serve -m path_to_your_saved_model(mlruns/0/experiment/artifacts/model) -p 1234
```

* You can make a CURL request to get the output from your model. For example: 

```
curl -X POST -H "Content-Type:application/json; format=pandas-split" --data '{"columns":["alcohol", "chlorides", "citric acid", "density", "fixed acidity", "free sulfur dioxide", "pH", "residual sugar", "sulphates", "total sulfur dioxide", "volatile acidity"],"data":[[12.8, 0.029, 0.48, 0.98, 6.2, 29, 3.33, 1.2, 0.39, 75, 0.66]]}' http://127.0.0.1:1234/invocations
```

N.B. You can create multiple experiments to compare model performance by the following command:

```
mlflow experiments create --experiment-name="name"

```

* For hyparameter tuning or multi step workflow we can always configure the `MLproject` file and run different experiments.

Examples:

* [Hyperparameter Tuning](https://github.com/mlflow/mlflow/tree/master/examples/hyperparam)
* [Multi-step workflow](https://github.com/mlflow/mlflow/tree/master/examples/multistep_workflow)


### Dockerize your model

Step 1: In the root directory create a `dockerfile` and specify your environment.
Step 2: Build the docker image and give a name.
Step 3: In the `MLproject` file define environment as follows:
```
name: docker-example

docker_env:
  image:  mlflow-docker-example

entry_points:
  main:
    parameters:
      alpha: float
      l1_ratio: {type: float, default: 0.1}
    command: "python train.py --alpha {alpha} --l1-ratio {l1_ratio}"

```

Step 4: Now you can run project using `mlflow run` command.

N.B When we run `mlflow run` it will create a docker image which includes code and all the requirements of our environments and the image 
will be tagged as `mlflow-docker-example-<git-version>`. After the image is built MLflow executes the entry point (specified in `MLproject` file) within the container using `docker run`.

Dockerization of model gives us the flexibility of including non-Python dependencies in our environments unlike conda environment.


## References
[MLflow docs](https://mlflow.org/docs/latest/index.html)



















