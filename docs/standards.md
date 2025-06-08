
# Whale-net app standards

## Technologies

- Postgres for DB
- RabbitMQ for messages/queues/events
- Temporal for job engine
- Opensearch for Logging/Tracing
    - otel collector 
- Keycloak for AuthCZ
- Kubernetes for deployment
    - Helm for manifests
    - ArgoCD for applying manifests
    - Tilt for local development
    - (partial support) apisix for gateway

### Gaps in technology
- Metrics

## Application Definition Standards

### K8s labels+annotations and such
environment -> application -> component -> deployable
- environment (APP_ENV)
    - usually `dev` or `prod`, but can be arbitrary
    - not enough to standardize 
- application (`app.kubernetes.io/part-of`)
    - name of app.
    - e.g. FCM or ManMan
- component (`app.kubernetes.io/component`)
    - the component within the app
    - e.g. host or worker in manman
- deployable (`app.kubernetes.io/name`)
    - the deployable
    - e.g. status service, or host api in manman

Although suggested, I am exlcuding instance and version. 
I don't know how to set version properly and don't want to figure it out. 
instance is not useful for me yet

 
### Application Types
Honestly not really standardized, but here are the current types of deployed apps

- API
- PubSub/EDA
- Temporal Worker
- generic headless
    - taskpool in fcm
    - slack bolt socket app in fcm

### Env Vars
- APP_ENV

## Practices

### Postgres
- Do not share databases between apps. Use API to share data

### API
- OpenAPI specs + code gen. Don't check in generated code

### RMQ
- create vhost per env+app
- external apps can enter vhost and create queues
- TODO - update queue naming scheme
- TODO - routing key naming scheme

### Temporal
- namespaces
- queue naming
- 

### Release + Deploy
- standard docker containers (not OCI, not OS-less)
- GHCR for hosting artifacts
- Github actions for building and testing
- Github release tag + actions for updating release tags
    - NOTE: every build on main should publish an image with the commit sha for argo deployment 
- ArgoCD
    - private gitops repo
    - specify version using ref
    - helm charts directly pulled from git repo, no helm repo/page publish
    - dev env usually deploys from main
    - prod env usually deploys wildcard semver `0.*.*` or `0.2.*` depending on risk

 ### Versioning
 
 most basic SemVer `v1.2.3`

semver rules I try to abide to:
- no breaking changes without incrementing major
- breaking changes allowed when major <1
- features should usually increment minor
- smaller features + everthing else increment patch

patch + minor are pretty interchangeable before major 1

