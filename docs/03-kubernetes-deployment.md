---
sidebar_position: 3
---

# MySQL on Kubernetes 

Hosting a database directly within Kubernetes has its advantages and disadvantages. One of the benefits of running a database on Kubernetes is that it allows for better integration with other Kubernetes-managed services, enabling a more streamlined deployment process. Additionally, Kubernetes provides features like automatic failover and scaling, which can make managing the database easier. On the other hand, hosting a database on Kubernetes can also introduce complexity and require specialized knowledge to manage effectively. It can be challenging to ensure that the database is properly configured and optimized for Kubernetes, and it may be more difficult to perform backups and manage database replicas.

## The good 

- Simplified management: By hosting the database directly within Kubernetes, the team can leverage the same infrastructure and tooling they are already using for their application, making management simpler and more streamlined.
- Customizability: Hosting the database within Kubernetes allows for more customization and control over the database environment.
- Scalability: Kubernetes makes it easy to scale the database deployment up or down as needed, providing greater flexibility and control.

## The bad
- Complexity: Running a database on Kubernetes introduces additional complexity to the infrastructure, which can lead to more maintenance and troubleshooting work.
- Performance: Kubernetes may not be optimized for database workloads, which can result in performance issues if not configured properly.
- Resources: Running a database on Kubernetes requires more resources than a traditional application, which can lead to increased infrastructure costs.


## The Ugly

In general I would recommend we stick to using AWS managed services for databases since they offer a more reliable and scalable solution. AWS has a number of managed database services that provide automatic backups, scalability, high availability, and built-in security, such as Amazon RDS, Amazon DynamoDB, and Amazon Aurora. While hosting a database directly within Kubernetes may seem like a good idea for some use cases, it requires a lot of overhead and expertise to manage effectively, especially for high-availability and disaster recovery scenarios. Additionally, running a database on Kubernetes can impact the overall performance of the cluster and make it harder to scale and manage other workloads.



 Kubernetes:

 ```
 apiVersion: v1
kind: Service
metadata:
  name: mysql
  labels:
    app: mysql
spec:
  ports:
    - port: 3306
  selector:
    app: mysql
  clusterIP: None
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: mysql
  replicas: 3
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:5.7
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: password
          ports:
            - containerPort: 3306
              name: mysql
          volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
      volumes:
        - name: mysql-persistent-storage
          persistentVolumeClaim:
            claimName: mysql-pvc
  volumeClaimTemplates:
    - metadata:
        name: mysql-pvc
      spec:
        accessModes: [ "ReadWriteOnce" ]
        resources:
          requests:
            storage: 20Gi
```


In terraform we can use AWS managed RDS with the following code:


```
provider "aws" {
  region = "us-west-2"
}

resource "aws_db_instance" "mysql_dev" {
  identifier        = "mysql-dev"
  allocated_storage = 20
  engine            = "mysql"
  engine_version    = "5.7"
  instance_class    = "db.t2.micro"
  name              = "mysql-dev"
  username          = "admin"
  password          = "password123"
}

output "mysql_dev_endpoint" {
  value = aws_db_instance.mysql_dev.endpoint
}

```