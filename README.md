Cielo
=====

[![Build status](https://ci.appveyor.com/api/projects/status/9nulf7grb2s8um37?svg=true)](https://ci.appveyor.com/project/lfreneda/cielo)
[![Nuget version](https://img.shields.io/nuget/v/Cielo.svg)](https://www.nuget.org/packages/Cielo/)
[![Nuget downloads](https://img.shields.io/nuget/dt/Cielo.svg)](https://www.nuget.org/packages/Cielo/)

Integração com o Cielo e-Commerce webservice.

Pull requests são bem vindos \o\~

Projeto de exemplo na solução.

Modelo CieloBuyPage
-----

### Criando uma transação

```c#
var order = new Order("12345", 4700.00m, DateTime.Now, "Descrição da ordem");
var paymentMethod = new PaymentMethod(CreditCard.MasterCard, PurchaseType.Credit);
var options = new CreateTransactionOptions(AuthorizationType.AuthorizePassByAuthentication, capture: true);
var createTransactionRequest = new CreateTransactionRequest(order, paymentMethod, options);

CreateTransactionResponse response = _cieloService.CreateTransaction(createTransactionRequest);
```

O objeto **response** (*CreateTransactionResponse*) contém a url para qual o cliente precisa ser redirecionado (ambiente da Cielo, onde ele vai informar os dados do cartão e etc) e o Tid (identificador único daquela transação) que usaremos mais tarde para verificar o status da transação.
		
Modelo LojaBuyPage
-----

### Criando uma transação

```c#
var order = new Order("12345", 4700.00m, DateTime.Now, "Descrição da ordem");
var paymentMethod = new PaymentMethod(CreditCard.MasterCard, PurchaseType.Credit);
var options = new CreateTransactionOptions(AuthorizationType.AuthorizePassByAuthentication, capture: true);
var creditCardData = new CreditCardData("6362970000457013", new CreditCardExpiration(2018, 05), SecurityCodeIndicator.Sent, 123);
var createTransactionRequest = new CreateTransactionRequest(order, paymentMethod, options, creditCardData);

CreateTransactionResponse response = _cieloService.CreateTransaction(createTransactionRequest);
```
	
O objeto **response** (*CreateTransactionResponse*) contém o Tid (identificador único daquela transação) que usaremos mais tarde para verificar o status da transação.
Nota: basicamente o que é diferente da integração CieloBuyPage é que dessa vez fornecemos os dados do cartão de crédito (através do objeto creditCardData)

Verificando status da transação
-----

```c#
var tid = GetTid(); // Recuperar o tid da transação (db,session,whatever)
var checkTransactionRequest = new CheckTransactionRequest(tid);

CheckTransactionResponse response = _cieloService.CheckTransaction(checkTransactionRequest);
```

Cancelando uma transação
-----

```c#
var tid = GetTid(); // Recuperar o tid da transação (db,session,whatever)
var cancelTransactionRequest = new CancelTransactionRequest(tid);

CancelTransactionResponse response = _cieloService.CancelTransaction(cancelTransactionRequest);
```

A verificação da transação é exatamente da mesma forma para os dois modelos, basta ter o Tid.
O objeto **response** (*CheckTransactionResponse* ou *CancelTransactionResponse*) contém o Status da transação que pode conter os seguintes valores:

### 
        Default = -1,
        Created = 0,
        InProgress = 1,
        Authenticated = 2,
        NotAuthenticated = 3,
        Authorized = 4,
        NotAuthorized = 5,
        Success = 6,
        Canceled = 9,
        AuthenticationProgress = 10,
        CancellationProgress = 12
        
        
## Contribuições 

1. Crie um fork
2. git clone https://github.com/(seu-usuario)/Cielo/
3. git checkout -b (nome-da-sua-funcionalidade)
4. git add --all && git commit -m "Descrição do que foi feito"
5. git push origin (nome-da-sua-funcionalidade)
6. Crie um pull request
