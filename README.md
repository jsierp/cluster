## Instructions:
First create postgres container and a data volume for the DB:
```
kubectl apply -f db
```

Next initializie odoo containers, volume with proper ownership:
```
kubectl apply -f odoo-one
```

Odoo will not work yet, because the database has to be initialized.
Currently it has to be done manualy (minikube ssh, docker exec, odoo -i base).


## Take aways
What I have learned:
- The docker container of Odoo is run as odoo 101 group, user which gets is run 

## Plan:

Odoo 17.0 \
  |        \
RabbitMQ    Ingress
  |        /
Odoo 17.0 /

+ MailServer - send from one instance to another
