# Summary

Welcome to an opinionated tutorial on release management!

Remember to be patient with yourself, use pair programming sessions generously, and do not worry about asking for or getting clarifications on anything that is not clear. Remember that many of these concepts are abstractions and the situation will dictate how we can proceed. It's okay for us to build new abstractions and ideas as the codebase and team dynamics evolve.

## Topology
```
Version 1.0

                                      +-------------+
                                      |AWS API Gateway|
                                      +------+------+
                                             |
                                             |
                                      +------v------+
                                      |    Ingress  |
                                      +------|------+
                                             |
                                             |
                                     +-------v------+
                                     |Kubernetes  |
                                     |   Cluster  |
                                     +-------|------+
                                             |
                      +----------------------+----------------------+
                      |                                              |
             +--------v-----+                   +--------v-----+    +-------------+
             |Managed MySQL  |                   | Managed MySQL |    |   Route 53  |
             |     RDS       |                   |     RDS       |    +------+------+
             +---------------+                   +---------------+           
```


Based on the given topology and components, I would recommend the following for the service and database versioning:

## a. Rollback strategy

Before releasing a new version of software, it's important to have a rollback strategy in place in case something goes wrong. If the team needs to do a rollback, they should have a way to identify the version that was previously deployed, and then redeploy it. To avoid this situation, the team should follow a branch cut approach, where they cut a branch for a feature release and deploy it. This will simplify testing and make it easier to identify which version caused the issue. They should also ensure that database changes are kept backwards compatible, so that they don't have to roll back the database as well.


- Branch cut for feature releases
- Keep database changes backwards compatible


## b. Versioning

 The team should version their software using Semantic Versioning, which consists of three numbers separated by dots (e.g., 1.2.3), where the first number is the major version, the second is the minor version, and the third is the patch version. They should reflect these versions in their API, code, builds, CI/CD system, etc. For example, they can use Git tags to tag their code with the version number, and include the version number in the API endpoint. They should also ensure that the versioning is consistent across all components, including the application code, database schema, Docker images, and Kubernetes infrastructure.



|Types|Technologies|
|-----------|-----------|
|Application Code:   |  Semantic Versioning (e.g., 1.2.3)|
|Database Schema:   |   sequential ordering (1, 2, 3, 4) |
|Docker Images:      |  Semantic Versioning (e.g., 1.2.3)|
|Kubernetes Infra:    | Semantic Versioning (e.g., 1.2.3)|

[Click here for Further ideas on Versioning](versioning)



## c. Monitoring

 To maintain their SLA, the team should use a combination of logs, metrics, user events, and distributed tracing. For log monitoring, they can consider using the ELK stack, which provides centralized log management and analysis, and allows for customized dashboards and real-time log queries. For metrics, they can use Prometheus and Grafana, which provide real-time alerts for issues like high response times, error rates, and CPU/memory utilization. For user events, they can use a custom logging framework to capture events like user sign-ups, logins, actions, and errors, and then visualize them with Apache Superset. Finally, for distributed tracing, they can use tools like Jaeger or Zipkin to monitor service-to-service communication and identify performance bottlenecks.


|Types|Technologies|
|-----------|-----------|
|Logs| ELK stack (Elasticsearch, Logstash, Kibana)|
|Logs:             |    ELK stack (Elasticsearch, Logstash, Kibana)|
|Metrics:           |   Prometheus and Grafana|
|User Events:        |  Custom logging framework + Apache Superset|
|Distributed Tracing:|  Jaeger or Zipkin|


[Click here for Further ideas on Monitoring/Observability](observability)