# MLflow-Basics

### How to create projects in MLflow

Step 1: Install MLflow by ```pip install MLflow.
                            ```
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

N.B. You can also create [docker environment](https://www.mlflow.org/docs/latest/projects.html#project-environments)

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

### That's it for creating the project. Now lets see how we can run the project and visualize logs defined by us using MLflow UI.

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












