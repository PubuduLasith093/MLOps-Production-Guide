# MLOps-Production-Guide

Wanna learn how to deploy ML models to production?

Let's go step-by-step, with a real-world example 🧵↓


𝗘𝘅𝗮𝗺𝗽𝗹𝗲

Imagine today is your first day as an ML engineer at Uber, and your task is to improve the ML service that predicts the Estimated Time of Arrival (ETA), which gives users an estimate of when their driver will arrive.

𝗔𝘀 𝗮 𝘀𝘁𝗮𝗿𝘁𝗲𝗿

You
-> define the problem
-> talk to other data engineers and ML engineers in the team
-> expand the set of model features, and
-> train a promising ML model.

You are happy with the results, so it is time to deploy

So far you have 4 things:

-> the model pickle

-> a predict function, that maps input features to output predictions, using the model artifact.

-> A FastAPI wrapper around your Python code, to build the REST API, and

-> A Dockerfile to package everything in an isolated box.

How do you deploy this code as a production ready API?


𝗦𝗼𝗹𝘂𝘁𝗶𝗼𝗻 -> 𝗧𝗵𝗲 𝗠𝗟𝗢𝗽𝘀 𝘄𝗮𝘆

Let’s go step by step

𝗦𝘁𝗲𝗽 𝟭 → Push code to new branch and open Pull Request (PR)

Use git and Github/Gitlab to track code changes.

I recommend you develop your code in a non-master branch, where you work, and push changes to remote.

Once you are happy with the results, you open a Pull Request


𝗦𝘁𝗲𝗽 𝟮 → Continuous Integration (CI)

The CI pipeline is a github action that is triggered automatically every time you open a Pull Request.

The end goal is to make sure that your new code version, and model artifact are actually good and deserve to be promoted to production.

The CI steps in this case are:

-> Unit tests, e.g. make sure you feature engineering functions work as expected

-> Trains the model and generates the model artifact.

-> Validates the ML model performance and possible biases, using a library like PyTest or Giskard

At the end, the pipeline checks can either

→ fail, so you need to work further on your code, or move to another project with higher priori

→ pass, so your model is ready to be deployed. In this case,

the CI pipeline pushes the model to your model registry, from where it can later be deployed, and

your branch “my_new_model” gets merged to master.


𝗦𝘁𝗲𝗽 𝟯 → Continuous Deployment (CD)

The Continuos Deployment (CD) pipeline is another github action that is triggered automatically after successful completion of the CI pipeline.

The role of the CD pipeline is to deploy the Dockerfile you wrote as a production ready API.

In this case, our github action

-> Pushes the Docker image to Uber’s docker registry,
-> Triggers the deployment to your compute platform, for example
- Kubernetes, with kubectl
- AWS Lambda, or
- Serverless platforms, like Beam or Cerebrium that do not require Docker images.
