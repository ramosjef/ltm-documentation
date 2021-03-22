[Início](/readme.md) &raquo; Compra / Resgates &raquo; Autorização de compra em dinheiro

# Autorização de compra em dinheiro

O fluxo de autorização em dinheiro segue o mesmo da [Autorização de compra](/purchase/authorize.md), porém, possui as seguintes particularidades no payload:

- Os custos são em dinheiro:
    - 1 aqui equivale à 1 real, mesmo que o tipo seja "Points" (pontos), o valor deve ser convertido e enviado em Reais (BRL) nos campos destacados abaixo:
        - Items.SellingPrice
        - Items.CostPrice
        - Payment.CurrencyValue
&nbsp;

- Pagamento com cartão de crédito
    - O campo Payment.CreditCardPayment será obrigatório quando o tipo de pagamento (Payment.Type) for "CreditCard"
    - O valor da tarifa de cartão de crédito será incorporado no valor do pagamento (Payment.Value).
        - A tarifa é o resultado do percentual, configurado no catálogo para essa finalidade, aplicado nesse tipo de pagamento:
            - Ex.:
                - Valor do pagamento com cartão = 100
                - Tarifa configurada = 6%
                - Payment.CurrencyValue = 106.00
        - A configuração de tarifa encontra-se no catálogo.
        - O Valor deve ser arredondado 2 casas para cima.
&nbsp;

- Serão aceitos os seguintes tipos de pagamento
    - Pontos ou Cartão externo
    - Pontos + Cartão
    - Pontos + Cartão externo
    - Tentativas de pagamento fora das possibilidades acima cairão em fluxo de exceção.
&nbsp;

- O Payment é uma lista de pagamentos, onde cada objeto contém a seguinte configuração:

    ```
    {
        "$schema": "https://json-schema.org/draft/2019-09/schema",
        "type": "object",
        "properties": {
            "Type" : {
                "type": "string",                
                "enum" : ["Points", "ExternalCreditCard", "CreditCard"],
                "description": "O tipo do pagamento: Pontos, Cartão de crédito externo ou Cartão de crédito"
            },
            "CurrencyValue" : {                
                "type": "number",
                "description": "Valor do pagamento em dinheiro"
            },
            "CreditCardPayment" : {
                "type": "object",
                "properties": {
                    "CardBrandId": {                        
                        "type": "integer",
                        "description": "Id da bandeira (alinhado com a LTM)"
                    },
                    "CardNumber": {                        
                        "type": "string",
                        "description": "Número do cartão de crédito"
                    },
                    "ExpirationMonth": {                        
                        "type": "integer",
                        "description": "Mês de expiração do cartão"
                    },
                    "ExpirationYear": {                        
                        "type": "integer",
                        "description": "Ano de expiração do cartão"
                    },
                    "SecurityCode": {                        
                        "type": "string",
                        "description": "Código de segurança do cartão"
                    },
                    "CardHolderName": {                        
                        "type": "string",
                        "description": "Nome do titular do cartão"
                    },
                    "Installments": {                        
                        "type": "integer",
                        "description": "Quantidade de parcelas"
                    }
                }
            }
        }
    }
    ```
## Dicionário de Erros

| Código | Mensagem |Sub Mensagem| Sub | Descrição | Http Status |
|-|-|-|-|-|-|
|347|Pagamento não autorizado.|O valor de custo do item (CostPrice) não pode ser diferente do valor determinado pelo parceiro.|não|O valor do costPrice é zero ou  diferente do valor retornado do parceiro.|422|
|346|Pagamento não autorizado.|O valor da tarifa informada não corresponde ao percentual cadastrado no catálogo. Percentual da tarifa esperado: {0}%. Valor correspondente {1}.|não|Valor de tarifa de cartão incorreto|422|
|345|Pagamento não autorizado.|A quantidade máxima de parcelas permitidas deve ser: {0} parcela(s).|não|Quantidade máxima de parcelas|422|
|344|Pagamento não autorizado.|O valor mínimo da parcela deve ser {0}.|não|Valor mínimo de parcela|422|
|343|Pagamento Inválido|Combinação incorreta ou tipo de pagamento duplicado. As combinações de pagamento permitidas são: "Pontos", "Cartão de Crédito Externo", "Pontos + Cartão de Crédito" ou "Pontos + Cartão de Crédito Externo".|não|Pagamento Inválido|422|
|340|Este cartão está expirado|Tente com outro cartão.|não|Cartão Expirado|422|
|339|Este cartão está bloqueado|Tente com outro cartão.|não|Cartão Bloqueado|422|
|338|Problemas com o Cartão de Crédito|Verifique os dados do cartão ou tente com outro cartão.|não|Problemas com o Cartão de Crédito|422|
|337|Parece que seu cartão está cancelado|Tente com outro cartão.|não|Cartão Cancelado|422|
|336|Você excedeu o tempo limite para realizar essa compra||não|Time Out|422|
|335|Sua transação não foi autorizada. Vamos fazer uma nova tentativa?|Verifique os dados do cartão ou tente com outro cartão.|não|Não Autorizada|422|
332|Você não tem pontos suficientes para fazer {type}|Verifique o seu saldo de pontos e ajuste o valor desejado.|não|Saldo insuficiente do cliente|422|
313|Uma tentativa de resgate inválida com cartão de crédito foi efetuada.||não|Tentativa de resgate inválida com cartão de crédito.|422|
11|O valor da compra é diferente do valor de pagamento informado.||não|O valor da compra é diferente do valor de pagamento informado.|422|
11|Itens obrigatórios||não|Não foram informados os itens do pedido no payload|422|


Link da documentação técnica no portal do desenvolvedor

> https://portal.ltm.digital/docs/services/cloud-loyalty-marketplace-api/operations/OrderCloudLoyalty_Authorize
    