# Flood Predictor

## Primary developers 
- Mark Bartlett (Mark.BartlettJr@stantec.com) 
- Assaad Mrad (Assaad.Mrad@stantec.com)
- Jinshu Li (Jinshi.Li@Stantec.com)
- Jared Van Blitterswyk (Jared.VanBlitterswyk@Stantec.com)

## Project objective
Machine Learning for Flood Prediction

## Project scope

### Business/Engineering problems solved

- Faster inference for flood predictions recreating FEMA maps
- Go beyond FEMA maps with satellite imagery to train and test
- Achieve high performance benchmark to FEMA maps and independent observations
- Reduce cost

### Metrics for success

- MLE metrics:
    - F1 score
    - Inference time

- Data metrics:
    - Ability to benchmark against satellite observations
    - Train with observations, not just FEMA Maps

- Reproducibility:
    - Ability to recreate data, ML, and deployment stages using pipeline

- Scientific soundness:
    - Data pre-processing - feature engineering based on state-of-the-science literature
    - Publish based on methodology developed for this projects

### Resources

- Microsoft Azure:
    - ML Workspace
        - Track experiments and register models according to the metrics in the `src/models` directory
        - Serve real-time endpoints
    - DevOps
        - Model training repository: DE-Insight-Flood-ML
        - Pipelines repository
    - Databricks
        - Jobs: run training and deployment jobs automatically
        - Feature store (to be used)
    - Blob Storage
        - Critical piece to speed up inference as pre-processed data can be saved
    - Kubernetes (To be implemented)

### Data

- FEMA maps
- Satellite Imagery
    - Planet
    - SWOT

## IT

IT has imposed policies in our Azure resource group which means firewalls and VNets are now in effect. We have found workarounds to work within these constraints:
- We are unable to install Libraries in Databricks clusters using the UI. We can create custom images with required packages pre-installed following https://docs.databricks.com/clusters/custom-containers.html
    - After the image is created, it is uploaded to ACR to which our Databricks worskpace can connect to.
- We are unable to download packages on VMs in the Development subscription. We can create custom Linux images on our machines and then spin up Linux VMs based on those images.
- In the `Development` Azure subscription, Databricks secret scopes are Key-Vault backed (https://docs.microsoft.com/en-us/azure/databricks/security/secrets/secret-scopes). When create the Databricks secret using the UI, use the `Creator` Manage Principal.

## Project organization

    ├── LICENSE
    ├── Makefile           <- Makefile with commands like `make data` or `make train`
    ├── README.md          <- The top-level README for developers using this project.
    ├── docker             <- Docker-compose, Dockerfile, requirements, etc. for the project.
    ├── data
    │   ├── external       <- Data from third party sources.
    │   ├── interim        <- Intermediate data that has been transformed.
    │   ├── processed      <- The final, canonical data sets for modeling.
    │   └── raw            <- The original, immutable data dump.
    │
    ├── docs               <- A default Sphinx project; see sphinx-doc.org for details
    │
    ├── models             <- Trained and serialized models, model predictions, or model summaries
    │
    ├── notebooks          <- Jupyter notebooks. Naming convention is a number (for ordering),
    │                         the creator's initials, and a short `-` delimited description, e.g.
    │                         `1.0-jqp-initial-data-exploration`.
    │
    ├── references         <- Data dictionaries, manuals, and all other explanatory materials.
    │
    ├── reports            <- Generated analysis as HTML, PDF, LaTeX, etc.
    │   └── figures        <- Generated graphics and figures to be used in reporting
    │
    ├── requirements.txt   <- The requirements file for reproducing the analysis environment, e.g.
    │                         generated with `pip freeze > requirements.txt`
    │
    ├── setup.py           <- makes project pip installable (pip install -e .) so src can be imported
    ├── src                <- Source code for use in this project.
    │   ├── __init__.py    <- Makes src a Python module
    │   │
    │   ├── data           <- Scripts to download or generate data
    │   │   └── make_dataset.py
    │   │  
    │   │── deployment     <- Scripts to deploy the model as a service
    │   │   └── deploy_local.py
    │   │   └── deploy.py    
    │   │
    │   ├── features       <- Scripts to turn raw data into features for modeling
    │   │   └── build_features.py
    │   │
    │   ├── models         <- Scripts to train models and then use trained models to make
    │   │   │                 predictions
    │   │   ├── predict_model.py
    │   │   └── train_model.py
    │   │
    │   └── visualization  <- Scripts to create exploratory and results oriented visualizations
    │       └── visualize.py
    │
    └── tox.ini            <- tox file with settings for running tox; see tox.readthedocs.io

