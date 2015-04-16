---
title: Integração Checkout Cielo

language_tabs:
  - json: JSON
  - shell: Shell
  - php: PHP
  - ruby: Ruby
  - python: Python
  - java: Java
  - csharp: C#

toc_footers:
  - <a href='/Checkout-Orientacoes-Gerais/'>Orientações Gerais</a>
  - <a href='/Checkout-Backoffice/'>Backoffice Cielo</a>
  - <a href='/Checkout-FAQ/'>FAQ</a>

search: true
---

# Manual Checkout Cielo

O objetivo desta documentação é orientar o desenvolvedor sobre como integrar com a **API Checkout Cielo**, solução de integração simples  no qual o consumidor é direcionado para uma **página de pagamento online segura da Cielo**, proporcionando um **alto nível de confiança**, dentro das mais rígidas normas de **segurança (PCI)**.
Nesta documentação são descritas **todas as funcionalidades** desta integração, os métodos a serem utilizados, listas com as informações a serem enviadas e recebidas e principalmente **códigos de exemplos** para facilitar o seu desenvolvimento. Em linhas gerais, o **Checkout Cielo** é uma solução de checkout projetada para aumentar a conversão, simplificar o processo de compra, reduzir fraudes e custos operacionais.

O Checkout Cielo utiliza uma **tecnologia REST** que deve ser usada **quando houver um *“carrinho de compras”*** a ser enviado, ou seja, no caso do consumidor navegar pelo site e escolher 1 ou mais produtos para adicionar ao carrinho e depois, então, finalizar a compra. Há também opção de **integração via botão** usada **sempre que não houver um *“carrinho de compras”*** em sua loja ou quando se deseja associar uma **compra rápida direta a um produto**.

### Orientações gerais

