# Kubernetes Demo Stack

This is a simple demo stack consisting of a Postgres database and a simple application written using Java Spring.

## Deployment

### Deploying with `docker-compose`

A [`docker-compose`](https://docs.docker.com/compose/) file is [provided](https://github.com/a13xie/docker-java-springboot-postgres-api/blob/main/docker-compose.yml) for quickly testing the application.

Be warned, the compose file does not provide persistance for the database. This can easily be added by adding a [volume](https://docs.docker.com/storage/volumes/#use-a-volume-with-docker-compose) and mounting it to `/var/lib/postgresql/data`.

1. Download the `docker-compose.yml` file: `wget https://raw.githubusercontent.com/a13xie/docker-java-springboot-postgres-api/main/docker-compose.yml`
1. Start the project: `docker compose up -d`

### Deploying to production with Kubernetes

The preferred method for deploying this project for production use is using Kubernetes.

The manifests are located in [this folder](https://github.com/a13xie/k8s-demo/tree/main/manifests).

Once you have cloned this repo, deployment is done using the `kubectl` utility.

```
kubectl apply -f manifests
```

The Postgres manifest includes a Deployment, PVC, Service and a Secret.
 - Depending on your environment, you might need to change the [PVC storage class](https://github.com/a13xie/k8s-demo/blob/main/manifests/postgres.yaml#L53).
 - Do not change the service type, the database should not be accessible outside of the cluster.
 - Do not change the Postgres [password](https://github.com/a13xie/k8s-demo/blob/main/manifests/postgres.yaml#L62) because the demo app has the password hard-coded.

The app's manifest contains a Deployment and a Service.
 - You might want to change the service type to `ClusterIP` and use [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) to publish the service.
 - Depending on the expected traffic, the amount of [replicas](https://github.com/a13xie/k8s-demo/blob/main/manifests/api.yaml#L8) can be increased.

Finally there is an optional manifest for monitoring Postgres.
 - Postgres Exporter connects to the Postgres database and exposes metrics using the Prometheus metric format.
 - This can then be scraped using a scraper of your choice.

If you don't have a Grafana instance yet, a Grafana manifest is included as well.
