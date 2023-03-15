---
sidebar_position: 2
---


# How to deploy

The team decides on the following strategy for managing database versioning: Every time they need to test a new version of their service and/or database, they want to create a new MySQL cluster and a new deployment into their dev cluster (this can be auto-triggered based on a PR in Github). The new MySQL database is a replica of the main database with any necessary migrations done to reflect the updates. They want to QA this new database / service pair and move it through dev, staging, and into production. How can they do this?

## Software Components

To better iterate on the code changes and deploy process, it's important to break up the above topology into some generalized components in order to have a precise understanding of the mechanics and technologies that can be used to speed up the development process.

- AWS Infrastructure Changes - Use terraform
- Database Schema Changes - use goose migration 
- Application Code Changes - Package through ci/cd Deploy step
- Docker Image Changes - Package through ci/cd Build step
- Kubernetes Infra Changes - Kubectl command via a deploy script

## Deployment  

1. Create a new database instance: Whenever the team needs to test a new version of their service and/or database, they can create a new database instance from a snapshot of the production database. This can be done using Terraform.


Sample Terraform Code:

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

2. Migrate Data: The team can then migrate any necessary data from the existing database to the new instance using a tool like Goose, Flyway or Liquibase. Follow the hands-on Goose workshop.

3. Deploy to Dev Environment: This can be done through deploy scripts, Terraform and CI/CD. We can create a deployment book with all the steps required to deploy. They should be identical in all environments. We will continuously improve the deploy process by identifying pain points.

4. Test and Validate: To begin with, the team may choose to conduct manual tests to gain a better understanding of the new service and database instance. Based on the results of these tests, they can identify areas that are error-prone or tedious and create an automated testing framework or tool to streamline the testing process.

For data validation, I highly recommend using an open-source tool called Great Expectations. Great Expectations provides a wide range of features and tools that can help automate the testing, profiling, and documentation of your data, making it an ideal solution for data engineers and data scientists.

Here is a sample Python code that leverages Great Expectations to check if a specific table exists in the database:

```python
from great_expectations.datasource import Datasource
from great_expectations.dataset import SqlAlchemyDataset
from great_expectations.core import ExpectationSuite

# Create a datasource object for the PostgreSQL database
postgres_ds = Datasource(
    "my_postgres_db",
    engine={
        "url": "postgresql://user:password@host:port/db_name"
    }
)

# Load a dataset corresponding to the table of interest
my_table = SqlAlchemyDataset(
    table_name="my_table_name",
    engine=postgres_ds
```