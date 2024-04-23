# Table of Content

## 1 (The rise of) platforms on top of Kubernetes

### 1.1 What is a platform, and why do I need one?

#### Cloud services and domain-specific needs

#### Your job as an organization

#### Working with cloud platforms

#### GCP dashboard, CLIs, and APIs

#### Why do cloud providers work?

### 1.2 Platforms built on top of Kubernetes

#### The Kubernetes adoption journey

#### The CNCF Landscape puzzle

### 1.3 Platform engineering

#### Why can’t I just buy a platform?

### 1.4 The need for a walking skeleton

#### Building a Conference application

#### Differences between a monolith and a distributed set of services

#### Our walking skeleton and building platforms

## 2 Cloud-native application challenges

### 2.1 Running our cloud-native applications

#### Choosing the best Kubernetes environment for you

#### Installing the walking skeleton

### 2.2 Installing the Conference application with a single command

#### Verifying that the application is up and running

#### Interacting with your application

### 2.3 Inspecting the walking skeleton

#### Kubernetes deployments basics

#### Exploring deployments

#### ReplicaSets

#### Connecting services

#### Exploring services

#### Service discovery in Kubernetes

#### Troubleshooting internal services

### 2.4 Cloud-native application challenges

#### Downtime is not allowed

#### Service’s resilience built-in

#### Dealing with the application state is not trivial

#### Dealing with inconsistent data

#### Understanding how the application is working

#### Application security and identity management

#### Other challenges

### 2.5 Linking back to platform engineering

## 3 Service pipelines: Building cloud-native applications

### 3.1 What does it take to deliver cloud-native applications continuously?

### 3.2 Service pipelines

### 3.3 Conventions that will save you time

### 3.4 Service pipeline structure

#### Service pipeline in real life

#### Service pipeline requirements

#### Opinions, limitations, and compromises around service pipelines

### 3.5 Service pipelines in action

#### Tekton in action

#### Pipelines in Tekton

#### Tekton advantages and extras

#### Dagger in action

#### Should I use Tekton, Dagger, or GitHub Actions?

### 3.6 Linking back to platform engineering

## 4 Environment pipelines: Deploying cloud-native applications

### 4.1 Environment pipelines

#### How did this work in the past, and what has changed lately?

#### What is GitOps, and how does it relate to environment pipelines?

#### Steps involved in an environment pipeline

#### Environment pipeline requirements and different approaches

### 4.2 Environment pipelines in action

#### Creating an Argo CD application

#### Dealing with changes the GitOps way

### 4.3 Service + environment pipelines

### 4.4 Linking back to platform engineering

## 5 Multi-cloud (app) infrastructure

### 5.1 The challenges of managing infrastructure in Kubernetes

#### Managing your application infrastructure

#### Connecting our services to the newly provisioned infrastructure

#### I’ve heard about Kubernetes operators. Should I use them?

### 5.2 Declarative infrastructure using Crossplane

#### Crossplane providers

#### Crossplane compositions

#### Crossplane components and requirements

#### Crossplane behaviors

### 5.3 Infrastructure for our walking skeleton

#### Connecting our services with the new provisioned infrastructure

### 5.4 Linking back to platform engineering

## 6 Let’s build a platform on top of Kubernetes

### 6.1 The importance of the platform APIs

#### Requesting development environments

### 6.2 Platform architecture

#### Platform challenges

#### Managing more than one cluster

#### Isolation and multi-tenancy

### 6.3 Our platform walking skeleton

#### vcluster for virtual Kubernetes clusters

#### The platform experience

### 6.4 Linking back to platform engineering

## 7 Platform capabilities I: Shared application concerns

### 7.1 What are most applications doing 95% of the time?

#### The challenges of coupling application and infrastructure

#### Service-to-service interaction challenges

#### Storing/reading state challenges

#### Asynchronous messaging challenges

#### Dealing with edge cases (the remaining 5%)

### 7.2 Standard APIs to separate applications from infrastructure

#### Exposing platform capabilities challenges

### 7.3 Providing application-level platform capabilities

#### Dapr in action

#### Dapr in Kubernetes

#### Dapr and your applications

#### Feature flags in action

#### Updating our Conference application to consume application-level platform capabilities

### 7.4 Linking back to platform engineering

## 8 Platform capabilities II: Enabling teams to experiment

### 8.1 Release strategies fundamentals

#### Canary releases

#### Blue/green deployments

#### A/B testing

#### Limitations and complexities of using built-in Kubernetes building blocks

### 8.2 Knative Serving: Advanced traffic management and release strategies

#### Knative Services: Containers-as-a-Service

#### Advanced traffic-splitting features

### 8.3 Argo Rollouts: Release strategies automated with GitOps

#### Argo Rollouts canary rollouts

#### Argo Rollouts blue/green deployments

#### Argo Rollouts analysis for progressive delivery

#### Argo Rollouts and traffic management

### 8.4 Linking back to platform engineering

## 9 Measuring your platforms

### 9.1 What to measure: DORA metrics and high-performant teams

#### The integration problem

### 9.2 How to measure our platform: CloudEvents and CDEvents

#### CloudEvents for continuous delivery: CDEvents

#### Building a CloudEvents-based metrics collection pipeline

#### Data collection from event sources

#### Knative Eventing event sources

#### Data transformation to CDEvents

#### Metrics calculation

#### Working example

### 9.3 Keptn Lifecycle Toolkit

### 9.4 What’s next on the platform engineering journey?

### 9.5 Final thoughts
