---
title: Integração Checkout Cielo

language_tabs:
  - json: JSON
  - shell: Shell
  - php: PHP

search: true
---

# Integração Checkout Cielo

O objetivo desta documentação é orientar o desenvolvedor sobre como integrar com a API Cielo, descrevendo as
funcionalidades, os métodos a serem utilizados, listando informações a serem enviadas e recebidas, e provendo exemplos.

O webservice Cielo utiliza uma tecnologia REST que deve ser usada sempre que houver um “carrinho de compras” a ser enviado, ou seja, no caso do consumidor navegar pelo site e escolher 1 ou mais produtos para adicionar ao carrinho e depois, então, finalizar a venda.

# Integração


## Endpoint

Todas as requisições serão enviadas para o webservice da Cielo utilizando o método POST para o endpoint `https://cieloecommerce.cielo.com.br/api/public/v1/orders`.

## Authentication

Por se tratar de transações, todas as requisições enviadas para a Cielo deverão ser autenticadas. A autenticação consiste no envio do MerchantId, que é o identificador único da loja, através do campo de cabeçalho HTTP MerchantId.

> Para autenticar você deve enviar o campo de cabeçalho MerchantId:

`MerchantId: 00000000-0000-0000-0000-000000000000`

```shell
curl -X POST \
     -H "MerchantId: 00000000-0000-0000-0000-000000000000" \
     -H "Content-Type: application/json"
     "https://cieloecommerce.cielo.com.br/api/public/v1/orders"
```

<aside class="notice">
Lembre-se de substituir `00000000-0000-0000-0000-000000000000` pelo seu MerchantId.
</aside>

## Requisição

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

### Cabeçalho HTTP

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|MerchantId|Guid|Sim|36|Identificador único da loja. **Formato:** 00000000-0000-0000-0000-000000000000|
|Content-type|Alphanumeric|Sim|n/a|Tipo do conteúdo da mensagem a ser enviada. **Utilizar:** "application/json"|

### Objeto raiz da requisição

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|OrderNumber|Alphanumeric|Opcional|0..64|Número do pedido da loja.|
|SoftDescriptor|Alphanumeric|Opcional|0..13|Texto para ser exibido na fatura do portador, após o nome do estabelecimento comercial.|
|Cart|[Cart](#cart)|Sim|n/a|Informações sobre o carrinho de compras.|
|Shipping|[Shipping](#shipping)|Sim|n/a|Informações sobre a entrega do pedido."|
|Payment|[Payment](#payment)|Conditional|n/a|Informações sobre o pagamento do pedido.|
|Customer|[Customer](#customer)|Condicional|n/a|Informações sobre dados pessoais do comprador.|
|Options|[Options](#options)|Conditional|n/a|Informações sobre opções configuráveis do pedido.|

## Resposta

### Em caso de sucesso

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

### Em caso de erro

```json
{
    "message":"An error has occurred."
}
```

|Campo|Tipo|Obrigatório|Tamanho|Descrição|
|-----|----|-----------|-------|---------|
|Message|String|Sim|1..254|Mensagem descritiva do erro|

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
