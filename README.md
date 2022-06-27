# db_mlops

This directory contains an ML project based on the
[Databricks MLOps Project Template](https://github.com/databricks/mlops-project-template).

## ML pipeline structure
By default, the project contains an ML pipeline with CI/CD workflows to test and deploy
automated model training and batch inference pipelines across your dev, staging, and prod Databricks
workspaces:

![MLOps project template diagram](./doc-images/mlops-template-summary.png)

See the [template README](https://github.com/databricks/mlops-project-template#ml-pipeline-structure-and-devloop) for additional details.

## Project structure
```
├── README.md          <- README for developers using this project
│
├── steps              <- Python modules implementing ML pipeline logic, e.g. model training and evaluation. Most
│                         development work happens here.
│
├── notebooks          <- Databricks notebooks that call into the pipeline step modules under `steps`. Used to
│                         drive code execution on Databricks for CI/CD. In most cases, you do not need to modify
│                         these notebooks.
│
├── .github            <- Configuration folder for CI/CD using GitHub Actions. The CI/CD workflows run the notebooks
│                         under `notebooks` to test and deploy model training code
│
├── databricks-config  <- ML resource (ML jobs, MLflow models) config definitions expressed as code, across staging/prod
│   ├── staging
│   ├── prod
│
├── requirements.txt   <- Specifies Python dependencies for ML code (model training, batch inference, etc) 
│
├── tests              <- Tests for the modules under `steps`
```

## Getting started

Data scientists can get started right away iterating on and testing ML code under ``steps``.

We expect most development to take place  in `steps/train.py` (model training) and `steps/ingest.py`
(data loading and preprocessing). Search for TODOs in the code for pointers to the specific code to edit.

### Developing on Databricks
You can iterate on ML code using [Repos](https://docs.databricks.com/repos/index.html). The provided
code examples require Databricks Runtime ML versions 10.5 and above. Using Databricks Repos also requires that you
push the template to a hosted Git repo and [set up git integration](https://docs.databricks.com/repos/set-up-git-integration.html).

If you'd like to iterate in your IDE but run code on Databricks, consider using the experimental
[dbx sync](https://dbx.readthedocs.io/en/latest/cli.html#dbx-sync) tool.

We recommend keeping your Python code modularized in unit-testable helper functions under ``steps``, iterating on the content
of functions and importing & invoking them in the provided notebooks for interactive development. This allows for
fast iteration while preserving testability of your ML code.

### Developing locally
You can also iterate on ML code locally. Be sure to install Python dependencies for local iteration
via `pip install -r requirements.txt -r test-requirements.txt`. You can then run unit tests for your ML code
via `pytest tests`

To configure your local environment to log to a Databricks MLflow tracking server, follow [this guide](https://docs.databricks.com/applications/mlflow/access-hosted-tracking-server.html).

## Productionizing your ML project
After you've explored and validated the ML problem at hand, you may be ready to start productionizing your ML pipeline.
To do this, you or your ops team must follow the steps below:

### Create a hosted Git repo
Create a hosted Git repo to store project code, if you haven't already done so. From within the project
directory, initialize git and add your hosted Git repo as a remote:
```
git init --initial-branch=main
git remote add upstream <hosted-git-repo-url>
```

Commit the current README file to the `main` branch of the repo, to enable forking the repo:
```
git add README.md doc-images
git commit -m "Adding project README"
git push upstream main
```

### Configure CI/CD
If using GitHub Actions, follow the guide in [.github/workflows/README.md](.github/workflows/README.md) to
configure and enable CI/CD for the hosted Git repo created in the previous step. If using another CI/CD provider,
configure it at this point. 

### Merge a PR with your initial ML code
Open a PR adding the ML code to the repository. We recommend including all files outside of `databricks-config`
in this PR, e.g via `git add -- . ':!databricks-config'`

CI will run to ensure that tests pass on your initial ML code. Get your PR reviewed and merged

### Deploy ML resources and enable production jobs
Follow the instructions in [databricks-config/README.md](databricks-config/README.md) to deploy ML resources
and production jobs.
