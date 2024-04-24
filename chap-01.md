# 1 (The rise of) platforms on top of Kubernetes

## 1.1 What is a platform, and why do I need one?

### Cloud services and domain-specific needs

| The stack: Manage or buy?             |     | Data Center Hosted | **IaaS** |     | **PaaS** |     | **SaaS** | AWS Computing examples | Other examples                 | Business model | Notes                                                |
| ------------------------------------- | --- | ------------------ | -------- | --- | -------- | --- | -------- | ---------------------- | ------------------------------ | -------------- | ---------------------------------------------------- |
| Application                           | 🔟  | M                  | M        | M   | M        | M   | 💸       |                        | Amazon (the bookstore)         |                |                                                      |
| (_Function_)                          |     | M                  | M        | M   | M        | M   | 💸       | Lambda                 | Cloudflare Worker (Serverless) | FaaS           |                                                      |
| Runtime (Database, Message Broker...) | 🛣️  | M                  | M        | M   | M        | 💸  | 💸       |                        | RDS, Dynamo (Database)         |                |                                                      |
| Container                             | 🐋  | M                  | M        | M   | 💸       | 💸  | 💸       |                        |                                |                |                                                      |
| (~ _Container Runtime_)               |     |                    |          | 💸  |          |     |          | ECS, EKS, Fargate      | OpenShift                      | CaaS           | Organization Platform (including operation tools...) |
| OS                                    | 🐧  | M                  | M        | 💸  | 💸       | 💸  | 💸       |                        |                                |                |                                                      |
| Virtualization                        | 📦  | M                  | 💸       | 💸  | 💸       | 💸  | 💸       | EC2                    |                                |                |                                                      |
| Servers                               | 🖥️  | M                  | 💸       | 💸  | 💸       | 💸  | 💸       |                        |                                |                |                                                      |
| Infrastructure                        | 🌐  | M                  | 💸       | 💸  | 💸       | 💸  | 💸       |                        |                                |                |                                                      |
| Facilities                            | 🏠  | 💸                 | 💸       | 💸  | 💸       | 💸  | 💸       |                        |                                |                |                                                      |

### Your job as an organization

Your organization needs to go through a learning curve and mature its practices around apply the tools, standards, workflows... to solve problems.

An organization platform

- allows the organization's development teams to
  - build complex systems for that organization
  - deliver new features to their customers.
- can works
  - on-premises or
  - with any cloud providers.
- is about system integrations, best practices, composable services to build software.

### Working with cloud platforms

All cloud providers provide their services using an API-first approach.

They also offers:

- SDK to be used in applications.
- CLI tool to be used in automation pipelines.
- dashboard that offer reporting, billing...

> [!CAUTION]
> There is no standard across cloud providers for their APIs, SDKs, CLIs, dashboards.

### Example: GCP dashboard, CLIs, and APIs

### Why do cloud providers work?

By offering their services using an API-first approach with dashboards, cloud provider allows any organization to:

- Build their golden paths to production (as code).
- Have a quick look of their production environment: operations, costs...

## 1.2 Platforms built on top of Kubernetes

Kubernetes (k8s):

- is a declarative system for cloud-native applications
- has a set of building blocks that allows us to run & deploy our workloads

Although there is no standard across cloud providers for theirs APIs, every major cloud providers offers Kubernetes-managed services, which allows us to use a standardized way of:

- packaging (with containers)
- deploying workloads.

But only Kubernetes is not enough. The building blocks of Kubernetes are very _low level_ and designed to be composed, _extended_ to build **platforms** that solve domain-specific challenges.

Kubernetes is a similar to a cloud provider that provide us the APIs, the CLI, a dashboard. K8s has its

- APIs `Kubernetes API`
- CLI `kubectl`
- [Web UI Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)

but K8s can also be extended (via its `Kubernetes API`, internal mechanisms) to build platforms that solve generic challenges:

- CI/CD
- Provision cloud resources
- Monitoring, observability
- Developer experience
- ...

