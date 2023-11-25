## Plan:

    Odoo 17.0 (odoo-one, 2 replicas)
      ^
      |
      v
    RabbitMQ
      ^
      |
      v
    Odoo 17.0

+ MailServer - send from one instance to another
## Instructions:
First create postgres container and a data volume for the DB:
```
kubectl apply -f db
```

Next initialize odoo db, volume with proper ownership:
```
kubectl apply -f odoo-one/odoo-pv.yaml -f odoo-one/odoo-migration.yaml
```

Finally run Odoo service:
```
kubectl apply -f odoo-one/odoo-deployment.yaml -f odoo-one/odoo-service.yaml
```

To install a module/run migration:
Modify `odoo-one/odoo-migration.yaml` as needed. Run:
```
kubectl apply -f odoo-one/odoo-migration.yaml
```
When migration is finished, run:
```
kubectl rollout restart -f odoo-one/odoo-deployment.yaml```
```

## TODO:
- rabbigmq's hostname changes on every restart thus the data is not preserved

## What I have learned:
- The docker container of Odoo is run as odoo user, which by default doesn't have access to the volume. Need to change permisions. The easiest way it through initContainers with chown command. This problem is probably specific to the storage type as I use a regular disk.
- For migrations you can use `ghcr.io/groundnuty/k8s-wait-for:v1.6` as initContainer - it will wait until the job is finished.
- It would be best to run Odoo migrations with zero downtime, changes would have to be backward compatible. Otherwise you can run migration in the background, with Odoo pods still running, but it may fail/cause errors in the existing pods. After the background-migration is finished rollout restart with:
```kubectl rollout restart -f odoo-one/odoo-deployment.yaml```
