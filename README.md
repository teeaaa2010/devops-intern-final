# DevOps Intern Final Assessment

**Name:** Taoheed 
**Date:** August 19, 2026  
**Repository:** [teeaaa2010/devops-intern-final](https://github.com/teeaaa2010/devops-intern-final)  


## Overview

This project repository demonstrates an end-to-end Devops pipeline integrating Linux administration, Docker containerization, 
GitHub Actions CI/CD automation, Nomad job scheduling, and log aggregation using Grafana Loki.


## 1. Git & GitHub Setup

I initialized the project repository and configured my Git credentials:

git config --global user.name "Taoheed"
git config --global user.email "teeaaa2010@gmail.com"

<img width="1470" height="956" alt="IFQ Github showing files" src="https://github.com/user-attachments/assets/1aebe794-f86a-46b1-8b9c-f630d4af2c7d" />


## 2. Linux Scripting

I created a system information script: scripts/sysinfo.sh that gathers system diagnostic data that includes details of the current user, 
system date and time, and disk space utilization.

<img width="1470" height="956" alt="IFQ Linux script" src="https://github.com/user-attachments/assets/00129aba-a3c4-45fa-ad24-bb527f6cb705" />

How To Run The Script:

# Make the script executable:
    chmod +x scripts/sysinfo.sh

# Execute the script:
    ./scripts/sysinfo.sh


## 3. Docker Containerization

I containerized the Python application: hello.py, using Python base image:

    FROM python:3.12-slim
    WORKDIR /app
    COPY hello.py .
    CMD ["python3", "hello.py"]

How To Run The Docker Container:

# Build the Docker image

    docker build -t devops-assessment .

# Run the container locally

    docker run --name devops-assessment-container devops-assessment

<img width="1470" height="956" alt="IFQ Docker image built" src="https://github.com/user-attachments/assets/374da8ea-98da-487e-b4e4-3c86ce0ae655" />


## 4. CI/CD with GitHub Actions

I configured CI using: .github/workflows/ci.yml. 
This workflow triggers automatically to build and run the application whenever code is pushed to the repository.

The Configuration:

    name: Python Application CI

    on: [push]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v3
          - name: Set up Python
            uses: actions/setup-python@v4
            with:
              python-version: '3.12'
          - name: Run Application
            run: python hello.py


How to Run The Workflow

Once you make a change or commit to the repository locally, add and push the commit to GitHub.

# Stage the file using:

    git add .

# Commit the file using:

    git commit -m "Trigger CI pipeline"

# Push to Github using:

    git push origin main

<img width="1470" height="956" alt="Workflows" src="https://github.com/user-attachments/assets/31a55f29-8cf6-4d49-b87f-20425b131803" />


## 5. Job Deployment with Nomad

I configured a Nomad service job specification in: nomad/hello.nomad, to manage container hardware resources.

The configuration:

    job "hello-devops" {
      datacenters = ["dc1"]
      type        = "service"

      group "hello" {
        count = 1

        task "hello" {
          driver = "docker"

          config {
            image = "devops-hello:latest"
          }

          resources {
            cpu    = 100
            memory = 128
          }
        }
      }
    }


How To Run:

# Start a local single-node Nomad agent (in development mode)

    sudo nomad agent -dev &

# Validate the job specification syntax

    nomad job plan nomad/hello.nomad

# Submit and run the job

    nomad job run nomad/hello.nomad

# Check deployment status

    nomad status hello-devops


<img width="1470" height="956" alt="Screenshot 2026-08-18 at 23 58 43" src="https://github.com/user-attachments/assets/3f82b16f-1ab7-4d15-9609-8b9d70880354" />


# 6. Monitoring with Grafana Loki
I integrated Grafana Loki with the Docker engine via loki-docker-driver to collect and centralize application container logs.

How To Run:
# Install and enable the Loki Docker Logging Plugin

    docker plugin install grafana/loki-docker-driver:latest --alias loki --grant-all-permissions

# Run Loki server in Docker

    docker run -d --name loki -p 3100:3100 grafana/loki:latest

# Verify Loki API availability

    curl -s http://localhost:3100/ready

# Run application container with Loki logging driver

    docker run --log-driver=loki --log-opt loki-url="http://localhost:3100/loki/api/v1/push" devops-assessment

# Query ingested container logs via Loki API

    curl -s http://localhost:3100/loki/api/v1/label/container_name/values



<img width="1470" height="956" alt="Screenshot 2026-08-19 at 18 34 31" src="https://github.com/user-attachments/assets/f8f4091d-d179-44ed-b77a-4eb7bd0820a9" />






