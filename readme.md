# Microservice-Based E-Commerce Platform

## Overview

This project demonstrates a microservices architecture using **gRPC** for inter-service communication and **GraphQL** as the API gateway. It includes services for account management, product catalog, and order processing. The project is built using **Go**, and an older, stable version of Go has been chosen to ensure manageability and ease of maintenance across all components.

## Project Structure

The project consists of the following main components:
- **Account Service**: Manages user accounts and authentication.
- **Catalog Service**: Handles product listings and search functionality.
- **Order Service**: Manages customer orders, including creation and tracking.
- **GraphQL API Gateway**: Provides a unified API interface to interact with all the microservices.

Each service has its own database:
- **Account and Order services** use **PostgreSQL**.
- **Catalog service** uses **Elasticsearch**.

## Getting Started

### Clone the Repository
```bash
git clone <repository-url>
cd <project-directory>
```

### Start the Services
Ensure you have Docker and Docker Compose installed, then start the services with:
```bash
docker-compose up -d --build
```

### Access the GraphQL API
Once the services are running, you can interact with the API using the GraphQL playground:
```
http://localhost:8000/playground
```

## gRPC File Generation

Follow these steps to generate the required gRPC files for the services:

1. Download the protobuf compiler:
   ```bash
   wget https://github.com/protocolbuffers/protobuf/releases/download/v23.0/protoc-23.0-linux-x86_64.zip
   unzip protoc-23.0-linux-x86_64.zip -d protoc
   sudo mv protoc/bin/protoc /usr/local/bin/
   ```

2. Install the Go protobuf and gRPC plugins:
   ```bash
   go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
   go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
   ```

3. Update your `$PATH`:
   ```bash
   echo $PATH
   export PATH="$PATH:$(go env GOPATH)/bin"
   source ~/.bashrc
   ```

4. Create a `pb` folder in your project and add this to your `.proto` file:
   ```proto
   option go_package = "./pb";
   ```

5. Run the following command to generate the gRPC code:
   ```bash
   protoc --go_out=./pb --go-grpc_out=./pb account.proto
   ```

## GraphQL API Usage

The GraphQL API serves as the primary interface for interacting with the microservices. Below are some example queries and mutations:

### Query Accounts
```graphql
query {
  accounts {
    id
    name
  }
}
```

### Create an Account
```graphql
mutation {
  createAccount(account: {name: "New Account"}) {
    id
    name
  }
}
```

### Query Products
```graphql
query {
  products {
    id
    name
    price
  }
}
```

### Create a Product
```graphql
mutation {
  createProduct(product: {name: "New Product", description: "A new product", price: 19.99}) {
    id
    name
    price
  }
}
```

### Create an Order
```graphql
mutation {
  createOrder(order: {accountId: "account_id", products: [{id: "product_id", quantity: 2}]}) {
    id
    totalPrice
    products {
      name
      quantity
    }
  }
}
```

### Query Account with Orders
```graphql
query {
  accounts(id: "account_id") {
    name
    orders {
      id
      createdAt
      totalPrice
      products {
        name
        quantity
        price
      }
    }
  }
}
```

### Advanced Queries

#### Pagination and Filtering
```graphql
query {
  products(pagination: {skip: 0, take: 5}, query: "search_term") {
    id
    name
    description
    price
  }
}
```

#### Calculate Total Spent by an Account
```graphql
query {
  accounts(id: "account_id") {
    name
    orders {
      totalPrice
    }
  }
}
```

## License
This project is open source and available under the MIT License.