### The Kubernetes adoption journey

Kubernetes by itself presents a _steep learning curve_, and when supplemented with additional tools and the necessary integration code, **complexity** can escalate rapidly.

The platform team needs to

- **abstract** these complexity from the consumers: development teams, testing teams, operation teams...
- into a clear **contract** that specify what the platform can do a team
- and exposed that contract as **APIs** that other teams can interact
  - programmatically
  - via automation
  - via a dashboard

In the journey of adopting Kubernetes, the platform teams may extend it with a lot of tools. Just remember that the cost of adopting a tool is not cheap.

For each new tool that is introduced to extend Kubernetes:

- Others teams needs to be trained about when to use that tool, how it work, how to use it...
- The operation teams will need to run it on production for a very long time.
- The platform team also need to decide whether to
  - use a existing tools in the CNCF landscape
  - or write a more tailored solution and maintain it internally.

A typical Kubernetes adoption journey toward platform engineering looks like this:

- [x] Adopt Kubernetes to run workloads.
- [ ] Researching and select tool in [CNCF Landscape] for your platform
- [ ] Configure, write glue code, make these tools work
- [ ] Hide these complexity behind a friendly platform APIs

  - [ ] Encode the knowledge about these tools into _workflows_ that other teams need to be productive.
  - [ ] (Optional) Use the Kubernetes API to provide a declarative approach
  - [ ] (Optional) Some platforms hide that the platform itself uses Kuberneets to reduce the cognition load from teams that use it.

### The [CNCF Landscape] puzzle

The CNCF Landscape is continuously expanding and evolving.

CNCF follows the public, _community-driven maturity model_. Each CNCF project must moving through the _maturity levels_[^cncf-project-metrics]:

- sandbox
- incubating
- graduated

Most cloud providers are now

- involved in CNCF projects,
- working on tools that can be used across cloud providers, removing barriers, allowing innovation in the open.
- collaborate to develope new tools

All CNCF projects work with Kubernetes, extending it to solve high-level challenges close to the development teams.

## 1.3 Platform engineering

Cloud providers have internal teams that define new services:

- how they scale
- which tools/APIs exposed to customers

that allow cloud users (development teams) be more productivity.

An organization can also have their internal _platform engineering teams_ that help:

- solve **software delivery** problems
- speed up the processes, workflows

by collaborate with

- development teams,
- operation teams,
- cloud provider experts

to

- decide the tools
- make platform-wide decisions

