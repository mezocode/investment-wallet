# Designing an investment wallet  

![Make it Rain](./dev_setup/img/make_it.jpg "Make it rain")

Design an investment wallet system for a company called `IronWallet` that incorporates a payment gateway and utilizes an omnibus account. How would you approach the architectural design to ensure seamless and secure user transactions?

## What to support (SCOPE)?

The system will support the core investment-wallet aspect, which is top-up (fund wallet) and fund transfer ( only from user bank to investment wallet)

### Services Included

* `IronWallet` API Gateway
it is a service that abstracts all `IronWallet` internal services and serves the clients ( Mobile app / Web app )

* investment-wallet service:
This service is to abstract wallet fund operations

* Payment Gateway service:
This service is to abstract processing payments and integration with payment gateway providers  

* Omnibus Service
This service is to abstract omnibus operations (virtual accounts) and integration with omnibus providers  

### Business Logic For Top-Up

1. The client ( mobile/web ) initiates a fund request to the `IronWallet` gateway
2. The `IronWallet` gateway will redirect the request to an investment-wallet service
3. Then the investment wallet service should communicate with the internal payment gateway service
   to create a request to an external payment gateway provider
4. After the payment gateway provider processes the payment, it should transfer the amount to `IronWallet` omnibus account and send the settlement document to `IronWallet` omnibus service through a webhook
5. The omnibus provider will create an account statement that has the payment provider amount  and the information about the transfer  
6. The omnibus should process the settlement and reconcile the virtual accounts balances, then communicate with the investment-wallet service about the latest transaction for the wallets
7. The wallet reflects the status of the fund request as paid  

### Business Logic Flow Fund Transfer  

1. The user gets the virtual IBAN for his invesmet-wallet from `IronWallet` app and then creates a local bank transfer using their local bank
2. The local bank will process the transfer and send the amount to the Omnibus account
3. The omnibus provider will create an account statement that has the virtual IBAN and the information about the transfer  
4. The omnibus should process the statement and reconcile the virtual accounts balances, then communicate with the investment-wallet service about the latest transaction for the wallets
5. The wallet reflects the status of the fund request as paid  

## Delivery

* Elaborate consideration for each key component, draw a high-level design of your system
  
* Define a clear data flow architecture to handle user requests, payments, and transfers and how you would handle potential challenges such as transaction concurrency and system scalability.

* Use the template to draft a POC implementation on how it would look like to implement APIs for communication between the investment wallet system, payment gateway, and omnibus

* State any futuer work, stuff you wanted to work on but couldn't due to time limitations or stuff you experimented it with but discarded it eventually
* 
### Assumptions During POC Implementations

* assume that you already have authenticated and authorized users to use the invesmet-wallet services
* Since your system will need a payment gateway/ omnibus provider, it is optional to read about the below APIs for better understanding and mocking  
  * [ANB statement API](https://developer.anb.com.sa/apis/api/account-statement/doc#operation/statement)
  * [Moyasar for payment gateway](https://docs.moyasar.com/)

## Running the POC Template

> 💡 Please note that this is an optional template; you can make your own if you want to

### Prerequisites

* Docker
* Poetry

### Setting up Dependencies

* run Docker Dependencies:

```shell
make up 
```

install python dependencies

```shell
make install 
```

### Starting a services

the template uses Poe to manage commands; each time, the file has a section where you can define shortcuts. For example, for running the gateway service, do the following
start Gateway service

```shell
cd /repos/gateway
poetry run poe api_service
```
