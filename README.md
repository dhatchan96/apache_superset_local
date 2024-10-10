
# Apache Superset x-ray feature(same as in Metabase) implementatLocal Setup and API Guide

This guide provides instructions for setting up Apache Superset locally and accessing its API to interact with your data. Superset is an open-source data exploration and visualization tool that allows you to build dashboards and perform analysis.

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Setup](#setup)
4. [Accessing the API](#accessing-the-api)
5. [Example API Usage](#example-api-usage)
6. [Contributing](#contributing)
7. [License](#license)

## Introduction

Apache Superset is a modern, enterprise-ready business intelligence web application. This guide will walk you through setting up a local instance of Superset and using its REST API to programmatically manage data sources, create charts, and query data.

## Prerequisites

Before starting, ensure you have the following installed:

1. **Python** (version 3.8 or higher) - [Download Python](https://www.python.org/downloads/)
2. **Node.js and npm** - [Download Node.js](https://nodejs.org/en/download/)
3. **Docker** (optional, for running Superset in Docker) - [Get Docker](https://www.docker.com/products/docker-desktop/)

## Setup

### Method 1: Local Installation

1. Clone the Superset repository:
   ```bash
   git clone https://github.com/apache/superset.git
   cd superset
   ```

2. Install the required dependencies:
   ```bash
   pip install -r requirements.txt
   ```

3. Initialize the database:
   ```bash
   superset db upgrade
   ```

4. Create an admin user:
   ```bash
   export FLASK_APP=superset
   superset fab create-admin
   ```

5. Set up Superset and run the development server:
   ```bash
   superset init
   flask run --host=0.0.0.0 --port=8088
   ```

6. Access Superset in your browser at `http://localhost:8088` and log in with the admin user credentials you created.

### Method 2: Using Docker (Recommended)

1. Navigate to the `superset` directory and start Docker Compose:
   ```bash
   docker-compose -f docker-compose-non-dev.yml up
   ```

2. Wait for Docker to pull and set up all necessary containers. This may take a few minutes.

3. Access Superset at `http://localhost:8088`. Use default credentials (admin/admin) to log in initially, and change the password after logging in.

## Accessing the API

Superset provides a RESTful API for accessing and managing data programmatically. Before you can access the API, you must obtain an access token.

1. **Authenticate and get a token**:
   ```bash
   curl -X POST http://localhost:8088/api/v1/security/login    -H 'Content-Type: application/json'    -d '{"username": "admin", "password": "your_password", "provider": "db"}'
   ```

2. **Extract the access token** from the response, which you’ll use in subsequent API requests.

3. **Make API requests** using the token:
   - Add the token to the `Authorization` header in the format `Bearer <ACCESS_TOKEN>`.

## Example API Usage

### List Available Datasets

```bash
curl -X GET http://localhost:8088/api/v1/dataset/ -H "Authorization: Bearer <ACCESS_TOKEN>"
```

### Create a New Chart

```bash
curl -X POST http://localhost:8088/api/v1/chart/ -H "Authorization: Bearer <ACCESS_TOKEN>" -H "Content-Type: application/json" -d '{
  "slice_name": "My New Chart",
  "viz_type": "pie",
  "params": "{"groupby": ["country"], "metric": "sum__value"}",
  "datasource_id": 1,
  "datasource_type": "table"
}'
```

### Query Data

```bash
curl -X POST http://localhost:8088/api/v1/chart/data -H "Authorization: Bearer <ACCESS_TOKEN>" -H "Content-Type: application/json" -d '{
  "queries": [{
    "metrics": ["sum__value"],
    "columns": ["country"],
    "datasource": {"id": 1, "type": "table"}
  }]
}'
```

## Contributing

Contributions are welcome! Please open an issue or submit a pull request for any improvements or new features.

## License

This project is licensed under the Apache License 2.0. See the [LICENSE](LICENSE) file for details.