A dedicated platform engineering team comes with a key cultural change (promoted by the book [Team Topologies](https://teamtopologies.com/)):

- treat the platform itself as and internal product
- treat the development teams as customers

This cultural can

- improve the entire organization software delivery practices
- add visibility to the who process, help the entire organization understand and see:
  - how teams produce new features
  - when those features will be available to our end users

### Why can’t I just buy a platform?

There are some off-the-shelf platforms (a.k.a _Kubernetes **distribution**_) in the markets:

- Commercial:

  - [Red Hat OpenShift](https://www.redhat.com/en/technologies/cloud-computing/openshift)
  - [VMware Tanzu](https://tanzu.vmware.com/tanzu)
  - [Amazon EKS-D](https://github.com/aws/eks-distro): used by Amazon EKS

- Open-source:
  - `k3s`: CNCF sandbox project
  - `k0s`: From Lens (now Mirantis)
  - `microk8s`, `Charmed Kubernetes`: From Canonical

These [Kubernetes distributions](https://landscape.cncf.io/guide#platform--certified-kubernetes-distribution) cover most of tools: CI/CDs, operations, developer tooling, frameworks..., which:

- for a small organization, can save a lot of time for platform teams (although the cost might be too high)
- can limit the number of choices the platform can make
- may not fit your organization's needs

## 1.4 The need for a walking skeleton

In the Kubernetes ecosystem, it's common to need to integrate at lease 10+ projects/framework to deliver a simple proof of concept (PoC).

> [!TIP]
> When experiment with a CNCF project, use a PoC to validate your understanding of:
>
> - how it works
> - how it will save you/your team time

"walking skeleton"
: ~ "proof of concept", "demo application"

The main purpose of the "walking skeleton" is

- to highlight how to solve a specific problems from 2 perspectives:

  - _architectural_
  - _delivery practice_

- provide a MVP that can deploy to a production environment where you can improve it

> [!IMPORTANT]
> The technology stack used to build the "walking skeleton" is unimportant.
>
> It's more important to understand:
>
> - How the pieces fit together
> - What tools, practices that allow each team (behind a service) to evolve safely, efficiently.

### Building a Conference application

An overview of the Conference application is available [here](https://github.com/salaboy/platforms-on-k8s/tree/main/chapter-1#conference-application-scenario)

The Conference application has a microservices architecture. It's a distributed set of services:

- Frontend:
  - NextJS client-side application
  - Backend of the Frontend: NextJS server
- Agenda service
- Call for Proposals (C4P) service
- Notification service

Each services provides has the resources needed to _independently_ deploy to your environment.

### Differences between a monolith and a distributed set of services

In a monolith application:

- There are no explicit requirements for
  - strong interfaces between internal services.
  - well-encapsulated modules
- It's easier to develope and operate.

with the cost of:

- All the logic to implement different use cases are bundled together
- Different teams work on the same codebase.
  Teams need to have complex collaboration practice to avoid conflicting changes.
- The entire monolith application can go down.

In a cloud-native applications:

- Services can evolve independently.
  - Team can go faster.
  - There is no bottleneck at the codebase level.
- Application can scale at the service level
- Each service can be built using a different programming, framework, version...
- A service can go down without effecting the whole application.
  The platform can even try to self-heal your application.

with the cost of:

- A much more complex system (it's distributed system)

### Our walking skeleton and building platforms

In this book, the _platform_

- is built for domain-specific purpose - not a generic ones - the "walking skeleton".
- provides tools, workflows to deliver the "waling skeleton".

With the "walking skeleton", we will:

| Chapter | Chapter Name                                                         | What to do?                                                                                                                               |
| ------- | -------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| 2       | How workloads run on K8s ? Challenges?                               | Get it up & running, and analyze the challenges when running workloads on top of Kubernetes.                                              |
| 3       | Service pipelines: Building cloud-native application                 | Add new features, create a release, deploy that new version to a live environment                                                         |
| 4       | Environment pipelines: Deploying cloud-native application (~ GitOps) | Configure, deploy applications across different environments, e.g. dev, test, staging, production                                         |
| 5       | Multi-cloud infrastructure (for app)                                 | Provision the infrastructure component: databases, message brokers, identity services... on multi-cloud (with Kubernetes-native approach) |
| 6       | Platform on top of K8s                                               | Abstract all the complexity, tools so other teams can do their job                                                                        |
| 7       | Platform that share application concerns                             | Decouple the components and expose them as a standardized APIs                                                                            |
| 8       | Platform that enable teams to experiment                             | Implement different release strategies so teams can have multiple versions running                                                        |
| 9       | Measuring your platform                                              | Using DORA metrics to understand: 1. How well the organization is delivering software; 2. How the platform can improve it?                |

## Summary

- (Cloud) platforms provide

  - pay-as-you-go model to consume hardware & software.
  - a set of services (for teams) to build their domain specific applications.
  - 3 main features: APIs, dashboards, SDKs that fit into different workflows (of different teams).

- K8s provides building block to build platforms:

  - fit into organization needs
  - independent of cloud providers

- Cloud Native & Computing Foundation (CNCF) promotes/fosters collaborations between open-source projects in the cloud-native space.

- **Platform engineering** (on Kubernetes) helps

  - choose the tools/frameworks/practices
  - manage the complexity

  so other teams can be more efficient at _delivering software_ (that run on top of Kubernetes)

[CNCF Landscape]: https://landscape.cncf.io

[^cncf-project-metrics]: https://www.cncf.io/project-metrics/