Após a conclusão da etapa de integração com o Checkout Cielo, é fundamental que o lojista ou administrador da loja online tenha conhecimento **dos processos** funcionais que farão parte do **cotidiano da loja**, como o **acompanhamento das movimentações financeiras**, status de cada venda, tomada de ações  (captura e cancelamento) com relação às vendas,  **extrato de cobrança**, entre outros. Veja a seção sobre o [BackOffice Checkout Cielo](#backoffice-cielo). Lá você encontra **orientações importantes** sobre como **administrar o e-Commerce** aproveitando ao máximo as funcionalidades  do Checkout Cielo

### Histórico de versões

* **Versão 1.3** - 21/01/2015
    - Troca de nomes – Solução Integrada para CHECKOUT CIELO
* **Versão 1.2** - 09/01/2015
    - Inclusão dos seguintes parâmetros no Post de notificação: `discount_amount`, `shipping_address_state`, `payment_boleto`, `number`, `tid`;
    - Alteração do parâmetro numero do pedido no Post de Mudança de Status
* **Versão 1.1** - 08/01/2015
    - Alinhamento dos fluxos de pagamento; inclusão de informações sobre os meios de pagamento; inclusão da tela de configurações do Backoffice
* **Versão 1.0** - 24/11/2014
    - Versão inicial

## Integração Carrinho de Compras

### Endpoint

Todas as requisições serão enviadas para o webservice da Cielo utilizando o método POST para o endpoint `https://cieloecommerce.cielo.com.br/api/public/v1/orders`.

### Autenticação

```shell
-H "MerchantId: 00000000-0000-0000-0000-000000000000" \
```

```php
<?php
curl_setopt($curl, CURLOPT_HTTPHEADER, array('MerchantId: 00000000-0000-0000-0000-000000000000'));
```

```ruby
headers  = {:content_type => "application/json",:merchantid => "00000000-0000-0000-0000-000000000000"}
```

```python
headers = {"Content-Type": "application/json", "MerchantId": "00000000-0000-0000-0000-000000000000"}
```

```java
connection.addRequestProperty("MerchantId", "0000000-0000-0000-0000-000000000000");
```

```csharp
request.Headers["MerchantId"] = "00000000-0000-0000-0000-000000000000";
```

Por se tratar de transações, todas as requisições enviadas para a Cielo deverão ser autenticadas. A autenticação consiste no envio do MerchantId, que é o identificador único da loja, através do campo de cabeçalho HTTP MerchantId.

<aside class="notice">
Lembre-se de substituir `00000000-0000-0000-0000-000000000000` pelo seu MerchantId.
</aside>

### Requisição

```json
{
    "OrderNumber": "12344",
    "SoftDescriptor": "Nome que aparecerá na fatura",
    "Cart": {
        "Discount": {
            "Type": "Percent",
            "Value": 10
        },
        "Items": [
            {
                "Name": "Nome do produto",
                "Description": "Descrição do produto",
                "UnitPrice": 100,
                "Quantity": 2,
                "Type": "Asset",
                "Sku": "Sku do item no carrinho",
                "Weight": 200
            }
        ]
    },
    "Shipping": {
        "Type": "Correios",
        "SourceZipCode": "14400000",
        "TargetZipCode": "11000000",
        "Address": {
            "Street": "Endereço de entrega",
            "Number": "123",
            "Complement": "",
            "District": "Bairro da entrega",
            "City": "Cidade de entrega",
            "State": "São Paulo"
        },
        "Services": [
            {
                "Name": "Serviço de frete",
                "Price": 123,
                "Deadline": 15
            }
        ]
    },
    "Payment": {
        "BoletoDiscount": 0,
        "DebitDiscount": 10
    },
    "Customer": {
        "Identity": 11111111111,
        "FullName": "Fulano Comprador da Silva",
        "Email": "fulano@email.com",
        "Phone": "11999999999"
    },
    "Options": {
        "AntifraudEnabled": false
    }
}
```

```shell
curl -X POST "https://cieloecommerce.cielo.com.br/api/public/v1/orders" \
     -H "MerchantId: 00000000-0000-0000-0000-000000000000" \
     -H "Content-Type: application/json" \
     -d '{
             "OrderNumber": "12344",
             "SoftDescriptor": "Nome que aparecerá na fatura",
             "Cart": {
                  "Discount": {
                      "Type": "Percent",
                      "Value": 10
                  },
                  "Items": [
                      {
                          "Name": "Nome do produto",
                          "Description": "Descrição do produto",
                          "UnitPrice": 100,
                          "Quantity": 2,
                          "Type": "Asset",
                          "Sku": "Sku do item no carrinho",
                          "Weight": 200
                      }
                  ]
             },
             "Shipping": {
                  "Type": "Correios",
                  "SourceZipCode": "14400000",
                  "TargetZipCode": "11000000",
                  "Address": {
                      "Street": "Endereço de entrega",
                      "Number": "123",
                      "Complement": "",
                      "District": "Bairro da entrega",
                      "City": "Cidade de entrega",
                      "State": "São Paulo"
                  },
                  "Services": [
                      {
                          "Name": "Serviço de frete",
                          "Price": 123,
                          "Deadline": 15
                      }
                  ]
             },
             "Payment": {
                  "BoletoDiscount": 0,
                  "DebitDiscount": 10
             },
             "Customer": {
                  "Identity": 11111111111,
                  "FullName": "Fulano Comprador da Silva",
                  "Email": "fulano@email.com",
                  "Phone": "11999999999"
             },
             "Options": {
                  "AntifraudEnabled": false
             }
         }'
```

```php
<?php
$order = new stdClass();
$order->OrderNumber = '1234';
$order->SoftDescriptor = 'Nome que aparecerá na fatura';
$order->Cart = new stdClass();
$order->Cart->Discount = new stdClass();
$order->Cart->Discount->Type = 'Percent';
$order->Cart->Discount->Value = 10;
$order->Cart->Items = array();
$order->Cart->Items[0] = new stdClass();
$order->Cart->Items[0]->Name = 'Nome do produto';
$order->Cart->Items[0]->Description = 'Descrição do produto';
$order->Cart->Items[0]->UnitPrice = 100;
$order->Cart->Items[0]->Quantity = 2;
$order->Cart->Items[0]->Type = 'Asset';
$order->Cart->Items[0]->Sku = 'Sku do item no carrinho';
$order->Cart->Items[0]->Weight = 200;
$order->Shipping = new stdClass();
$order->Shipping->Type = 'Correios';
$order->Shipping->SourceZipCode = '14400000';
$order->Shipping->TargetZipCode = '11000000';
$order->Shipping->Address = new stdClass();
$order->Shipping->Address->Street = 'Endereço de entrega';
$order->Shipping->Address->Number = '123';
$order->Shipping->Address->Complement = '';
$order->Shipping->Address->District = 'Bairro da entrega';
$order->Shipping->Address->City = 'Cidade da entrega';
$order->Shipping->Address->State = 'São Paulo';
$order->Shipping->Services = array();
$order->Shipping->Services[0] = new stdClass();
$order->Shipping->Services[0]->Name = 'Serviço de frete';
$order->Shipping->Services[0]->Price = 123;
$order->Shipping->Services[0]->DeadLine = 15;
$order->Payment = new stdClass();
$order->Payment->BoletoDiscount = 0;
$order->Payment->DebitDiscount = 10;
$order->Customer = new stdClass();
$order->Customer->Identity = 11111111111;
$order->Customer->FullName = 'Fulano Comprador da Silva';
$order->Customer->Email = 'fulano@email.com';
$order->Customer->Phone = '11999999999';
$order->Options = new stdClass();
$order->Options->AntifraudEnabled = false;

$curl = curl_init();

curl_setopt($curl, CURLOPT_URL, 'https://cieloecommerce.cielo.com.br/api/public/v1/orders');
curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, false);
curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
curl_setopt($curl, CURLOPT_POST, true);
curl_setopt($curl, CURLOPT_POSTFIELDS, json_encode($order));
curl_setopt($curl, CURLOPT_HTTPHEADER, array(
    'MerchantId: 00000000-0000-0000-0000-000000000000',
    'Content-Type: application/json'
));

$response = curl_exec($curl);

curl_close($curl);

$json = json_decode($response);
```

```python
from urllib2 import Request, urlopen
from json import dumps

json = dumps({
    "OrderNumber": "12344",
    "SoftDescriptor": "Nome que aparecerá na fatura",
    "Cart": {
        "Discount": {
            "Type": "Percent",
            "Value": 10
        },
        "Items": [
            {
                "Name": "Nome do produto",
                "Description": "Descrição do produto",
                "UnitPrice": 100,
                "Quantity": 2,
                "Type": "Asset",
                "Sku": "Sku do item no carrinho",
                "Weight": 200
            }
        ]
    },
    "Shipping": {
        "Type": "Correios",
        "SourceZipCode": "14400000",
        "TargetZipCode": "11000000",
        "Address": {
            "Street": "Endereço de entrega",
            "Number": "123",
            "Complement": "",
            "District": "Bairro da entrega",
            "City": "Cidade de entrega",
            "State": "São Paulo"
        },
        "Services": [
            {
                "Name": "Serviço de frete",
                "Price": 123,
                "Deadline": 15
            }
        ]
    },
    "Payment": {
        "BoletoDiscount": 0,
        "DebitDiscount": 10
    },
    "Customer": {
        "Identity": 11111111111,
        "FullName": "Fulano Comprador da Silva",
        "Email": "fulano@email.com",
        "Phone": "11999999999"
    },
    "Options": {
        "AntifraudEnabled": false
    }
})

headers = {"Content-Type": "application/json", "MerchantId": "00000000-0000-0000-0000-000000000000"}
request = Request("https://cieloecommerce.cielo.com.br/api/public/v1/orders", data=json, headers=headers)
response = urlopen(request).read()

print response
```

```ruby
require 'rubygems' if RUBY_VERSION < '1.9'
require 'rest-client'
require 'json'

request = JSON.generate({
    "OrderNumber" => "12344",
    "SoftDescriptor" => "Nome que aparecerá na fatura",
    "Cart" => {
        "Discount" => {
            "Type" => "Percent",
            "Value" => 10
        },
        "Items" => [
            {
                "Name" => "Nome do produto",
                "Description" => "Descrição do produto",
                "UnitPrice" => 100,
                "Quantity" => 2,
                "Type" => "Asset",
                "Sku" => "Sku do item no carrinho",
                "Weight" => 200
            }
        ]
    },
    "Shipping" => {
        "Type" => "Correios",
        "SourceZipCode" => "14400000",
        "TargetZipCode" => "11000000",
        "Address" => {
            "Street" => "Endereço de entrega",
            "Number" => "123",
            "Complement" => "",
            "District" => "Bairro da entrega",
            "City" => "Cidade de entrega",
            "State" => "São Paulo"
        },
        "Services" => [
            {
                "Name" => "Serviço de frete",
                "Price" => 123,
                "Deadline" => 15
            }
        ]
    },
    "Payment" => {
        "BoletoDiscount" => 0,
        "DebitDiscount" => 10
    },
    "Customer" => {
        "Identity" => 11111111111,
        "FullName" => "Fulano Comprador da Silva",
        "Email" => "fulano@email.com",
        "Phone" => "11999999999"
    },
    "Options" => {
        "AntifraudEnabled" => false
    }
})

headers  = {:content_type => "application/json",:merchantid => "00000000-0000-0000-0000-000000000000"}
response = RestClient.post "https://cieloecommerce.cielo.com.br/api/public/v1/orders", request, headers

puts response
```

```java
String json = "{"
            + "    \"OrderNumber\": \"12344\","
            + "    \"SoftDescriptor\": \"Nome que aparecerá na fatura\","
            + "    \"Cart\": {"
            + "        \"Discount\": {"
            + "            \"Type\": \"Percent\","
            + "            \"Value\": 10"
            + "        },"
            + "        \"Items\": ["
            + "            {"
            + "                \"Name\": \"Nome do produto\","
            + "                \"Description\": \"Descrição do produto\","
            + "                \"UnitPrice\": 100,"
            + "                \"Quantity\": 2,"
            + "                \"Type\": \"Asset\","
            + "                \"Sku\": \"Sku do item no carrinho\","
            + "                \"Weight\": 200"
            + "            }"
            + "        ]"
            + "    },"
            + "    \"Shipping\": {"
            + "        \"Type\": \"Correios\","
            + "        \"SourceZipCode\": \"14400000\","
            + "        \"TargetZipCode\": \"11000000\","
            + "        \"Address\": {"
            + "            \"Street\": \"Endereço de entrega\","
            + "            \"Number\": \"123\","
            + "            \"Complement\": \"\","
            + "            \"District\": \"Bairro da entrega\","
            + "            \"City\": \"Cidade de entrega\","
            + "            \"State\": \"São Paulo\""
            + "        },"
            + "        \"Services\": ["
            + "            {"
            + "                \"Name\": \"Serviço de frete\","
            + "                \"Price\": 123,"
            + "                \"Deadline\": 15"
            + "            }"
            + "        ]"
            + "    },"
            + "    \"Payment\": {"
            + "        \"BoletoDiscount\": 0,"
            + "        \"DebitDiscount\": 10"
            + "     },"
            + "     \"Customer\": {"
            + "         \"Identity\": 11111111111,"
            + "         \"FullName\": \"Fulano Comprador da Silva\","
            + "         \"Email\": \"fulano@email.com\","
            + "         \"Phone\": \"11999999999\""
            + "     },"
            + "     \"Options\": {"
            + "         \"AntifraudEnabled\": false"
            + "     }"
            + "}";

URL url;
HttpURLConnection connection;
BufferedReader bufferedReader;

try {
    url = new URL("https://cieloecommerce.cielo.com.br/api/public/v1/orders");

    connection = (HttpURLConnection) url.openConnection();
    connection.setRequestMethod("POST");
    connection.addRequestProperty("MerchantId", "0000000-0000-0000-0000-000000000000");
    connection.setRequestProperty("Content-Type", "application/json; charset=utf-8");
    connection.setDoOutput(true);

    DataOutputStream jsonRequest = new DataOutputStream(
                connection.getOutputStream());

    jsonRequest.writeBytes(json);
    jsonRequest.flush();
    jsonRequest.close();

    bufferedReader = new BufferedReader(new InputStreamReader(
                connection.getInputStream()));

    String responseLine;
    StringBuffer jsonResponse = new StringBuffer();

    while ((responseLine = bufferedReader.readLine()) != null) {
        jsonResponse.append(responseLine);
    }

    bufferedReader.close();

    connection.disconnect();
} catch (Exception e) {
    e.printStackTrace();
}
```

```csharp
HttpWebRequest request = (HttpWebRequest)
                         WebRequest.Create("https://cieloecommerce.cielo.com.br/api/public/v1/orders");

request.Method = "POST";
request.Headers["Content-Type"] = "text/json";
request.Headers["MerchantKey"] = "06eadc0b-2e32-449b-be61-6fd4f1811708";

string json = "{"
            + "    \"OrderNumber\": \"12344\","
            + "    \"SoftDescriptor\": \"Nome que aparecerá na fatura\","
            + "    \"Cart\": {"
            + "        \"Discount\": {"
            + "            \"Type\": \"Percent\","
            + "            \"Value\": 10"
            + "        },"
            + "        \"Items\": ["
            + "            {"
            + "                \"Name\": \"Nome do produto\","
            + "                \"Description\": \"Descrição do produto\","
            + "                \"UnitPrice\": 100,"
            + "                \"Quantity\": 2,"
            + "                \"Type\": \"Asset\","
            + "                \"Sku\": \"Sku do item no carrinho\","
            + "                \"Weight\": 200"
            + "            }"
            + "        ]"
            + "    },"
            + "    \"Shipping\": {"
            + "        \"Type\": \"Correios\","
            + "        \"SourceZipCode\": \"14400000\","
            + "        \"TargetZipCode\": \"11000000\","
            + "        \"Address\": {"
            + "            \"Street\": \"Endereço de entrega\","
            + "            \"Number\": \"123\","
            + "            \"Complement\": \"\","
            + "            \"District\": \"Bairro da entrega\","
            + "            \"City\": \"Cidade de entrega\","
            + "            \"State\": \"São Paulo\""
            + "        },"
            + "        \"Services\": ["
            + "            {"
            + "                \"Name\": \"Serviço de frete\","
            + "                \"Price\": 123,"
            + "                \"Deadline\": 15"
            + "            }"
            + "        ]"
            + "    },"
            + "    \"Payment\": {"
            + "        \"BoletoDiscount\": 0,"
            + "        \"DebitDiscount\": 10"
            + "     },"
            + "     \"Customer\": {"
            + "         \"Identity\": 11111111111,"
            + "         \"FullName\": \"Fulano Comprador da Silva\","
            + "         \"Email\": \"fulano@email.com\","
            + "         \"Phone\": \"11999999999\""
            + "     },"
            + "     \"Options\": {"
            + "         \"AntifraudEnabled\": false"
            + "     }"
            + "}";

using (var writer = new StreamWriter(request.GetRequestStream()))
{
    writer.Write(json);
	writer.Close();
}

HttpWebRequest response = (HttpWebResponse) request.GetResponse();
```

#### Cabeçalho HTTP

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|MerchantId|Guid|Sim|36|Identificador único da loja. **Formato:** 00000000-0000-0000-0000-000000000000|
|Content-type|Alphanumeric|Sim|n/a|Tipo do conteúdo da mensagem a ser enviada. **Utilizar:** "application/json"|

#### Objeto raiz da requisição

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|OrderNumber|Alphanumeric|Opcional|0..64|Número do pedido da loja.|
|SoftDescriptor|Alphanumeric|Opcional|0..13|Texto para ser exibido na fatura do portador, após o nome do estabelecimento comercial.|
|Cart|[Cart](#cart)|Sim|n/a|Informações sobre o carrinho de compras.|
|Shipping|[Shipping](#shipping)|Sim|n/a|Informações sobre a entrega do pedido."|
|Payment|[Payment](#payment)|Conditional|n/a|Informações sobre o pagamento do pedido.|
|Customer|[Customer](#customer)|Condicional|n/a|Informações sobre dados pessoais do comprador.|
|Options|[Options](#options)|Conditional|n/a|Informações sobre opções configuráveis do pedido.|

### Resposta

#### Em caso de sucesso

```json
{
    "Settings": {
        "CheckoutUrl": "https://cieloecommerce.cielo.com.br/transacional/order/index?id=123",
        "Profile": "CheckoutCielo",
        "Version": 1 
    }
}
```

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|Settings|[Settings](#settings)|Sim|n/a|Informações da resposta sobre a criação do pedido.|

#### Em caso de erro

```json
{
    "message":"An error has occurred."
}
```

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|Message|String|Sim|1..254|Mensagem descritiva do erro|

## Integração via Botão

Integração via Botão é um método de compra usada sempre que não houver um “carrinho de compras” em sua loja ou quando se deseja associar uma compra rápida direta a um produto, como uma promoção numa homepage pulando a etapa do carrinho.

A integração via botão também pode ser usada para enviar um e-mail marketing, ou uma cobrança via e-mail, ou seja, adicionando o botão (HTML) referente ao produto/serviço a ser comprado/pago. Ou sempre que desejar disponibilizar uma venda rápida.

Para utilizar este recurso, é necessário cadastrar o produto que deseja vender, suas informações, e depois simplesmente copiar o código fonte gerado para este botão. A inclusão dos produtos é feita dentro do Backoffice, no menu de Produtos/Cadastrar Produto.

### Características do Botão

* Cada botão gerado serve somente para um determinado produto.
* O preço do produto não pode ser alterado na tela de Checkout
* Não é necessário o desenvolvimento de um carrinho
* O cadastro do produto é obrigatório para a criação do botão.

Cada botão possui um código único que só permite comprar aquele determinado produto nas condições de preço e frete cadastrado. Portanto, um fraudador não consegue alterar nenhuma destas informações na hora de submeter à compra, pois o Checkout Cielo vai buscar todos os dados do produto no cadastro do Backoffice, e valerão os dados do cadastro.

# Configurações de Pagamento

## Cartão de Crédito

O Checkout Cielo trabalha com diferentes bandeiras de crédito, como Visa, Mastercard, Diners, Amex e Elo.

![Cartões de crédito](/images/checkout-cielo-cartao-credito.png)

### Recebendo uma Venda de Cartão de Crédito

Pedidos por meio de cartão de crédito serão incluídos no Backoffice como **“AUTORIZADO”** ou **“NÃO AUTORIZADO”**, dependendo do resultado da autorização na Cielo. Caso haja algum problema no processamento deste pedido (consumidor fechou a tela, por exemplo), ele constará como **“NÃO FINALIZADO”**.

## Boleto

Todo boleto gerado (emitido) aparece com o status de “PENDENTE” no Relatório de Pedidos. Sua troca de status vai depender de ações manuais do proprio lojista.

### Possiveis Status do Boleto

* **PENDENTE** – boleto emitido pelo processo de transação. Status continua até alteração manual pelo lojista.
* **PAGO** – Status usado quando o botão "Conciliar” é ativado pelo lojista. Esse status pode ser revertido para pendente utiliando o Botão “Desfazer conciliação”.
* **EXPIRADO** – Status ativo após 10 dias da criação do boleto, caso esse não tenha sito conciliado nesse periodo. Boletos com status “EXPIRADO” podem ser conciliados.

### Conciliando um Boleto

Cabe ao lojista através de uma Conciliação Manual com seu extrato bancário, confirmar o pagamento do mesmo.

![Conciliando um boleto](/images/checkout-cielo-conciliar-boleto.png)

Para realizar a Conciliação você deve:

1. Acessar o relatório de pedidos no Backoffice;
2. Filtrar os pedidos por Meio de Pagamento “Boleto” e status “PENDENTE” e identificar o boleto em questão pelo Valor;
3. Clicar no sinal de + no final da linha para acessar a página de “Detalhes”;
4. Clicar no botão de “ Confirmar Pagamento ” e informar a data de pagamento, para seu futuro controle;

O pedido passa para status **PAGO**.

O Comprador também verá o pedido como **PAGO** no “Backoffice do Comprador”

Desfazendo a conciliação (pagamento) de um Boleto. Caso a conciliação tenha sido feito errada, basta:

1. Encontrar o Pedido;
2. Entrar no seu detalhe e clicar no botão “Desfazer Pagamento”;
3. O Pedido voltará para o Status de “PENDENTE”.

### Boletos Expirados

Se o boleto não for conciliado dentro de um prazo de 10 dias após seu vencimento, seu Status será alterado para **“EXPIRADO”**, para um melhor controle dos boletos vencidos. Boletos EXPIRADOS podem ser conciliados.

<aside class="notice">Validade do Boleto – Caso o boleto expire em um dia não útil, como Sábado, ele será valido até o próximo dia útil.</aside>

![Boleto](/images/checkout-cielo-boleto.png)

## Débito Online

Pedidos vendidos por meio de Débito online serão incluídos no Backoffice como PENDENTE, PAGO, NÃO AUTORIZADO ou NÃO FINALIZADO, dependendo do resultado da autorização junto ao Banco.

* **Pendente** - Corresponde quando o comprador ao finalizar o pedido e não obtem resposta por parte do Banco, ou seja, não conseguir nem carregar a página do Banco para inserir os dados para o Débito.
* **Pago** - Corresponde quando o comprador conseguir realizar o pagamento do débito com sucesso.
* **Não Autorizado** - Apresentado para o Lojista quando o comprador tentar realizar uma transação via débito e não ter saldo para a transação.
* **Não Finalizado** - Apresentado para o Lojista caso o comprador tenha algum problema para finalizar o pagamento do meio Débito, seja fechando a janela do banco ou simplesmente nem chegando à tela do banco.

## Captura/Cancelamento Automático

### Captura automática

As vendas **“AUTORIZADAS”**, e com **“BAIXO RISCO”** no antifraude serão **CAPTURADAS** automaticamente pelo sistema.

O status apresentado será **“PAGO”**. Esta venda será então confirmada (capturada) na Cielo.

### Cancelamento Automático

As vendas **“AUTORIZADAS”**, e com **“ALTO RISCO”** no antifraude serão **CANCELADAS** automaticamente pelo sistema.

O status apresentado será **“PAGO”**. Esta venda será então cancelada (desfeita) na Cielo.

<aside class="warning">Atenção! Você tem a opção de escolher a melhor integração para o seu negócio, a captura/cancelamento manual ou automático é feito diretamente pelo seu backoffice.</aside>

![Cancelamento e captura automáticos](/images/checkout-cielo-anti-fraude-cancelamento-captura.png)

## Captura/Cancelamento manual

Concluindo uma Venda (captura ou cancelamento)

As vendas **“AUTORIZADAS”** aguardam uma decisão de confirmação ou cancelamento. E esta decisão deve vir em conformidade com a análise de fraude.

A confirmação da venda deve ser feita pelo botão **CAPTURAR** no Detalhe do Pedido, e seu status mudará para **“PAGO”**. Esta venda será então confirmada (capturada) na Cielo.

E o cancelamento deve ser feito pelo botão **CANCELAR** no Detalhe do Pedido, e seu status mudará para **“CANCELADO”**. Esta venda será então cancelada (desfeita) na Cielo.

<aside class="warning">Atenção! Você tem até 5 dias pra confirmar a venda! Caso isso não seja feito ela não será mais válida na Cielo, e o limite reservado para sua loja/venda será liberado. Este é um procedimento padrão para todas as lojas.</aside>

## Estorno de Venda

Caso a venda já tenha sido confirmada (status PAGO) ela pode ser ainda, futuramente, estornada. Para isso, basta clicar no botão no Detalhe do Pedido.

### Diferença entre Cancelado e Estornado

* **Cancelamento:** é feito no mesmo dia da captura, devolvendo o limite ao cartão do comprador em até 72h conforme regras do banco emissor do cartão. Não é apresentado na fatura do comprador;
* **Estorno:** a partir do dia seguinte da captura, o valor é “devolvido” na fatura do comprador em até 120 dias. É apresentado na fatura do comprador.

### Vendas de Cartões de Crédito Expiradas

Conforme o prazo de confirmação da venda autorizada se acabar, seu pedido passará automaticamente para o status “EXPIRADO”. Isso acontecerá no sexto dia após a data de autorização (data do venda).

## Chargeback

Seu cliente pode por algum motivo cancelar a compra diretamente com o banco emissor de seu cartão de crédito. Caso isso ocorra você receberá da Cielo um aviso de Chargeback de “Não Reconhecimento de compra”. Ou caso tenha havido uma compra com cartão fraudado, você recebera um aviso de Chargeback por “Fraude”.

Essa comunicação não é feita via o backoffice do Checkout Cielo, mas sim pelo extrato da própria Cielo. Após esse recebimento, você pode entrar no Backoffice e sinalizar o pedido como tendo recebido um Chargeback, pra seu melhor controle. Basta entrar no Detalhe do Pedido e clicar no botão “ChargeBack”, e seu status passará a ser **“CHARGEBACK”**.

![Chargeback](/images/checkout-cielo-chargeback.png)

### Análise de Fraude

Pedidos **“AUTORIZADOS”** serão enviados online, ainda no ato da venda, para ferramenta de Antifraude da Cybersource para análise. O resultado desta análise será traduzido no campo **“Indicação AF”** no Relatório de Pedido, para cada pedido.

Esta análise indicará um **“BAIXO RISCO”** ou **“ALTO RISCO”** para a venda em questão. Esta sugestão é o que deve guiar a decisão de se confirmar a venda ou não. A analise será apresentada no “Detalhes do Pedido”, como abaixo:

[!Análise de risco(/images/checkout-cielo-analise-risco.png)










# Referência de tipos

## Cart

```json
{
    "Discount": {},
    "Items": []
}
```

Informações sobre o carrinho de compras.

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|Discount|[Discount](#discount)|Opcional|n/a|Informações do desconto sobre o carrinho de compras.|
|Items|[Item[]](#item)|Sim|n/a|Lista de itens do carrinho de compras *(deve conter no mínimo 1 item)*.|

### Discount

```json
{
    "Type": "Percent",
    "Value": 10
}
```

<aside class="notice">Independentemente do tipo do desconto, ele deverá ser calculado antes da soma do valor do frete.</aside>

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|Type|Alphanumeric|Condicional|n/a|Tipo do desconto a ser aplicado: "Amount", "Percent". Obrigatório caso `Value` for maior ou igual a zero.|
|Value|Numeric|Condicional|0..18|Valor do desconto a ser aplicado *(pode ser valor absoluto ou percentual)*. Obrigatório caso `Type` for "Amount" ou "Percent".|

#### Discount.Type = Amount

Caso o tipo de desconto escolhido seja o `Amount`, deverá ser inserido o **valor em centavos**.
Ex.: 100 = 1,00.

#### Discount.Type Percent

Caso o tipo de desconto escolhido seja o `Percentual`, deverá ser inserido o **valor em número inteiro**.
Ex.: 10 = 10%.

### Item

```json
{
    "Name": "Nome do produto",
    "Description": "Descrição do produto",
    "UnitPrice": 100,
    "Quantity": 2,
    "Type": "Asset",
    "Sku": "Sku do item no carrinho",
    "Weight": 200
}
```

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|Name|Alphanumeric|Sim|1..128|Nome do item no carrinho.|
|Description|Alphanumeric|Opcional|0.256|Descrição do item no carrinho.|
|UnitPrice|Numeric|Sim|1..18|Preço unitário do item no carrinho *(**em centavos.** Ex: R$ 1,00 = 100)*.|
|Quantity|Numeric|Sim|1..9|Quantidade do item no carrinho.|
|Type|Alphanumeric|Sim|n/a|Tipo do item no carrinho.|
|Sku|Alphanumeric|Opcional|0..32|Sku do item no carrinho.|
|Weight|Numeric|Condicional|0..9|Peso em gramas do item no carrinho.|

#### Tipos de item

|Tipo|Descrição|
|----|---------|
|Asset|Material Físico|
|Digital|Produtos Digitais|
|Service|Serviços|
|Payment|Outros pagamentos|

## Shipping

```json
{
    "Type": "Correios",
    "SourceZipCode": "14400000",
    "TargetZipCode": "11000000",
    "Address": {},
    "Services": []
}
```

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|Type|Alphanumeric|Sim|n/a|Tipo do frete: "Correios", "FixedAmount", "Free", "WithoutShippingPickUp", "WithoutShipping".|
|SourceZipCode|Numeric|Condicional|8|CEP de origem do carrinho de compras.|
|TargetZipCode|Numeric|Opcional|8|CEP do endereço de entrega do comprador.|
|Address|[Address](#address)|Opcional|n/a|Informações sobre o endereço de entrega do comprador.|
|Services|[Service[]](#service)|Condicional|n/a|Lista de serviços de frete.|

### Address

```json
{
    "Street": "Endereço de entrega",
    "Number": "123",
    "Complement": "",
    "District": "Bairro da entrega",
    "City": "Cidade de entrega",
    "State": "São Paulo"
}
```

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|Street|Alphanumeric|Sim|1..256|Rua, avenida, travessa, etc, do endereço de entrega do comprador.|
|Number|Alphanumeric|Sim|1..8|Número do endereço de entrega do comprador.|
|Complement|Alphanumeric|Opcional|0..256|Complemento do endereço de entrega do comprador.|
|District|Alphanumeric|Sim|1..64|Bairro do endereço de entrega do comprador.|
|City|Alphanumeric|Sim|1..64|Cidade do endereço de entrega do comprador.|
|State|Alphanumeric|Sim|2|Estado (UF) do endereço de entrega do comprador.|

### Service

```json
{
    "Name": "Serviço de frete",
    "Price": 123,
    "Deadline": 15
}
```

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|Name|Alphanumeric|Sim|1..128|Nome do serviço de frete.|
|Price|Numeric|Sim|1..18|Preço do serviço de frete (em centavos. Ex: R$ 1,00 = 100).|
|Deadline|Numeric|Condicional|0..9|Prazo de entrega (em dias).|

## Payment

```json
{
    "BoletoDiscount": 0,
    "DebitDiscount": 10
}
```

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|BoletoDiscount|Numeric|Condicional|0..3|Desconto, em porcentagem, para pagamentos a serem realizados com boleto.|
|DebitDiscount|Numeric|Condicional|0..3|Desconto, em porcentagem, para pagamentos a serem realizados com débito online.|

## Customer

```json
{
    "Identity": 11111111111,
    "FullName": "Fulano Comprador da Silva",
    "Email": "fulano@email.com",
    "Phone": "11999999999"
}
```

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|Identity|Numeric|Condicional|0..14|CPF ou CNPJ do comprador.|
|FullName|Alphanumeric|Condicional|0..288|Nome completo do comprador.|
|Email|Alphanumeric|Condicional|0..64|Email do comprador.|
|Phone|Numeric|Condicional|0..11|Telefone do comprador.|

## Options

```json
{
    "AntifraudEnabled": false
}
```

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|AntifraudEnabled|Boolean|Conditional|n/a|Habilitar ou não a análise de fraude para o pedido.|

## Settings

```json
{
    "CheckoutUrl": "https://cieloecommerce.cielo.com.br/transacional/order/index?id=123",
    "Profile": "CheckoutCielo",
    "Version": 1 
}
```

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|CheckoutUrl|Alphanumeric|Sim|1..128|URL de checkout do pedido. **Formato:** `https://cieloecommerce.cielo.com.br/transacional/order/index?id={id}`|
|Profile|Alphanumeric|Sim|1..16|Perfil do lojista: fixo "CheckoutCielo".|
|Version|Alphanumeric|Sim|1|Versão do serviço de criação de pedido *(versão: 1)*.|

# Perguntas frequentes

## Dúvidas técnicas

### Aonde eu cadastro meus produtos?

A inclusão dos produtos é feita dentro do Backoffice, no menu de Produtos/Cadastrar Produto.

### Existe limite de produtos a serem cadastrados no CHECKOUT CIELO?

Não, você pode cadastrar quantos produtos desejar.

### É necessario cadastrar todos os meus produtos no CHECKOUT CIELO para que eu possa transacionar?

Não, o cadastro de produtos é opcional. Ele é realizado para a criação de um “botão”, que pode ser utilizado para a venda de um produto especifico.

### Quais informações eu preciso para montar o Post?

Os dados a serem preenchidos no post se referem basicamente a 4 grupos:

1. Dados do Pedido
2. Carrinho de Compras
3. Dados de Frete
4. Dados do Consumidor.

Todas as informações para auxiliar nesse processo, encontram-se no manual de desenvolvimento.

### Posso enviar um e-mail como forma de cobrança?

Sim, através da integração via botão você pode usar para enviar um e-mail marketing, ou uma cobrança por e-mail, adicionando ao HTML do e-mail, o botão referente ao produto/serviço sendo comprado/pago. Ou sempre que se desejar disponibilizar uma transação rápida sem desenvolvimento algum.

### O comprador precisa ser cliente do CHECKOUT CIELO para fechar a transação?

Não. A solução de checkout é simples e não exige que o cliente esteja logado ou se cadastre. No entanto todas as informações solicitadas para fechar a compra são salvas no pedido. E o lojista terá acesso a todas elas.

### Para que serve a URL de retorno?

Para que ao finalizar a compra, o consumidor final tenha a opção de voltar ao site do lojista. O Consumidor é redirecionado automaticamente a pagina definida nesta opção.

### Para que serve a URL de Notificação?

Ao finalizar a compra é enviando um post contendo informações sobre a transação. A utilização dessa URL é indicada para clientes que entregam através de uma Plataforma de E-commerce. Dessa maneira os dados do pedido ficam atualizados no Backoffice do CHECKOUT CIELO e no Backoffice junto a Plataforma.

### Para que serve a URL de Notificação?

Ao finalizar a compra é enviando um post contendo informações sobre a transação. A utilização dessa URL é indicada para clientes que entregam através de uma Plataforma de E-commerce. Dessa maneira os dados do pedido ficam atualizados no Backoffice do CHECKOUT CIELO e no Backoffice junto a Plataforma.

### Onde é feito o cadastro dessas URLs?

No seu backoffice, aba configurações -> configurações da loja.

### Posso dar desconto ao meu cliente?

Sim. É possível enviar um parâmetro com o valor ou a Porcentagem (%) do desconto a ser oferecido naquela transação. Esse parametro é valido para todos os meios de pagamento, não sendo aplicado a meios especificos e o desconto é sobre o valor total da transação.

Tambem é possivel definir um valor de desconto para pagamento com Debito online ou boleto, ambos podendo ser enviados via Post ou definidos no Backoffice. O desconto de boleto/débito pode ser acumulado ao desconto total.

### Como são os Posts para lojas que utilizam carrinhos?

O tipo de post a ser utilizado em sua loja dependerá do produto e de como esse é comercializado na sua loja.

Os Posts serão formados por parametros que devem ser enviados a URL, `https://cieloecommmerce.cielo.com.br/transactional/order/index`, onde serão processados. Veja a seção [Integração](#integraco) dessa documentação para mais informações.

<aside class="notice">É importante entender que apesar de alguns parametros descritos no manual de integração não serem obrigatorios para um determinado produto, isso não significa que eles não serão obrigatorios a outros tipos.</aside>

### Qual a relação entre os tipos de produtos e os tipos de fretes?

O tipo de produto a ser vendido no CHECKOUT CIELO influencia diretamente qual o tipo de frete e a informação a ser enviada para o processamento da transação. O tipo de frete a ser utilizado dependerá do tipo de produto que a sua loja comercializa. No CHECKOUT CIELO você pode comercializar 3 tipos de produtos:

* Bens Fisicos
* Bens digitais
* Serviços

Os tipos de frete que podem ser usados são:

* Correios
* Frete Fixo
* Frete Grátis
* Sem Frete (Retirada em mãos)
* Sem cobrança de frete (Usada para bens Digitais ou Serviços)

Se Produtos do tipo “Bens fisicos” necessitam de algum tipo de frete para serem enviados, obrigatoriamente será preciso a inclusão de informações como do peso do produto, Cep de origem e Cep de entrega, para o calculo do frete. Veja a seção [Integração](#integraco) dessa documentação para mais informações.

<aside class="notice">Diferentemente, “Bens digitais” ou “Serviços” não necessitam desse tipo de informação.</aside>

## Dúvidas comerciais

### Quais são os meios de pagamento disponíveis no CHECKOUT CIELO?

Cartões de Crédito (Bandeiras: Visa, Mastercard, Diners, Amex e Elo), Boleto Bancário
e Débito Online (Banco do Brasil e Bradesco).

### Quais são as diferenças entre o CHECKOUT CIELO e os Facilitadores de Pagamento, como Pagseguro e Paypal?

* Não intermediamos o dinheiro referente a suas transações. Isso significa que o lojista vende com suas afiliações. Seu dinheiro será depositado diretamente na sua conta corrente pela Cielo.
* Controle sobre os recebimentos. O lojista negocia diretamente com Cielo se deseja antecipar seus recebíveis
* Possibilidade de capturar transações imediatamente. O lojista decide se captura manualmente ou libera a captura automatica para transações de baixo risco.
* O lojista decide para quem quer vender com apoio de ferramenta antifraude. O Lojista decide se assume o risco informado (“Baixo, Moderado ou Alto”) em relação a um pedido, capturando ou cancelando a transação (conclui a transação ou não).

### É preciso ter contas nos bancos para adquirir as afiliações de boleto e Débito?

Sim. Para maiores informações sobre esse processo entre em contato com [cieloecommerce@cielo.com.br](mailto:cieloecommerce@cielo.com.br).

O CHECKOUT CIELO não gera boletos para cooperativas de crédito, mesmo que estas sejam vinculadas aos bancos utilizados.

### Quando o valor da transações é depositado em minha conta corrente?

O valor transacionado será depositado em sua conta corrente de acordo com os prazos estabelecidos em cada meio de pagamento. Para maiores informações sugerimos que entre em contato direto com cada meio que a sua loja utiliza.

### É possivel realizar parcelamentos no CHECKOUT CIELO?

Sim, o CHECKOUT CIELO pode parcelar compras com cartão de crédito em até 12x. As parcelas do CHECKOUT CIELO não incluem juros, sendo uma divisão do valor comprado pelo prazo do pagamento.

<aside class="notice">O numero maximo de parcelas é definido na afiliação da loja. Caso a sua afiliação tenha liberado 6x na afiliação e 12X no CHECKOUT CIELO, a transação não será autorizada. A alteração do numero maximo de parcelas da afiliação pode ser realizada diretamente com a Cielo.</aside>

### Saio do ambiente da minha loja durante a transação?

Sim, quando o comprador decidir realizar o checkout ele será redirecionado para a tela do CHECKOUT CIELO. Nessa tela segura, o comprador irá inserir os dados do meio de pagamento e o meio de entrega/frete (caso a loja permita) de sua preferencia.

### O CHECKOUT CIELO detém informações sobre prazo de entrega do produto?

Não , o CHECKOUT CIELO tem acesso somente a informações da transação de pagamento. Caso um cliente do lojista CHECKOUT CIELO entre em contato com duvidas a respeito do prazo de entrega ou solicitações a respeito do mesmo assunto, será sugerido a ele que entre em contato com a loja responsavel pela venda.

A equipe CHECKOUT CIELO não fornece os dados de contato do Lojista ao comprador em hipótese alguma.

## Dúvidas operacionais

### Quando esquecer a senha de acesso ao Backoffice, como proceder?

1. Acesse o site do backoffice, e clique em “Esqueci Minha Senha”
2. Preencha com o e-mail cadastrado. Um e-mail para redefinição de senha será enviado.

### Não consigo visualizar o pedido no backoffice, como resolver?

Primeiramente, verifique se na integração foi utilizado o MID correto. MID é o Merchant_Id de sua loja, e deve ser utilizado na integração com CHECKOUT CIELO, coforme o Manual de Desenvolvimento.

### O que devo fazer quando o pedido estiver com o status “Autorizado”?

Deverá conferir o status do antifraude e decidir por capturar (aceitar) ou cancelar a transação. Ressaltamos que o risco e a responsabilidade sempre serão do lojista caso venha a sofrer uma fraude e/ou um chargeback.

### Durante quanto tempo o pedido pode permanecer com o status “Autorizado”?

O pedido poderá ficar “Autorizado” por 5 dias corridos, após esse prazo não pode mais ser capturado junto a operadora de cartão de crédito/adquirente (Cielo). Recomendamos que Capture ou Cancele o pedido o quanto antes, agilizando a entrega da mercadoria ou liberando o limite de crédito do seu cliente.

### Qual é a diferença entre os status “Autorizado” e “Pago” em um pedido de cartão de
crédito?

* **Autorizado** – o valor da transação fica reservado no limite do cartão do cliente, aguardando a captura (efetivação do pagamento) ou o cancelamento ( o retorno do limite ao cliente). Significa que a transação ainda NÃO foi concluída.
* **Pago** – o valor foi efetivado e será cobrado na fatura do cliente. Significa que a transação foi concluída 

<aside class="warning">ATENÇÃO, para pedidos feitos com cartão de crédito o status “Pago” não significa que o dinheiro estará disponível na sua conta corrente imediatamente. A operadora de cartão de crédito (Cielo) efetiva o depósito dos valores referente à suas transações 30 dias após a captura. É possível receber antes dos 30 dias negociando diretamente com a Cielo ou com o seu banco a (Antecipação de recebíveis de cartão de crédito).</aside>

### O que devo fazer quando o pedido estiver com o status “Expirado”?

* Para pedidos com Cartão de Crédito, não será possível capturar a transação. O pedido foi automaticamente cancelado pela operadora de cartão de crédito/adquirente (Cielo).
* Para pedidos com Boleto Bancário, é necessário consultar no seu extrato bancário se valor foi creditado e alterar o status para PAGO.

### O que acontece se eu não alterar os status dos meus boletos que foram pagos?

Não haverá nenhum impacto em suas vendas, porém a visão da sua operação nos relatórios e gráficos do Dashboard ficará prejudicada por não refletir exatamente o status de sua operação.

### O que significa o status “Pendente” para cartões de crédito?

Significa que não foi repassada até o momento nenhuma resposta do Banco ou da Cielo.
Os pedidos serão sondados juntos as operadoras de cartão de crédito/Adquirentes, e seu status atualizado.

### Quais os possíveis status em cada meio de pagamento?

* **Cartão de Crédito**:
   * **Não Finalizado** - Falha de conexão no processamento da transação é possível recuperar a transação solicitando que o cliente refaça a transação.
   * **Pendente** - Status intermediário.
   * **Autorizado** - Quando um pedido foi aprovado e está pendente de captura. É necessário Capturar ou Cancelar a transação conforme a analise de risco.
   * **Não Autorizado** - pedido não foi aprovado pela Cielo/Banco Emissor Pago - status após a Captura manual ou automática da transação.
   * **Cancelado** - Quando é solicitado um cancelamento junto a operadora de cartão de crédito/adquirente.
   * **Chargeback** - Quando o comprador solicita o estorno junto ao banco emissor, o lojista pode utilizar esse status para seu controle.
   * **Expirado** - Não foi Capturado ou cancelado no prazo de 5 dias.
* **Boleto Bancário**:
   * **Pendente** - Aguardando pagamento
   * **Não Finalizado** - Falha de conexão no processamento da transação é possível recuperar a transação solicitando que o cliente refaça a transação.
   * **Expirado** - Não foi alterado o status no prazo de 5 dias após a data de vencimento, se o pagamento for confirmado no seu extrato bancário é possível alterar o status da transação para PAGO.
   * **Pago** - Confirmação de Pagamento após verificação no seu extrato bancário
* **Débito Online**:
   *  **Não Autorizado** - pedido não aprovado pelo banco.
   *  **Pago** - Pedido confirmado pelo banco, valor debitado da conta do comprador.
   *  **Não Finalizado** - Falha de conexão no processamento da transação. É necessário solicitar ao cliente que refaça a transação.
   *  **Pendente** - Status intermediário.

### Cliquei errado no botão “Cancelar”. Perdi a transação?

Sim, a transação foi cancelada junto a Cielo. Não sendo possível desfazer um cancelamento.

### Qual a diferença de Cancelamento e Estorno?

* **Cancelamento** - é feito no mesmo dia da captura, devolvendo o limite ao cartão do comprador em até 72h conforme regras do banco emissor do cartão.
* **Estorno** - a partir do dia seguinte da captura, o valor é “devolvido” na fatura do comprador em até 120 dias.

<aside class="warning">ATENÇÃO, o cancelamento e o estorno são realizamos através do “botão” cancelar. Seguindo as regras acima.</aside>

### Quanto tempo depois da transação eu posso efetuar um estorno?

Você tem até 120 dias após a data de captura do pedido.

### É possivel definir um valor minimo de parcelamento?

Sim, na aba configurações do CHECKOUT CIELO é possivel definir o valor minimo que uma parcela pode atingir. Dessa maneira, o valor da parcela não será inferior a um valor idal ao lojista.

### É possivel definir um valor minimo de boleto?

Sim, é possivel definir um valor minimo para que o boleto seja disponibilizado. Para maiores informações acesse BackOffice -> Manuais -> Tutorial do lojista.

### É possivel dar um desconto para o uso de boleto ou meio de pagamento?

Sim, é possivel definir um valor de desconto para que o boleto seja disponibilizado. Isso pode ser feito de duas maneiras: Backoffice ou via API de integração - descrita na seção [Integração](integraco).

### O que é antifraude? E para que serve?

“Anti-Fraudes” são ferramentas de Análise e Avaliação de Risco. Essas ferramentas verificam e apontam a possibilidade de uma transação incorrer em fraude, protegendo o empreendedor virtual de eventuais golpes.

Ao vender pela internet não existe a possibilidade de utilizar a segurança dos dispositivos físicos do cartão tais como o chip e a tarja magnética e por isso os lojistas virtuais são alvos constantes de tentativas de fraude.

Diante desse cenário, surgiram no mercado ferramentas específicas que buscam combater e inibir tais iniciativas, ou seja, os “anti-fraudes”.

Todas as transações de cartão de crédito autorizadas na Cielo no CHECKOUT CIELO são
processadas no antifraude Decision Manager da Cybersource.

### Para capturar minhas transações, necessito entrar no Backoffice

Depende. Caso deseje visualizar o status de cada transação e acompanhar pessoalmente o processo de venda, você pode mantar a captura manual.

Caso, prefira que o processo de captura seja automatizado, há na aba configurações opções de captura e rejeição de pedidos automaticamente.

### Qual a diferença de Captuta Automática e Captura Manual?

* **Captura Automática** – a decisão da venda é feita automaticamente após analise de fraude. Onde todos os pedidos autorizados e com “Baixo Risco” serão marcados como PAGO.
* **Captura Manual** – a decisão de venda é feita manualmente pelo lojista no backoffice. Analisando a resposta do antifraude.

### O que devo fazer quando o status do antifraude estiver como “Baixo Risco” e a minha captura é manual?

Recomendamos que capture o pedido e conclua a transação. Lembrando que não existe risco zero em transações online pois os mecanismos de segurança, tais como tarja magnética, chip e senhas não funcionam para transações online.

### O que devo fazer quando o status do antifraude estiver como “Alto Risco” e a minha captura é manual?

Neste caso recomendamos o cancelamento da autorização, porém você ainda pode optar por fazer uma análise manual do pedido e decidir por capturar a autorização e concluir a transação ignorando a orientação do antifraude e assumindo o risco.

### O que é um Chargeback?

Chargeback é o cancelamento feito pelo comprador de uma transação realizada com cartão de crédito. Que pode acontecer por dois motivos:

1. O não reconhecimento da compra por parte do titular do cartão
2. Ou a transação não obedecer às regras previstas entre o comprador e o lojista.

Caso um ChargeBack ocorra, o lojista não recebe o valor da compra ou seja, caso seja uma fraude, o fraudador, pode realizar uma compra com dados falsos.

### Quais a opções de frete eu posso utilizar?

* Correios
* Frete Fixo
* Frete Grátis
* Sem Frete (Retirada em mãos)
* Sem cobrança de frete (Usada para bens Digitais ou Serviços)

### Para utilizar o frete dos Correios o que eu devo fazer?

* Se você tem um contrato com o correio basta cadastrar seu usuário e senha no menu Configurações -> Configurações de loja do seu Backoffice.
* Se não tiver contrato, será necessario inserir CEP de origem, o CEP de destino e o peso do produto, calculando o frete do produto a ser enviado.

### Como o comprador final escolhe o frete?

O frete é escolhido na tela de finalização de compra, onde serão exibidas as opções cadastradas pelo vendedor.

<aside class="notice">OBS: O lojista tambem tem a opção de calcular o valor do frete dentro de sua loja.</aside>

### Como o valor do frete é repassado ao lojista?

O frete é incluso no valor da transação (valor do pedido + frete = Valor da transação).

# Backoffice Cielo

Na tela inicial do Checkout Cielo é possivel se logar, se cadastrar ou fazer o login voltado para a Consulta a um
determinado pedido. Essa tela é acessada via a URL: [https://cieloecommerce.cielo.com.br/backoffice](https://cieloecommerce.cielo.com.br/backoffice)

![Backoffice Cielo - página inicial](/images/checkout-cielo-login.png)

### Páginas do Backoffice Cielo

O Backoffice é formado por 6 paginas diferentes de administração do Checkout Cielo. Elas são:

1. [DashBoard](#dashboard) - Pagina inicial onde são apresentadas informações sobre a sua conta e sobre o volume e tipo de transações que sua loja vem realizando vio Checkout Cielo.
2. [Pedidos](#pedidos) - Nessa pagina fica contida toda a listagem de transações realizadas por um determinado periodo de tempo no Checkout Cielo.
3. [Produtos](#produtos) - Nessa pagina são listados todos os produtos cadastrados no Checkout Cielo. Tambem é possivel fazer uma busca pelo nome do produto nesta pagina.
4. [Relatórios](#relatórios) - Nesta pagina é possivel gerar 03 tipos de relatorios: Relatório financeiro, Detalhado de vendas e Listagem de clientes.
5. [Manuais](#manuais) - Nesta pagina constam os manuais do Checkout Cielo, assim como a pagina de FAQ e de “Duvidas”, onde o lojista pode entrar em contato com a equipe de suporte Checkout Cielo.
6. [Configurações](#configurações) - Página onde é possivel fazer alterações nas configurações da Loja, dos seus dados cadastrais e Alterar sua Senha.

## DashBoard

Pagina inicial onde são apresentadas informações sobre a sua conta e sobre o volume e tipo de transações que sua
loja vem realizando vio Checkout Cielo.

### Nessa tela você encontra 2 tipos de informações.

* **Alertas** – Indica se há pedidos a expirar na data presente.
* **Volume financeiro e transacional** - São gráficos interativos que demonstram em porcentagem (e em valores totais) qual a participação de cada meio de pagamento no total de transações realizadas e o volume total de acordo com o status das transações.

![DashBoard](/images/checkout-cielo-dashboard.png)

## Pedidos

Nessa pagina fica contida toda a listagem de transações realizadas por um determinado periodo de tempo no Checkout Cielo. Nessa pagina é possivel pesquisar um determinado pedido , via a colocação de um determinado parametro de busca nos campos la existentes ou desmarcando os “checkbox” dos “Meios de pagamento” ou “Status de pagamento” e apertando o botão “Buscar”.

O resultado da pesquisa é exposta em forma de uma listagem de operações. Essa listagem pode ser exportada como forma de excel.

![Pedidos](/images/checkout-cielo-pedidos.png)

## Produtos

Nessa pagina são listados todos os produtos cadastrados no Checkout Cielo. Tambem é possivel fazer uma busca pelo nome do produto nesta pagina. A lista de produtos pode ser exportada no formato Excel.

No menu Produtos, há também outras duas áreas: **Cadastrar de produtos** e **Listar Produtos Cadastrados**.

### Listar Produtos Cadastrados

![Produtos cadastrador](/images/checkout-cielo-produtos-cadastrados.png)

Clicando no Titulo ou SKU do produto, você será redirecionado a página de informações de Produto, onde todas as caracteristicas do produto são informadas e onde você pode definir o padrão do Botão (caso a sua integração seja com base no Botão Checkout Cielo) a ser usado na venda desse produto.

![Detalhes do produto](/images/checkout-cielo-detalhes-produto.png)

### Cadastrar de Produtos

Nessa pagina é possivel cadastrar seus produtos com base no tipo de produto em si. O Checkout Cielo considera 3 tipos de produtos: Material Fisico, Digital e Serviço.

* **Material Fisico** – Produtos Fisicos que necessitam ser enviados pelos lojistas. Ex: Roupas, Brinquedos, etc.
* **Digital** – Bems digitais vendidos pela internet. Ex: Software, Jogos , Musicas, etc.
* **Serviço** – Serviços a serem prestados. Ex: Entrega delivery, projetos e orçamentos.

![Cadastrar produtos](/images/checkout-cielo-cadastrar-produtos.png)

<aside class="notice">Material Fisico exige que um tipo de frete seja cadastrado.</aside>

<aside class="notice">O campo “SoftDescriptor” permite que lojista insira uma mensagem que será exibida na fatura do cartão de crédito do comprador. Essa funcionalidade é indicada para lojas que tem o nome fantasia muito diferente em relação a Razão social.</aside>

<aside class="notice">A mensagem inclusa no campo “SoftDescriptor” deve ser limitada a 13 letras e não pode conter espaços. Exemplo: “Loja X” (errado) “Loja_X” (correto)</aside>

<aside class="notice">O cadastro de produtos não é obrigatório para lojistas que utilizem a integração via carrinho</aside>

## Relatórios

Nesta pagina é possivel gerar 03 tipos de relatorios: Relatório financeiro, Detalhado de vendas e Listagem de clientes.

### Relatorio Financeiro

Esse relatorio apresenta as vendas pagas em um determinado periodo de tempo, sendo separadas por meio de pagamento. Selecionando o periodo e tipo de pagamento, após pressionar “Buscar” o resultado será apresentado.

![Relatório financeiro](/images/checkout-cielo-relatorio-financeiro.png)

### Relatório detalhado de vendas.

Esse relatorio informa o valor de cada pedido assim como dados sobre o produto e o comprador. O relatorio somente informa dados dos pedidos considerados com status “PAGO”.

<aside class="notice">Pedidos realizados no Modo de teste não são apresentados nesse relatório, mesmo que estejam com o status “PAGO”</aside>

![Relatório detalhado de vendas](/images/checkout-cielo-relatorio-detalhado-de-venda.png)

### Listagem de clientes

A listagem de clientes gera um arquivo excel contendo os dados dos clientes que realizaram compras em sua loja.

#### Os dados apresentados são:

1. Nome
2. E-mail
3. Telefone
4. CPF
5. Endereço (como descrito pelo cliente ou como retornado pela informação do CEP)
6. Numero
7. Complemento (se houver)
8. Bairro
9. Cidade
10. Estado
11. CEP

### Extrato de cobrança

A lista do valor cobrado pelos serviços oferecidos pela Cielo será apresentada neste relatório. Todos os dados dos planos e custos transacionais serão apresentados aqui:

![Extrato de cobrança](/images/checkout-cielo-relatorio-extrato-cobranca.png)

## Manuais

Nesta pagina constam os manuais do Checkout Cielo, assim como a pagina de FAQ e de “Duvidas”, onde o lojista pode entrar em contato com a equipe de suporte Checkout Cielo.

![Dúvidas](/images/checkout-cielo-manuais-duvidas.png)

Nessa pagina é possivel entrar em contato a respeito de duvidas Operacionais, tecnicas e Comerciais e ter acesso aos documentos técnicos e de suporte do Checkout Cielo.

* **Manual do Desenvolvedor** – Contém os procedimentos e diretrizes de integração do Checkout Cielo ao seu site.
* **Tutorial do Lojista** – Principal fonte de informação sobre a utilização do Checkout Cielo do ponto de vista do Lojista
* **FAQ** – Perguntas mais comuns a respeito do Checkout Cielo. Contem informações sobre questões Comerciais, Técnicas, Operacionais e sobre o Modo de teste.

## Configurações

Página onde é possivel fazer alterações nas **configurações da Loja**, dos seus **dados cadastrais** e **Alterar sua Senha**.

### Configurações da Loja

Nesta pagina é possivel fazer configurações em diferentes mecanismos dentro do Checkout Cielo. Essa área é dividida em 4 partes diferentes: Exibição, Pagamentos, Antifraude, Frete de Correios & serviços.

#### Exibição

Aqui é possivel alterar o logo do meio de pagamento disponível em sua loja e a cor de fundo do site via o uso da caixa de opções ou digitando o código relativo a cor escolhida (As cores estão no padrão RGB).

![Configuração Exibição](/images/checkout-cielo-configuracao-exibicao.png)

<aside class="notice">O logo de sua loja será exibido na tela de Checkout centralizado.</aside>

#### Pagamentos

Nesta tela é possivel alterar as configurações dos meios de pagamento disponiveis em sua loja , os definindo como ativos ou não, e configurar a URL de retorno, notificação e Mudança de Status.

![Configuração Pagamentos](/images/checkout-cielo-configuracao-pagamentos.png)

##### Cartões de crédito e Parcelamento

Basta marcar a caixa de seleção do cartão que deseja disponibilizar no momento do pagamento. Para desabilita-lo, basta desmarcar a caixa de seleção O parcelamento é definido por bandeira de cartão. O numero de parcelas maximas disponibilizadas para cada cartão passa a ser definida pelo Lojista. O Checkout Cielo permite parcelamento até 12 vezes sem juros.

Há a opção de definir um valor minimo de parcelamento. O valor definido faz com que independentemente do valor da compra, o comprador somente possa fazer parcelamentos com valor acima do Valor Minimo.

**Exemplo:** Caso o valor minimo de parcelamento seja de R$10,00, uma compra de R$100,00 poderá ser parcelada máximo em 10x, mesmo que na configuração da loja o parcelamento em 12x esteja habilitado.

<aside class="warning">O numero maximo de parcelamento da loja depende do limite definido na Afiliação. Por padrão toda afiliação tem limite de 3 vezes.</aside>

<aside class="warning">O cartão American Express tem limite de parcelamento em 3 vezes por padrão. Essa configuração é standart da bandeira AMEX, logo , não é possivel aumentar o numero de parcelas alem deste limite.</aside>

##### URLs principais do Checkout Cielo

As URL’s devem ser cadastradas pelo próprio lojista no seu Backoffice, na aba “configurações / Configurações
da loja”.

* **URL de Retorno** - Ao finalizar a transação o comprador final poderá ser redirecionado para a URL de retorno. Ao clicar no botão “VOLTAR” na tela de comprovante de vendas, o comprador será direcionando para a URL de retorno previamente cadastrada no BackOffice.
* **URL de Notificação** - Ao finalizar uma transação é enviado um post com todos os dados da venda para a URL de Notificação, previamente cadastrada no BackOffice. O POST de notificação é enviado apenas no momento que a transação é finalizada, independentemente se houve alteração do status da transação.
* **URL de Mudança de Status** - Quando um pedido tiver seu status alterado, será enviando um post para a URL de Mudança de Status, previamente cadastrada no BackOffice. O POST de mudança de status não contem dados do carrinho, apenas dados de identificação do pedido.

Na tela de pedidos, dentro de cada transação, há a opção de reenvio do POST de mudança de status. Basta clicar nos botões azuis, marcados na imagem abaixo:

![Detalhes do pedido](/images/checkout-cielo-detalhe-pedidos.png)

##### Desconto para Boletos e débito online

É possivel realizar oferecer descontos nos meios de pagamento boleto e débito online. Esse desconto pode ser definido de duas maneiras.

* **Via Backoffice:** Basta selecionar o valor em (%) que o meio de pagamento virá a oferecer.
* **Via POST:** é possivel enviar o no POST do carrinho um parametro contendo o desconto (%) que o meio de pagamento virá a oferecer.

##### Valor Minimo de boleto

É possivel definir um valor minimo para que o boleto seja apresentado. Compras em valor inferior ao definido não tem o boleto disponibilizado na tela de checkout.

<aside class="warning">Se valor da compra for inferior, caso não haja outro meio de pagamento disponivel, não haverá opção para o comprador, obrigando-o a retornar a loja criar um carrinho com o valor acima do minimo.</aside>

Para evitar a situação descrita no alerta acima, sugerimos:

* Caso sua loja não tenha outros meios de pagamento, informe ao comprador sobre o valor minimo do boleto.
* Adquira outros meios de pagamento como Cartões de crédito (procedimento realizado pelo Checkout Cielo) ou débito online.

<aside class="warning">O valor minimo do boleto não funciona em caso de descontos definidos pelo lojista. Caso o lojista defina valor minimo de boleto de R$100,00 e um desconto de 10%, será gerado um boleto de R$90,00 (inferior ao minimo)</aside>

##### Antifraude

Aqui é possivel definir a automação dos processos de captura e cancelamento de pedidos com base no resultado da analise de anti-fraude.

<aside class="notice">Se o lojista não tem habilitado o antifraude em seu contrato junto a Cielo ou não enviar no POST a solicitação de analise de fraude, a captura automática não será executada. Caberá ao lojista a captura manual do pedido.</aside>

![Anti fraude](/images/checkout-cielo-anti-fraude.png)

##### Frete de Correios & Serviços

Nesta área você configura as opções de frete disponiveis em sua Loja.

Na seção sobre informações sobre frete há uma explicação mais detalhada sobre os tipos de fretes disponiveis no Checkout Cielo. Há tambem na área de fretes de Correiros, uma calculadora de frete para consultas (essa calculadora dá o valor de frete de cada tipo de frete cadastrado para um determinado peso e localidade)

![Frete Correios](/images/checkout-cielo-frete-correios.png)

##### Dados Cadastrais

Nesta seção, ficam listados os dados da loja cadastrada e do Lojista.

![Dados cadastrais](/images/checkout-cielo-dados-cadastrais.png)

##### Alterar sua Senha

Aqui é possivel alterar a senha de acesso ao Checkout Cielo.

![Alterar senha](/images/checkout-cielo-alterar-senha.png)