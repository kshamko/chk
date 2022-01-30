# Payment Gateway

- system based on microservices
- main parts of the system:
  - API gateway to communicate with clients
  - AUTH service
  - Transactions service
  - Bank integration service(s). Here we go with the assumption that these services already exist

## High Level Diagram of the System

![high level diagram](./docs/images/highlevel.drawio.png)

## API Gateway

1. API geteway serves all the required endpoint via HTTP
2. API gateway is written in Golang
3. API described with openAPI format
4. *swagger* tool is used to generate server/client code

Please follow the [link](https://github.com/kshamko/boilerplate/tree/master/gateway) to review the code / get more details about the approach 

## API Spec

Please follow the [link](https://kshamko.github.io/chk/) to review swagger spec

## Auth Service

1. gRPC is used as communication protocol
2. Auth Service is written in Golang
3. API described with protofile

Please follow the [link](https://github.com/kshamko/boilerplate/tree/master/grpc) to review the code / get more details about the approach 

### Auth Service Architecture Diagram

![auth diagram](./docs/images/authService.drawio.png)

### Auth Service API spec

```proto
message AuthRequest {
    CreditCard CC = 1;
    Amount Amnt = 2;
}

message CreditCard {
    string Num = 1;
    string Name = 2;
    string Date = 3;
    string CVV = 4;
}

message Amount {
    float Amount = 1;
    string Currency = 2;    
}

message Token {
    string Value = 1;    
}

message Resp {
    Token Token = 1;
    Amount Amount = 2;
}

service AuthService {
    rpc Authorize(AuthRequest) returns (Resp);
    rpc Validate(Token) returns (Resp);
    rpc Unauthorize(Token) returns (Resp);
} 
```

## Transactions service

1. gRPC is used as communication protocol
2. Auth Service is written in Golang
3. API described with protofile
4. We need distributed locks system to control concurrent access to particular user's transactions

Please follow the [link](https://github.com/kshamko/boilerplate/tree/master/grpc) to review the code / get more details about the approach 

### Transactions Service Architecture Diagram

![transactions diagram](./docs/images/transactionService.drawio.png)

### Transactions Service API Spec

```proto
message TransactionRequest {
    Token Token = 1;
    Amount Amnt = 2;
}

message Amount {
    float Amount = 1;
    string Currency = 2;    
}

message Token {
    string Value = 1;    
}

message Resp {
    bool OK = 1;
    string TransactionID = 2;
}

service AuthService {
    rpc Transaction(AuthRequest) returns (Resp);
} 
```

## Sequence Diagrams

### Auth

![auth flow](./docs/images/auth.png)

### Transaction

![transaction flow](./docs/images/transaction.png)