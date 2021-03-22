# Example Blue Green Deployment with Argo rollouts and Codefresh


This is an example Java application that uses Spring Boot 2, Maven and Docker.
It is compiled using Codefresh.

## Instructions

To compile (also runs unit tests)

```
mvn package
```

## Create a multi-stage docker image

To compile and package using Docker multi-stage builds

```bash
docker build . -t my-spring-boot-sample
```

## To run the webapp manually

```
docker run -p 8080:8080 my-spring-boot-sample
```

....and navigate your browser to  http://localhost:8080/

The Dockerfile also has a healthcheck

## To run integration tests

```
docker run -p 8080:8080 my-spring-boot-sample
mvn verify
```

## To use with Argo Rollouts

To install the controller

```
kubectl create namespace argo-rollouts
kubectl apply -n argo-rollouts -f https://raw.githubusercontent.com/argoproj/argo-rollouts/stable/manifests/install.yaml
```

To create the first deployment

```
kubectl create ns blue-green
kubectl apply -f ./blue-green-manual-approval -n blue-green
```

To monitor the status

```
kubectl argo rollouts get rollout spring-sample-app-deployment --watch -n blue-green
```

To create a new color

```
kubectl argo rollouts set image spring-sample-app-deployment spring-sample-app-container=kostiscodefresh/argo-rollouts-blue-green-sample-app:9c7732e -n blue-green
```

To promote the color
```
kubectl argo rollouts promote spring-sample-app-deployment -n blue-green
```

To reject or undo the deployment

```
kubectl argo rollouts abort spring-sample-app-deployment -n blue-green
kubectl argo rollouts undo spring-sample-app-deployment -n blue-green
```

To get the IP and port of the preview service

```
kubectl get svc  rollout-bluegreen-preview -n blue-green -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
kubectl get svc  rollout-bluegreen-preview -n blue-green -o jsonpath='{.spec.ports[0].port}'
```

## To use this project in Codefresh 


There is also a [codefresh.yaml](blue-green-manual-approval/codefresh.yaml) for easy usage with the [Codefresh](codefresh.io) CI/CD platform.


More details can be found in [Codefresh documentation](https://codefresh.io/docs/docs/ci-cd-guides/progressive-delivery/)


Enjoy!
