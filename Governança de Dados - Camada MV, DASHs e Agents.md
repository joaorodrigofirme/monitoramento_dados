
## Governança dos MVs

### Tabela com todos os Metrics View

| Catálogo | Schema (Avenida) | Metric View                   | Owner                            |
| -------- | ---------------- | ----------------------------- | -------------------------------- |
| gold     | ads              | mv_ads_bonification           | bi_team                          |
| gold     | ads              | mv_ads_message                | bi_team                          |
| gold     | ads              | mv_ads_offer                  | bi_team                          |
| gold     | ads              | mv_ads_receita_ads            | bruno.brinstein@crmbonus.com     |
| gold     | ads              | mv_ads_resgate                | bi_team                          |
| gold     | crmback          | mv_carrinhos                  | ext.wesley.silveira@crmbonus.com |
| gold     | crmback          | mv_pedidos                    | ext.wesley.silveira@crmbonus.com |
| gold     | crmback          | mv_produtos_visualizados      | ext.wesley.silveira@crmbonus.com |
| gold     | giftback         | mv_bonus_gerado_marca         | joao.ferreira@crmbonus.com       |
| gold     | giftback         | mv_bonus_gerado_master        | joao.ferreira@crmbonus.com       |
| gold     | giftback         | mv_bonus_perdido_marca        | joao.ferreira@crmbonus.com       |
| gold     | giftback         | mv_bonus_perdido_master       | joao.ferreira@crmbonus.com       |
| gold     | giftback         | mv_bonus_resgate_marca        | joao.ferreira@crmbonus.com       |
| gold     | giftback         | mv_bonus_resgate_master       | joao.ferreira@crmbonus.com       |
| gold     | giftback         | mv_bonus_vendatotal_marca     | joao.ferreira@crmbonus.com       |
| gold     | giftback         | mv_bonus_vendatotal_master    | joao.ferreira@crmbonus.com       |
| gold     | giftback         | mv_engajamento_marca          | joao.ferreira@crmbonus.com       |
| gold     | giftback         | mv_engajamento_master         | joao.ferreira@crmbonus.com       |
| gold     | presenteia       | mv_atendimentos               | bi_team                          |
| gold     | presenteia       | mv_contatos                   | bi_team                          |
| gold     | presenteia       | mv_convites                   | bi_team                          |
| gold     | presenteia       | mv_custo_meta                 | bi_team                          |
| gold     | presenteia       | mv_enderecos                  | bi_team                          |
| gold     | presenteia       | mv_kids                       | victor.martins@crmbonus.com      |
| gold     | presenteia       | mv_lembretes_criados          | bi_team                          |
| gold     | presenteia       | mv_lembretes_criados_2        | guilherme.moreira@crmbonus.com   |
| gold     | presenteia       | mv_lembretes_enviados         | bi_team                          |
| gold     | presenteia       | mv_numeros                    | victor.martins@crmbonus.com      |
| gold     | presenteia       | mv_pedidos                    | bi_team                          |
| gold     | presenteia       | mv_pedidos_2                  | guilherme.moreira@crmbonus.com   |
| gold     | presenteia       | mv_take_rate                  | bi_team                          |
| gold     | presenteia       | mv_usuario_com_engajamento    | bi_team                          |
| gold     | presenteia       | mv_usuarios                   | bi_team                          |
| gold     | valebonus        | mv_emissoes                   | bi_team                          |
| gold     | valebonus        | mv_resgate_ifood_embedded     | bruno.brinstein@crmbonus.com     |
| gold     | valebonus        | mv_resgates                   | bi_team                          |
| gold     | valebonus        | mv_valebonus_cadastro         | bi_team                          |
| gold     | valebonus        | mv_valebonus_resgate_usuarios | bruno.brinstein@crmbonus.com     |

### Measures dos Metrics Views


> Camada semântica completa: nome técnico, nome de exibição, fórmula (expr) e descrição de cada measure. Coletado via `DESCRIBE EXTENDED` no Databricks (2026-06-07).


#### 📢 ADS (5 MVs · 28 measures)


##### `gold.ads.mv_ads_bonification` — 3 measures

| Nome Técnico             | Nome de Exibição          | Fórmula (expr)               | Descrição                                                                                                                                                              |
| ------------------------ | ------------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `var_distinct_customers` | —                         | —                            | Número total de clientes únicos impactados.  Indica o alcance (reach) da estratégia de bonificação, removendo duplicidades de clientes que receberam mais de um bônus. |
| `var_qtd_bonus_gerado`   | Quantidade Total de Bônus | `COUNT(DISTINCT `bonus_id`)` | Volume total de transações de bônus emitidas.  Diferente de 'clientes únicos', esta métrica foca na quantidade de eventos de bonificação gerados com sucesso.          |
| `View Text`              | —                         | —                            | —                                                                                                                                                                      |


##### `gold.ads.mv_ads_message` — 9 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_payload_recebido` | — | — | Volume total bruto de requisições recebidas. É a base do funil (entrada de dados). |
| `var_usuarios_unicos` | Alcance de Usuários Únicos | `COUNT(DISTINCT waId) filter(where DeliverTime IS NOT NULL)` | Quantidade de clientes únicos (por WhatsApp ID) que efetivamente receberam a mensagem no dispositivo. |
| `var_solicitado` | Mensagens Solicitadas | `COUNT(DISTINCT request_uuid) filter(where requestTime IS NOT NULL)` | Etapa 1 do funil: Mensagens que entraram na fila de processamento e tiveram a solicitação registrada. |
| `var_enviados` | Mensagens Enviadas | `COUNT(DISTINCT request_uuid) filter(where sendSentTime IS NOT NULL)` | Etapa 2 do funil: Mensagens que saíram do sistema em direção ao provedor (ex: WhatsApp/Gateway). |
| `var_disparos` | Mensagens Entregues (Disparos) | `COUNT(DISTINCT request_uuid) filter(where DeliverTime IS NOT NULL)` | Etapa 3 do funil: Confirmação de entrega no dispositivo do usuário (Status: Delivered). |
| `var_lidos` | Mensagens Lidas | `COUNT(DISTINCT request_uuid) filter(where ReadTime IS NOT NULL)` | Etapa 4 do funil: Mensagens que foram visualizadas/lidas pelo cliente.  Utilize para calcular a taxa de abertura. |
| `var_ativou` | Ofertas Ativadas | `COUNT(DISTINCT request_uuid) filter(where flag_ativar_oferta <> 0)` | Conversão Final: Clientes que interagiram com a mensagem e ativaram a oferta sugerida.  Indica o sucesso direto da campanha. |
| `var_optout` | Total de Opt-outs | `count(distinct request_uuid) filter (where flag_opt_out = 1)` | Métrica de rejeição: Número de usuários que solicitaram o cancelamento (descadastro)  ao receber a mensagem. |
| `View Text` | — | — | — |


##### `gold.ads.mv_ads_offer` — 3 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_alcance` | — | — | Quantidade de usuários únicos (baseado no número do cliente) que foram impactados por pelo menos uma oferta.  Métrica de penetração de base. |
| `var_impressao` | Total de Impressões | `COUNT(Key)` | Volume total de vezes que as ofertas foram carregadas/exibidas.  Diferente do alcance, uma mesma pessoa pode gerar múltiplas impressões.  Indica a intensidade de exposição da marca. |
| `View Text` | — | — | — |


##### `gold.ads.mv_ads_receita_ads` — 10 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_cpa` | — | — | Valor que representa o custo por ação, útil para avaliar a eficiência da campanha na geração de ações desejadas. |
| `var_cpl` | CPL | `max(cpl)` | Valor que representa o custo por lead, importante para medir o custo de captação de leads nas campanhas. |
| `var_cpi` | CPI | `max(cpi)` | Valor que denota o custo por instalação, essencial para avaliar campanhas que visam a instalação de aplicativos. |
| `var_alcance` | Alcance | `sum(alcance)` | Contagem de usuários alcançados, fundamental para entender a abrangência da campanha publicitária. |
| `var_impressao` | Impressão | `sum(impressao)` | Número total de impressões geradas, que oferece uma visão da visibilidade da campanha. |
| `var_resgate_ads` | Resgate ADS | `sum(resgate_ads)` | Quantidade de resgates realizados através de campanhas ADS, importante para medir a eficácia das estratégias publicitárias. |
| `var_resgate_gb` | Resgate Giftback | `sum(resgate_gb)` | Quantidade de resgates realizados em campanhas GB, que indica o desempenho de campanhas específicas. |
| `var_valor_incremental` | Incremental Total | `sum(incremental)` | Valor adicional gerado a partir das campanhas, que ajuda a quantificar o impacto das ações publicitárias. |
| `var_valor_receita_ads` | Receita ADS | `sum(receita_ads)` | Receita ADS final considerando CPL, CPI, CPA e regras especiais. |
| `View Text` | — | — | — |


##### `gold.ads.mv_ads_resgate` — 3 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_receita_incremental` | — | — | Soma do valor financeiro (GMV) das vendas consideradas incrementais.  O filtro 'vl_incremental = 1' garante que apenas transações qualificadas  pelas regras de negócio sejam contabilizadas. |
| `var_qtd_bonus_resgatado` | Quantidade de Resgates | `COUNT(DISTINCT `bonus_id`)` | Volume total de bônus únicos que foram convertidos em compras.  Representa o sucesso absoluto da campanha em gerar transações. |
| `View Text` | — | — | — |


#### 🛒 CRMBACK (3 MVs · 20 measures)


##### `gold.crmback.mv_carrinhos` — 14 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `receita_potencial_bruta` | — | — | Soma total do valor de todos os carrinhos que chegaram à etapa de finalização de compra. |
| `qtde_carrinhos_monitorados` | Qtde Carrinhos Monitorados | `COUNT(1)` | Indicador que contabiliza o número absoluto de carrinhos |
| `qtde_carrinhos_convertidos` | Qtde Carrinhos Convertidos | `COUNT(1) FILTER (WHERE source.status_carrinho = 'converted')` | Indicador que contabiliza o número de carrinhos que completaram o ciclo de compra no site do e-commerce. |
| `ticket_medio_carrinho` | Ticket Médio Carrinho | `MEASURE(receita_potencial_bruta) / MEASURE(qtde_carrinhos_monitorados)` | Representa o valor médio contido em cada carrinho que inicia o processo de checkout, antes de qualquer perda por abandono |
| `receita_carrinhos_abandonados` | Receita Carrinhos Abandonados | `SUM(source.valor_total_carrinho) FILTER (WHERE source.status_carrinho = 'abandoned')` | Soma financeira total dos itens adicionados ao carrinho que não avançaram para conclusão de compra no fluxo de checkout. |
| `qtde_carrinhos_abandonados` | Qtde Carrinhos Abandonados | `COUNT(1) FILTER (WHERE source.status_carrinho = 'abandoned')` | Quantidade de carrinhos criados, mas não concluídos. |
| `taxa_abandono` | Taxa de Abandono | `(MEASURE(qtde_carrinhos_convertidos) / NULLIF(MEASURE(qtde_carrinhos_abandonados),\ \ 0)) * 100` | Indicador que mede o percentual de usuários ou clientes que iniciam um processo, mas o interrompem antes da conclusão. |
| `ticket_medio_carrinhos_abandonados` | Ticket Médio Carrinhos Abandonados | `MEASURE(receita_carrinhos_abandonados) / MEASURE(qtde_carrinhos_abandonados)` | Valor financeiro médio contido nos carrinhos que foram iniciados, mas não convertidos em venda. |
| `receita_pedidos_convertidos` | Receita Pedidos Convertidos | `SUM(source.valor_total_carrinho) FILTER (WHERE source.status_carrinho = 'converted')` | Soma total do valor de todos os carrinhos que chegaram à etapa de finalização de compra. |
| `carrinhos_identificados` | Carrinhos Identificados | `COUNT(1) FILTER (WHERE source.status_carrinho = 'abandoned' AND source.tipo_consumidor = 'IDENTIFIED') / COUNT(1) FILTER (WHERE source.status_carrinho = 'abandoned')` | Proporção da receita para usuários identificados |
| `taxa_carrinhos_identificados` | Taxa Carrinhos Identificados | `MEASURE(carrinhos_identificados) * 100` | — |
| `carrinhos_anonimos` | Carrinhos Anônimos | `COUNT(1) FILTER (WHERE source.status_carrinho = 'abandoned' AND source.tipo_consumidor = 'ANONYMOUS') / COUNT(1) FILTER (WHERE source.status_carrinho = 'abandoned')` | Proporção da receita para usuários anônimos |
| `taxa_carrinhos_anonimos` | Taxa Carrinhos Anônimos | `MEASURE(carrinhos_anonimos) * 100` | — |
| `View Text` | — | — | — |


##### `gold.crmback.mv_pedidos` — 4 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `qtde_pedidos` | — | — | Indicador que representa a quantidade de pedidos |
| `receita_total_bruta` | Receita Total Bruta | `SUM(source.valor_total_pedido)` | Soma total do valor de todos os pedidos |
| `ticket_medio_pedidos` | Ticket Medio Pedidos | `MEASURE(receita_total_bruta) / MEASURE(qtde_pedidos)` | — |
| `View Text` | — | — | — |


##### `gold.crmback.mv_produtos_visualizados` — 2 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `qtde_produtos_visualizados` | — | — | Representa o número total de linhas no conjunto de dados. Utilize esta medida para contar todas |
| `View Text` | — | — | — |


#### 🎁 GIFTBACK (10 MVs · 58 measures)


##### `gold.giftback.mv_bonus_gerado_marca` — 6 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_valor_bonus_gerado` | — | — | Somatório do valor total de bônus emitidos para os clientes no período e grupo analisado.  Representa o investimento total em incentivos do programa de fidelidade. |
| `var_valor_venda` | Valor Venda | `SUM(valor_venda)` | Somatório do valor total das vendas que foram capturadas pela interação |
| `var_qtde_bonus_gerado` | Quantidade de Bônus Gerado | `COUNT(DISTINCT bonus_id)` | Contagem de identificadores únicos de bônus emitidos.  Mede o volume de bônus gerados. |
| `var_qtde_users` | Quantidade de Usuários Distintos | `COUNT(DISTINCT codigo_usuario)` | Número de clientes únicos que receberam bônus no período.  Permite mensurar o alcance do programa e a base ativa de consumidores. |
| `var_ticket_medio` | Ticket Médio | `MEDIAN(valor_venda) filter (where tipo = 'FIDELIDADE' and valor_bonus >` | Valor mediano das vendas em transações de fidelidade com bônus.  Útil para entender o comportamento de gasto médio nas compras bonificadas. |
| `View Text` | — | — | — |


##### `gold.giftback.mv_bonus_gerado_master` — 6 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_valor_bonus_gerado` | — | — | Somatório do valor total de bônus emitidos para os clientes no período e grupo analisado.  Representa o investimento total em incentivos do programa de fidelidade. |
| `var_valor_venda` | Valor Venda | `SUM(valor_venda)` | Somatório do valor total das vendas associadas à geração de bônus. Esse é o valor que um percentual dele virá bônus. |
| `var_qtde_bonus_gerado` | Quantidade de Bônus Gerado | `COUNT(DISTINCT bonus_id)` | Contagem de identificadores únicos de bônus emitidos.  Mede o volume de bônus gerados. |
| `var_qtde_users` | Quantidade de Usuários Distintos | `COUNT(DISTINCT codigo_usuario)` | Número de clientes únicos que receberam bônus no período.  Permite mensurar o alcance do programa e a base ativa de consumidores. |
| `var_ticket_medio` | Ticket Médio | `MEDIAN(valor_venda) filter (where tipo = 'FIDELIDADE' and valor_bonus >` | Valor mediano das vendas em transações de fidelidade com bônus.  Útil para entender o comportamento de gasto médio nas compras bonificadas. |
| `View Text` | — | — | — |


##### `gold.giftback.mv_bonus_perdido_marca` — 6 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_valor_bonus_perdido` | — | — | Somatório do valor de todos os bônus que EXPIRARAM sem uso dentro da janela de validade considerada (após a aplicação da regra D-1). Representa o incentivo não convertido em consumo. |
| `var_qtde_bonus_perdido` | Qtde Bônus Perdido | `COUNT(DISTINCT bonus_id) filter(where situacao_bonus = 'PERDIDO')` | Quantidade de bônus únicos que expiraram sem uso no período de validade analisado. Útil para medir volume de oportunidades não convertidas. |
| `var_qtde_bonus_usado` | Qtde Bônus Usado | `COUNT(DISTINCT bonus_id) filter(where situacao_bonus = 'USADO')` | Quantidade de bônus únicos que foram resgatados (usados) antes do fim da validade na mesma janela de referência. Indica conversão do incentivo em consumo. |
| `var_qtde_bonus_total` | Qtde Bônus Total | `COUNT(DISTINCT bonus_id)` | Total de bônus únicos considerados na janela (independente da situação). Serve como base para taxas de conversão/retorno. |
| `var_retorno` | Taxa de Retorno | `ROUND(COUNT(DISTINCT bonus_id) filter(where situacao_bonus = 'USADO') /\ \ COUNT(DISTINCT bonus_id),4)` | Percentual de bônus USADOS sobre o total de bônus considerados na janela de validade (coorte). Interpretação prática: entre os bônus que expirariam nesse período de referência, qual fração conseguiu ser resgatada antes da expiração |
| `View Text` | — | — | — |


##### `gold.giftback.mv_bonus_perdido_master` — 6 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_valor_bonus_perdido` | — | — | Somatório do valor de todos os bônus que EXPIRARAM sem uso dentro da janela de validade considerada (após a aplicação da regra D-1). Representa o incentivo não convertido em consumo. |
| `var_qtde_bonus_perdido` | Qtde Bônus Perdido | `COUNT(DISTINCT bonus_id) filter(where situacao_bonus = 'PERDIDO')` | Quantidade de bônus únicos que expiraram sem uso no período de validade analisado. Útil para medir volume de oportunidades não convertidas. |
| `var_qtde_bonus_usado` | Qtde Bônus Usado | `COUNT(DISTINCT bonus_id) filter(where situacao_bonus = 'USADO')` | Quantidade de bônus únicos que foram resgatados (usados) antes do fim da validade na mesma janela de referência. Indica conversão do incentivo em consumo. |
| `var_qtde_bonus_total` | Qtde Bônus Total | `COUNT(DISTINCT bonus_id)` | Total de bônus únicos considerados na janela (independente da situação). Serve como base para taxas de conversão/retorno. |
| `var_retorno` | Taxa de Retorno | `ROUND(COUNT(DISTINCT bonus_id) filter(where situacao_bonus = 'USADO') /\ \ COUNT(DISTINCT bonus_id),4)` | Percentual de bônus USADOS sobre o total de bônus considerados na janela de validade (coorte). Interpretação prática: entre os bônus que expirariam nesse período de referência, qual fração conseguiu ser resgatada antes da expiração. |
| `View Text` | — | — | — |


##### `gold.giftback.mv_bonus_resgate_marca` — 10 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_valor_resgate` | — | — | Somatório do valor total de bônus utilizados pelos clientes nas compras.   Representa o montante de "desconto" concedido via programa de bonificação. |
| `var_receita_incremental_total` | receita Incremental Total | `SUM(`valor_incremental`) filter (where vl_incremental_total = 1)` | Valor total efetivamente pago pelos clientes após o uso do bônus — ou seja,  o valor adicional desembolsado além do crédito recebido.   Esta é a métrica central para mensurar a geração de receita incremental proveniente dos resgates. Utilizar apenas quando temos o filtro de marca ou loja |
| `var_receita_incremental_tipo` | receita Incremental por Tipo | `SUM(`valor_incremental`) filter (where vl_incremental_tipo = 1)` | Valor total efetivamente pago pelos clientes após o uso do bônus — ou seja,  o valor adicional desembolsado além do crédito recebido.   Esta é a métrica central para mensurar a geração de receita incremental proveniente dos resgates. Utilizar apenas quando temos o filtro de tipo. |
| `var_receita_incremental_tipodetalhe` | receita Incremental por Tipo Detalhe | `SUM(`valor_incremental`) filter (where vl_incremental_tipodetalhe = 1)` | Valor total efetivamente pago pelos clientes após o uso do bônus — ou seja,  o valor adicional desembolsado além do crédito recebido.   Esta é a métrica central para mensurar a geração de receita incremental proveniente dos resgates. Utilizar apenas quando temos o filtro de tipo_detalhe. |
| `var_receita_incremental_fracionado` | receita Incremental por Fracionado | `SUM(`valor_incremental_fracionado`)` | Valor efetivamente pago pelos clientes após o uso do bônus — ou seja,  o valor adicional desembolsado além do crédito recebido. No entanto, em uma versão fracionado pela quantidade de bônus usado em uma compra. |
| `var_roi` | ROI | `ROUND((SUM(`valor_incremental`) filter (where vl_incremental_total = 1)\ \ + SUM(`valor_resgate`)) / SUM(`valor_resgate`),1)` | Indicador quantas vezes a mais os clientes estão gastando frente ao valor do bônus recebido. |
| `var_qtd_bonus_resgatado` | Qtde de bônus resgatado | `COUNT(DISTINCT `bonus_id`)` | Contagem de bônus únicos que foram efetivamente utilizados pelos clientes.   Representa o volume de resgates realizados no período. |
| `var_desconto_2` | Desconto da Segunda Compra | `ROUND(SUM(`valor_resgate`) / (SUM(`valor_incremental`) filter (where vl_incremental_total\ \ = 1) + SUM(`valor_resgate`)),4)` | Percentual médio de desconto aplicado na *segunda compra*, considerando o valor de bônus utilizado em relação ao total pago (bônus + valor incremental).   Indica o nível de incentivo concedido ao cliente. |
| `var_desconto_efetivo` | Desconto Efetivo | `ROUND(SUM(`valor_resgate`) / (SUM(`valor_incremental`) filter (where vl_incremental_total\ \ = 1) + SUM(`valor_resgate`) + SUM(valor_venda)),4)` | Percentual de desconto efetivo considerando o total de bônus resgatados em relação à soma das receitas incremental, de resgate e de venda, valor que iniciou o bônus.   Reflete o impacto financeiro total do programa de bônus sobre o faturamento. Mede o percentual de desconto na jornada completa to cliente (geração e resgate). Esse indicador só faz sentido quando o tipo do bônus é FIDELIDADE. |
| `View Text` | — | — | — |


##### `gold.giftback.mv_bonus_resgate_master` — 10 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_valor_resgate` | — | — | Somatório do valor total de bônus utilizados pelos clientes nas compras.   Representa o montante de "desconto" concedido via programa de bonificação. |
| `var_receita_incremental_total` | receita Incremental Total | `SUM(`valor_incremental`) filter (where vl_incremental_total = 1)` | Valor total efetivamente pago pelos clientes após o uso do bônus — ou seja,  o valor adicional desembolsado além do crédito recebido.   Esta é a métrica central para mensurar a geração de receita incremental proveniente dos resgates. Utilizar apenas quando temos o filtro de master |
| `var_receita_incremental_tipo` | receita Incremental por Tipo | `SUM(`valor_incremental`) filter (where vl_incremental_tipo = 1)` | Valor total efetivamente pago pelos clientes após o uso do bônus — ou seja,  o valor adicional desembolsado além do crédito recebido.   Esta é a métrica central para mensurar a geração de receita incremental proveniente dos resgates. Utilizar apenas quando temos o filtro de tipo. |
| `var_receita_incremental_tipodetalhe` | receita Incremental por Tipo Detalhe | `SUM(`valor_incremental`) filter (where vl_incremental_tipodetalhe = 1)` | Valor total efetivamente pago pelos clientes após o uso do bônus — ou seja,  o valor adicional desembolsado além do crédito recebido.   Esta é a métrica central para mensurar a geração de receita incremental proveniente dos resgates. Utilizar apenas quando temos o filtro de tipo_detalhe. |
| `var_receita_incremental_fracionado` | receita Incremental por Fracionado | `SUM(`valor_incremental_fracionado`)` | Valor efetivamente pago pelos clientes após o uso do bônus — ou seja,  o valor adicional desembolsado além do crédito recebido. No entanto, em uma versão fracionado pela quantidade de bônus usado em uma compra. |
| `var_roi` | ROI | `ROUND((SUM(`valor_incremental`) filter (where vl_incremental_total = 1)\ \ + SUM(`valor_resgate`)) / SUM(`valor_resgate`),1)` | Indicador quantas vezes a mais os clientes estão gastando frente ao valor do bônus recebido. |
| `var_qtd_bonus_resgatado` | Qtde de bônus resgatado | `COUNT(DISTINCT `bonus_id`)` | Contagem de bônus únicos que foram efetivamente utilizados pelos clientes.   Representa o volume de resgates realizados no período. |
| `var_desconto_2` | Desconto da Segunda Compra | `ROUND(SUM(`valor_resgate`) / (SUM(`valor_incremental`) filter (where vl_incremental_total\ \ = 1) + SUM(`valor_resgate`)),4)` | Percentual médio de desconto aplicado na *segunda compra*, considerando o valor de bônus utilizado em relação ao total pago (bônus + valor incremental).   Indica o nível de incentivo concedido ao cliente. |
| `var_desconto_efetivo` | Desconto Efetivo | `ROUND(SUM(`valor_resgate`) / (SUM(`valor_incremental`) filter (where vl_incremental_total\ \ = 1) + SUM(`valor_resgate`) + SUM(valor_venda)),4)` | Percentual de desconto efetivo considerando o total de bônus resgatados em relação à soma das receitas incremental, de resgate e de venda, valor que iniciou o bônus.   Reflete o impacto financeiro total do programa de bônus sobre o faturamento. Mede o percentual de desconto na jornada completa to cliente (geração e resgate). Esse indicador só faz sentido quando o tipo do bônus é FIDELIDADE. |
| `View Text` | — | — | — |


##### `gold.giftback.mv_bonus_vendatotal_marca` — 3 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_qtde_vendas` | — | — | Quantidade total de pedidos/vendas no período selecionado, independente de terem gerado bônus. Métrica aditiva ao longo das dimensões da view. |
| `var_faturamento` | Faturamento | `SUM(valor_liquido)` | Somatório do valor líquido das vendas totais (valores efetivamente pagos), independente de participação no programa de bônus. Moeda: BRL. |
| `View Text` | — | — | — |


##### `gold.giftback.mv_bonus_vendatotal_master` — 3 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_qtde_vendas` | — | — | Quantidade total de pedidos/vendas no período selecionado, independente de terem gerado bônus. Métrica aditiva ao longo das dimensões da view. |
| `var_faturamento` | Faturamento | `SUM(valor_liquido)` | Somatório do valor líquido das vendas totais (valores efetivamente pagos), independente de participação no programa de bônus. Moeda: BRL. |
| `View Text` | — | — | — |


##### `gold.giftback.mv_engajamento_marca` — 4 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_total_bonus` | — | — | Quantidade total de bônus de fidelidade emitidos |
| `var_total_vendas` | Total de Vendas | `SUM(qtd_vendas)` | Quantidade total de vendas (pedidos) |
| `var_engajamento` | Engajamento | `ROUND(MEASURE(var_total_bonus) / NULLIF(MEASURE(var_total_vendas), 0),\ \ 4)` | Taxa de engajamento: percentual de bônus em relação às vendas. Quanto mais próximo de 100%, mais bônus são gerados a partir das vendas. Esse indicador precisa ser multiplicado por 100 para gerar o percentual, ou seja, 90% está nessa métrica como 0.9 |
| `View Text` | — | — | — |


##### `gold.giftback.mv_engajamento_master` — 4 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_total_bonus` | — | — | Quantidade total de bônus de fidelidade emitidos |
| `var_total_vendas` | Total de Vendas | `SUM(qtd_vendas)` | Quantidade total de vendas (pedidos) |
| `var_engajamento` | Engajamento | `ROUND( MEASURE(var_total_bonus) / NULLIF(MEASURE(var_total_vendas), 0),\ \ 4)` | Taxa de engajamento: percentual de bônus em relação às vendas. Quanto mais próximo de 100%, mais bônus são gerados a partir das vendas. Esse indicador precisa ser multiplicado por 100 para gerar o percentual, ou seja, 90% está nessa métrica como 0.9 |
| `View Text` | — | — | — |


#### 🎀 PRESENTEIA (15 MVs · 61 measures)


##### `gold.presenteia.mv_atendimentos` — 2 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_qntd_atendimentos` | — | — | Contagem total de tickets/atendimentos |
| `View Text` | — | — | — |


##### `gold.presenteia.mv_contatos` — 3 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_usuarios_distintos` | — | — | Quantidade de números de telefone únicos (usuários distintos) |
| `var_total_cadastrados` | Total de Cadastrados | `COUNT(phone_number)` | Quantidade total de contatos cadastrados (telefones) |
| `View Text` | — | — | — |


##### `gold.presenteia.mv_convites` — 3 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_total_convidados` | — | — | Total de convidados distintos por usuário e data de referência |
| `var_total_usuarios_com_convidados` | Usuários com Convites | `count(distinct user_id)` | Total de usuários que enviaram convites por data de referência |
| `View Text` | — | — | — |


##### `gold.presenteia.mv_custo_meta` — 5 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_enviado` | — | — | Total de mensagens enviadas |
| `var_custo` | Custo Total | `ROUND(SUM(amount_spent), 2)` | Custo total gasto com envios |
| `var_custo_medio` | Custo Médio | `ROUND(AVG(cost_per_delivered), 2)` | Custo médio por mensagem entregue |
| `var_ultimo_envio` | Último Envio | `MAX(start_date)` | Data do último envio realizado |
| `View Text` | — | — | — |


##### `gold.presenteia.mv_enderecos` — 3 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_total_usuarios` | — | — | Contagem total de usuários únicos |
| `var_total_enderecos` | Total de Endereços | `COUNT(1)` | Contagem total de endereços (um por usuário) |
| `View Text` | — | — | — |


##### `gold.presenteia.mv_kids` — 2 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_total_usuarios_distintos` | — | — | Contagem de usuários únicos |
| `View Text` | — | — | — |


##### `gold.presenteia.mv_lembretes_criados` — 4 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_total_lembretes` | — | — | Soma total de lembretes registrados por combinação das dimensões |
| `var_total_usuarios_com_lembretes` | Total de Usuários com lembretes | `COUNT(DISTINCT user_id)` | Número de usuários distintos que tiveram lembretes por granularidade das dimensões |
| `var_media_lembretes_usuario` | Média de lembretes por Usuário | `ROUND(MEASURE(var_total_lembretes) * 1.0 / NULLIF(MEASURE(var_total_usuarios_com_lembretes),\ \ 0), 1)` | Média de lembretes por usuário distinto que tem lembrete criado |
| `View Text` | — | — | — |


##### `gold.presenteia.mv_lembretes_criados_2` — 5 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `total_eventos_contato_valido` | — | — | Total de eventos ativos com contato válido (telefone preenchido, ativo, e relacionamento diferente de 19) |
| `total_eventos_com_aniversario` | Total de Eventos com Aniversário | `COUNT(CASE WHEN source.is_active = 1 AND uc.phone_number IS NOT NULL AND` | Total de eventos ativos com contato válido que possui data de aniversário |
| `total_usuarios_com_evento` | Total de Usuários com Evento | `COUNT(DISTINCT CASE WHEN source.is_active = 1 THEN source.user_id END)` | Número de usuários distintos que criaram eventos ativos |
| `media_eventos_usuario` | Média de Eventos por Usuário | `ROUND(CAST(total_eventos_contato_valido AS DOUBLE) / NULLIF(total_usuarios_com_evento,\ \ 0), 1)` | Média de eventos com contato válido por usuário distinto |
| `View Text` | — | — | — |


##### `gold.presenteia.mv_lembretes_enviados` — 4 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_total_lembretes` | — | — | Quantidade total de lembretes enviados |
| `var_total_conversoes` | Total de Conversões | `COUNT(*) FILTER(WHERE status = 'Conversão')` | Quantidade de lembretes que resultaram em conversão (compra em até 2 dias) |
| `var_total_apenas_lembretes` | Total Apenas Lembretes | `COUNT(*) FILTER(WHERE status = 'Apenas Lembrete')` | Quantidade de lembretes que não resultaram em conversão |
| `View Text` | — | — | — |


##### `gold.presenteia.mv_numeros` — 2 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_total_phone_numbers` | — | — | Contagem total de números de telefone |
| `View Text` | — | — | — |


##### `gold.presenteia.mv_pedidos` — 8 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_preco_produto` | — | — | Soma total do preço dos produtos |
| `var_quantidade` | Quantidade Total | `SUM(qtd)` | Quantidade total de itens |
| `var_custo_envio` | Custo Total de Envio | `SUM(custo_envio)` | Custo total de envio |
| `var_comissao_percentual` | Comissão Percentual Média | `AVG(comissao_percentual)` | Percentual médio de comissão |
| `var_valor_take_rate` | Valor Total Take Rate | `SUM(valor_take_rate)` | Valor total do take rate (comissão calculada) |
| `var_valor_total` | Valor Total do Pedido | `SUM(valor_total)` | Valor total do pedido (produto + envio) |
| `var_qntd_pedidos` | Quantidade de Pedidos | `COUNT(DISTINCT referential_id)` | Total de pedidos únicos |
| `View Text` | — | — | — |


##### `gold.presenteia.mv_pedidos_2` — 8 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `total_pedidos` | — | — | Quantidade total de pedidos distintos |
| `total_itens` | Total de Itens | `SUM(qtd)` | Quantidade total de itens vendidos |
| `valor_total` | Valor Total | `SUM(summary_final_price)` | Valor total dos pedidos (preço final) |
| `preco_medio_produto` | Preço Médio do Produto | `AVG(preco_produto)` | Preço médio dos produtos |
| `custo_total_envio` | Custo Total de Envio | `SUM(custo_envio)` | Custo total de envio |
| `valor_total_take_rate` | Valor Total Take Rate | `SUM(valor_take_rate)` | Valor total de take rate (comissão sobre preço + frete) |
| `ticket_medio` | Ticket Médio | `MEASURE(valor_total) / MEASURE(total_pedidos)` | Ticket médio por pedido |
| `View Text` | — | — | — |


##### `gold.presenteia.mv_take_rate` — 5 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_valor_produto` | — | — | Valor total dos produtos |
| `var_custo_envio` | Custo Envio | `ROUND(SUM(custo_envio), 2)` | Custo total de envio |
| `var_valor_total` | Valor Total | `ROUND(SUM(summary_final_price), 2)` | Valor total (summary_final_price do pedido) |
| `var_take_rate` | Take Rate Total | `ROUND(SUM(valor_take_rate), 2)` | Valor total de comissão (take rate) |
| `View Text` | — | — | — |


##### `gold.presenteia.mv_usuario_com_engajamento` — 3 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_usuarios_com_evento` | — | — | Count of distinct users who have events |
| `var_total_eventos` | Total Events | `COUNT(id)` | Total count of all events |
| `View Text` | — | — | — |


##### `gold.presenteia.mv_usuarios` — 4 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `var_usuarios_onboarding` | — | — | Quantidade de usuários ativos (onboarding) |
| `var_perfis_criados` | Perfis Criados | `COUNT(id) filter(where is_active = 1 AND deleted_at IS NULL AND validated_at` | Quantidade de usuários que finalizaram o onboarding (perfil criado) |
| `var_usuarios_deletados` | Usuários Deletados | `COUNT(id) filter(where is_active = 0 AND deleted_at IS NOT NULL)` | Quantidade de usuários deletados |
| `View Text` | — | — | — |


#### 🎫 VALEBONUS (5 MVs · 19 measures)


##### `gold.valebonus.mv_emissoes` — 4 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `emissoes` | — | — | Quantidade total de emissões |
| `emissoes_unicas` | — | `COUNT(DISTINCT codigo_usuario)` | Quantidade de Emissões únicas |
| `valor_emissao` | — | `SUM(valor_transacao)` | Valor total emitido |
| `View Text` | — | — | — |


##### `gold.valebonus.mv_resgate_ifood_embedded` — 6 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `qtde_carteira` | — | — | Carteiras distintas |
| `qtde_bonus` | Quantidade de Bônus | `COUNT(DISTINCT gb_bonus_id)` | Quantidade de bônus distintos |
| `qtde_bonus_usados` | Quantidade de Bônus Usados | `COUNT(DISTINCT CASE WHEN gb_used_value > 0 THEN gb_bonus_id END)` | Bônus usados |
| `usuarios_unicos` | Usuários Únicos | `COUNT(DISTINCT codigo_usuario)` | Usuários únicos |
| `valor_incremental` | Valor Incremental Total | `SUM(valor_incremental)` | Valor incremental total |
| `View Text` | — | — | — |


##### `gold.valebonus.mv_resgates` — 4 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `resgates` | — | — | Quantidade total de resgates |
| `valor_resgatado` | — | `SUM(valor_resgate)` | Valor total resgatado na moeda VB |
| `valor_faturamento_total` | — | `SUM(valor_faturamento)` | Valor total de faturamento em reais. |
| `View Text` | — | — | — |


##### `gold.valebonus.mv_valebonus_cadastro` — 3 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `saldo_medio` | — | — | Saldo médio dos usuários com saldo maior que zero |
| `total_usuarios` | Total de Usuários | `COUNT(DISTINCT codigo_usuario)` | Quantidade total de usuários únicos que se cadastraram |
| `View Text` | — | — | — |


##### `gold.valebonus.mv_valebonus_resgate_usuarios` — 2 measures

| Nome Técnico | Nome de Exibição | Fórmula (expr) | Descrição |
|---|---|---|---|
| `qtd_usuarios` | — | — | Quantidade de usuários únicos que realizaram resgates |
| `View Text` | — | — | — |


## Governança dos Dashs

### 📢 ADS

#### 📊 Ads -  Gerencial

---

#### Análise 1 — Fontes (tabelas) utilizadas

##### ✅ Metric Views usados (via Databricks)

| Tabela PBI | Metric View | Campos usados no visual |
|---|---|---|
| `f_mv_ads_message` | `gold.ads.mv_ads_message` | Disparos, Interesse, Clientes_Unicos |
| `f_mv_ads_bonification` | `gold.ads.mv_ads_bonification` | qtd_bonus_gerad, qtde_customers |
| `f_mv_ads_offer` | `gold.ads.mv_ads_offer` | qtd_alcance, impressao |
| `f_mv_ads_resgate` | `gold.ads.mv_ads_resgate` | qtd_bonus_resgatado, qtde_receita_incremental |
| `f_mv_ads_receita` | `gold.ads.mv_ads_receita_ads` | valor_receita, qtde_receita_incremental |

> ✅ **Todas as métricas exibidas nas telas são calculadas sobre Metric Views** via DAX (tabela `Medidas`). Os MVs são a única fonte de dados de fatos.

#### 📋 Tabelas de dimensão / suporte (não são Metric Views)

| Tabela PBI    | Fonte Databricks                                   | Uso                                                                |
| ------------- | -------------------------------------------------- | ------------------------------------------------------------------ |
| `dCalendar`   | DAX calculada                                      | Filtro de data (slicer) em todas as páginas                        |
| `brand`       | `bronze.giftback.brands` + `gold.ads.b2b_marca_cs` | Filtro de marca (`brand_name`)                                     |
| `pub_praca`   | `gold.ads.b2b_message`                             | Filtro de publisher (`new_publisher_name`) e praça (`square_name`) |
| `DeliverTime` | `gold.ads.b2b_message`                             | Indicador de último disparo (não aparece em visuais)               |
| `patrocinado` | `gold.ads.ads_patrocinado`                         | Não aparece em visuais — provável uso em filtros/medidas           |

---

#### Análise 2 — Detalhamento por página

| Página                    | Medida                             | Cálculo DAX                                                             | Cálculo SQL (Metric View)                               | Tabela Fonte                   |
| ------------------------- | ---------------------------------- | ----------------------------------------------------------------------- | ------------------------------------------------------- | ------------------------------ |
| Resultados Gerais - Marca | `%Gerado Impressão`                | `[Bonus Gerado] / [Impressao]`                                          | `[Bonus Gerado] / [Impressao]` (razão entre medidas)    | `—`                            |
| Resultados Gerais - Marca | `%Resgate`                         | `[Bonus Resgatado] / [Interesse]`                                       | `[Bonus Resgatado] / [Interesse]` (razão entre medidas) | `—`                            |
| Resultados Gerais - Marca | `ARI`                              | `[Receita ADS] / [Impressao]`                                           | `[Receita ADS] / [Impressao]` (razão entre medidas)     | `—`                            |
| Resultados Gerais - Marca | `Bonus Gerado`                     | `SUM(f_mv_ads_bonification[qtd_bonus_gerad])`                           | `MEASURE(var_qtd_bonus_gerado)`                         | `gold.ads.mv_ads_bonification` |
| Resultados Gerais - Marca | `Bonus Resgatado`                  | `SUM(f_mv_ads_resgate[qtd_bonus_resgatado])`                            | `MEASURE(var_qtd_bonus_resgatado)`                      | `gold.ads.mv_ads_resgate`      |
| Resultados Gerais - Marca | `Impressao`                        | `SUM(f_mv_ads_offer[impressao])`                                        | `MEASURE(var_impressao)`                                | `gold.ads.mv_ads_offer`        |
| Resultados Gerais - Marca | `Receita ADS`                      | `SUM(f_mv_ads_receita[valor_receita])`                                  | `MEASURE(var_valor_receita_ads)`                        | `gold.ads.mv_ads_receita_ads`  |
| Resultados Gerais - Marca | `Receita Incremental`              | `SUM(f_mv_ads_receita[qtde_receita_incremental])`                       | `MEASURE(var_valor_incremental)`                        | `gold.ads.mv_ads_receita_ads`  |
| Disparos                  | `%Interesse`                       | `[Interesse] / [Conversas]`                                             | `[Interesse] / [Conversas]` (razão entre medidas)       | `—`                            |
| Disparos                  | `Conversas`                        | `SUM(f_mv_ads_message[disparos])`                                       | `MEASURE(var_disparos)`                                 | `gold.ads.mv_ads_message`      |
| Disparos                  | `Conversas Mês Anterior`           | `CALCULATE([Conversas], DATEADD(dCalendar[Date], -1, MONTH))`           | `CALCULATE([Conversas], mês anterior)`                  | `—`                            |
| Disparos                  | `Interesse`                        | `SUM(f_mv_ads_message[Interesse])`                                      | `MEASURE(var_ativou)`                                   | `gold.ads.mv_ads_message`      |
| Disparos                  | `Interesse Mês Anterior`           | `CALCULATE([Interesse], DATEADD(dCalendar[Date], -1, MONTH))`           | `CALCULATE([Interesse], mês anterior)`                  | `—`                            |
| Bônus                     | `%Resgate`                         | `[Bonus Resgatado] / [Interesse]`                                       | `[Bonus Resgatado] / [Interesse]` (razão entre medidas) | `—`                            |
| Bônus                     | `Bonus Gerado`                     | `SUM(f_mv_ads_bonification[qtd_bonus_gerad])`                           | `MEASURE(var_qtd_bonus_gerado)`                         | `gold.ads.mv_ads_bonification` |
| Bônus                     | `Bonus Gerado Mês Anterior`        | `CALCULATE([Bonus Gerado], DATEADD(dCalendar[Date], -1, MONTH))`        | `CALCULATE([Bonus Gerado], mês anterior)`               | `—`                            |
| Bônus                     | `Bonus Resgatado`                  | `SUM(f_mv_ads_resgate[qtd_bonus_resgatado])`                            | `MEASURE(var_qtd_bonus_resgatado)`                      | `gold.ads.mv_ads_resgate`      |
| Bônus                     | `Bonus Resgatado Mês Anterior`     | `CALCULATE([Bonus Resgatado], DATEADD(dCalendar[Date], -1, MONTH))`     | `CALCULATE([Bonus Resgatado], mês anterior)`            | `—`                            |
| Receita                   | `Receita ADS`                      | `SUM(f_mv_ads_receita[valor_receita])`                                  | `MEASURE(var_valor_receita_ads)`                        | `gold.ads.mv_ads_receita_ads`  |
| Receita                   | `Receita ADS Mês Anterior`         | `CALCULATE([Receita ADS], DATEADD(dCalendar[Date], -1, MONTH))`         | `CALCULATE([Receita ADS], mês anterior)`                | `—`                            |
| Receita                   | `Receita Incremental`              | `SUM(f_mv_ads_receita[qtde_receita_incremental])`                       | `MEASURE(var_valor_incremental)`                        | `gold.ads.mv_ads_receita_ads`  |
| Receita                   | `Receita Incremental Mês Anterior` | `CALCULATE([Receita Incremental], DATEADD(dCalendar[Date], -1, MONTH))` | `CALCULATE([Receita Incremental], mês anterior)`        | `—`                            |
| Resumo                    | `%Gerado Impressão`                | `[Bonus Gerado] / [Impressao]`                                          | `[Bonus Gerado] / [Impressao]` (razão entre medidas)    | `—`                            |
| Resumo                    | `%Resgate iMP`                     | `[Bonus Resgatado] / [Impressao]`                                       | `[Bonus Resgatado] / [Impressao]` (razão entre medidas) | `—`                            |
| Resumo                    | `ARI`                              | `[Receita ADS] / [Impressao]`                                           | `[Receita ADS] / [Impressao]` (razão entre medidas)     | `—`                            |
| Resumo                    | `ARM`                              | `[Receita ADS] / [Conversas]`                                           | `[Receita ADS] / [Conversas]` (razão entre medidas)     | `—`                            |
| Resumo                    | `Alcance old`                      | `SUM(f_mv_ads_offer[qtd_alcance])`                                      | `MEASURE(var_alcance)`                                  | `gold.ads.mv_ads_offer`        |
| Resumo                    | `Bonus Gerado`                     | `SUM(f_mv_ads_bonification[qtd_bonus_gerad])`                           | `MEASURE(var_qtd_bonus_gerado)`                         | `gold.ads.mv_ads_bonification` |
| Resumo                    | `Bonus Resgatado`                  | `SUM(f_mv_ads_resgate[qtd_bonus_resgatado])`                            | `MEASURE(var_qtd_bonus_resgatado)`                      | `gold.ads.mv_ads_resgate`      |
| Resumo                    | `Impressao`                        | `SUM(f_mv_ads_offer[impressao])`                                        | `MEASURE(var_impressao)`                                | `gold.ads.mv_ads_offer`        |
| Resumo                    | `Receita ADS`                      | `SUM(f_mv_ads_receita[valor_receita])`                                  | `MEASURE(var_valor_receita_ads)`                        | `gold.ads.mv_ads_receita_ads`  |
| Resumo                    | `Receita Incremental`              | `SUM(f_mv_ads_receita[qtde_receita_incremental])`                       | `MEASURE(var_valor_incremental)`                        | `gold.ads.mv_ads_receita_ads`  |
| Resumo Praça              | `%Gerado Impressão`                | `[Bonus Gerado] / [Impressao]`                                          | `[Bonus Gerado] / [Impressao]` (razão entre medidas)    | `—`                            |
| Resumo Praça              | `%Interesse`                       | `[Interesse] / [Conversas]`                                             | `[Interesse] / [Conversas]` (razão entre medidas)       | `—`                            |
| Resumo Praça              | `%Resgate`                         | `[Bonus Resgatado] / [Interesse]`                                       | `[Bonus Resgatado] / [Interesse]` (razão entre medidas) | `—`                            |
| Resumo Praça              | `ARI`                              | `[Receita ADS] / [Impressao]`                                           | `[Receita ADS] / [Impressao]` (razão entre medidas)     | `—`                            |
| Resumo Praça              | `ARM`                              | `[Receita ADS] / [Conversas]`                                           | `[Receita ADS] / [Conversas]` (razão entre medidas)     | `—`                            |
| Resumo Praça              | `Alcance old`                      | `SUM(f_mv_ads_offer[qtd_alcance])`                                      | `MEASURE(var_alcance)`                                  | `gold.ads.mv_ads_offer`        |
| Resumo Praça              | `Bonus Gerado`                     | `SUM(f_mv_ads_bonification[qtd_bonus_gerad])`                           | `MEASURE(var_qtd_bonus_gerado)`                         | `gold.ads.mv_ads_bonification` |
| Resumo Praça              | `Bonus Resgatado`                  | `SUM(f_mv_ads_resgate[qtd_bonus_resgatado])`                            | `MEASURE(var_qtd_bonus_resgatado)`                      | `gold.ads.mv_ads_resgate`      |
| Resumo Praça              | `Conversas`                        | `SUM(f_mv_ads_message[disparos])`                                       | `MEASURE(var_disparos)`                                 | `gold.ads.mv_ads_message`      |
| Resumo Praça              | `Impressao`                        | `SUM(f_mv_ads_offer[impressao])`                                        | `MEASURE(var_impressao)`                                | `gold.ads.mv_ads_offer`        |
| Resumo Praça              | `Interesse`                        | `SUM(f_mv_ads_message[Interesse])`                                      | `MEASURE(var_ativou)`                                   | `gold.ads.mv_ads_message`      |
| Resumo Praça              | `Receita ADS`                      | `SUM(f_mv_ads_receita[valor_receita])`                                  | `MEASURE(var_valor_receita_ads)`                        | `gold.ads.mv_ads_receita_ads`  |
| Resumo Praça              | `Receita Incremental`              | `SUM(f_mv_ads_receita[qtde_receita_incremental])`                       | `MEASURE(var_valor_incremental)`                        | `gold.ads.mv_ads_receita_ads`  |
| Resultados Gerais         | `%Gerado`                          | `[Bonus Gerado] / [Interesse]`                                          | `[Bonus Gerado] / [Interesse]` (razão entre medidas)    | `—`                            |
| Resultados Gerais         | `%Interesse`                       | `[Interesse] / [Conversas]`                                             | `[Interesse] / [Conversas]` (razão entre medidas)       | `—`                            |
| Resultados Gerais         | `%Resgate`                         | `[Bonus Resgatado] / [Interesse]`                                       | `[Bonus Resgatado] / [Interesse]` (razão entre medidas) | `—`                            |
| Resultados Gerais         | `ARM`                              | `[Receita ADS] / [Conversas]`                                           | `[Receita ADS] / [Conversas]` (razão entre medidas)     | `—`                            |
| Resultados Gerais         | `Bonus Gerado`                     | `SUM(f_mv_ads_bonification[qtd_bonus_gerad])`                           | `MEASURE(var_qtd_bonus_gerado)`                         | `gold.ads.mv_ads_bonification` |
| Resultados Gerais         | `Bonus Resgatado`                  | `SUM(f_mv_ads_resgate[qtd_bonus_resgatado])`                            | `MEASURE(var_qtd_bonus_resgatado)`                      | `gold.ads.mv_ads_resgate`      |
| Resultados Gerais         | `Conversas`                        | `SUM(f_mv_ads_message[disparos])`                                       | `MEASURE(var_disparos)`                                 | `gold.ads.mv_ads_message`      |
| Resultados Gerais         | `Interesse`                        | `SUM(f_mv_ads_message[Interesse])`                                      | `MEASURE(var_ativou)`                                   | `gold.ads.mv_ads_message`      |
| Resultados Gerais         | `Receita ADS`                      | `SUM(f_mv_ads_receita[valor_receita])`                                  | `MEASURE(var_valor_receita_ads)`                        | `gold.ads.mv_ads_receita_ads`  |
| Resultados Gerais         | `Receita Incremental`              | `SUM(f_mv_ads_receita[qtde_receita_incremental])`                       | `MEASURE(var_valor_incremental)`                        | `gold.ads.mv_ads_receita_ads`  |
| Resumo dia                | `%Gerado Impressão`                | `[Bonus Gerado] / [Impressao]`                                          | `[Bonus Gerado] / [Impressao]` (razão entre medidas)    | `—`                            |
| Resumo dia                | `%Interesse`                       | `[Interesse] / [Conversas]`                                             | `[Interesse] / [Conversas]` (razão entre medidas)       | `—`                            |
| Resumo dia                | `%Resgate`                         | `[Bonus Resgatado] / [Interesse]`                                       | `[Bonus Resgatado] / [Interesse]` (razão entre medidas) | `—`                            |
| Resumo dia                | `ARI`                              | `[Receita ADS] / [Impressao]`                                           | `[Receita ADS] / [Impressao]` (razão entre medidas)     | `—`                            |
| Resumo dia                | `ARM`                              | `[Receita ADS] / [Conversas]`                                           | `[Receita ADS] / [Conversas]` (razão entre medidas)     | `—`                            |
| Resumo dia                | `Alcance`                          | `SUM(f_mv_ads_message[Clientes_Unicos])`                                | `MEASURE(var_usuarios_unicos)`                          | `gold.ads.mv_ads_message`      |
| Resumo dia                | `Bonus Gerado`                     | `SUM(f_mv_ads_bonification[qtd_bonus_gerad])`                           | `MEASURE(var_qtd_bonus_gerado)`                         | `gold.ads.mv_ads_bonification` |
| Resumo dia                | `Bonus Resgatado`                  | `SUM(f_mv_ads_resgate[qtd_bonus_resgatado])`                            | `MEASURE(var_qtd_bonus_resgatado)`                      | `gold.ads.mv_ads_resgate`      |
| Resumo dia                | `Conversas`                        | `SUM(f_mv_ads_message[disparos])`                                       | `MEASURE(var_disparos)`                                 | `gold.ads.mv_ads_message`      |
| Resumo dia                | `Impressao`                        | `SUM(f_mv_ads_offer[impressao])`                                        | `MEASURE(var_impressao)`                                | `gold.ads.mv_ads_offer`        |
| Resumo dia                | `Interesse`                        | `SUM(f_mv_ads_message[Interesse])`                                      | `MEASURE(var_ativou)`                                   | `gold.ads.mv_ads_message`      |
| Resumo dia                | `Receita ADS`                      | `SUM(f_mv_ads_receita[valor_receita])`                                  | `MEASURE(var_valor_receita_ads)`                        | `gold.ads.mv_ads_receita_ads`  |
| Resumo dia                | `Receita Incremental`              | `SUM(f_mv_ads_receita[qtde_receita_incremental])`                       | `MEASURE(var_valor_incremental)`                        | `gold.ads.mv_ads_receita_ads`  |


---

#### Análise 3 — Semântica dos Metric Views (gold.ads)

> Definições e comentários extraídos diretamente do Unity Catalog (Databricks) em 2026-06-05.
> **"Usado no Dash?"** refere-se ao dashboard *Ads - Gerencial*.

#### Tabela 1 — Metric Views e suas fontes SQL

| Metric View | Descrição | Tabela Fonte | Filtro Padrão |
|---|---|---|---|
| `mv_ads_message` | Rastreia o funil completo de mensageria (WhatsApp/Push) do ADS B2B — do payload técnico até a ativação da oferta. | `gold.ads.b2b_message` | `year(data_payload) >= 2025` |
| `mv_ads_bonification` | Registros detalhados de bonificações geradas via ADS B2B. Performance de campanhas, engajamento de publishers e volume de incentivo. | `gold.ads.b2b_bonification` | `year(bonus_created) >= 2025` |
| `mv_ads_offer` | Registra a entrega e visualização de ofertas B2B. Mede eficácia da vitrine de ofertas e volume de exposição por marca e canal. | `gold.ads.b2b_offer` | `year(data_entrega_oferta) >= 2025` |
| `mv_ads_resgate` | Fechamento da jornada: registra quando um bônus ADS foi efetivamente utilizado em uma compra, gerando valor incremental. | `gold.ads.vw_ads_resgate` | `—` |
| `mv_ads_receita_ads` | Apuração de receita ADS com regras de CPL, CPI e CPA. Fonte das métricas financeiras das campanhas. | CTE multi-tabela: `gold.ads.cpl_cpi_cpa`, `gold.ads.b2b_offer`, `gold.ads.b2b_bonification`, `gold.ads.b2b_message` | `—` |

#### Tabela 2 — Measures por Metric View

| Metric View           | Measure                   | Descrição (Unity Catalog)                                                                                                          | Usado no Dash? |
| --------------------- | ------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- | -------------- |
| `mv_ads_message`      | `var_payload_recebido`    | Volume total bruto de requisições recebidas. É a base do funil (entrada de dados).                                                 | ❌ Não          |
| `mv_ads_message`      | `var_usuarios_unicos`     | Quantidade de clientes únicos (por WhatsApp ID) que efetivamente receberam a mensagem no dispositivo.                              | ✅ Sim          |
| `mv_ads_message`      | `var_solicitado`          | Etapa 1 do funil: Mensagens que entraram na fila de processamento e tiveram a solicitação registrada.                              | ❌ Não          |
| `mv_ads_message`      | `var_enviados`            | Etapa 2 do funil: Mensagens que saíram do sistema em direção ao provedor (ex: WhatsApp/Gateway).                                   | ❌ Não          |
| `mv_ads_message`      | `var_disparos`            | Etapa 3 do funil: Confirmação de entrega no dispositivo do usuário (Status: Delivered).                                            | ✅ Sim          |
| `mv_ads_message`      | `var_lidos`               | Etapa 4 do funil: Mensagens visualizadas/lidas pelo cliente. Utilize para calcular a taxa de abertura.                             | ❌ Não          |
| `mv_ads_message`      | `var_ativou`              | Conversão Final: Clientes que interagiram com a mensagem e ativaram a oferta sugerida. Indica o sucesso direto da campanha.        | ✅ Sim          |
| `mv_ads_message`      | `var_optout`              | Métrica de rejeição: Usuários que solicitaram o cancelamento ao receber a mensagem.                                                | ❌ Não          |
| `mv_ads_bonification` | `var_distinct_customers`  | Número total de clientes únicos impactados. Indica o alcance (reach) da estratégia de bonificação, removendo duplicidades.         | ❌ Não          |
| `mv_ads_bonification` | `var_qtd_bonus_gerado`    | Volume total de transações de bônus emitidas. Foca na quantidade de eventos de bonificação gerados com sucesso.                    | ✅ Sim          |
| `mv_ads_offer`        | `var_alcance`             | Quantidade de usuários únicos impactados por pelo menos uma oferta. Métrica de penetração de base.                                 | ✅ Sim          |
| `mv_ads_offer`        | `var_impressao`           | Volume total de vezes que as ofertas foram carregadas/exibidas. Uma mesma pessoa pode gerar múltiplas impressões.                  | ✅ Sim          |
| `mv_ads_resgate`      | `var_qtd_bonus_resgatado` | Volume total de bônus únicos que foram convertidos em compras. Representa o sucesso absoluto da campanha em gerar transações.      | ✅ Sim          |
| `mv_ads_resgate`      | `var_receita_incremental` | Soma do valor financeiro (GMV) das vendas incrementais. Apenas transações qualificadas pelas regras de negócio são contabilizadas. | ✅ Sim          |
| `mv_ads_receita_ads`  | `var_cpa`                 | Custo por ação — avalia a eficiência da campanha na geração de ações desejadas.                                                    | ❌ Não          |
| `mv_ads_receita_ads`  | `var_cpl`                 | Custo por lead — mede o custo de captação de leads nas campanhas.                                                                  | ❌ Não          |
| `mv_ads_receita_ads`  | `var_cpi`                 | Custo por instalação — avalia campanhas que visam instalação de aplicativos.                                                       | ❌ Não          |
| `mv_ads_receita_ads`  | `var_alcance`             | Contagem de usuários alcançados pela campanha publicitária.                                                                        | ❌ Não          |
| `mv_ads_receita_ads`  | `var_impressao`           | Número total de impressões geradas pela campanha.                                                                                  | ❌ Não          |
| `mv_ads_receita_ads`  | `var_resgate_ads`         | Quantidade de resgates realizados através de campanhas ADS.                                                                        | ❌ Não          |
| `mv_ads_receita_ads`  | `var_resgate_gb`          | Quantidade de resgates realizados em campanhas GB.                                                                                 | ❌ Não          |
| `mv_ads_receita_ads`  | `var_valor_incremental`   | Valor adicional gerado a partir das campanhas. Quantifica o impacto incremental das ações publicitárias.                           | ✅ Sim          |
| `mv_ads_receita_ads`  | `var_valor_receita_ads`   | Receita ADS final considerando CPL, CPI, CPA e regras especiais de apuração.                                                       | ✅ Sim          |


#### Pontos de Atenção:

No Metric View mv_ads_receita_ads, tem uma tabela gold.ads.cpl_cpi_cpa que monta a receita do ADS. Essa tabela vem de um sheet atualizado pela área de negócio. Não tem Job, a atualização é Manual. Existem mais 2 tabelas que são de atualização Manual (Ofertas desintegradas e Patrocinado)

#### Análise de Governança — Ads - Gerencial

**Nível: 🟠 Moderado** — ✅ Verde 89% (MV+Gold) | 🟡 Amarelo 0% (Silver) | 🔴 Vermelho 11% (Bronze/Externas)

> **Regra de governança:** ✅ Verde = Metric View ou Gold | 🟡 Amarelo = Silver | 🔴 Vermelho = Bronze ou fontes externas (ex: Google Sheets)

| Tabela Databricks | Classificação | Usada em (tabela PBI) | Observação |
|---|---|---|---|
| `gold.ads.mv_ads_bonification` | ✅ Metric View | `f_mv_ads_bonification` | Padrão ideal ✅ |
| `gold.ads.mv_ads_message` | ✅ Metric View | `f_mv_ads_message` | Padrão ideal ✅ |
| `gold.ads.mv_ads_offer` | ✅ Metric View | `f_mv_ads_offer` | Padrão ideal ✅ |
| `gold.ads.mv_ads_receita_ads` | ✅ Metric View | `f_mv_ads_receita` | Padrão ideal ✅ |
| `gold.ads.mv_ads_resgate` | ✅ Metric View | `f_mv_ads_resgate` | Padrão ideal ✅ |
| `gold.ads.ads_patrocinado` | ✅ Gold | `patrocinado` | Sem MV correspondente. Candidata a novo Metric View mv_ads_patrocinado. |
| `gold.ads.b2b_marca_cs` | ✅ Gold | `brand` | Mapeamento marca → CS. Candidata a dim_marca no MV. |
| `gold.ads.b2b_message` | ✅ Gold | `DeliverTime`, `pub_praca` | Coberta parcialmente por mv_ads_message/mv_ads_resgate. Avaliar se dim publisher/praça pode entrar como dimensão nos MVs. |
| `bronze.giftback.brands` | 🔴 Bronze | `brand` | Cadastro de marcas sem tratamento. Criar gold.giftback.dim_brands ou absorver nos MVs como dimensão. |

##### 🚨 Mensagem para o time de Engenharia de Dados

> **Contexto:** O dashboard *Ads - Gerencial* consome 1 tabela(s) bronze diretamente, representando risco de qualidade, falta de tratamento e possível exposição de dados sensíveis.

**Ação solicitada — Bronze → Gold:**
- `bronze.giftback.brands` — Cadastro de marcas sem tratamento. Criar gold.giftback.dim_brands ou absorver nos MVs como dimensão.

> **Resultado esperado:** Criar tabelas gold (ou Metric Views) equivalentes e atualizar os datasets do Power BI para eliminar o consumo direto de bronze em dashboards de negócio.

##### 🔍 Gold usado no lugar de Metric View — avaliar atualização

- `gold.ads.ads_patrocinado` — Sem MV correspondente. Candidata a novo Metric View mv_ads_patrocinado.
- `gold.ads.b2b_marca_cs` — Mapeamento marca → CS. Candidata a dim_marca no MV.
- `gold.ads.b2b_message` — Coberta parcialmente por mv_ads_message/mv_ads_resgate. Avaliar se dim publisher/praça pode entrar como dimensão nos MVs.

---

#### 📊 Ads - Acompanhamento Motor

#### Análise 1 — Fontes

| Tabela PBI | Tipo | Tabelas no FROM |
|---|---|---|
| `f_ads_geral_motor` | ⚠️ SQL direto | `bronze.giftback.ads_activities`, `gold.ads.b2b_bonification`, `gold.ads.b2b_message`, `gold.ads.b2b_offer` |
| `dCalendar` | 📐 DAX | — |
| `DeliverTime` | ⚠️ SQL direto | `gold.ads.b2b_message` |
| `brand` | ⚠️ SQL direto | `bronze.giftback.brands`, `gold.ads.b2b_marca_cs`, `gold.ads.b2b_offer` |
| `pub_praca` | ⚠️ SQL direto | `gold.ads.b2b_message` |
| `Brand_Offer` | ⚠️ SQL direto | `bronze.giftback.ads_offers`, `bronze.giftback.brands`, `gold.ads.b2b_marca_cs` |

##### SQL completo por tabela

**`f_ads_geral_motor`**
```sql
with cte1 as (

    SELECT
    a.request_uuid,
    a.ads_publisher_id,
    a.ads_publisher_square_id,
    concat(a.ads_publisher_square_id,'_',a.ads_publisher_id) as key, 
    pe.oferta,
    pe.codigo_marca,
    pe.ordem,
    pe.restricted,
    pe.rank_motor_final,
     case when pe.rank_motor_final = 1 then 'CRMBACK'
          when pe.rank_motor_final = 2 then 'CONTEUDO'
          when pe.rank_motor_final = 3 then 'COLABORATIVO'
          when pe.rank_motor_final = 4 then 'POPULARIDADE'
          when pe.rank_motor_final = 0 then 'PRIORIZADO'
          when pe.rank_motor_final = 5 then 'COLABORATIVO'
          else pe.rank_motor_final
        end as Motor,
    to_date(a.created) AS created_date,
  count(distinct get_json_object(a.payload_publisher, '$.phonenumber')) alcance
  FROM bronze.giftback.ads_activities a
  LATERAL VIEW explode(
    from_json(
      a.payload_engine_recomendation,
      'array<struct<
        row_num:int,
        oferta:string,
        codigo_marca:int,
        ordem:int,
        rank_motor_final:int,
        restricted:string
      >>'
    )
  ) t
   AS pe
  WHERE  date(a.created) >= add_months(date_trunc('month', current_date()), -2)
    AND a.is_test = 0 and webhook_trigger_status = 'send_message_to_meta_with_success' and pe.ordem <= 6
    group by all
)
select  
date(m.data_payload) date,
o.bonusb2b_campaign_id,
concat(o.square_id,'_',o.bonusb2b_campaign_id) as key, 
o.square_id,
o.brand_id,
o.offerId,
c.motor,
c.ordem,
c.restricted,
count(o.key) impressao,
count(distinct b.bonus_id)qtd_bonus,
count(distinct m.request_uuid) Conversas,
count(DISTINCT CASE WHEN b.bonus_status = 'RESGATADO' THEN b.bonus_id END) AS qtd_bonus_Usados
from gold.ads.b2b_offer o
left join gold.ads.b2b_bonification b 
          on o.request_uuid = b.request_uuid and o.offerId = b.offer_id
left join cte1 c 
          on o.request_uuid = c.request_uuid and o.offerId = c.oferta 
left join gold.ads.b2b_message m 
          on o.request_uuid = m.request_uuid
group by all
```

**`DeliverTime`**
```sql
select 
        to_timestamp(max(DeliverTime)) AS ultimo_disparo 
    from
        gold.ads.b2b_message 
    where 
        DeliverTime > current_date-3
```

**`brand`**
```sql
select 
distinct brand_id, 
UPPER(b.name)brand_name,
upper(CASE 
                WHEN brand_id IN (5703, 5706, 5707, 5708, 5709, 5710, 5711, 5713, 5715,5704,5705,5712) THEN 'IFOOD'
    ELSE brand_name
    END) AS SubBrand,
    UPPER(case when m.CS is null then 'Sem Responsável' else m.CS end) as CS
from gold.ads.b2b_offer o
left join bronze.giftback.brands b on o.brand_id = b.id
left join gold.ads.b2b_marca_cs m on replace(id_marca,'.','') = o.brand_id
```

**`pub_praca`**
```sql
with base as (
select distinct
    concat(coalesce(square_id,0),'_',bonusb2b_campaign_id)                             as key,
    case when bonusb2b_campaign_id in (9,37) then 'Sem Parar - Estacione'
    when bonusb2b_campaign_id in (5,39) then 'Sem Parar - Abastece'
    when bonusb2b_campaign_id in (3,43) then 'Click - Embarque'
    else publisher_name end as new_publisher_name,
    square_id,
    case when square_id is null then 'Desconhecido' else square_name end as square_name,
    publisher_name,
    bonusb2b_campaign_id
from gold.ads.b2b_message )

select *, row_number()over(partition by key order by square_id) as rnk
from base
qualify rnk = 1
order by key
```

**`Brand_Offer`**
```sql
select 
distinct o.id,
brand_id,
title,
b.name,
m.cs
from bronze.giftback.ads_offers o
left join bronze.giftback.brands b on o.brand_id = b.id
left join gold.ads.b2b_marca_cs m on replace(id_marca,'.','') = o.brand_id
```

#### Análise 2 — Medidas por página

| Página | Medida | Cálculo DAX |
|---|---|---|
| Principal | `Bonus_resgatados` | `sum(f_ads_geral_motor[qtd_bonus_Usados])` |
| Principal | `Impressao` | `sum(f_ads_geral_motor[impressao])` |
| Principal | `Qtde Bonus` | `sum(f_ads_geral_motor[qtd_bonus])` |
| Principal | `conversao_resgate` | `DIVIDE([Bonus_resgatados],[Qtde Bonus])` |
| Principal | `qtde_Ofertas` | `DISTINCTCOUNT(f_ads_geral_motor[offerId])` |
| Principal | `tx_conversao` | `DIVIDE([Qtde Bonus],[Impressao])` |
| Resumo | `Bonus_resgatados` | `sum(f_ads_geral_motor[qtd_bonus_Usados])` |
| Resumo | `Impressao` | `sum(f_ads_geral_motor[impressao])` |
| Resumo | `Qtde Bonus` | `sum(f_ads_geral_motor[qtd_bonus])` |
| Resumo | `tx_conversao` | `DIVIDE([Qtde Bonus],[Impressao])` |
| Resumo | `tx_conversao_resgate` | `DIVIDE([Bonus_resgatados],[Impressao])` |


#### Análise de Governança — Ads - Acompanhamento Motor

**Nível: 🔴 Crítico** — ✅ Verde 57% (MV+Gold) | 🟡 Amarelo 0% (Silver) | 🔴 Vermelho 43% (Bronze/Externas)

> **Regra de governança:** ✅ Verde = Metric View ou Gold | 🟡 Amarelo = Silver | 🔴 Vermelho = Bronze ou fontes externas (ex: Google Sheets)

| Tabela Databricks | Classificação | Usada em (tabela PBI) | Observação |
|---|---|---|---|
| `gold.ads.b2b_bonification` | ✅ Gold | `f_ads_geral_motor` | mv_ads_bonification já existe. Verificar se colunas extras usadas cabem como dimensão no MV. |
| `gold.ads.b2b_marca_cs` | ✅ Gold | `Brand_Offer`, `brand` | Mapeamento marca → CS. Candidata a dim_marca no MV. |
| `gold.ads.b2b_message` | ✅ Gold | `DeliverTime`, `f_ads_geral_motor`, `pub_praca` | Coberta parcialmente por mv_ads_message/mv_ads_resgate. Avaliar se dim publisher/praça pode entrar como dimensão nos MVs. |
| `gold.ads.b2b_offer` | ✅ Gold | `brand`, `f_ads_geral_motor` | mv_ads_offer já existe. Verificar se campos extras cabem no MV. |
| `bronze.giftback.ads_activities` | 🔴 Bronze | `f_ads_geral_motor` | Atividades ADS do bronze. Criar gold.ads.b2b_activities. |
| `bronze.giftback.ads_offers` | 🔴 Bronze | `Brand_Offer` | Catálogo de ofertas sem tratamento. Criar gold.ads.dim_offers. |
| `bronze.giftback.brands` | 🔴 Bronze | `Brand_Offer`, `brand` | Cadastro de marcas sem tratamento. Criar gold.giftback.dim_brands ou absorver nos MVs como dimensão. |

##### 🚨 Mensagem para o time de Engenharia de Dados

> **Contexto:** O dashboard *Ads - Acompanhamento Motor* consome 3 tabela(s) bronze diretamente, representando risco de qualidade, falta de tratamento e possível exposição de dados sensíveis.

**Ação solicitada — Bronze → Gold:**
- `bronze.giftback.ads_activities` — Atividades ADS do bronze. Criar gold.ads.b2b_activities.
- `bronze.giftback.ads_offers` — Catálogo de ofertas sem tratamento. Criar gold.ads.dim_offers.
- `bronze.giftback.brands` — Cadastro de marcas sem tratamento. Criar gold.giftback.dim_brands ou absorver nos MVs como dimensão.

> **Resultado esperado:** Criar tabelas gold (ou Metric Views) equivalentes e atualizar os datasets do Power BI para eliminar o consumo direto de bronze em dashboards de negócio.

##### 🔍 Gold usado no lugar de Metric View — avaliar atualização

- `gold.ads.b2b_bonification` — mv_ads_bonification já existe. Verificar se colunas extras usadas cabem como dimensão no MV.
- `gold.ads.b2b_marca_cs` — Mapeamento marca → CS. Candidata a dim_marca no MV.
- `gold.ads.b2b_message` — Coberta parcialmente por mv_ads_message/mv_ads_resgate. Avaliar se dim publisher/praça pode entrar como dimensão nos MVs.
- `gold.ads.b2b_offer` — mv_ads_offer já existe. Verificar se campos extras cabem no MV.

---

#### 📊 Ads - Monitoramento

#### Análise 1 — Fontes

| Tabela PBI | Tipo | Tabelas no FROM |
|---|---|---|
| `fFunil_geral` | ⚠️ SQL direto | `gold.ads.b2b_bonification`, `gold.ads.b2b_message` |
| `dCalendar` | 📐 DAX | — |
| `DeliverTime` | ⚠️ SQL direto | `gold.ads.b2b_message` |
| `fFunil_definitivo` | ⚠️ SQL direto | `bronze.giftback.bonus`, `bronze.giftback.brands`, `bronze.giftback.orders`, `gold.ads.arm_receita_incremental`, `gold.ads.b2b_bonification`, `gold.ads.b2b_message`, `gold.ads.b2b_offer`, `gold.ads.cpl_cpi_cpa`, `silver.giftback.bonus` |
| `dbrand` | ⚠️ SQL direto | `bronze.giftback.brands`, `gold.ads.b2b_offer` |
| `dpub_praca` | ⚠️ SQL direto | `gold.ads.b2b_message` |
| `fOfertaMotor` | ⚠️ SQL direto | `bronze.giftback.ads_offers`, `silver.ads.restricao_ofertas_logs` |

##### SQL completo por tabela

**`fFunil_geral`**
```sql
select
      date(m.data_payload)                                                       as dia,
      --m.schema_name                                                              as schema_name,
      --m.publisher_name                                                           as publisher_name,
     -- m.square_id                                                                as square_id,
     -- m.square_name                                                              as square_name,
     -- m.bonusb2b_campaign_id                                                     as bonusb2b_campaign_id,
       m.canal                                                                    AS canal,
      concat(coalesce(m.square_id,0),'_',m.bonusb2b_campaign_id)                 as key,
      --m.versao                                                                   as versao,
      b.brand_id                                                                 as brand_id,
      --count(distinct m.request_uuid)                                             as payload_recebido,
      --count(distinct m.waId)         filter (where m.DeliverTime is not null)    as usuarios_unicos,
      --count(distinct m.request_uuid) filter (where m.requestTime is not null)    as solicitado,
      --count(distinct m.request_uuid) filter (where m.sendSentTime is not null)   as enviados,
      count(distinct m.request_uuid)         filter (where flag_opt_out = 1)     as optout,
      count(distinct m.request_uuid) filter (where m.DeliverTime is not null)    as disparos, 
      count(distinct m.request_uuid) filter (where m.ReadTime is not null)       as lidos,
      count(distinct m.request_uuid) filter (where m.flag_ativar_oferta <> 0)    as ativou
      --count(distinct m.request_uuid) filter (where m.flag_oferta_escolhida <> 0) as escolha
    from gold.ads.b2b_message m
    left join gold.ads.b2b_bonification b on m.request_uuid = b.request_uuid 
    where date(m.data_payload) >= trunc(add_months(current_date(), -'" & Text.From(pMeses) & "' ), 'month')
    group by all
```

**`DeliverTime`**
```sql
select max(DeliverTime) as ultimo_disparo from gold.ads.b2b_message
```

**`fFunil_definitivo`**
```sql
WITH taxas AS (
  SELECT brand_id, data_ini, data_fim, CPL, CPI, CPA
  FROM gold.ads.cpl_cpi_cpa
  WHERE praca IS NULL
),

-- Impressão & Alcance (otimizado com filtro early)
imp AS (
  SELECT 
    o.brand_id,
    o.versao,
    o.square_id, 
    m.canal,
    DATE(o.data_entrega_oferta) AS dia, 
    o.bonusb2b_campaign_id,
    o.schema_name, 
    COUNT(DISTINCT o.numero) AS alcance, 
    COUNT(DISTINCT o.request_uuid) AS impressao
  FROM gold.ads.b2b_offer o
  INNER JOIN gold.ads.b2b_message m ON o.request_uuid = m.request_uuid
  GROUP BY ALL
),

gera_ads AS (
  SELECT 
    date(bo.bonus_created) as dia,
    bo.schema_name,
    bo.versao,
    bo.square_id, 
    bo.campaign_id AS bonusb2b_campaign_id,
    bo.brand_id, 
    COUNT(DISTINCT bo.bonus_id) AS resgate_ads
  FROM gold.ads.b2b_bonification bo
  WHERE bo.bonus_id is not null
  GROUP BY ALL
),

-- Resgate GB (otimizado com CTEs mais claras)
bonus_resgatados AS (
  SELECT * 
  FROM silver.giftback.bonus 
  WHERE flag_resgate = true AND tipo_detalhe IN ('CRMADS','BONUS B2B')
),

order_ AS (
  SELECT
    o.id AS used_orders_id,
    bo.data_resgate AS dia,
    bo.bonus_id,
    o.total,
    ROW_NUMBER() OVER (PARTITION BY o.id ORDER BY bo.bonus_id DESC) AS qtd
  FROM bonus_resgatados bo
  JOIN bronze.giftback.bonus b ON b.id = bo.bonus_id
  JOIN bronze.giftback.orders o ON b.used_orders_id = o.id
),

incremental_ AS (
  SELECT 
    dia, 
    bonus_id, 
    CASE WHEN qtd > 1 THEN 0 ELSE total END AS valor_incremental_fracionado
  FROM order_
),

resgate_gb AS (
  SELECT 
    bi.dia, 
    bo.schema_name, 
    bo.versao,
    bo.campaign_id AS bonusb2b_campaign_id,
    bo.square_id, 
    bo.brand_id, 
    SUM(bi.valor_incremental_fracionado) AS incremental, 
    COUNT(DISTINCT bo.bonus_id) AS resgate_gb 
  FROM gold.ads.b2b_bonification bo
  JOIN incremental_ bi ON bi.bonus_id = bo.bonus_id
  GROUP BY ALL
), 

-- Junção otimizada usando COALESCE em vez de FULL OUTER JOIN
feito AS (
  SELECT 
    COALESCE(im.brand_id, g.brand_id, gb.brand_id) AS brand_id,
    COALESCE(im.dia, g.dia, gb.dia) AS dia,
    COALESCE(im.versao, g.versao, gb.versao) AS versao,
    COALESCE(im.square_id, g.square_id, gb.square_id) AS square_id,
    COALESCE(im.schema_name, g.schema_name, gb.schema_name) AS schema_name,
    COALESCE(im.bonusb2b_campaign_id, g.bonusb2b_campaign_id, gb.bonusb2b_campaign_id) AS bonusb2b_campaign_id,
    im.canal AS canal, -- <-- AQUI (só vem do imp)
    COALESCE(im.alcance, 0) AS alcance,
    COALESCE(im.impressao, 0) AS impressao,
    COALESCE(g.resgate_ads, 0) AS resgate_ads,
    COALESCE(gb.incremental, 0) AS incremental,
    COALESCE(gb.resgate_gb, 0) AS resgate_gb
  FROM imp im
  FULL OUTER JOIN gera_ads g 
    ON im.brand_id = g.brand_id 
    AND im.schema_name = g.schema_name 
    AND im.bonusb2b_campaign_id = g.bonusb2b_campaign_id
    AND im.dia = g.dia
    AND im.versao = g.versao
    AND im.square_id = g.square_id
  FULL OUTER JOIN resgate_gb gb 
    ON im.brand_id = gb.brand_id 
    AND im.schema_name = gb.schema_name 
    AND im.bonusb2b_campaign_id = gb.bonusb2b_campaign_id
    AND im.dia = gb.dia
    AND im.versao = gb.versao
    AND im.square_id = gb.square_id
),

-- Ajuste com junções otimizadas
ajuste2 AS (
  SELECT
    f.dia,
    --f.schema_name,
    f.bonusb2b_campaign_id,
    f.brand_id,
    --f.versao,
    f.square_id,
    f.canal,  -- <-- AQUI (só vem do imp)
    f.alcance,
    f.impressao,
    f.resgate_ads,
    f.resgate_gb,
    f.incremental,
    COALESCE(c.CPA, 0) AS cpa, 
    COALESCE(c.CPL, 0) AS cpl, 
    COALESCE(c.CPI, 0) AS cpi,
    COALESCE(
      (SELECT SUM(ii.valor_total) 
       FROM gold.ads.arm_receita_incremental ii
       WHERE ii.id_marca = f.brand_id 
         AND ii.data_criacacao = f.dia 
         AND ii.id_publisher = f.bonusb2b_campaign_id),
      0
    ) AS incremental_man
  FROM feito f
  LEFT JOIN taxas c
    ON c.data_ini <= f.dia
    AND c.data_fim >= f.dia
    AND c.brand_id = f.brand_id
),

ajuste as (
  select *,   
      incremental_man / COUNT(*) OVER (PARTITION BY dia, bonusb2b_campaign_id, brand_id) AS incremental_manual 
  from ajuste2

),

-- Cálculos finais
calculos AS (
  SELECT 
    dia,
    --schema_name,
    --versao,
    square_id,
    bonusb2b_campaign_id,
    brand_id,
    canal,  -- <-- AQUI (só vem do imp)
    SUM(alcance) AS alcance, 
    SUM(impressao) AS impressao, 
    SUM(resgate_ads) AS resgate_ads,
    SUM(resgate_gb) AS resgate_gb,
    SUM(incremental) AS incremental,
    SUM(incremental_manual) AS incremental_manual,
    SUM(incremental + incremental_manual) AS incremental_total,
    MAX(cpa) AS cpa,
    MAX(cpl) AS cpl,
    MAX(cpi) AS cpi,
    CASE 
      WHEN MAX(brand_id) = 5237 THEN SUM(incremental + incremental_manual)
      ELSE SUM((cpl*resgate_ads) + ((incremental + incremental_manual)*cpa) + (impressao*cpi))
    END AS receita_ads
  FROM ajuste
  GROUP BY ALL
)

SELECT
  c.dia,
  c.brand_id,
 -- c.bonusb2b_campaign_id,
  b.name,
  --c.schema_name,
  --c.versao,
 -- c.square_id,
  concat(coalesce(c.square_id,0),'_',c.bonusb2b_campaign_id)                             as key,
  c.canal,  -- <-- AQUI (só vem do imp)
  c.cpa,
  c.cpl,
  c.cpi,
  c.alcance, 
  c.impressao, 
  c.resgate_ads, 
  c.resgate_gb, 
  c.incremental_total AS incremental,
  c.receita_ads,
  CASE 
    WHEN c.cpl > 0 AND c.cpi > 0 AND c.cpa > 0 THEN 'CPL, CPI e CPA'
    WHEN c.cpi > 0 AND c.cpl > 0 THEN 'CPI e CPL'
    WHEN c.cpi > 0 AND c.cpa > 0 THEN 'CPI e CPA'
    WHEN c.cpa > 0 AND c.cpl > 0 THEN 'CPA e CPL'
    WHEN c.cpa > 0 THEN 'CPA'
    WHEN c.cpl > 0 THEN 'CPL'
    WHEN c.cpi > 0 THEN 'CPI'
  END AS filtro_cpa_cpl,

  CASE 
    WHEN c.brand_id IN (5703, 5706, 5707, 5708, 5709, 5710, 5711, 5713, 5715) THEN 'Ifood'
    ELSE b.name 
    END AS SubBrand


FROM calculos c
LEFT JOIN bronze.giftback.brands b ON b.id = c.brand_id
where c.dia >= trunc(add_months(current_date(), -'" & Text.From(pMeses) & "' ), 'month')
```

**`dbrand`**
```sql
select 
distinct brand_id, 
UPPER(b.name)brand_name,
--upper(CASE 
--                WHEN brand_id IN (5703, 5706, 5707, 5708, 5709, 5710, 5711, 5713, 5715,5704,5705,5712) THEN 'IFOOD'
--    ELSE brand_name
 --   END) AS SubBrand,
case 
  when brand_id in (106,53,5083,12,552,5194,5498,5502,5497,5500,5499,0,5471,5501,5496,36,3,2008,3980,1276,5,91,3952,409,3343,399,1020,4015,76,191,4963,1366,55,5208,1629,2369) then 'VANESSA'
  when brand_id in (5704,5705,3297,5712,5714) then 'IFOOD'
  when brand_id in (345,5138,175,5823) then 'VALE BONUS'
  when brand_id in (4900,172,5695,11,99,227,1150,246,5662,5800,43,5581,362,68,1219,1797,1351,539,5306,4635,5442,5378,607,608,803,819,5664,883,512,145,493,5586,0,5798,4534,1776,5738,4812,5814,3559,5524,5800,5814) then 'FLÁVIA'
  when brand_id in (75,30,5747,1314,5717,5753,4797,5632,16,21,5223,5604,5670,4053,5250,5322,546,5472,5746,5468,2881,4943,129,684,5239,4729,2254,5676,5555,5634,5655,5716,5635,818,4219,498,5457,5465,5084,5674,5788,5018,5647,5659,5669,386,4134,379,5363,5536,5644,5563,5675,5772,276,5720,5658,5482,1175,3860,5195,5448,5279,102,5265,5350,5834,3552,5839) then 'JOÃO'
  when brand_id in (717,40,1941,125,4066,1017,71,2446,5829,5830) then 'MATHEUS' else 'SEM RESPONSAVEL' end as CS
from gold.ads.b2b_offer o
left join bronze.giftback.brands b 
on o.brand_id = b.id
```

**`dpub_praca`**
```sql
with base as (
select distinct
    concat(coalesce(square_id,0),'_',bonusb2b_campaign_id)  as key,
    case 
        when bonusb2b_campaign_id in (9,37) then 'Sem Parar - Estacione'
        when bonusb2b_campaign_id in (5,39) then 'Sem Parar - Abastece'
        when bonusb2b_campaign_id in (3,43) then 'Click - Embarque'
        else publisher_name end 
    as new_publisher_name,
    --square_id,
    case 
        when square_id is null then 'Desconhecido' 
        else square_name end 
    as square_name,
    publisher_name,
    bonusb2b_campaign_id,
    row_number()over(partition by concat(coalesce(square_id,0),'_',bonusb2b_campaign_id)  order by square_id) as rnk
from gold.ads.b2b_message 
where
    is_test = 0
)

select --*, row_number()over(partition by key order by square_id) as rnk
    key
    ,new_publisher_name
    --,square_id
    ,square_name
    ,publisher_name
    --,bonusb2b_campaign_id
from 
    base
where 
    rnk = 1
```

**`fOfertaMotor`**
```sql
SELECT DISTINCT
    date_format(max(data_hora_atualizacao), 'HH:mm') AS data_hora_atualizacao,
    to_char(date_trunc('HOUR', max(data_hora_atualizacao)), 'HH:mm') as hora_arredondada,
    marca,
    publisher_name,
    CONCAT(codigo_praca, '_', codigo_publisher) AS key,
    praca_name,
    restricao_atual,
    status_oferta_atual,
    codigo_oferta,
 o.title,
 budget_value,
    DATE(data_hora_atualizacao) AS data,
     COUNT(DISTINCT codigo_oferta) FILTER (WHERE restricao_atual = ""ok"") AS `Ofertas disponiveis para uso`
    , COUNT(DISTINCT codigo_oferta) FILTER (WHERE restricao_atual != ""ok"") AS `Ofertas restritas`

FROM silver.ads.restricao_ofertas_logs l
left join bronze.giftback.ads_offers o on l.codigo_oferta = o.id and l.codigo_marca = o.brand_id
where data_hora_atualizacao::DATE >= trunc(add_months(current_date(), -'2' ), 'month')
group by  all
```

#### Análise 2 — Medidas por página

| Página | Medida | Cálculo DAX |
|---|---|---|
| Base Marca | `% Ger. Bônus` | `[Resgate Ads]/[Disparos]` |
| Base Marca | `% Resgate Ads` | `[Resgate Ads]/[Impressão]` |
| Base Marca | `% Resgate GB` | `DIVIDE([Resgate],[Impressão])` |
| Base Marca | `ARI` | `DIVIDE([Receita Ads],[Impressão],0)` |
| Base Marca | `CPA` | `CALCULATE(MAX(fFunil_definitivo[cpa]))` |
| Base Marca | `CPI` | `CALCULATE(MAX(fFunil_definitivo[cpi]))` |
| Base Marca | `CPL` | `CALCULATE(MAX(fFunil_definitivo[cpl]))` |
| Base Marca | `Qtd Praças` | `DISTINCTCOUNT(dpub_praca[square_name])` |
| Base Marca | `Qtd Publishers` | `//DISTINCTCOUNT(dpublisher[publisher_name]) DISTINCTCOUNT(dpub_praca[publisher_name])` |
| Base Marca | `Receita Ads` | `CALCULATE(sum(fFunil_definitivo[receita_ads]))` |
| Base Marca | `Receita Incremental` | `CALCULATE(sum(fFunil_definitivo[incremental]))` |
| Painel Alerta | `% Ger Bonus Ajustado` | `VAR GerBonus = [Ger Bonus Ajustado] VAR GerBonusads = SUM(fFunil_definitivo[resgate_ads]) VAR Disparos = [Interesse Ajustado] VAR Impressoes = [impressao Ajustado] VAR PRACA = ISFILTERED(dpub_praca[publisher_name]) VAR BRAND = ISFILTERED(dbrand[brand_name])  RETURN SWITCH(     TRUE(),          // Apenas Marca filtrada     BRAND && NOT PRACA,     DIVIDE(GerBonus, Impressoes),      // Apenas Publisher filtrado     PRACA && NOT BRAND,     DIVIDE(GerBonus, Disparos),      // Ambos filtrados (Marca e Publisher)     BRAND && PRACA,     DIVIDE(GerBonus, Impressoes),      // Nenhum dos dois filtrado     DIVIDE(GerBonus, Disparos) )` |
| Painel Alerta | `% Resgate GB` | `DIVIDE([Resgate],[Impressão])` |
| Painel Alerta | `Alerta Queda Ativação` | `VAR CurrentRevenue = [Media_Ativacao]  VAR Avg3Months = [Media ativacao 3 Meses]  RETURN IF(    ISBLANK(CurrentRevenue),    "⚠️ Sem Dados",    IF(       CurrentRevenue < (Avg3Months * 0.5),       "🔴 Queda Crítica",       IF(          CurrentRevenue < (Avg3Months * 0.7),          "🚨 Queda Grave",          IF(             CurrentRevenue < (Avg3Months * 0.9),             "🔶 Queda Mediana",             IF(                CurrentRevenue < (Avg3Months * 1.1),                "✅ Estável",                "🟢 Crescimento"             )          )       )    ) )` |
| Painel Alerta | `Alerta Queda Bonus` | `VAR CurrentRevenue = [Media_Bonus] VAR Avg3Months = [Media bonus 3 Meses]   RETURN IF(    ISBLANK(CurrentRevenue),    "⚠️ Sem Dados",    IF(       CurrentRevenue < (Avg3Months * 0.5),       "🔴 Queda Crítica",       IF(          CurrentRevenue < (Avg3Months * 0.7),          "🚨 Queda Grave",          IF(             CurrentRevenue < (Avg3Months * 0.9),             "🔶 Queda Mediana",             IF(                CurrentRevenue < (Avg3Months * 1.1),                "✅ Estável",                "🟢 Crescimento"             )          )       )    ) )` |
| Painel Alerta | `Alerta Queda Impressao` | `VAR CurrentRevenue = [Media_Impressao] VAR Avg3Months = [Media Impressao 3 Meses]  RETURN IF(    ISBLANK(CurrentRevenue),    "⚠️ Sem Dados",    IF(       CurrentRevenue < (Avg3Months * 0.5),       "🔴 Queda Crítica",       IF(          CurrentRevenue < (Avg3Months * 0.7),          "🚨 Queda Grave",          IF(             CurrentRevenue < (Avg3Months * 0.9),             "🔶 Queda Mediana",             IF(                CurrentRevenue < (Avg3Months * 1.1),                "✅ Estável",                "🟢 Crescimento"             )          )       )    ) )` |
| Painel Alerta | `Alerta Queda Playload` | `VAR CurrentRevenue = [Media_Playload] VAR Avg3Months = [Media disparos 3 Meses]  RETURN IF(    ISBLANK(CurrentRevenue),    "⚠️ Sem Dados",    IF(       CurrentRevenue < (Avg3Months * 0.5),       "🔴 Queda Crítica",       IF(          CurrentRevenue < (Avg3Months * 0.7),          "🚨 Queda Grave",          IF(             CurrentRevenue < (Avg3Months * 0.9),             "🔶 Queda Mediana",             IF(                CurrentRevenue < (Avg3Months * 1.1),                "✅ Estável",                "🟢 Crescimento"             )          )       )    ) )` |
| Painel Alerta | `Alerta Queda Receita ADS` | `VAR CurrentRevenue = [Media_Receita] VAR Avg3Months = [Media receita ads 3 Meses]  RETURN IF(    ISBLANK(CurrentRevenue),    "⚠️ Sem Dados",    IF(       CurrentRevenue < (Avg3Months * 0.5),       "🔴 Queda Crítica",       IF(          CurrentRevenue < (Avg3Months * 0.7),          "🚨 Queda Grave",          IF(             CurrentRevenue < (Avg3Months * 0.9),             "🔶 Queda Mediana",             IF(                CurrentRevenue < (Avg3Months * 1.1),                "✅ Estável",                "🟢 Crescimento"             )          )       )    ) )` |
| Painel Alerta | `Disparo Ajustado` | `IF (     ISFILTERED(dbrand[brand_name]) &&     (ISFILTERED(dpub_praca[publisher_name]) || NOT ISFILTERED(dpub_praca[publisher_name])),     0,     SUM(fFunil_geral[disparos]) )` |
| Painel Alerta | `Disparos` | `CALCULATE(sum('fFunil_geral'[disparos]))` |
| Painel Alerta | `Ger Bonus Ajustado` | `VAR GerBonus = SUM(fFunil_definitivo[resgate_gb]) VAR GerBonusads = SUM(fFunil_definitivo[resgate_ads]) VAR Disparos = SUM(fFunil_geral[disparos]) VAR Impressoes = SUM(fFunil_definitivo[impressao]) VAR PRACA = ISFILTERED(dpub_praca[publisher_name]) VAR BRAND = ISFILTERED(dbrand[brand_name])  RETURN SWITCH(     TRUE(),          // Apenas Marca filtrada     BRAND && NOT PRACA,     GerBonus,      // Apenas Publisher filtrado     PRACA && NOT BRAND,     GerBonusads,      // Ambos filtrados (Marca e Publisher)     BRAND && PRACA,     GerBonus,      // Nenhum dos dois filtrado     GerBonusads )` |
| Painel Alerta | `Interesse` | `CALCULATE(SUM('fFunil_geral'[ativou]))` |
| Painel Alerta | `Interesse Ajustado` | `IF (     ISFILTERED(dbrand[brand_name]) &&     (ISFILTERED(dpub_praca[publisher_name]) || NOT ISFILTERED(dpub_praca[publisher_name])),     0,     SUM(fFunil_geral[ativou]) )` |
| Painel Alerta | `Resgate` | `CALCULATE(SUM(fFunil_definitivo[resgate_gb]))` |
| Painel Alerta | `Resgate Ads` | `CALCULATE(SUM(fFunil_definitivo[resgate_ads]))` |
| Painel Alerta | `Soma Receita Ads` | `SUM(fFunil_definitivo[receita_ads])` |
| Painel Alerta | `impressao Ajustado` | `IF (     ISFILTERED(dpub_praca[publisher_name]) &&     NOT ISFILTERED(dbrand[brand_name]),     0,     SUM(fFunil_definitivo[impressao]) )` |
| CRMCHAT x PlusFlow | `Disparos` | `CALCULATE(sum('fFunil_geral'[disparos]))` |
| CRMCHAT x PlusFlow | `Interesse` | `CALCULATE(SUM('fFunil_geral'[ativou]))` |
| CRMCHAT x PlusFlow | `Lidos` | `SUM(fFunil_geral[lidos])` |
| CRMCHAT x PlusFlow | `Resgate` | `CALCULATE(SUM(fFunil_definitivo[resgate_gb]))` |
| CRMCHAT x PlusFlow | `Resgate Ads` | `CALCULATE(SUM(fFunil_definitivo[resgate_ads]))` |
| CRMCHAT x PlusFlow | `optout` | `sum(fFunil_geral[optout])` |


#### Análise de Governança — Ads - Monitoramento

**Nível: 🔴 Crítico** — ✅ Verde 45% (MV+Gold) | 🟡 Amarelo 18% (Silver) | 🔴 Vermelho 36% (Bronze/Externas)

> **Regra de governança:** ✅ Verde = Metric View ou Gold | 🟡 Amarelo = Silver | 🔴 Vermelho = Bronze ou fontes externas (ex: Google Sheets)

| Tabela Databricks | Classificação | Usada em (tabela PBI) | Observação |
|---|---|---|---|
| `gold.ads.arm_receita_incremental` | ✅ Gold | `fFunil_definitivo` | Usada no cálculo de receita. Avaliar absorção pelo mv_ads_receita_ads. |
| `gold.ads.b2b_bonification` | ✅ Gold | `fFunil_definitivo`, `fFunil_geral` | mv_ads_bonification já existe. Verificar se colunas extras usadas cabem como dimensão no MV. |
| `gold.ads.b2b_message` | ✅ Gold | `DeliverTime`, `dpub_praca`, `fFunil_definitivo`, `fFunil_geral` | Coberta parcialmente por mv_ads_message/mv_ads_resgate. Avaliar se dim publisher/praça pode entrar como dimensão nos MVs. |
| `gold.ads.b2b_offer` | ✅ Gold | `dbrand`, `fFunil_definitivo` | mv_ads_offer já existe. Verificar se campos extras cabem no MV. |
| `gold.ads.cpl_cpi_cpa` | ✅ Gold | `fFunil_definitivo` | Tabela de parâmetros (CPL/CPI/CPA). Candidata a dim_taxas no mv_ads_receita_ads ou MV próprio. |
| `silver.ads.restricao_ofertas_logs` | 🟡 Silver | `fOfertaMotor` | Logs de restrição na silver. Promover a gold.ads para uso consistente em dashboards. |
| `silver.giftback.bonus` | 🟡 Silver | `fFunil_definitivo` | Camada silver — melhor que bronze. Verificar se existe gold.giftback.bonus para promover. |
| `bronze.giftback.ads_offers` | 🔴 Bronze | `fOfertaMotor` | Catálogo de ofertas sem tratamento. Criar gold.ads.dim_offers. |
| `bronze.giftback.bonus` | 🔴 Bronze | `fFunil_definitivo` | Dados transacionais de bônus sem tratamento. Usar silver.giftback.bonus ou equivalente gold. |
| `bronze.giftback.brands` | 🔴 Bronze | `dbrand`, `fFunil_definitivo` | Cadastro de marcas sem tratamento. Criar gold.giftback.dim_brands ou absorver nos MVs como dimensão. |
| `bronze.giftback.orders` | 🔴 Bronze | `fFunil_definitivo` | Pedidos sem tratamento. Criar gold.giftback.orders. |

##### 🚨 Mensagem para o time de Engenharia de Dados

> **Contexto:** O dashboard *Ads - Monitoramento* consome 4 tabela(s) bronze diretamente, representando risco de qualidade, falta de tratamento e possível exposição de dados sensíveis.

**Ação solicitada — Bronze → Gold:**
- `bronze.giftback.ads_offers` — Catálogo de ofertas sem tratamento. Criar gold.ads.dim_offers.
- `bronze.giftback.bonus` — Dados transacionais de bônus sem tratamento. Usar silver.giftback.bonus ou equivalente gold.
- `bronze.giftback.brands` — Cadastro de marcas sem tratamento. Criar gold.giftback.dim_brands ou absorver nos MVs como dimensão.
- `bronze.giftback.orders` — Pedidos sem tratamento. Criar gold.giftback.orders.

> **Resultado esperado:** Criar tabelas gold (ou Metric Views) equivalentes e atualizar os datasets do Power BI para eliminar o consumo direto de bronze em dashboards de negócio.

##### 🟡 Silver em uso — verificar promoção a Gold

- `silver.ads.restricao_ofertas_logs` — Logs de restrição na silver. Promover a gold.ads para uso consistente em dashboards.
- `silver.giftback.bonus` — Camada silver — melhor que bronze. Verificar se existe gold.giftback.bonus para promover.

##### 🔍 Gold usado no lugar de Metric View — avaliar atualização

- `gold.ads.arm_receita_incremental` — Usada no cálculo de receita. Avaliar absorção pelo mv_ads_receita_ads.
- `gold.ads.b2b_bonification` — mv_ads_bonification já existe. Verificar se colunas extras usadas cabem como dimensão no MV.
- `gold.ads.b2b_message` — Coberta parcialmente por mv_ads_message/mv_ads_resgate. Avaliar se dim publisher/praça pode entrar como dimensão nos MVs.
- `gold.ads.b2b_offer` — mv_ads_offer já existe. Verificar se campos extras cabem no MV.
- `gold.ads.cpl_cpi_cpa` — Tabela de parâmetros (CPL/CPI/CPA). Candidata a dim_taxas no mv_ads_receita_ads ou MV próprio.

---

#### 📊 Ads - quartou

#### Análise 1 — Fontes

| Tabela PBI | Tipo | Tabelas no FROM |
|---|---|---|
| `fEmisao` | ⚠️ SQL direto | `bronze.giftback.bonus_b2b_campaigns`, `bronze.giftback.prebonus`, `bronze.giftback.prebonus_file`, `gold.ads.emissao_quartou`, `prebonus` |
| `fResgate` | ⚠️ SQL direto | `bronze.giftback.bonus_b2b_campaigns`, `bronze.giftback.customers`, `bronze.giftback.prebonus`, `bronze.giftback.prebonus_file`, `gold.ads.resgate_quartou`, `silver.giftback.bonus` |
| `fRetorno` | ⚠️ SQL direto | `bronze.giftback.bonus_b2b_campaigns`, `bronze.giftback.customers`, `bronze.giftback.prebonus`, `bronze.giftback.prebonus_file`, `gold.ads.resgate_usuario_quartou`, `silver.giftback.bonus` |
| `auxUpdate` | ⚠️ SQL direto | `bronze.giftback.bonus_b2b_campaigns`, `bronze.giftback.prebonus`, `bronze.giftback.prebonus_file`, `silver.giftback.bonus` |
| `fNovos_marca_tipo` | ⚠️ SQL direto | `bronze.giftback.bonus_b2b_campaigns`, `bronze.giftback.customers`, `bronze.giftback.prebonus`, `bronze.giftback.prebonus_file`, `gold.ads.resgate_usuario_quartou`, `silver.giftback.bonus` |

##### SQL completo por tabela

**`fEmisao`**
```sql
with prebonus as(
    Select 
      ca.name as campanha,
      date(ca.connect_campaign_date_init) as data_envio,
      count(distinct pr.cpf_hash) as emissao_distinta,
      count(pr.cpf_hash) as emissao_total
      --sum(case when pr.status_id = 1 then 1 else 0 end) as emissao_disponivel,
      --sum(case when pr.status_id = 2 then 1 else 0 end ) as emissao_utilizada,
      --count(pr.prebonus_id) filter (Where pr.status_id = 2 and cupom is not null) as emissao_utilizada_online,
      -- count(pr.prebonus_id) filter (Where pr.status_id = 2 and cupom is null) as emissao_utilizada_loja,
      --sum(case when pr.status_id = 3 then 1 else 0 end) as emissao_expirado,
      --sum(case when pr.status_id = 4 then 1 else 0 end) as emissao_cancelado
    from bronze.giftback.prebonus pr
      join bronze.giftback.prebonus_file fi on pr.file_id = fi.file_id
      join bronze.giftback.bonus_b2b_campaigns ca on fi.bonusb2b_campaign_id = ca.id
    group by 1, 2
)

    SELECT
      data_envio,
      emissao_distinta,
      emissao_total    
    FROM prebonus 
    WHERE campanha NOT LIKE '%TESTE%'

    union 

    SELECT 
      data_envio,
      emissao_distinta,
      emissao_total  
    FROM gold.ads.emissao_quartou 
    WHERE campanha NOT IN (SELECT DISTINCT campanha FROM prebonus) 
      AND campanha NOT LIKE '%TESTE%'
```

**`fResgate`**
```sql
with  
  final as (
    select 
      b.*, 
      c.is_ecommerce as flag_ecom, 
      uc.is_ecommerce as flag_cupom
    from silver.giftback.bonus b
      join bronze.giftback.customers c on b.codigo_loja = c.id
      left join bronze.giftback.customers uc on b.codigo_loja_resgate = uc.id
    where tipo_detalhe = 'ADSCN_VIVO_QUARTOU'
  ),

  base_vivo as (
    select
      ca.name as campanha,
      date(ca.connect_campaign_date_init) as data_envio,
      pr.cpf_hash, 
      pr.prebonus_id, 
      pr.status_id, 
      pr.file_id, 
      pr.bonus_id, 
      pr.cupom
    from bronze.giftback.prebonus pr
      join bronze.giftback.prebonus_file fi on pr.file_id = fi.file_id
      join bronze.giftback.bonus_b2b_campaigns ca on fi.bonusb2b_campaign_id = ca.id
    where pr.status_id = 2
  ),

  saida as (
    select 
      pr.campanha,
      pr.data_envio,
      pr.cupom,
      b.*,
      case 
        when b.valor_incremental is null then 0 
        else b.valor_incremental 
      end as receita_gerada
    from final b
      join base_vivo pr on b.bonus_id = pr.bonus_id
  )

select 
  data_envio,
  flag_cupom,
  loja,
  marca,
  bonus_id,
  flag_ecom,
  situacao_bonus,
  valor_incremental,
  valor_bonus
from saida

union 

select 
  data_envio,
  flag_cupom,
  loja,
  marca,
  bonus_id,
  flag_ecom,
  situacao_bonus,
  valor_incremental,
  valor_bonus
from gold.ads.resgate_quartou
where campanha not in (select distinct campanha from saida)
```

**`fRetorno`**
```sql
with 
  final as (
    Select b.*, c.is_ecommerce as flag_ecom, uc.is_ecommerce as flag_cupom
    from silver.giftback.bonus b
    join bronze.giftback.customers c on b.codigo_loja = c.id
    join bronze.giftback.customers uc on b.codigo_loja_resgate = uc.id
    where tipo_detalhe = 'ADSCN_VIVO_QUARTOU'
  ),
  base_vivo_resgate as (
    Select
      ca.name as campanha,
      date(ca.connect_campaign_date_init) as data_envio,
      pr.cpf_hash, 
      pr.prebonus_id, 
      pr.status_id, 
      pr.file_id,
      pr.bonus_id, 
      pr.cupom
    from bronze.giftback.prebonus pr
      join bronze.giftback.prebonus_file fi on pr.file_id = fi.file_id
      join bronze.giftback.bonus_b2b_campaigns ca on fi.bonusb2b_campaign_id = ca.id
    where  pr.status_id = 2
  ),
  base as (
    select cpf_hash, prebonus_id, status_id, file_id, bonus_id, cupom, data_envio, campanha from base_vivo_resgate
    union 
    select cpf_hash, prebonus_id, status_id, file_id, bonus_id, cupom, data_envio, campanha 
      from gold.ads.resgate_usuario_quartou 
      where campanha not in (select distinct campanha from base_vivo_resgate)
  ),
  resultado as (
    Select 
      pr.campanha,
      pr.data_envio,
      pr.cupom,
      b.*,
      --case when loja_resgate rlike 'E-COM|ECO GIFTBACK|QUARTOU|SHOP2GETHER|OQVESTIR|POLISHOP' then true else false end as flag_cupom,
      case when b.valor_incremental IS NULL then 0 else b.valor_incremental end as receita_gerada
    from final b
    join base pr on b.bonus_id = pr.bonus_id
    --order by datahora_referencia desc
  ),
  aplica as (
    select 
      campanha, 
      data_envio,
      --case when cupom is null then 'Lojas Físicas' else 'E-com' end as tipo_loja,
      --case when upper(loja_resgate) rlike 'E-COM|ECO GIFTBACK|QUARTOU|SHOP2GETHER|OQVESTIR' then 'E-com' else 'Lojas Físicas' end as tipo_loja,
      case when flag_cupom = 1 then 'E-com' else 'Lojas Físicas' end as tipo_loja,
      coalesce(sum(valor_incremental),0) as receita_gerada
    from resultado
    group by 1,2,3
  ),
  cpl as (
    select distinct 
      date(ca.connect_campaign_date_init) as data_enviado,
      ca.id, 
      ca.name, 
      ca.customer_id, 
      ca.connect_cpa,
      ca.connect_revenue_percentage_crmbonus,
      ca.connect_cpa_ecom,
      ca.connect_bonus_value,
      ca.connect_revenue_percentage_ecom_crmbonus
    from bronze.giftback.bonus_b2b_campaigns ca 
      join bronze.giftback.prebonus_file fi on fi.bonusb2b_campaign_id = ca.id
  )

select 
  --ap.*, 
  --cp.*,
  ap.data_envio,
  ap.receita_gerada,
  case when tipo_loja = 'E-com' then ap.receita_gerada * cp.connect_cpa_ecom/100 * cp.connect_revenue_percentage_ecom_crmbonus/100 
    else ap.receita_gerada * cp.connect_cpa/100 * cp.connect_revenue_percentage_crmbonus/100 
    end as retorno_crm,
  case when tipo_loja = 'E-com' then ap.receita_gerada * cp.connect_cpa_ecom/100 * ( 100 - cp.connect_revenue_percentage_ecom_crmbonus)/100 
    else ap.receita_gerada * cp.connect_cpa/100 * (100 - cp.connect_revenue_percentage_crmbonus)/100 
    end as retorno_vivo
from aplica ap
  join cpl cp on cp.data_enviado = ap.data_envio and cp.name = ap.campanha
```

**`auxUpdate`**
```sql
--Select 
--max(pr.used_date)
--from bronze.giftback.prebonus pr
--join bronze.giftback.prebonus_file fi on pr.file_id = --fi.file_id
--join bronze.giftback.bonus_b2b_campaigns ca on --fi.bonusb2b_campaign_id = ca.id
--where pr.status_id = 2

select max(datahora_referencia) as dtmax
from silver.giftback.bonus
where tipo_detalhe = 'ADSCN_VIVO_QUARTOU'
```

**`fNovos_marca_tipo`**
```sql
with 
  prebase as (
    Select 
      pr.cpf_hash, 
      pr.prebonus_id, 
      pr.status_id, 
      pr.file_id, 
      pr.bonus_id, 
      pr.cupom, 
      date(ca.connect_campaign_date_init) as data_envio,  
      ca.name as campanha
    from bronze.giftback.prebonus pr
      join bronze.giftback.prebonus_file fi on pr.file_id = fi.file_id
      join bronze.giftback.bonus_b2b_campaigns ca on fi.bonusb2b_campaign_id = ca.id
    where pr.status_id = 2
  ),

  base as (
    select * from prebase
    union 
    select * from gold.ads.resgate_usuario_quartou 
    where campanha not in (select distinct campanha from prebase)),

  usuarios as (
    Select 
      --pr.file_id,
      pr.data_envio,
      b.bonus_id,
      b.codigo_loja_resgate,
      b.loja_resgate,
      b.codigo_marca,
      b.codigo_usuario,
      b.data_referencia as data_gerou_bonus
    from silver.giftback.bonus b
      join base pr on b.bonus_id = pr.bonus_id
    where b.situacao_bonus = 'USADO'
  ),

  usuarios2 as (
  Select distinct
  codigo_loja_resgate,
    loja_resgate,
    codigo_marca,
    codigo_usuario
  from usuarios),

  usu_existe as (
    select 
    us.data_envio,
    us.codigo_marca,
    us.codigo_usuario,
    us.data_gerou_bonus,
    min(b.data_referencia) as primeira_compra
    from usuarios us
    left join silver.giftback.bonus b on us.codigo_usuario = b.codigo_usuario and us.codigo_marca = b.codigo_marca
    group by 1,2,3,4
  ),

  final as (
    select *
    from usu_existe
    where data_gerou_bonus = primeira_compra
  )

select 
  --final.*, 
  COUNT(final.codigo_usuario) AS qtd_usuarios,
  final.data_envio,
  --usuarios2.loja_resgate, 
  --case when upper(usuarios2.loja_resgate) rlike 'E-COM|ECO GIFTBACK|QUARTOU|SHOP2GETHER|OQVESTIR|POLISHOP' then 1 else 0 end as flag_ecom
  uc.is_ecommerce as flag_ecom
from final 
  left join usuarios2 on final.codigo_marca = usuarios2.codigo_marca and final.codigo_usuario = usuarios2.codigo_usuario
  join bronze.giftback.customers uc on usuarios2.codigo_loja_resgate = uc.id
GROUP BY all
```

#### Análise 2 — Medidas por página

| Página | Medida | Cálculo DAX |
|---|---|---|
| Home | `Bonus Gerado` | `—` |
| Home | `Bonus Utilizado` | `—` |
| Home | `Emissao Total` | `—` |
| Home | `Novos Cli E` | `—` |
| Home | `Novos Cli F` | `—` |
| Home | `Receita Incremental` | `—` |
| Home | `Retor CRM` | `—` |
| Home | `Retor Vivo` | `—` |
| Home | `Update` | `—` |
| Home | `Valor Bônus Utilizado` | `—` |
| Home | `ticket` | `—` |


#### Análise de Governança — Ads - quartou

**Nível: 🔴 Crítico** — ✅ Verde 38% (MV+Gold) | 🟡 Amarelo 12% (Silver) | 🔴 Vermelho 50% (Bronze/Externas)

> **Regra de governança:** ✅ Verde = Metric View ou Gold | 🟡 Amarelo = Silver | 🔴 Vermelho = Bronze ou fontes externas (ex: Google Sheets)

| Tabela Databricks | Classificação | Usada em (tabela PBI) | Observação |
|---|---|---|---|
| `gold.ads.emissao_quartou` | ✅ Gold | `fEmisao` | Específica Quartou. Candidata a mv_ads_emissao_quartou. |
| `gold.ads.resgate_quartou` | ✅ Gold | `fResgate` | Específica Quartou. Candidata a mv_ads_resgate_quartou. |
| `gold.ads.resgate_usuario_quartou` | ✅ Gold | `fNovos_marca_tipo`, `fRetorno` | Específica Quartou. Candidata a mv_ads_usuario_quartou. |
| `silver.giftback.bonus` | 🟡 Silver | `auxUpdate`, `fNovos_marca_tipo`, `fResgate`, `fRetorno` | Camada silver — melhor que bronze. Verificar se existe gold.giftback.bonus para promover. |
| `bronze.giftback.bonus_b2b_campaigns` | 🔴 Bronze | `auxUpdate`, `fEmisao`, `fNovos_marca_tipo`, `fResgate`, `fRetorno` | Configurações de campanhas B2B. Criar gold.ads.dim_campaigns. |
| `bronze.giftback.customers` | 🔴 Bronze | `fNovos_marca_tipo`, `fResgate`, `fRetorno` | Cadastro de clientes (dados PII) do bronze. Urgente: usar gold.giftback.customers. |
| `bronze.giftback.prebonus` | 🔴 Bronze | `auxUpdate`, `fEmisao`, `fNovos_marca_tipo`, `fResgate`, `fRetorno` | Pré-bônus (Quartou) sem camada de tratamento. Promover a silver/gold. |
| `bronze.giftback.prebonus_file` | 🔴 Bronze | `auxUpdate`, `fEmisao`, `fNovos_marca_tipo`, `fResgate`, `fRetorno` | Arquivo de pré-bônus sem tratamento. Promover a silver/gold. |

##### 🚨 Mensagem para o time de Engenharia de Dados

> **Contexto:** O dashboard *Ads - quartou* consome 4 tabela(s) bronze diretamente, representando risco de qualidade, falta de tratamento e possível exposição de dados sensíveis.

**Ação solicitada — Bronze → Gold:**
- `bronze.giftback.bonus_b2b_campaigns` — Configurações de campanhas B2B. Criar gold.ads.dim_campaigns.
- `bronze.giftback.customers` — Cadastro de clientes (dados PII) do bronze. Urgente: usar gold.giftback.customers.
- `bronze.giftback.prebonus` — Pré-bônus (Quartou) sem camada de tratamento. Promover a silver/gold.
- `bronze.giftback.prebonus_file` — Arquivo de pré-bônus sem tratamento. Promover a silver/gold.

> **Resultado esperado:** Criar tabelas gold (ou Metric Views) equivalentes e atualizar os datasets do Power BI para eliminar o consumo direto de bronze em dashboards de negócio.

##### 🟡 Silver em uso — verificar promoção a Gold

- `silver.giftback.bonus` — Camada silver — melhor que bronze. Verificar se existe gold.giftback.bonus para promover.

##### 🔍 Gold usado no lugar de Metric View — avaliar atualização

- `gold.ads.emissao_quartou` — Específica Quartou. Candidata a mv_ads_emissao_quartou.
- `gold.ads.resgate_quartou` — Específica Quartou. Candidata a mv_ads_resgate_quartou.
- `gold.ads.resgate_usuario_quartou` — Específica Quartou. Candidata a mv_ads_usuario_quartou.

---

#### 📊 Ads - Sem Parar ARM

#### Análise 1 — Fontes

| Tabela PBI | Tipo | Tabelas no FROM |
|---|---|---|
| `fFunil_geral` | ⚠️ SQL direto | `gold.ads.b2b_bonification`, `gold.ads.b2b_message` |
| `dCalendar` | 📐 DAX | — |
| `DeliverTime` | ⚠️ SQL direto | `gold.ads.b2b_message` |
| `fFunil_definitivo` | ⚠️ SQL direto | `bronze.giftback.bonus`, `bronze.giftback.brands`, `bronze.giftback.orders`, `gold.ads.arm_receita_incremental`, `gold.ads.b2b_bonification`, `gold.ads.b2b_offer`, `gold.ads.cpl_cpi_cpa`, `silver.giftback.bonus` |
| `schema` | ⚠️ SQL direto | `gold.ads.b2b_message` |
| `brand` | ⚠️ SQL direto | `gold.ads.b2b_bonification` |
| `square` | ⚠️ SQL direto | `gold.ads.b2b_message` |
| `publisher` | ⚠️ SQL direto | `gold.ads.b2b_message` |
| `pub_praca` | ⚠️ SQL direto | `gold.ads.b2b_message` |

##### SQL completo por tabela

**`fFunil_geral`**
```sql
select
      date(m.data_payload)                                                       as dia,
      m.schema_name                                                              as schema_name,
      m.publisher_name                                                           as publisher_name,
      m.square_id                                                                as square_id,
      m.square_name                                                              as square_name,
      m.bonusb2b_campaign_id                                                     as bonusb2b_campaign_id,
      concat(coalesce(m.square_id,0),'_',m.bonusb2b_campaign_id)                 as key,
      m.versao                                                                   as versao,
      b.brand_id                                                                 as brand_id,
      count(distinct m.request_uuid)                                             as payload_recebido,
      count(distinct m.waId)         filter (where m.DeliverTime is not null)    as usuarios_unicos,
      count(distinct m.request_uuid) filter (where m.requestTime is not null)    as solicitado,
      count(distinct m.request_uuid) filter (where m.sendSentTime is not null)   as enviados,
      count(distinct m.request_uuid) filter (where m.DeliverTime is not null)    as disparos, 
      count(distinct m.request_uuid) filter (where m.ReadTime is not null)       as lidos,
      count(distinct m.request_uuid) filter (where m.flag_ativar_oferta <> 0)    as ativou,
      count(distinct m.request_uuid) filter (where m.flag_oferta_escolhida <> 0) as escolha
    from gold.ads.b2b_message m
    left join gold.ads.b2b_bonification b on m.request_uuid = b.request_uuid 
    group by all
```

**`DeliverTime`**
```sql
select max(DeliverTime) from gold.ads.b2b_message
```

**`fFunil_definitivo`**
```sql
WITH taxas AS (
  SELECT brand_id, data_ini, data_fim, CPL, CPI, CPA
  FROM gold.ads.cpl_cpi_cpa
  WHERE praca IS NULL
),

-- Impressão & Alcance (agora trazendo o canal do offer)
imp AS (
  SELECT 
    o.brand_id,
    o.versao,
    o.square_id, 
    DATE(o.data_entrega_oferta) AS dia, 
    o.bonusb2b_campaign_id,
    o.schema_name,
    o.canal AS canal,                           -- <-- AQUI
    COUNT(DISTINCT o.numero) AS alcance, 
    COUNT(DISTINCT o.request_uuid) AS impressao
  FROM gold.ads.b2b_offer o
  GROUP BY ALL
),

gera_ads AS (
  SELECT 
    DATE(bo.bonus_created) AS dia,
    bo.schema_name,
    bo.versao,
    bo.square_id, 
    bo.campaign_id AS bonusb2b_campaign_id,
    bo.brand_id, 
    COUNT(DISTINCT bo.bonus_id) AS resgate_ads
  FROM gold.ads.b2b_bonification bo
  WHERE bo.bonus_id IS NOT NULL
  GROUP BY ALL
),

-- Resgate GB
bonus_resgatados AS (
  SELECT * 
  FROM silver.giftback.bonus 
  WHERE flag_resgate = TRUE 
    AND tipo_detalhe IN ('CRMADS','BONUS B2B')
),

order_ AS (
  SELECT
    o.id AS used_orders_id,
    bo.data_resgate AS dia,
    bo.bonus_id,
    o.total,
    ROW_NUMBER() OVER (PARTITION BY o.id ORDER BY bo.bonus_id DESC) AS qtd
  FROM bonus_resgatados bo
  JOIN bronze.giftback.bonus b ON b.id = bo.bonus_id
  JOIN bronze.giftback.orders o ON b.used_orders_id = o.id
),

incremental_ AS (
  SELECT 
    dia, 
    bonus_id, 
    CASE WHEN qtd > 1 THEN 0 ELSE total END AS valor_incremental_fracionado
  FROM order_
),

resgate_gb AS (
  SELECT 
    bi.dia, 
    bo.schema_name, 
    bo.versao,
    bo.campaign_id AS bonusb2b_campaign_id,
    bo.square_id, 
    bo.brand_id, 
    SUM(bi.valor_incremental_fracionado) AS incremental, 
    COUNT(DISTINCT bo.bonus_id) AS resgate_gb 
  FROM gold.ads.b2b_bonification bo
  JOIN incremental_ bi ON bi.bonus_id = bo.bonus_id
  GROUP BY ALL
), 

-- Junção (propagando o canal do offer/imp)
feito AS (
  SELECT 
    COALESCE(im.brand_id, g.brand_id, gb.brand_id) AS brand_id,
    COALESCE(im.dia, g.dia, gb.dia) AS dia,
    COALESCE(im.versao, g.versao, gb.versao) AS versao,
    COALESCE(im.square_id, g.square_id, gb.square_id) AS square_id,
    COALESCE(im.schema_name, g.schema_name, gb.schema_name) AS schema_name,
    COALESCE(im.bonusb2b_campaign_id, g.bonusb2b_campaign_id, gb.bonusb2b_campaign_id) AS bonusb2b_campaign_id,
    im.canal AS canal,                             -- <-- AQUI (só vem do imp)
    COALESCE(im.alcance, 0) AS alcance,
    COALESCE(im.impressao, 0) AS impressao,
    COALESCE(g.resgate_ads, 0) AS resgate_ads,
    COALESCE(gb.incremental, 0) AS incremental,
    COALESCE(gb.resgate_gb, 0) AS resgate_gb
  FROM imp im
  FULL OUTER JOIN gera_ads g 
    ON im.brand_id = g.brand_id 
    AND im.schema_name = g.schema_name 
    AND im.bonusb2b_campaign_id = g.bonusb2b_campaign_id
    AND im.dia = g.dia
    AND im.versao = g.versao
    AND im.square_id = g.square_id
  FULL OUTER JOIN resgate_gb gb 
    ON im.brand_id = gb.brand_id 
    AND im.schema_name = gb.schema_name 
    AND im.bonusb2b_campaign_id = gb.bonusb2b_campaign_id
    AND im.dia = gb.dia
    AND im.versao = gb.versao
    AND im.square_id = gb.square_id
),

-- Ajuste com taxas e receita manual
ajuste2 AS (
  SELECT
    f.dia,
    f.schema_name,
    f.bonusb2b_campaign_id,
    f.brand_id,
    f.versao,
    f.square_id,
    f.canal,                                     -- <-- AQUI
    f.alcance,
    f.impressao,
    f.resgate_ads,
    f.resgate_gb,
    f.incremental,
    COALESCE(c.CPA, 0) AS cpa, 
    COALESCE(c.CPL, 0) AS cpl, 
    COALESCE(c.CPI, 0) AS cpi,
    COALESCE(
      (SELECT SUM(ii.valor_total) 
       FROM gold.ads.arm_receita_incremental ii
       WHERE ii.id_marca = f.brand_id 
         AND ii.data_criacacao = f.dia 
         AND ii.id_publisher = f.bonusb2b_campaign_id),
      0
    ) AS incremental_man
  FROM feito f
  LEFT JOIN taxas c
    ON c.data_ini <= f.dia
    AND c.data_fim >= f.dia
    AND c.brand_id = f.brand_id
),

ajuste AS (
  SELECT
    *,
    incremental_man / COUNT(*) OVER (PARTITION BY dia, bonusb2b_campaign_id, brand_id) AS incremental_manual 
  FROM ajuste2
),

-- Cálculos finais (inclui canal no agrupamento)
calculos AS (
  SELECT 
    dia,
    schema_name,
    versao,
    square_id,
    bonusb2b_campaign_id,
    brand_id,
    canal,                                       -- <-- AQUI
    SUM(alcance) AS alcance, 
    SUM(impressao) AS impressao, 
    SUM(resgate_ads) AS resgate_ads,
    SUM(resgate_gb) AS resgate_gb,
    SUM(incremental) AS incremental,
    SUM(incremental_manual) AS incremental_manual,
    SUM(incremental + incremental_manual) AS incremental_total,
    MAX(cpa) AS cpa,
    MAX(cpl) AS cpl,
    MAX(cpi) AS cpi,
    CASE 
      WHEN MAX(brand_id) = 5237 THEN SUM(incremental + incremental_manual)
      ELSE SUM((cpl*resgate_ads) + ((incremental + incremental_manual)*cpa) + (impressao*cpi))
    END AS receita_ads
  FROM ajuste
  GROUP BY ALL
)

SELECT
  c.dia,
  c.brand_id,
  c.bonusb2b_campaign_id,
  UPPER(b.name) AS name,                        
  c.schema_name,
  c.versao,
  c.square_id,
  c.canal,                                       -- <-- AQUI (no resultado final)
  concat(c.square_id,'_',c.bonusb2b_campaign_id) AS key,
  c.cpa,
  c.cpl,
  c.cpi,
  c.alcance, 
  c.impressao, 
  c.resgate_ads, 
  c.resgate_gb, 
  c.incremental_total AS incremental,
  c.receita_ads,
  CASE 
    WHEN c.cpl > 0 AND c.cpi > 0 AND c.cpa > 0 THEN 'CPL, CPI e CPA'
    WHEN c.cpi > 0 AND c.cpl > 0 THEN 'CPI e CPL'
    WHEN c.cpi > 0 AND c.cpa > 0 THEN 'CPI e CPA'
    WHEN c.cpa > 0 AND c.cpl > 0 THEN 'CPA e CPL'
    WHEN c.cpa > 0 THEN 'CPA'
    WHEN c.cpl > 0 THEN 'CPL'
    WHEN c.cpi > 0 THEN 'CPI'
  END AS filtro_cpa_cpl,
  UPPER(
    CASE 
      WHEN c.brand_id IN (5703, 5706, 5707, 5708, 5709, 5710, 5711, 5713, 5715) THEN 'Ifood'
      ELSE b.name 
    END
  ) AS SubBrand
FROM calculos c
LEFT JOIN bronze.giftback.brands b ON b.id = c.brand_id
```

**`schema`**
```sql
select distinct schema_name,
  case
      when schema_name like '%azul%' then 'Azul'
      when schema_name like '%allos%' then 'Allos'
      when schema_name like '%canaldobenja%' then 'Canal do Benja'
      when schema_name like '%clickbus%' then 'Clickbus'
      when schema_name like '%gruponomura%' then 'Grupo Nomura'
      when schema_name like '%heroseguros%' then 'Hero Seguros'
      when schema_name like '%outback%' then 'Outback'
      when schema_name like '%semparar%' then 'Sem Parar'
      when schema_name like '%stmarche%' then 'St Marche'
      when schema_name like '%lolla%' then 'Santa Lolla'
      when schema_name like '%cinepolis%' then 'Cinepolis'
      when schema_name like '%sbt%' then 'SBT'
      end as name
from gold.ads.b2b_message
```

**`brand`**
```sql
select distinct brand_id, brand_name
from gold.ads.b2b_bonification
```

**`square`**
```sql
select distinct square_id, square_name
from gold.ads.b2b_message
where square_id is not null
```

**`publisher`**
```sql
WITH base AS (
  SELECT
    bonusb2b_campaign_id,
    publisher_name,
    ROW_NUMBER() OVER (
      PARTITION BY bonusb2b_campaign_id
      ORDER BY publisher_name
    ) AS rn
  FROM gold.ads.b2b_message
  WHERE is_test = 0
)
SELECT bonusb2b_campaign_id, publisher_name
FROM base
WHERE rn = 1
```

**`pub_praca`**
```sql
with base as (
select distinct
    concat(coalesce(square_id,0),'_',bonusb2b_campaign_id)                             as key,
    square_id,
    case when square_id is null then 'Desconhecido' else square_name end as square_name,
    publisher_name,
    bonusb2b_campaign_id
from gold.ads.b2b_message )

select *, row_number()over(partition by key order by square_id) as rnk
from base
qualify rnk = 1
order by key
```

#### Análise 2 — Medidas por página

| Página | Medida | Cálculo DAX |
|---|---|---|
| Patrocinado | `Rceita Patro` | `sum(patrocinado[valor])` |
| Patrocinado | `Receita Ads` | `CALCULATE(sum(fFunil_definitivo[receita_ads]))` |
| Patrocinado | `Soma ARM` | `DIVIDE([Receita Ads],[Disparos])` |
| Patrocinado | `Soma ARM ADD` | `DIVIDE([REC TOTAL],[Disparos])` |
| Principal - Dash | `% Ger. Bônus` | `[Resgate Ads]/[Disparos]` |
| Principal - Dash | `% Resgate Ads` | `[Resgate Ads]/[Impressão]` |
| Principal - Dash | `% Resgate GB` | `DIVIDE([Resgate GB],[Impressão])` |
| Principal - Dash | `% Resgate GB BigNumber` | `DIVIDE([Resgate GB],[Disparos])` |
| Principal - Dash | `ARI` | `DIVIDE([Receita Ads],[Impressão],0)` |
| Principal - Dash | `ARI BigNumber` | `DIVIDE([Receita Ads],[Impressão])` |
| Principal - Dash | `CPA` | `CALCULATE(MAX(fFunil_definitivo[cpa]))` |
| Principal - Dash | `CPI` | `CALCULATE(MAX(fFunil_definitivo[cpi]))` |
| Principal - Dash | `CPL` | `CALCULATE(MAX(fFunil_definitivo[cpl]))` |
| Principal - Dash | `Clientes Únicos` | `sum(fFunil_geral[usuarios_unicos])` |
| Principal - Dash | `Disparos` | `CALCULATE(sum('fFunil_geral'[disparos]))` |
| Principal - Dash | `Inte % Ger. Bônus` | `[Resgate Ads]/[Interesse]` |
| Principal - Dash | `Interesse` | `CALCULATE(SUM('fFunil_geral'[ativou]))` |
| Principal - Dash | `Receita Ads` | `CALCULATE(sum(fFunil_definitivo[receita_ads]))` |
| Principal - Dash | `Receita Incremental` | `CALCULATE(sum(fFunil_definitivo[incremental]))` |
| Principal - Dash | `Soma ARM` | `DIVIDE([Receita Ads],[Disparos])` |
| Principal - Dash | `_perc_ativaram_bonus` | `DIVIDE([Interesse],[Disparos],0)` |


#### Análise de Governança — Ads - Sem Parar ARM

**Nível: 🔴 Crítico** — ✅ Verde 56% (MV+Gold) | 🟡 Amarelo 11% (Silver) | 🔴 Vermelho 33% (Bronze/Externas)

> **Regra de governança:** ✅ Verde = Metric View ou Gold | 🟡 Amarelo = Silver | 🔴 Vermelho = Bronze ou fontes externas (ex: Google Sheets)

| Tabela Databricks | Classificação | Usada em (tabela PBI) | Observação |
|---|---|---|---|
| `gold.ads.arm_receita_incremental` | ✅ Gold | `fFunil_definitivo` | Usada no cálculo de receita. Avaliar absorção pelo mv_ads_receita_ads. |
| `gold.ads.b2b_bonification` | ✅ Gold | `brand`, `fFunil_definitivo`, `fFunil_geral` | mv_ads_bonification já existe. Verificar se colunas extras usadas cabem como dimensão no MV. |
| `gold.ads.b2b_message` | ✅ Gold | `DeliverTime`, `fFunil_geral`, `pub_praca`, `publisher`, `schema`, `square` | Coberta parcialmente por mv_ads_message/mv_ads_resgate. Avaliar se dim publisher/praça pode entrar como dimensão nos MVs. |
| `gold.ads.b2b_offer` | ✅ Gold | `fFunil_definitivo` | mv_ads_offer já existe. Verificar se campos extras cabem no MV. |
| `gold.ads.cpl_cpi_cpa` | ✅ Gold | `fFunil_definitivo` | Tabela de parâmetros (CPL/CPI/CPA). Candidata a dim_taxas no mv_ads_receita_ads ou MV próprio. |
| `silver.giftback.bonus` | 🟡 Silver | `fFunil_definitivo` | Camada silver — melhor que bronze. Verificar se existe gold.giftback.bonus para promover. |
| `bronze.giftback.bonus` | 🔴 Bronze | `fFunil_definitivo` | Dados transacionais de bônus sem tratamento. Usar silver.giftback.bonus ou equivalente gold. |
| `bronze.giftback.brands` | 🔴 Bronze | `fFunil_definitivo` | Cadastro de marcas sem tratamento. Criar gold.giftback.dim_brands ou absorver nos MVs como dimensão. |
| `bronze.giftback.orders` | 🔴 Bronze | `fFunil_definitivo` | Pedidos sem tratamento. Criar gold.giftback.orders. |

##### 🚨 Mensagem para o time de Engenharia de Dados

> **Contexto:** O dashboard *Ads - Sem Parar ARM* consome 3 tabela(s) bronze diretamente, representando risco de qualidade, falta de tratamento e possível exposição de dados sensíveis.

**Ação solicitada — Bronze → Gold:**
- `bronze.giftback.bonus` — Dados transacionais de bônus sem tratamento. Usar silver.giftback.bonus ou equivalente gold.
- `bronze.giftback.brands` — Cadastro de marcas sem tratamento. Criar gold.giftback.dim_brands ou absorver nos MVs como dimensão.
- `bronze.giftback.orders` — Pedidos sem tratamento. Criar gold.giftback.orders.

> **Resultado esperado:** Criar tabelas gold (ou Metric Views) equivalentes e atualizar os datasets do Power BI para eliminar o consumo direto de bronze em dashboards de negócio.

##### 🟡 Silver em uso — verificar promoção a Gold

- `silver.giftback.bonus` — Camada silver — melhor que bronze. Verificar se existe gold.giftback.bonus para promover.

##### 🔍 Gold usado no lugar de Metric View — avaliar atualização

- `gold.ads.arm_receita_incremental` — Usada no cálculo de receita. Avaliar absorção pelo mv_ads_receita_ads.
- `gold.ads.b2b_bonification` — mv_ads_bonification já existe. Verificar se colunas extras usadas cabem como dimensão no MV.
- `gold.ads.b2b_message` — Coberta parcialmente por mv_ads_message/mv_ads_resgate. Avaliar se dim publisher/praça pode entrar como dimensão nos MVs.
- `gold.ads.b2b_offer` — mv_ads_offer já existe. Verificar se campos extras cabem no MV.
- `gold.ads.cpl_cpi_cpa` — Tabela de parâmetros (CPL/CPI/CPA). Candidata a dim_taxas no mv_ads_receita_ads ou MV próprio.

---

#### 📊 Ads - Sem Parar

#### Análise 1 — Fontes

| Tabela PBI | Tipo | Tabelas no FROM |
|---|---|---|
| `Erros` | ⚠️ SQL direto | `bronze.becon.w_company_chat_flow_message`, `bronze.becon.w_company_chat_flow_session`, `bronze.becon.w_company_contact`, `bronze.becon.w_message_error` |
| `Blocklist` | ⚠️ SQL direto | `bronze.becon.w_company_chat_flow_session`, `bronze.becon.w_company_contact`, `bronze.becon.w_company_label`, `bronze.becon.w_message_label` |
| `dMarcas` | ⚠️ SQL direto | `bronze.giftback.brands` |
| `fFunil_marca` | ⚠️ SQL direto | `bronze.giftback.bonus`, `bronze.giftback.brands`, `bronze.giftback.orders`, `gold.ads.arm_receita_incremental`, `gold.ads.b2b_bonification`, `gold.ads.b2b_message`, `gold.ads.b2b_offer`, `gold.ads.cpl_cpi_cpa`, `silver.giftback.bonus` |
| `fFunil_geral` | ⚠️ SQL direto | `bronze.giftback.bonus_b2b_logs`, `gold.ads.b2b_bonification`, `gold.ads.b2b_message`, `gold.ads.b2b_offer` |
| `dShopping` | ⚠️ SQL direto | `bronze.giftback.bonus_b2b_logs` |
| `Atualizado em` | ⚠️ SQL direto | `gold.ads.b2b_message` |
| `Praca` | ⚠️ SQL direto | `bronze.giftback.ads_publisher_squares` |
| `Campanha` | ⚠️ SQL direto | `gold.ads.b2b_message` |
| `dOptout` | ⚠️ SQL direto | `bronze.becon.w_company_chat_flow_message`, `bronze.becon.w_company_chat_flow_session`, `bronze.becon.w_company_label`, `bronze.becon.w_message_label`, `gold.ads.b2b_message` |
| `dEntregues/Enviadas` | ⚠️ SQL direto | `bronze.becon.w_company_chat_flow_message`, `bronze.becon.w_company_chat_flow_session`, `bronze.becon.w_message`, `bronze.giftback.ads_activities` |

##### SQL completo por tabela

**`Erros`**
```sql
select
  case
    when er.title :error is not null then er.title :error :message
    else er.title
  end as erro,
  cast(from_unixtime((er.errortime - 10800000) / 1000) as date) as data,
  cc.waId
from
  bronze.becon.w_message_error er
  join bronze.becon.w_company_chat_flow_message cfm on er.messageId = cfm.messageId
  join bronze.becon.w_company_chat_flow_session cfs on cfm.sessionId = cfs.flowSessionId
  join bronze.becon.w_company_contact cc on cfs.contactId = cc.contactId
where
  er.schema_name = 'wm_semparar_crmbonus'
  and cfs.requestId is not null
```

**`Blocklist`**
```sql
select cc.waid as telefone, cast(from_unixtime((ml.createdTime-10800000)/ 1000) as date) as data
from bronze.becon.w_message_label ml
join bronze.becon.w_company_contact cc on ml.waId = cc.waId
join bronze.becon.w_company_chat_flow_session cfs on cc.contactId = cfs.contactId
where ml.labelId = (select cl.labelId
                    from bronze.becon.w_company_label cl
                    where cl.labelDescription = 'b2b_select_optout' and cl.schema_name = 'wm_semparar_crmbonus')
    and ml.schema_name = 'wm_semparar_crmbonus'
    and cfs.requestId is not null
group by all
```

**`dMarcas`**
```sql
select distinct id as brand_id, upper(name) as brand_name from bronze.giftback.brands
```

**`fFunil_marca`**
```sql
WITH taxas AS (
  SELECT brand_id, data_ini, data_fim, CPL, CPI, CPA
  FROM gold.ads.cpl_cpi_cpa
  WHERE praca IS NULL
),

-- Impressão & Alcance (agora trazendo o canal do offer)
imp AS (
  SELECT 
    o.brand_id,
    o.versao,
    o.square_id, 
    m.canal,
    m.id_canal,
    DATE(o.data_entrega_oferta) AS dia, 
    o.bonusb2b_campaign_id,
    o.schema_name,                      -- <-- AQUI
    COUNT(DISTINCT o.numero) AS alcance, 
    COUNT(DISTINCT o.request_uuid) AS impressao
  FROM gold.ads.b2b_offer o
  INNER JOIN gold.ads.b2b_message m ON o.request_uuid = m.request_uuid
  GROUP BY ALL
),

gera_ads AS (
  SELECT 
    DATE(bo.bonus_created) AS dia,
    bo.schema_name,
    bo.versao,
    bo.square_id, 
    bo.campaign_id AS bonusb2b_campaign_id,
    bo.brand_id, 
    COUNT(DISTINCT bo.bonus_id) AS resgate_ads
  FROM gold.ads.b2b_bonification bo
  WHERE bo.bonus_id IS NOT NULL
  GROUP BY ALL
),

-- Resgate GB
bonus_resgatados AS (
  SELECT * 
  FROM silver.giftback.bonus 
  WHERE flag_resgate = TRUE 
    AND tipo_detalhe IN ('CRMADS','BONUS B2B')
),

order_ AS (
  SELECT
    o.id AS used_orders_id,
    bo.data_resgate AS dia,
    bo.bonus_id,
    o.total,
    ROW_NUMBER() OVER (PARTITION BY o.id ORDER BY bo.bonus_id DESC) AS qtd
  FROM bonus_resgatados bo
  JOIN bronze.giftback.bonus b ON b.id = bo.bonus_id
  JOIN bronze.giftback.orders o ON b.used_orders_id = o.id
),

incremental_ AS (
  SELECT 
    dia, 
    bonus_id, 
    CASE WHEN qtd > 1 THEN 0 ELSE total END AS valor_incremental_fracionado
  FROM order_
),

resgate_gb AS (
  SELECT 
    bi.dia, 
    bo.schema_name, 
    bo.versao,
    bo.campaign_id AS bonusb2b_campaign_id,
    bo.square_id, 
    bo.brand_id, 
    SUM(bi.valor_incremental_fracionado) AS incremental, 
    COUNT(DISTINCT bo.bonus_id) AS resgate_gb 
  FROM gold.ads.b2b_bonification bo
  JOIN incremental_ bi ON bi.bonus_id = bo.bonus_id
  GROUP BY ALL
), 

-- Junção (propagando o canal do offer/imp)
feito AS (
  SELECT 
    COALESCE(im.brand_id, g.brand_id, gb.brand_id) AS brand_id,
    COALESCE(im.dia, g.dia, gb.dia) AS dia,
    COALESCE(im.versao, g.versao, gb.versao) AS versao,
    COALESCE(im.square_id, g.square_id, gb.square_id) AS square_id,
    COALESCE(im.schema_name, g.schema_name, gb.schema_name) AS schema_name,
    COALESCE(im.bonusb2b_campaign_id, g.bonusb2b_campaign_id, gb.bonusb2b_campaign_id) AS bonusb2b_campaign_id,
    im.canal AS canal,                             -- <-- AQUI (só vem do imp)
    COALESCE(im.alcance, 0) AS alcance,
    COALESCE(im.impressao, 0) AS impressao,
    COALESCE(g.resgate_ads, 0) AS resgate_ads,
    COALESCE(gb.incremental, 0) AS incremental,
    COALESCE(gb.resgate_gb, 0) AS resgate_gb
  FROM imp im
  FULL OUTER JOIN gera_ads g 
    ON im.brand_id = g.brand_id 
    AND im.schema_name = g.schema_name 
    AND im.bonusb2b_campaign_id = g.bonusb2b_campaign_id
    AND im.dia = g.dia
    AND im.versao = g.versao
    AND im.square_id = g.square_id
  FULL OUTER JOIN resgate_gb gb 
    ON im.brand_id = gb.brand_id 
    AND im.schema_name = gb.schema_name 
    AND im.bonusb2b_campaign_id = gb.bonusb2b_campaign_id
    AND im.dia = gb.dia
    AND im.versao = gb.versao
    AND im.square_id = gb.square_id
),

-- Ajuste com taxas e receita manual
ajuste2 AS (
  SELECT
    f.dia,
    f.schema_name,
    f.bonusb2b_campaign_id,
    f.brand_id,
    f.versao,
    f.square_id,
    f.canal,                                     -- <-- AQUI
    f.alcance,
    f.impressao,
    f.resgate_ads,
    f.resgate_gb,
    f.incremental,
    COALESCE(c.CPA, 0) AS cpa, 
    COALESCE(c.CPL, 0) AS cpl, 
    COALESCE(c.CPI, 0) AS cpi,
    COALESCE(
      (SELECT SUM(ii.valor_total) 
       FROM gold.ads.arm_receita_incremental ii
       WHERE ii.id_marca = f.brand_id 
         AND ii.data_criacacao = f.dia 
         AND ii.id_publisher = f.bonusb2b_campaign_id),
      0
    ) AS incremental_man
  FROM feito f
  LEFT JOIN taxas c
    ON c.data_ini <= f.dia
    AND c.data_fim >= f.dia
    AND c.brand_id = f.brand_id
),

ajuste AS (
  SELECT
    *,
    incremental_man / COUNT(*) OVER (PARTITION BY dia, bonusb2b_campaign_id, brand_id) AS incremental_manual 
  FROM ajuste2
),

-- Cálculos finais (inclui canal no agrupamento)
calculos AS (
  SELECT 
    dia,
    schema_name,
    versao,
    square_id,
    bonusb2b_campaign_id,
    brand_id,
    canal,                                       -- <-- AQUI
    SUM(alcance) AS alcance, 
    SUM(impressao) AS impressao, 
    SUM(resgate_ads) AS resgate_ads,
    SUM(resgate_gb) AS resgate_gb,
    SUM(incremental) AS incremental,
    SUM(incremental_manual) AS incremental_manual,
    SUM(incremental + incremental_manual) AS incremental_total,
    MAX(cpa) AS cpa,
    MAX(cpl) AS cpl,
    MAX(cpi) AS cpi,
    CASE 
      WHEN MAX(brand_id) = 5237 THEN SUM(incremental + incremental_manual)
      ELSE SUM((cpl*resgate_ads) + ((incremental + incremental_manual)*cpa) + (impressao*cpi))
    END AS receita_ads
  FROM ajuste
  GROUP BY ALL
)

SELECT
  c.dia,
  c.brand_id,
  c.bonusb2b_campaign_id,
  UPPER(b.name) AS name,                        
  c.schema_name,
  c.versao,
  c.square_id,
  c.canal,                                       -- <-- AQUI (no resultado final)
  concat(c.square_id,'_',c.bonusb2b_campaign_id) AS key,
  c.cpa,
  c.cpl,
  c.cpi,
  c.alcance, 
  c.impressao, 
  c.resgate_ads, 
  c.resgate_gb, 
  c.incremental_total AS incremental,
  c.receita_ads,
  CASE 
    WHEN c.cpl > 0 AND c.cpi > 0 AND c.cpa > 0 THEN 'CPL, CPI e CPA'
    WHEN c.cpi > 0 AND c.cpl > 0 THEN 'CPI e CPL'
    WHEN c.cpi > 0 AND c.cpa > 0 THEN 'CPI e CPA'
    WHEN c.cpa > 0 AND c.cpl > 0 THEN 'CPA e CPL'
    WHEN c.cpa > 0 THEN 'CPA'
    WHEN c.cpl > 0 THEN 'CPL'
    WHEN c.cpi > 0 THEN 'CPI'
  END AS filtro_cpa_cpl,
  UPPER(
    CASE 
      WHEN c.brand_id IN (5703, 5706, 5707, 5708, 5709, 5710, 5711, 5713, 5715) THEN 'Ifood'
      ELSE b.name 
    END
  ) AS SubBrand
FROM calculos c
LEFT JOIN bronze.giftback.brands b ON b.id = c.brand_id
WHERE c.bonusb2b_campaign_id IN ('9','5','37','39')
```

**`fFunil_geral`**
```sql
select
      date(m.data_payload)                                                       as dia,
      hour( to_timestamp(date(m.data_payload) || ' ' || lpad(hour(m.data_payload), 2, '0') || ':00:00', 'yyyy-MM-dd HH:mm:ss')) as hora,
      m.schema_name                                                              as schema_name,
      m.publisher_name                                                           as publisher_name,
      m.square_id                                                                as square_id,
      m.id_canal                                                                 as id_canal,
      m.canal                                                                    AS canal,
      m.square_name                                                              as square_name,
      m.bonusb2b_campaign_id                                                     as bonusb2b_campaign_id,
      b.bonus_amount                                                             as bonus_resgatado,
      count(distinct b.bonus_id)                                                 as bonus,
      count(distinct b.request_uuid)                                             as payload_gb_chat,
      concat(m.square_id,'_',m.bonusb2b_campaign_id)                             as key,
      m.versao                                                                   as versao,
      b.brand_id                                                                 as brand_id,
      count(distinct m.request_uuid)                                             as payload_recebido,
      count(distinct m.waId)         filter (where m.DeliverTime is not null)    as usuarios_unicos,
      count(distinct m.request_uuid) filter (where m.requestTime is not null)    as solicitado,
      count(distinct m.request_uuid) filter (where m.sendSentTime is not null)   as enviados,
      count(distinct m.request_uuid) filter (where m.DeliverTime is not null)    as disparos, 
      count(distinct m.request_uuid) filter (where m.ReadTime is not null)       as lidos,
      count(distinct m.request_uuid) filter (where m.flag_ativar_oferta <> 0)    as ativou,
      count(distinct m.request_uuid) filter (where m.flag_oferta_escolhida <> 0) as escolha,
      count(distinct m.request_uuid) filter (where flag_opt_out = 1)   as OptOut,
      count(distinct m.request_uuid) filter (where m.flag_oferta_escolhida <> 0 or b.bonus_id is not null) as escolhe,
      case when COUNT(offerId) > 6 then '6+' else COUNT(offerId) end  total_views
    from gold.ads.b2b_message m
    left join gold.ads.b2b_bonification b on m.request_uuid = b.request_uuid
    left join bronze.giftback.bonus_b2b_logs b2b on m.request_uuid = b2b.request
    left join gold.ads.b2b_offer O ON M.request_uuid = O.request_uuid AND O.versao = 'v2' 
    where m.bonusb2b_campaign_id in ('9','5','37','39')

    group by all
```

**`dShopping`**
```sql
select distinct
      case when pr.request:customFields:venue = 'Shopping Iguatemi' then 'Iguatemi'
      when pr.request:customFields:venue = 'IGUATEMI' then 'Iguatemi'
      when pr.request:customFields:venue = 'Shopping Patio Osasco' then 'Pátio Osasco'
      when pr.request:customFields:venue = 'Shopping Higienopolis' then 'Pátio Higienópolis'
      when pr.request:customFields:venue = 'Shopping Higienópolis' then 'Pátio Higienópolis'
      when pr.request:customFields:venue = 'Shopping Patio Higienopolis' then 'Pátio Higienópolis'
      when pr.request:customFields:venue = 'Patio Higienopolis' then 'Pátio Higienópolis'
      when pr.request:customFields:venue = 'Shopping Pátio Higienópolis' then 'Pátio Higienópolis'
      when pr.request:customFields:venue = 'Shopping Pátio Higienópolis ' then 'Pátio Higienópolis'
      when pr.request:customFields:venue = 'Pátio Higienópolis' then 'Pátio Higienópolis'    
      when pr.request:customFields:venue = 'ELDORADO' then 'Eldorado'
      when pr.request:customFields:venue = 'Shopping Eldorado' then 'Eldorado'
     when pr.request:customFields:venue = 'SHOPPING ELDORADO' then 'Eldorado'
     when pr.request:customFields:venue = 'SHOPPING BOURBON' then 'Bourbon'
     when pr.request:customFields:venue = 'Shopping Bourbon' then 'Bourbon'
     else pr.request:customFields:venue end as shopping
      from
      bronze.giftback.bonus_b2b_logs pr
      where
      pr.bonusb2b_campaign_id = 9
      and pr.request:customFields:venue is not null
      and pr.type like 'create_request%'
      and date(pr.created) >= '2024-06-01'
      and request:teste is null
      and pr.request:phonenumber not in 
      ('5511965776069',
      '5511970933933',
      '5511916099332',
      '5511999896330',
      '5511958322850',
      '5511941475811',
      '5511915936905',
      '5511994528490',
      '5511951324494',
      '558597465114',
      '5511987867868',	
      '5585997465114',	
      '5511999864382',
      '11987867868')
```

**`Atualizado em`**
```sql
select max(DeliverTime) from gold.ads.b2b_message where versao = 'v2' and schema_name like '%semparar%'
```

**`Praca`**
```sql
select id, name                                                                from bronze.giftback.ads_publisher_squares
WHERE ads_publisher_id IN (5,9,37,39)
```

**`Campanha`**
```sql
select distinct bonusb2b_campaign_id, 
case 
when bonusb2b_campaign_id in (5,39) then 'Abastece'
 when bonusb2b_campaign_id in (9,37) then 'Estacione'
end as jornada
from gold.ads.b2b_message
 where bonusb2b_campaign_id in (9,37,5,39)
```

**`dOptout`**
```sql
with optout as 
(         select distinct
                 ml.messageId,
                 cl.labelDescription
            from bronze.becon.w_message_label ml
            join bronze.becon.w_company_label cl
              on ml.labelId = cl.labelId
           where cl.labelDescription in
                 ('b2b_optout_select',
                  'b2b_previous_optout',
                  'ads_OptoutPrevio',
                  'ads_BloqueioDireto'))

select me.publisher_name    as nome_campanha,
bonusb2b_campaign_id,
       date(me.data_payload)      as data_requisicao_criada,
       date(me.sendsentTime)      as data_envio,
       date(me.DeliverTime)       as data_entrega,
       date(me.readTime)          as data_lida,
      count(distinct op.messageId) flag_optout
      from gold.ads.b2b_message me
      join bronze.becon.w_company_chat_flow_session cfs
        on cfs.requestId = me.request_uuid
      join bronze.becon.w_company_chat_flow_message cfm
        on cfm.sessionId = cfs.flowSessionId
      left
      join optout op
        on op.messageId = cfm.messageId
      where me.sendsentTime >= '2025-01-01' and me.schema_name like 'wm_semparar_crmbonus%'
group by all
```

**`dEntregues/Enviadas`**
```sql
select 
        date(from_unixtime((m.sendSentTime/1000) - 10800)) as Data,
        aa.ads_publisher_id,
        aa.ads_publisher_square_id,
        COUNT(DISTINCT m.messageId) FILTER (WHERE m.sendSentTime IS NOT NULL AND m.sendSentTime != 0) AS enviadas,
        COUNT(DISTINCT m.messageId) FILTER (WHERE m.sendDeliverTime IS NOT NULL AND m.sendDeliverTime != 0) AS entregue
from bronze.becon.w_message m  
join bronze.becon.w_company_chat_flow_message fm on m.messageId = fm.messageId
join bronze.becon.w_company_chat_flow_session fs on fm.sessionId = fs.flowSessionId
join bronze.giftback.ads_activities aa on fs.requestId = aa.request_uuid
where fm.schema_name like '%semparar%'
group by all
```

#### Análise 2 — Medidas por página

| Página | Medida | Cálculo DAX |
|---|---|---|
| Visão de Diária | `ARI` | `DIVIDE([Receita Ads],[Impressão Marca])` |
| Visão de Diária | `ARM` | `DIVIDE([Receita Ads],[Disparos])` |
| Visão de Diária | `Clique em ofertas` | `CALCULATE(SUM('fFunil_geral'[escolhe]))` |
| Visão de Diária | `Disparos` | `CALCULATE(sum('fFunil_geral'[disparos]))` |
| Visão de Diária | `Ger. de Bônus` | `sum(fFunil_marca[resgate_ads])` |
| Visão de Diária | `Impressão Marca` | `sum(fFunil_marca[impressao])` |
| Visão de Diária | `Rec. Incremental` | `sum(fFunil_marca[incremental])` |
| Visão de Diária | `Receita Ads` | `SUM(fFunil_marca[receita_ads])` |
| Visão de Diária | `Resgate GB` | `sum(fFunil_marca[resgate_gb])` |
| Visão de Diária | `Taxa de Conversão` | `DIVIDE([Ger. de Bônus],[Disparos])` |
| Visão de Diária | `Taxa de Interesse` | `DIVIDE([interesse],[Disparos])` |
| Visão de Diária | `_perc_ativaram_bonus` | `DIVIDE([_Clicaram_ativar_bonus],[Disparos],0)` |
| Visão de Diária | `_perc_geraram_bonus` | `DIVIDE([Ger. de Bônus],[Disparos],0)` |
| Visão de Diária | `interesse` | `CALCULATE(sum(fFunil_geral[ativou]))` |
| Visão de Diária | `perc_escolheram_oferta` | `DIVIDE([Clique em ofertas],[Disparos],0)` |
| Visão de Diária | `perc_geraram_bonus_geral` | `DIVIDE(sum(fFunil_marca[resgate_ads]),sum(fFunil_geral[disparos]),0)` |
| Visão de Diária | `perc_geraram_bonus_marca` | `DIVIDE(sum(fFunil_marca[resgate_ads]),sum(fFunil_marca[impressao]),0)` |
| Visão por Praça  | `ARI` | `DIVIDE([Receita Ads],[Impressão Marca])` |
| Visão por Praça  | `ARM` | `DIVIDE([Receita Ads],[Disparos])` |
| Visão por Praça  | `Clique em ofertas` | `CALCULATE(SUM('fFunil_geral'[escolhe]))` |
| Visão por Praça  | `Disparos` | `CALCULATE(sum('fFunil_geral'[disparos]))` |
| Visão por Praça  | `Ger. de Bônus` | `sum(fFunil_marca[resgate_ads])` |
| Visão por Praça  | `Rec. Incremental` | `sum(fFunil_marca[incremental])` |
| Visão por Praça  | `Receita Ads` | `SUM(fFunil_marca[receita_ads])` |
| Visão por Praça  | `Resg. de Bônus` | `sum(fFunil_geral[bonus_resgatado])` |
| Visão por Praça  | `Resgate GB` | `sum(fFunil_marca[resgate_gb])` |
| Visão por Praça  | `_perc_ativaram_bonus` | `DIVIDE([_Clicaram_ativar_bonus],[Disparos],0)` |
| Visão por Praça  | `_perc_geraram_bonus` | `DIVIDE([Ger. de Bônus],[Disparos],0)` |
| Visão por Praça  | `interesse` | `CALCULATE(sum(fFunil_geral[ativou]))` |
| Visão por Praça  | `perc_escolheram_oferta` | `DIVIDE([Clique em ofertas],[Disparos],0)` |
| Visão por Praça  | `perc_geraram_bonus_geral` | `DIVIDE(sum(fFunil_marca[resgate_ads]),sum(fFunil_geral[disparos]),0)` |
| Visão por Praça  | `perc_geraram_bonus_marca` | `DIVIDE(sum(fFunil_marca[resgate_ads]),sum(fFunil_marca[impressao]),0)` |
| Geral por hora | `ARI` | `DIVIDE([Receita Ads],[Impressão Marca])` |
| Geral por hora | `ARM` | `DIVIDE([Receita Ads],[Disparos])` |
| Geral por hora | `Clique em ofertas` | `CALCULATE(SUM('fFunil_geral'[escolhe]))` |
| Geral por hora | `Disparos` | `CALCULATE(sum('fFunil_geral'[disparos]))` |
| Geral por hora | `Ger. de Bônus` | `sum(fFunil_marca[resgate_ads])` |
| Geral por hora | `Rec. Incremental` | `sum(fFunil_marca[incremental])` |
| Geral por hora | `Receita Ads` | `SUM(fFunil_marca[receita_ads])` |
| Geral por hora | `Resg. de Bônus` | `sum(fFunil_geral[bonus_resgatado])` |
| Geral por hora | `Resgate GB` | `sum(fFunil_marca[resgate_gb])` |
| Geral por hora | `_Clicaram_ativar_bonus` | `CALCULATE(SUM('fFunil_geral'[ativou]))` |
| Geral por hora | `_perc_ativaram_bonus` | `DIVIDE([_Clicaram_ativar_bonus],[Disparos],0)` |
| Geral por hora | `_perc_geraram_bonus` | `DIVIDE([Ger. de Bônus],[Disparos],0)` |
| Geral por hora | `interesse` | `CALCULATE(sum(fFunil_geral[ativou]))` |
| Geral por hora | `perc_escolheram_oferta` | `DIVIDE([Clique em ofertas],[Disparos],0)` |
| Geral por hora | `perc_geraram_bonus_geral` | `DIVIDE(sum(fFunil_marca[resgate_ads]),sum(fFunil_geral[disparos]),0)` |
| Geral | `Clique em ofertas` | `CALCULATE(SUM('fFunil_geral'[escolhe]))` |
| Geral | `Disparos` | `CALCULATE(sum('fFunil_geral'[disparos]))` |
| Geral | `Ger. de Bônus` | `sum(fFunil_marca[resgate_ads])` |
| Geral | `Rec. Incremental` | `sum(fFunil_marca[incremental])` |
| Geral | `Resgate GB` | `sum(fFunil_marca[resgate_gb])` |
| Geral | `_Clicaram_ativar_bonus` | `CALCULATE(SUM('fFunil_geral'[ativou]))` |
| Geral | `_Clientes_únicos` | `CALCULATE(SUM('fFunil_geral'[usuarios_unicos]))` |
| Geral | `_perc_OptOut` | `DIVIDE([_OptOut],[Disparos],0)` |
| Geral | `_perc_ativaram_bonus` | `DIVIDE([_Clicaram_ativar_bonus],[Disparos],0)` |
| Geral | `_perc_geraram_bonus` | `DIVIDE([Ger. de Bônus],[Disparos],0)` |
| Geral | `perc_escolheram_oferta` | `DIVIDE([Clique em ofertas],[Disparos],0)` |
| Geral | `perc_geraram_bonus_geral` | `DIVIDE(sum(fFunil_marca[resgate_ads]),sum(fFunil_geral[disparos]),0)` |
| Visão por Marca | `ARI` | `DIVIDE([Receita Ads],[Impressão Marca])` |
| Visão por Marca | `ARM` | `DIVIDE([Receita Ads],[Disparos])` |
| Visão por Marca | `Alcance Marca` | `sum(fFunil_marca[alcance])` |
| Visão por Marca | `Clique em ofertas` | `CALCULATE(SUM('fFunil_geral'[escolhe]))` |
| Visão por Marca | `Disparos` | `CALCULATE(sum('fFunil_geral'[disparos]))` |
| Visão por Marca | `Ger. de Bônus` | `sum(fFunil_marca[resgate_ads])` |
| Visão por Marca | `Impressão Marca` | `sum(fFunil_marca[impressao])` |
| Visão por Marca | `Rec. Incremental` | `sum(fFunil_marca[incremental])` |
| Visão por Marca | `Receita Ads` | `SUM(fFunil_marca[receita_ads])` |
| Visão por Marca | `Resgate GB` | `sum(fFunil_marca[resgate_gb])` |
| Visão por Marca | `_perc_ativaram_bonus` | `DIVIDE([_Clicaram_ativar_bonus],[Disparos],0)` |
| Visão por Marca | `_perc_geraram_bonus` | `DIVIDE([Ger. de Bônus],[Disparos],0)` |
| Visão por Marca | `interesse` | `CALCULATE(sum(fFunil_geral[ativou]))` |
| Visão por Marca | `perc_escolheram_oferta` | `DIVIDE([Clique em ofertas],[Disparos],0)` |
| Visão por Marca | `perc_geraram_bonus_geral` | `DIVIDE(sum(fFunil_marca[resgate_ads]),sum(fFunil_geral[disparos]),0)` |
| Visão por Marca | `perc_geraram_bonus_marca` | `DIVIDE(sum(fFunil_marca[resgate_ads]),sum(fFunil_marca[impressao]),0)` |
| Recorrência | `Alcance Marca` | `sum(fFunil_marca[alcance])` |
| Recorrência | `Disparos` | `CALCULATE(sum('fFunil_geral'[disparos]))` |
| Recorrência | `Quantidade de Clientes` | `—` |
| Recorrência | `_Clicaram_ativar_bonus` | `CALCULATE(SUM('fFunil_geral'[ativou]))` |
| Recorrência | `_Clientes_únicos` | `CALCULATE(SUM('fFunil_geral'[usuarios_unicos]))` |
| Recorrência | `_Escolheram_oferta` | `CALCULATE(SUM('fFunil_geral'[escolha]))` |
| Recorrência | `_perc_ativaram_bonus` | `DIVIDE([_Clicaram_ativar_bonus],[Disparos],0)` |
| Recorrência | `_perc_escolheram_oferta` | `DIVIDE([_Escolheram_oferta],[Disparos],0)` |
| Recorrência | `_perc_geraram_bonus` | `DIVIDE([Ger. de Bônus],[Disparos],0)` |
| Blocklist | `Alcance Marca` | `sum(fFunil_marca[alcance])` |
| Blocklist | `Disparos` | `CALCULATE(sum('fFunil_geral'[disparos]))` |
| Blocklist | `_Clicaram_ativar_bonus` | `CALCULATE(SUM('fFunil_geral'[ativou]))` |
| Blocklist | `_Clientes_únicos` | `CALCULATE(SUM('fFunil_geral'[usuarios_unicos]))` |
| Blocklist | `_Escolheram_oferta` | `CALCULATE(SUM('fFunil_geral'[escolha]))` |
| Blocklist | `_perc_ativaram_bonus` | `DIVIDE([_Clicaram_ativar_bonus],[Disparos],0)` |
| Blocklist | `_perc_escolheram_oferta` | `DIVIDE([_Escolheram_oferta],[Disparos],0)` |
| Blocklist | `_perc_geraram_bonus` | `DIVIDE([Ger. de Bônus],[Disparos],0)` |
| Erros | `Alcance Marca` | `sum(fFunil_marca[alcance])` |
| Erros | `Disparos` | `CALCULATE(sum('fFunil_geral'[disparos]))` |
| Erros | `_Bônus_resgatados_Marca` | `CALCULATE(SUM('fFunil_marca'[bonus_resgatados]))` |
| Erros | `_Clicaram_ativar_bonus` | `CALCULATE(SUM('fFunil_geral'[ativou]))` |
| Erros | `_Clientes_únicos` | `CALCULATE(SUM('fFunil_geral'[usuarios_unicos]))` |
| Erros | `_Escolheram_oferta` | `CALCULATE(SUM('fFunil_geral'[escolha]))` |
| Erros | `_perc_ativaram_bonus` | `DIVIDE([_Clicaram_ativar_bonus],[Disparos],0)` |
| Erros | `_perc_escolheram_oferta` | `DIVIDE([_Escolheram_oferta],[Disparos],0)` |
| Erros | `_perc_geraram_bonus` | `DIVIDE([Ger. de Bônus],[Disparos],0)` |

#### Análise de Governança — Ads - Sem Parar

**Nível: 🔴 Crítico** — ✅ Verde 26% (MV+Gold) | 🟡 Amarelo 5% (Silver) | 🔴 Vermelho 68% (Bronze/Externas)

> **Regra de governança:** ✅ Verde = Metric View ou Gold | 🟡 Amarelo = Silver | 🔴 Vermelho = Bronze ou fontes externas (ex: Google Sheets)

| Tabela Databricks | Classificação | Usada em (tabela PBI) | Observação |
|---|---|---|---|
| `gold.ads.arm_receita_incremental` | ✅ Gold | `fFunil_marca` | Usada no cálculo de receita. Avaliar absorção pelo mv_ads_receita_ads. |
| `gold.ads.b2b_bonification` | ✅ Gold | `fFunil_geral`, `fFunil_marca` | mv_ads_bonification já existe. Verificar se colunas extras usadas cabem como dimensão no MV. |
| `gold.ads.b2b_message` | ✅ Gold | `Atualizado em`, `Campanha`, `dOptout`, `fFunil_geral`, `fFunil_marca` | Coberta parcialmente por mv_ads_message/mv_ads_resgate. Avaliar se dim publisher/praça pode entrar como dimensão nos MVs. |
| `gold.ads.b2b_offer` | ✅ Gold | `fFunil_geral`, `fFunil_marca` | mv_ads_offer já existe. Verificar se campos extras cabem no MV. |
| `gold.ads.cpl_cpi_cpa` | ✅ Gold | `fFunil_marca` | Tabela de parâmetros (CPL/CPI/CPA). Candidata a dim_taxas no mv_ads_receita_ads ou MV próprio. |
| `silver.giftback.bonus` | 🟡 Silver | `fFunil_marca` | Camada silver — melhor que bronze. Verificar se existe gold.giftback.bonus para promover. |
| `bronze.becon.w_company_chat_flow_message` | 🔴 Bronze | `Erros`, `dEntregues/Enviadas`, `dOptout` | Mensagens de chat (Becon) do bronze. Criar silver/gold.becon equivalente. |
| `bronze.becon.w_company_chat_flow_session` | 🔴 Bronze | `Blocklist`, `Erros`, `dEntregues/Enviadas`, `dOptout` | Sessões de chat (Becon) do bronze. Criar silver/gold equivalente. |
| `bronze.becon.w_company_contact` | 🔴 Bronze | `Blocklist`, `Erros` | Contatos Becon (dados PII) do bronze. Urgente: criar silver/gold. |
| `bronze.becon.w_company_label` | 🔴 Bronze | `Blocklist`, `dOptout` | Labels Becon do bronze. Criar silver/gold. |
| `bronze.becon.w_message` | 🔴 Bronze | `dEntregues/Enviadas` | Mensagens Becon do bronze. Criar silver/gold. |
| `bronze.becon.w_message_error` | 🔴 Bronze | `Erros` | Erros Becon do bronze. Criar silver/gold. |
| `bronze.becon.w_message_label` | 🔴 Bronze | `Blocklist`, `dOptout` | Labels de mensagem Becon do bronze. Criar silver/gold. |
| `bronze.giftback.ads_activities` | 🔴 Bronze | `dEntregues/Enviadas` | Atividades ADS do bronze. Criar gold.ads.b2b_activities. |
| `bronze.giftback.ads_publisher_squares` | 🔴 Bronze | `Praca` | Mapeamento publisher/praça do bronze. Criar gold.ads.dim_publisher_squares. |
| `bronze.giftback.bonus` | 🔴 Bronze | `fFunil_marca` | Dados transacionais de bônus sem tratamento. Usar silver.giftback.bonus ou equivalente gold. |
| `bronze.giftback.bonus_b2b_logs` | 🔴 Bronze | `dShopping`, `fFunil_geral` | Logs de bônus B2B do bronze. Criar gold.ads.b2b_logs. |
| `bronze.giftback.brands` | 🔴 Bronze | `dMarcas`, `fFunil_marca` | Cadastro de marcas sem tratamento. Criar gold.giftback.dim_brands ou absorver nos MVs como dimensão. |
| `bronze.giftback.orders` | 🔴 Bronze | `fFunil_marca` | Pedidos sem tratamento. Criar gold.giftback.orders. |

##### 🚨 Mensagem para o time de Engenharia de Dados

> **Contexto:** O dashboard *Ads - Sem Parar* consome 13 tabela(s) bronze diretamente, representando risco de qualidade, falta de tratamento e possível exposição de dados sensíveis.

**Ação solicitada — Bronze → Gold:**
- `bronze.becon.w_company_chat_flow_message` — Mensagens de chat (Becon) do bronze. Criar silver/gold.becon equivalente.
- `bronze.becon.w_company_chat_flow_session` — Sessões de chat (Becon) do bronze. Criar silver/gold equivalente.
- `bronze.becon.w_company_contact` — Contatos Becon (dados PII) do bronze. Urgente: criar silver/gold.
- `bronze.becon.w_company_label` — Labels Becon do bronze. Criar silver/gold.
- `bronze.becon.w_message` — Mensagens Becon do bronze. Criar silver/gold.
- `bronze.becon.w_message_error` — Erros Becon do bronze. Criar silver/gold.
- `bronze.becon.w_message_label` — Labels de mensagem Becon do bronze. Criar silver/gold.
- `bronze.giftback.ads_activities` — Atividades ADS do bronze. Criar gold.ads.b2b_activities.
- `bronze.giftback.ads_publisher_squares` — Mapeamento publisher/praça do bronze. Criar gold.ads.dim_publisher_squares.
- `bronze.giftback.bonus` — Dados transacionais de bônus sem tratamento. Usar silver.giftback.bonus ou equivalente gold.
- `bronze.giftback.bonus_b2b_logs` — Logs de bônus B2B do bronze. Criar gold.ads.b2b_logs.
- `bronze.giftback.brands` — Cadastro de marcas sem tratamento. Criar gold.giftback.dim_brands ou absorver nos MVs como dimensão.
- `bronze.giftback.orders` — Pedidos sem tratamento. Criar gold.giftback.orders.

> **Resultado esperado:** Criar tabelas gold (ou Metric Views) equivalentes e atualizar os datasets do Power BI para eliminar o consumo direto de bronze em dashboards de negócio.

##### 🟡 Silver em uso — verificar promoção a Gold

- `silver.giftback.bonus` — Camada silver — melhor que bronze. Verificar se existe gold.giftback.bonus para promover.

##### 🔍 Gold usado no lugar de Metric View — avaliar atualização

- `gold.ads.arm_receita_incremental` — Usada no cálculo de receita. Avaliar absorção pelo mv_ads_receita_ads.
- `gold.ads.b2b_bonification` — mv_ads_bonification já existe. Verificar se colunas extras usadas cabem como dimensão no MV.
- `gold.ads.b2b_message` — Coberta parcialmente por mv_ads_message/mv_ads_resgate. Avaliar se dim publisher/praça pode entrar como dimensão nos MVs.
- `gold.ads.b2b_offer` — mv_ads_offer já existe. Verificar se campos extras cabem no MV.
- `gold.ads.cpl_cpi_cpa` — Tabela de parâmetros (CPL/CPI/CPA). Candidata a dim_taxas no mv_ads_receita_ads ou MV próprio.

---


---

### 🎁 Giftback


#### 📊 GB - cliente_natura

---

##### Análise 1 — Fontes (tabelas) utilizadas

| Tabela PBI | Fonte (Databricks) | Tipo | Governança |
|---|---|---|---|
| `dLojas` | `silver.giftback.bonus` | Tabela silver | 🟡 Amarelo |
| `dLojas` | `bronze.giftback.customer_configs` | Tabela bronze | 🔴 Vermelho |
| `dLojas` | `bronze.giftback.customers` | Tabela bronze | 🔴 Vermelho |
| `dMasters` | `silver.giftback.masters` | Tabela silver | 🟡 Amarelo |
| `fperdidos` | `bronze.giftback.bonus` | Tabela bronze | 🔴 Vermelho |
| `fvendas` | `bronze.giftback.total_sales` | Tabela bronze | 🔴 Vermelho |
| `fResgatados correto` | `bronze.giftback.orders` | Tabela bronze | 🔴 Vermelho |
| `fmv_perdidos` | `gold.giftback.mv_bonus_perdido_master` | Metric View | ✅ Verde |
| `fmv_gerados` | `gold.giftback.mv_bonus_gerado_master` | Metric View | ✅ Verde |
| `fmv_resgatados` | `gold.giftback.mv_bonus_resgate_master` | Metric View | ✅ Verde |
| `ftop_clientes` | `bronze.giftback.customer_users` | Tabela bronze | 🔴 Vermelho |
| `fmv_engajamento` | `gold.giftback.mv_engajamento_master` | Metric View | ✅ Verde |

**Scorecard: ✅ 4 · 🟡 2 · 🔴 6 → 🔴 Crítico**


##### SQL por fonte


**`silver.giftback.bonus`**
```sql
let
 
Query =
"
    select distinct
        b.codigo_loja, 
        b.loja,
        --cc.franchisee,
        cc.regional_manager,
        cc.supervisor,
        CASE 
            WHEN c.customer_type = 0 THEN 'Própria'
            WHEN c.customer_type = 1 THEN 'Franquia'
            WHEN c.customer_
```


**`bronze.giftback.customer_configs`**
```sql
let
 
Query =
"
    select distinct
        b.codigo_loja, 
        b.loja,
        --cc.franchisee,
        cc.regional_manager,
        cc.supervisor,
        CASE 
            WHEN c.customer_type = 0 THEN 'Própria'
            WHEN c.customer_type = 1 THEN 'Franquia'
            WHEN c.customer_
```


**`bronze.giftback.customers`**
```sql
let
 
Query =
"
    select distinct
        b.codigo_loja, 
        b.loja,
        --cc.franchisee,
        cc.regional_manager,
        cc.supervisor,
        CASE 
            WHEN c.customer_type = 0 THEN 'Própria'
            WHEN c.customer_type = 1 THEN 'Franquia'
            WHEN c.customer_
```


**`silver.giftback.masters`**
```sql
// let
//     Fonte = Value.NativeQuery(Databricks.Catalogs("adb-7976517966788827.7.azuredatabricks.net", "sql/1.0/warehouses/1042454831133080", [Catalog="bronze", Database=null, EnableAutomaticProxyDiscovery=null]){[Name="bronze",Kind="Database"]}[Data], "select master, codigo_master, codigo_marca,
```


**`bronze.giftback.bonus`**
```sql
// let
//     Fonte = Value.NativeQuery(Databricks.Catalogs("adb-7976517966788827.7.azuredatabricks.net", "/sql/1.0/warehouses/1042454831133080", [Catalog="silver", Database=null, EnableAutomaticProxyDiscovery=null]){[Name="silver",Kind="Database"]}[Data], "SELECT b.codigo_marca, b.codigo_loja, b.ti
```


**`bronze.giftback.total_sales`**
```sql
// let
//     Fonte = Value.NativeQuery(Databricks.Catalogs("adb-7976517966788827.7.azuredatabricks.net", "/sql/1.0/warehouses/1042454831133080", [Catalog="silver", Database=null, EnableAutomaticProxyDiscovery=null]){[Name="silver",Kind="Database"]}[Data], "SELECT
  m.codigo_loja,
  m.codigo
```


**`bronze.giftback.orders`**
```sql
// let
//     Fonte = Value.NativeQuery(Databricks.Catalogs("adb-7976517966788827.7.azuredatabricks.net", "/sql/1.0/warehouses/1042454831133080", [Catalog="silver", Database=null, EnableAutomaticProxyDiscovery=null]){[Name="silver",Kind="Database"]}[Data], "SELECT
m.codigo_master, o.id as used_o
```


**`gold.giftback.mv_bonus_perdido_master`**
```sql
let
 
Query =
    "
    WITH base AS ( 
    SELECT 
        dim_codigo_loja, 
        dim_tipo, 
        dim_data_validade, 
        dim_tipodetalhe, 
        dim_codigo_master,
        measure(var_valor_bonus_perdido) as valor_bonus, 
        measure(var_qtde_bonus_perdido) as qtd_bonus 
    FROM g
```


**`gold.giftback.mv_bonus_gerado_master`**
```sql
let
Query =
    "
    SELECT
        --dim_codigo_master,
        dim_codigo_loja,
        dim_tipo,
        dim_tipodetalhe,
        dim_data_referencia,
        measure(var_valor_bonus_gerado),
        measure(var_qtde_bonus_gerado)
    FROM 
        gold.giftback.mv_bonus_gerado_master m
    WHER
```


**`gold.giftback.mv_bonus_resgate_master`**
```sql
let
Query =
    "
    WITH base AS ( 
    SELECT 
        dim_codigo_loja, 
        dim_tipo, 
        dim_data_resgate, 
        dim_tipodetalhe, 
        dim_codigo_master,
        measure(var_valor_resgate) as var_valor_resgate,
        measure(var_qtd_bonus_resgatado) as var_qtd_bonus_resgatado,
```


**`bronze.giftback.customer_users`**
```sql
let
 
Query =
    "
SELECT
  b.data_referencia,
  b.tipo,
  b.loja as loja_geradora,
  b.codigo_loja as codigo_loja,
  u.user_id,
  --u.name,
  --u.celular,
  --u.cpf,
  count(distinct b.bonus_id) as qntd_gerados,
  sum(b.valor_bonus) as valor_bonus_gerados,
  count(distinct b.bonus_id)FILTER (WHERE
```


**`gold.giftback.mv_engajamento_master`**
```sql
let
 
Query =
    "
    SELECT 
        dim_data_referencia,
        --dim_codigo_master,
        dim_master,
        dim_codigo_loja,
        dim_loja,
        MEASURE(var_total_bonus),
        MEASURE(var_total_vendas),
        MEASURE(var_engajamento)
    FROM 
        gold.giftback.mv_engajament
```


##### Análise 2 — Medidas DAX (59 measures)

| Medida | Expressão DAX |
|---|---|
| `Engajamento` | `CALCULATE(     DIVIDE(         SUM(fgerados[bonus_gerados]),         SUM(fvendas[qntd_vendas]),         0     ),     dTi` |
| `Retorno` | `DIVIDE([Bônus Resgatados],[Bônus Gerados],0)` |
| `Desconto no Resgate` | `VAR Resgate = SUM(fatv_resgatado[valor_resgate]) VAR Incremental = SUM(fatv_resgatado[valor_incremental]) RETURN DIVIDE(` |
| `Multiplicador` | `DIVIDE(1,[Desconto no Resgate])` |
| `Desconto Efetivo` | `VAR Bonus = [PV bonus resgatado]  VAR ValorVenda =     SUMX(         DISTINCT(             SELECTCOLUMNS(               ` |
| `Desconto Absoluto` | `VAR ValorResgate =     SUMX(         DISTINCT(             SELECTCOLUMNS(                 'fResgatados correto',        ` |
| `Bônus Gerados` | `sum(fatv_gerado[bonus_gerados])` |
| `Bônus Resgatados` | `Sum(fatv_resgatado[bonus_resgatados])` |
| `Receita Incremental` | `sum(fatv_resgatado[valor_incremental])` |
| `P resgatado` | `COUNTROWS(DISTINCT(SELECTCOLUMNS('fResgatados correto',[used_orders_id],[bonus_id],[total])))` |
| `P Gerado` | `sum(fgerados[bonus_gerados])` |
| `P Taxa Retorno` | `DIVIDE([P resgatado],[P Gerado])` |
| `P incremental` | `SUMX(DISTINCT(SELECTCOLUMNS('fResgatados correto',[used_orders_id],[total])),'fResgatados correto'[total])` |
| `P Desconto no Resgate` | `VAR Bonus = [PV bonus resgatado]  VAR Total =     SUMX(         DISTINCT(             SELECTCOLUMNS(                 'fR` |
| `P Multiplicador` | `DIVIDE(1,[P Desconto no Resgate])` |
| `PV Gerado` | `sum(fgerados[valor_bonus_gerados])` |
| `PV bonus resgatado` | `SUMX(DISTINCT(SELECTCOLUMNS('fResgatados correto',[used_orders_id],[bonus_id],[valor_resgate])),'fResgatados correto'[va` |
| `Retorno Geral` | `CALCULATE(     [Retorno],  // Use a sua medida de retorno existente     ALL(fatv_gerado)  // Substitua 'TabelaBônus' pel` |
| `Farol Conversão` | `IF([Retorno]>[Retorno Geral],"✔️","🔻")` |
| `Valor Total` | `SUM(fperdidos[bonus_total])` |
| `Valor Usados` | `SUM(fperdidos[bonus_usados_total])` |
| `% Retorno` | `DIVIDE([Valor Usados],[Valor Total])` |
| `% Gerados` | `DIVIDE(     SUM('fFaixa de Bônus'[gerados]),      CALCULATE(         SUM('fFaixa de Bônus'[gerados]),         REMOVEFILT` |
| `% Receita` | `DIVIDE(     SUM('fFaixa de Bônus'[total_incremental]),      CALCULATE(         SUM('fFaixa de Bônus'[total_incremental])` |
| `Ticket Médio por Faixa` | `DIVIDE([Receita Incremental (Faixa)],[Bônus Resgatados (Faixa)])` |
| `Receita Incremental (Faixa)` | `SUMX(DISTINCT(SELECTCOLUMNS('ffaixa_resgatados',[used_orders_id],[total])),'ffaixa_resgatados'[total])` |
| `Bônus Resgatados (Faixa)` | `DISTINCTCOUNT(ffaixa_resgatados[bonus_id])` |
| `Retorno (Faixa)` | `DIVIDE([Bônus Resgatados (Faixa)],[Bônus Gerados (Faixa)],0)` |
| `Bônus Gerados (Faixa)` | `sum(ffaixa_gerados[bonus_gerados])` |
| `% Retorno Demográfico` | `DIVIDE(sum(fdados_demograficos[count_bonus_redeemed]), sum(fdados_demograficos[count_bonus_generated]))` |
| ... | *+29 medidas adicionais* |

##### Análise de Governança — GB - cliente_natura

| Tabela Bronze/Não-Governada | Governança | Uso no Dashboard | Ação Recomendada |
|---|---|---|---|
| `bronze.giftback.bonus` | 🔴 Vermelho | `fperdidos` | Usar `silver.giftback.bonus` ou criar MV gold |
| `bronze.giftback.customer_configs` | 🔴 Vermelho | `dLojas` | Incorporar em `gold.giftback.dim_customers` ou MV |
| `bronze.giftback.customer_users` | 🔴 Vermelho | `ftop_clientes` | Criar `gold.giftback.dim_customer_users` |
| `bronze.giftback.customers` | 🔴 Vermelho | `dLojas` | Criar `gold.giftback.dim_customers` |
| `bronze.giftback.orders` | 🔴 Vermelho | `fResgatados correto` | Criar `gold.giftback.orders` ou MV equivalente |
| `bronze.giftback.total_sales` | 🔴 Vermelho | `fvendas` | Usar `silver.giftback.vendas_totais` ou criar MV |

#### 📊 GB - cliente_vivara

---

##### Análise 1 — Fontes (tabelas) utilizadas

| Tabela PBI | Fonte (Databricks) | Tipo | Governança |
|---|---|---|---|
| `dLojas` | `silver.giftback.masters` | Tabela silver | 🟡 Amarelo |
| `dLojas` | `bronze.giftback.customers` | Tabela bronze | 🔴 Vermelho |
| `dLojas` | `silver.giftback.bonus` | Tabela silver | 🟡 Amarelo |
| `fperdidos` | `bronze.giftback.bonus` | Tabela bronze | 🔴 Vermelho |
| `fmgm_incremental_amgm` | `bronze.giftback.orders` | Tabela bronze | 🔴 Vermelho |
| `fmgm_incremental_amgm` | `bronze.giftback.sms_mgms` | Tabela bronze | 🔴 Vermelho |
| `fbonusativos` | `gold.estudos_giftback.usuarios_cel_tratado` | Tabela gold | ✅ Verde |
| `dqualidade_master` | `silver.giftback.usuarios_qualidade` | Tabela silver | 🟡 Amarelo |

**Scorecard: ✅ 1 · 🟡 3 · 🔴 4 → 🔴 Crítico**


##### SQL por fonte


**`silver.giftback.masters`**
```sql
let
    Query = 
    "
    WITH Master AS (
        SELECT DISTINCT
            codigo_loja
        FROM
            silver.giftback.masters
        WHERE 
            codigo_master IN (406,20803,20807,573)
    )
    SELECT 
        c.id AS codigo_loja,
        --c.company AS loja,
        b.marca,
```


**`bronze.giftback.customers`**
```sql
let
    Query = 
    "
    WITH Master AS (
        SELECT DISTINCT
            codigo_loja
        FROM
            silver.giftback.masters
        WHERE 
            codigo_master IN (406,20803,20807,573)
    )
    SELECT 
        c.id AS codigo_loja,
        --c.company AS loja,
        b.marca,
```


**`silver.giftback.bonus`**
```sql
let
    Query = 
    "
    WITH Master AS (
        SELECT DISTINCT
            codigo_loja
        FROM
            silver.giftback.masters
        WHERE 
            codigo_master IN (406,20803,20807,573)
    )
    SELECT 
        c.id AS codigo_loja,
        --c.company AS loja,
        b.marca,
```


**`bronze.giftback.bonus`**
```sql
let
    Query = 
    "SELECT
    b.codigo_loja,
    b.tipo,
    b.tipo_detalhe,
    DATE_ADD(DATE(b.validade_fim - INTERVAL 3 HOURS), 1) AS data_validade,
    SUM(b.valor_bonus) FILTER (WHERE b.flag_resgate = FALSE AND br.used = 0) AS valor_bonus_gerados,
    COUNT(DISTINCT b.bonus_id) FILTER (WHERE
```


**`bronze.giftback.orders`**
```sql
let
    Query = 
    "
    with tipo as (
        SELECT distinct
        --cs.brand_id,
        --br.date_used
        try_cast(br.used_orders_id AS BIGINT) as used_orders_id,
            upper(trim(origin)) as tipo_detalhe,
            case
            --when origin is null
```


**`bronze.giftback.sms_mgms`**
```sql
let
    Query = 
    "
    with tipo as (
        SELECT distinct
        --cs.brand_id,
        --br.date_used
        try_cast(br.used_orders_id AS BIGINT) as used_orders_id,
            upper(trim(origin)) as tipo_detalhe,
            case
            --when origin is null
```


**`gold.estudos_giftback.usuarios_cel_tratado`**
```sql
let
    Query = 
    "
    WITH base AS (
    SELECT
        codigo_usuario,
        bonus_id,
        marca,
        codigo_loja,
        situacao_bonus,
        tipo
    FROM silver.giftback.bonus
    WHERE codigo_marca = 71
      AND tipo = 'FIDELIDADE'
      AND situacao_bonus = 'ATIVO'
),

base
```


**`silver.giftback.usuarios_qualidade`**
```sql
let
    Query = 
    "
   select
  m.codigo_master,
  count(cpf_valido)
    filter ( where cpf_valido = true )
  / count(*) as correto_cpf,
  count(email_valido)
    filter ( where email_valido = true )
  / count(*) as correto_email,
  count(celular_valido)
    filter ( where celular_valido = true )
```


##### Análise 2 — Medidas DAX (51 measures)

| Medida | Expressão DAX |
|---|---|
| `Engajamento` | `CALCULATE(     DIVIDE(         SUM(fgerados[bonus_gerados]),         SUM(fvendas[qtd_vendas]),         0     ),     dTip` |
| `Retorno` | `DIVIDE([Bônus Resgatados],[Bônus Gerados],0)` |
| `Desconto no Resgate` | `VAR Resgate = SUM(fatv_resgatado[valor_resgate]) VAR Incremental = SUM(fatv_resgatado[valor_incremental]) RETURN DIVIDE(` |
| `Multiplicador` | `DIVIDE(1,[Desconto no Resgate])` |
| `Desconto Efetivo` | `VAR Vendas =     SUM('fResgatados Correto'[valor_venda]) VAR Bonus = [PV bonus resgatado] VAR Total =     SUMX(         ` |
| `Desconto Absoluto` | `VAR Resgate = SUM('fResgatados Correto'[valor_resgate]) VAR Faturamento = SUM(fvendas[faturamento]) RETURN DIVIDE(Resgat` |
| `Bônus Gerados` | `sum(fatv_gerado[bonus_gerados])` |
| `Bônus Resgatados` | `sum('fResgatados Correto'[Quantidade])` |
| `Vendas Totais` | `sum(fvendas[faturamento])` |
| `Bônus Perdidos` | `sum(fperdidos[bonus_vencidos_total])` |
| `Incremental Geral` | `[Incremental dos Indicadores] + [Incremental dos Indicados]` |
| `Quantidade Bônus Utilizado pelos indicado` | `CALCULATE(     sum(         fmgm_indicadores_resgate[qtde_bonus_resgatado_amgm]     ),     ALLEXCEPT(         fmgm_indic` |
| `Quantidade Bônus Utilizado pelos Indicadores` | `sum(fmgm_indicadores_resgate[qtde_bonus_resgatado])` |
| `Quantidade de Bônus Enviados` | `SUM(fmgm_indicadores[qtde_bonus_enviado])` |
| `Quantidade de usuários que indicaram` | `CALCULATE(     sum(fmgm_indicadores[qtde_clientes_q_indicaram]),     ALLEXCEPT(fmgm_indicadores,dCalendario[Data]),     ` |
| `Geral Bônus Utilizados` | `[Quantidade Bônus Utilizado pelos indicado] + [Quantidade Bônus Utilizado pelos Indicadores]` |
| `Taxa Indicação` | `DIVIDE([Quantidade de usuários que indicaram],[Quantidade de Bônus Enviados])` |
| `Taxa Utilização de Bônus` | `DIVIDE([Geral Bônus Utilizados],[Quantidade de usuários que indicaram]+[Quantidade de Bônus Enviados])` |
| `Incremental dos Indicadores` | `SUMX(     CALCULATETABLE(         SUMMARIZE(             'fResgatados Correto',             'fResgatados Correto'[used_o` |
| `Incremental dos Indicados` | `CALCULATE(     SUMX(         SUMMARIZE(             'fResgatados Correto',             'fResgatados Correto'[used_orders` |
| `tm indicadores` | `AVERAGE(fmgm_incremental[tm])  --AVERAGE(mgm_indicadores_resgate[tm])` |
| `tm indicados` | `AVERAGE(fmgm_incremental_amgm[tm]) --AVERAGE(mgm_indicados[tm])` |
| `P resgatado` | `SUM('fResgatados Correto'[Quantidade])` |
| `P Gerado` | `sum(fgerados[bonus_gerados])` |
| `P Incremental` | `SUMX(DISTINCT(SELECTCOLUMNS('fResgatados Correto',[used_orders_id],[total])),'fResgatados correto'[total])` |
| `P Desconto no Resgate` | `VAR Bonus = [PV bonus resgatado] VAR Total =     SUMX(         SUMMARIZE(             'fResgatados Correto',            ` |
| `P Multiplicador` | `DIVIDE(1,[P Desconto no Resgate])` |
| `PV Gerado` | `sum(fgerados[valor_bonus_gerados])` |
| `PV bonus resgatado` | `sum('fResgatados Correto'[valor_resgate])` |
| `PV perdido` | `sum(fperdidos[valor_bonus_gerados])` |
| ... | *+21 medidas adicionais* |

##### Análise de Governança — GB - cliente_vivara

| Tabela Bronze/Não-Governada | Governança | Uso no Dashboard | Ação Recomendada |
|---|---|---|---|
| `bronze.giftback.bonus` | 🔴 Vermelho | `fperdidos` | Usar `silver.giftback.bonus` ou criar MV gold |
| `bronze.giftback.customers` | 🔴 Vermelho | `dLojas` | Criar `gold.giftback.dim_customers` |
| `bronze.giftback.orders` | 🔴 Vermelho | `fmgm_incremental_amgm` | Criar `gold.giftback.orders` ou MV equivalente |
| `bronze.giftback.sms_mgms` | 🔴 Vermelho | `fmgm_incremental_amgm` | Criar tabela gold/silver para dados de SMS |

#### 📊 GB - cs_giftback

---

##### Análise 1 — Fontes (tabelas) utilizadas

| Tabela PBI | Fonte (Databricks) | Tipo | Governança |
|---|---|---|---|
| `dMasters` | `bronze.giftback.customer_masters` | Tabela bronze | 🔴 Vermelho |
| `dMasters` | `bronze.giftback.masters` | Tabela bronze | 🔴 Vermelho |
| `dMasters` | `silver.giftback.bonus` | Tabela silver | 🟡 Amarelo |
| `fvendas` | `silver.giftback.vendas_totais` | Tabela silver | 🟡 Amarelo |
| `f_mv_gerados` | `gold.giftback.mv_bonus_gerado_marca` | Metric View | ✅ Verde |
| `f_mv_vendas` | `gold.giftback.mv_bonus_vendatotal_marca` | Metric View | ✅ Verde |
| `f_mv_perdidos` | `gold.giftback.mv_bonus_perdido_marca` | Metric View | ✅ Verde |
| `f_mv_resgatados` | `gold.giftback.mv_bonus_resgate_marca` | Metric View | ✅ Verde |
| `fvendedor` | `bronze.giftback.total_sales` | Tabela bronze | 🔴 Vermelho |

**Scorecard: ✅ 4 · 🟡 2 · 🔴 3 → 🟠 Moderado**


##### SQL por fonte


**`bronze.giftback.customer_masters`**
```sql
let 
    Query = 
    "SELECT DISTINCT
        c.customer_id
        ,m.name
        --,b.loja as `dLojas.loja`
        --,CASE
        -- loja like '%' || marca || '%'           --> Se a coluna loja contém mesmo texto que a marca
        -- Exemplo: Loja= 'VLG MENS WEAR' e Marca= 'OK!' Resultado FA
```


**`bronze.giftback.masters`**
```sql
let 
    Query = 
    "SELECT DISTINCT
        c.customer_id
        ,m.name
        --,b.loja as `dLojas.loja`
        --,CASE
        -- loja like '%' || marca || '%'           --> Se a coluna loja contém mesmo texto que a marca
        -- Exemplo: Loja= 'VLG MENS WEAR' e Marca= 'OK!' Resultado FA
```


**`silver.giftback.bonus`**
```sql
let 
    Query = 
    "SELECT DISTINCT
        c.customer_id
        ,m.name
        --,b.loja as `dLojas.loja`
        --,CASE
        -- loja like '%' || marca || '%'           --> Se a coluna loja contém mesmo texto que a marca
        -- Exemplo: Loja= 'VLG MENS WEAR' e Marca= 'OK!' Resultado FA
```


**`silver.giftback.vendas_totais`**
```sql
let
    Query = 
    " 
       WITH Filtro AS(
        SELECT DISTINCT
        b.codigo_loja
    FROM
        silver.giftback.bonus as b
        join bronze.giftback.customer_masters as c on b.codigo_loja = c.customer_id
    WHERE
        b.loja not like '% AÇÃO%'
        and b.loja not like '%INFLU
```


**`gold.giftback.mv_bonus_gerado_marca`**
```sql
let

    Query = 
    "    
    select 
        dim_codigo_loja,
        dim_data_referencia,
        dim_tipo,
        --dim_tipodetalhe,
        MEASURE(var_valor_bonus_gerado),
        --MEASURE(var_valor_venda),
        MEASURE(var_qtde_bonus_gerado)--,
        --MEASURE(var_qtde_users),
```


**`gold.giftback.mv_bonus_vendatotal_marca`**
```sql
let

    Query = 
    "    
    select 
        dim_codigo_loja,
        dim_data_referencia,
        MEASURE(var_qtde_vendas),
        MEASURE(var_faturamento)
    from 
        gold.giftback.mv_bonus_vendatotal_marca
    where
        dim_data_referencia >= CURRENT_DATE() - interval '25 MONTH'
```


**`gold.giftback.mv_bonus_perdido_marca`**
```sql
let

    Query = 
    "    
    select 
        dim_codigo_loja,
        dim_data_validade,
        dim_tipo,
        --dim_tipodetalhe,
        MEASURE(var_valor_bonus_perdido),
        MEASURE(var_qtde_bonus_perdido),
        MEASURE(var_qtde_bonus_usado),
        MEASURE(var_qtde_bonus_total)--,
```


**`gold.giftback.mv_bonus_resgate_marca`**
```sql
let

    Query = 
    "    
    select 
        dim_codigo_loja,
        dim_data_resgate,
        dim_tipo,
        --dim_tipodetalhe,
        MEASURE(var_valor_resgate),
        MEASURE(var_receita_incremental_total),
        MEASURE(var_receita_incremental_tipo),
        --MEASURE(var_receita_inc
```


**`bronze.giftback.total_sales`**
```sql
let

    Query = 
    "    
    SELECT
    customer_id AS codigo_loja,
    nome_vendedor AS Vendedor,
    DATE(created) AS data,

    --COUNT(DISTINCT nr_pedido) AS qtd_vendas,
    COUNT(DISTINCT id) AS qtd_vendas,

    COUNT(DISTINCT CASE 
        WHEN bonus_id IS NOT NULL AND bonus_id <> '0'
```


##### Análise 2 — Medidas DAX (108 measures)

| Medida | Expressão DAX |
|---|---|
| `Engajamento` | `DIVIDE(     CALCULATE(         sum(fgerados2[geral]),         fgerados2[tipo]="FIDELIDADE"     ),     sum(fvendas[qtd_ve` |
| `% Retorno` | `DIVIDE([Valor Usados],[Valor Total])` |
| `P Desconto no Resgate` | `VAR vBonus = SUM(fresgatado[valor_bonus_resgatados]) VAR vTotal = [P Incremental] RETURN  DIVIDE(     vBonus,     vTotal` |
| `Multiplicador` | `DIVIDE(1,[P Desconto no Resgate])` |
| `Desconto Efetivo` | `VAR vBonus = SUM(fresgatado[valor_bonus_resgatados]) VAR vCompra = SUM(fresgatado[valor_primeira_compra]) VAR vTotal = [` |
| `Desconto Absoluto` | `DIVIDE(     sum(fresgatado[valor_bonus_resgatados]),     sum(fvendas[faturamento]) )` |
| `Engajamento Travado` | `MIN(1,[Engajamento])` |
| `Bônus Gerados` | `sum(fgerados[bonus_gerados])` |
| `P resgatado` | `SUM(fresgatado[bonus_resgatados])` |
| `P Incremental` | `SUMX(     SUMMARIZE(         'fResgatados Correto',         'fResgatados Correto'[total],         'fResgatados Correto'[` |
| `Valor Bônus Gerados` | `sum(fgerados[valor_bonus])` |
| `Valor Bônus Resgatados` | `sum(fresgatado[valor_bonus_resgatados])` |
| `Qntd. Vendas` | `sum(fvendas[qtd_vendas])` |
| `Faturamento` | `sum(fvendas[faturamento])` |
| `Masters Name` | `MIN(dMasters[name])` |
| `Valor Usados` | `CALCULATE(     SUM(fperdidos[bonus_usados_total]),     fperdidos[bonus_usados_total] > 0 )` |
| `Valor Total` | `sum(fperdidos[bonus_total])` |
| `Representa` | `DIVIDE([P Incremental],[Faturamento])` |
| `Engajamento Real` | `DIVIDE(     CALCULATE(         sum(fgerados2[bonus_gerados_sem_pin]),         fgerados2[tipo]="FIDELIDADE"     ),     su` |
| `Loja Ativa` | `VAR Bonus = [Bônus Gerados] RETURN SWITCH(     TRUE(),     Bonus > 10,"Ativo",     Bonus > 0 \|\| [Faturamento] > 0 \|\|` |
| `Tkt Médio Gerados` | `DIVIDE([Valor Bônus Gerados],[Bônus Gerados])` |
| `Tkt Médio Resgatados` | `DIVIDE([Valor Bônus Resgatados],[P resgatado])` |
| `Valor Bônus Perdido` | `SUM(fperdidos[valor_bonus_gerados])` |
| `Qntd Perdidos` | `CALCULATE(     SUM(fperdidos[bonus_vencidos_total]),     fperdidos[bonus_vencidos_total] > 0 )` |
| `Tkt Médio Perdidos` | `DIVIDE([Valor Bônus Perdido], [Qntd Perdidos])` |
| `P Incremental LY` | `CALCULATE(     [P Incremental],     SAMEPERIODLASTYEAR('dCalendario'[Data]) )` |
| `P Incremental LM` | `CALCULATE(     [P Incremental],     DATEADD('dCalendario'[Data], -1, MONTH) )` |
| `P Incremental MoM` | `VAR LM = [P Incremental LM] RETURN DIVIDE([P Incremental] - LM, LM)` |
| `P Incremental YoY` | `VAR LY = [P Incremental LY] RETURN DIVIDE([P Incremental] - LY, LY)` |
| `P Resgatado LY` | `CALCULATE(     [P resgatado],     SAMEPERIODLASTYEAR('dCalendario'[Data]) )` |
| ... | *+78 medidas adicionais* |

##### Análise de Governança — GB - cs_giftback

| Tabela Bronze/Não-Governada | Governança | Uso no Dashboard | Ação Recomendada |
|---|---|---|---|
| `bronze.giftback.customer_masters` | 🔴 Vermelho | `dMasters` | Usar `silver.giftback.masters` ou promover para gold |
| `bronze.giftback.masters` | 🔴 Vermelho | `dMasters` | Usar `silver.giftback.masters` ou promover para gold |
| `bronze.giftback.total_sales` | 🔴 Vermelho | `fvendedor` | Usar `silver.giftback.vendas_totais` ou criar MV |

#### 📊 GB - cs_acoes

---

##### Análise 1 — Fontes (tabelas) utilizadas

| Tabela PBI | Fonte (Databricks) | Tipo | Governança |
|---|---|---|---|
| `BonusGerados` | `silver.giftback.bonus` | Tabela silver | 🟡 Amarelo |
| `BonusResgatados` | `bronze.giftback.bonus` | Tabela bronze | 🔴 Vermelho |
| `BonusResgatados` | `bronze.giftback.orders` | Tabela bronze | 🔴 Vermelho |
| `dLojas` | `silver.giftback.masters` | Tabela silver | 🟡 Amarelo |

**Scorecard: ✅ 0 · 🟡 2 · 🔴 2 → 🔴 Crítico**


##### SQL por fonte


**`silver.giftback.bonus`**
```sql
let
    Query = "
    SELECT 
    COUNT(DISTINCT bonus_id) AS bonus_gerados,
    SUM(valor_bonus) AS total_valor_bonus,
    loja,
    codigo_loja,
    marca,
    codigo_marca,
    data_referencia,
    tipo_detalhe,
    CASE 
        WHEN valor_bonus <= 20 THEN
```


**`bronze.giftback.bonus`**
```sql
let
    Query ="
    SELECT
    o.id AS used_orders_id,
    bo.bonus_id,
    bo.tipo,
    bo.tipo_detalhe,
    bo.data_resgate,
    bo.codigo_loja_resgate,
    bo.loja_resgate,
    bo.marca,
    bo.codigo_loja AS codigo_loja_geradora,
    o.total,
    bo.v
```


**`bronze.giftback.orders`**
```sql
let
    Query ="
    SELECT
    o.id AS used_orders_id,
    bo.bonus_id,
    bo.tipo,
    bo.tipo_detalhe,
    bo.data_resgate,
    bo.codigo_loja_resgate,
    bo.loja_resgate,
    bo.marca,
    bo.codigo_loja AS codigo_loja_geradora,
    o.total,
    bo.v
```


**`silver.giftback.masters`**
```sql
let
    Query = "select b.marca,
b.tipo_detalhe,
--MIN(b.data_referencia),
b.codigo_marca,
b.codigo_loja,
m.master,
m.codigo_master
from silver.giftback.bonus b 
join silver.giftback.masters m ON b.codigo_loja = m.codigo_loja
--where b.tipo in ('REATIVAÇÃO','ATIVA
```


##### Análise 2 — Medidas DAX (14 measures)

| Medida | Expressão DAX |
|---|---|
| `Retorno` | `DIVIDE([Bônus Resgatados],[Bônus Gerados],0)` |
| `P Desconto no Resgate` | `VAR BonusFiltrado =      CALCULATETABLE(         BonusResgatados,         TREATAS(VALUES(Bridge_MasterTipo[master]), dLo` |
| `Multiplicador` | `DIVIDE(1,[P Desconto no Resgate])` |
| `Bônus Gerados` | `CALCULATE(     SUM(BonusGerados[bonus_gerados]),     TREATAS(VALUES(Bridge_MasterTipo[master]), dLojas[master]) )` |
| `Bônus Resgatados` | `CALCULATE(     DISTINCTCOUNT(BonusResgatados[bonus_id]),     TREATAS(VALUES(Bridge_MasterTipo[master]), dLojas[master]) ` |
| `Receita Incremental` | `VAR BonusFiltrado =      CALCULATETABLE(         BonusResgatados,         TREATAS(VALUES(Bridge_MasterTipo[master]), dLo` |
| `Conversão por Faixa` | `SUM('Faixa de Bônus'[resgatados])/SUM('Faixa de Bônus'[gerados])` |
| `Desconto por Faixa` | `sum('Faixa de Bônus'[total_resgatado])/(SUM('Faixa de Bônus'[total_resgatado])+SUM('Faixa de Bônus'[total_incremental]))` |
| `Ticket Médio por Faixa` | `sum('Faixa de Bônus'[total_incremental])/SUM('Faixa de Bônus'[resgatados])` |
| `Receita por Faixa` | `sum('Faixa de Bônus'[total_incremental])` |
| `Valor Bônus Resgatado` | `sum('Faixa de Bônus'[total_resgatado])` |
| `Valor Bônus Gerado` | `sum('Faixa de Bônus'[total_gerado])` |
| `Medida` | `` |
| `Tipos_Detalhe` | `LOOKUPVALUE (      dTipo[tipo_detalhe], )` |

##### Análise de Governança — GB - cs_acoes

| Tabela Bronze/Não-Governada | Governança | Uso no Dashboard | Ação Recomendada |
|---|---|---|---|
| `bronze.giftback.bonus` | 🔴 Vermelho | `BonusResgatados` | Usar `silver.giftback.bonus` ou criar MV gold |
| `bronze.giftback.orders` | 🔴 Vermelho | `BonusResgatados` | Criar `gold.giftback.orders` ou MV equivalente |


### 🎫 ValeBonus

#### 📊 VB - Ifood Embedded

---

##### Análise 1 — Fontes (tabelas) utilizadas

| Tabela PBI | Fonte (Databricks) | Tipo | Governança |
|---|---|---|---|
| `fFunil_Resgate` | `bronze.valebonus.redemptions` | Tabela bronze | 🔴 Vermelho |
| `fFunil_Resgate` | `bronze.valebonus.wallet_balances` | Tabela bronze | 🔴 Vermelho |
| `fFunil_Resgate` | `silver.bsat.user` | Tabela silver | 🟡 Amarelo |
| `fFunil_Resgate` | `silver.giftback.bonus` | Tabela silver | 🟡 Amarelo |
| `fFunil_Resgate` | `silver.giftback.usuarios` | Tabela silver | 🟡 Amarelo |
| `DeliverTime` | `gold.ads.b2b_message` | Tabela gold | ✅ Verde |
| `fFunil_Cadastro` | `bronze.valebonus.users` | Tabela bronze | 🔴 Vermelho |
| `fFunil_Cadastro` | `gold.vuc.people` | Tabela gold | ✅ Verde |
| `fFunil_Emissao` | `bronze.valebonus.wallet_balances` | Tabela bronze | 🔴 Vermelho |
| `fVendas_Totais` | `silver.giftback.vendas_totais` | Tabela silver | 🟡 Amarelo |

**Scorecard: ✅ 2 · 🟡 4 · 🔴 4 → 🔴 Crítico**


##### SQL por fonte


**`fFunil_Resgate`**
```sql
let
    Query = "
SELECT 
  DATE(r.created_at) AS created_at,
  r.offer_name,
  b.codigo_marca,
  b.marca AS nome_marca,
  CASE 
    WHEN r.type IN ('ECOMMERCE', 'ECOMMERCE_V2') THEN 'ECOMMERCE'
    WHEN r.type = 'PHYSICAL_STORE' THEN 'LOJA FISICA'
    ELSE r.type 
  END AS canal,

  t.qtde_usuarios_total,

  bsat_user.gender,

  CASE 
    WHEN UPPER(bsat_user.city) IN ('R DE JANEIRO') THEN 'RIO DE JANEIRO'
    WHEN UPPER(bsat_user.city) IN ('CABODESANTOAGOSTINHO') THEN 'CABO DE SANTO AGOSTINHO'
    WHEN UPPER(bsat_user.city) IN ('PINDAMONHAGABA') THEN 'PINDAMONHANGABA'
    WHEN UPPER(bsat_user.city) IN ('BALNEAIRO CAMBORIU') THEN 'BALNEARIO CAMBORIU'
    ELSE UPPER(TRIM(REGEXP_REPLACE(TRANSLATE(bsat_user.city,
      'ÁÀÂÃÄÉÈÊËÍÌÎÏÓÒÔÕÖÚÙÛÛÇ',
      'AAAAAEEEEIIIIOOOOOUUUUC'),
    ' +',' ')))
  END AS city,

  CASE 
    WHEN UPPER(bsat_user.state) IN ('AC','ACRE') THEN 'AC'
    WHEN UPPER(bsat_user.state) IN ('AL','ALAGOAS') THEN 'AL'
    WHEN UPPER(bsat_user.state) IN ('AP') THEN 'AP'
    WHEN UPPER(bsat_user.state) IN ('AM','AMAZONAS') THEN 'AM'
    WHEN UPPER(bsat_user.state) IN ('BA','BAHIA') THEN 'BA'
    WHEN UPPER(bsat_user.state) IN ('CE','CEARÁ','CEARA') THEN 'CE'
    WHEN UPPER(bsat_user.state) IN ('DF','DISTRITO FEDERAL') THEN 'DF'
    WHEN UPPER(bsat_user.state) IN ('ES','ESPÍRITO SANTO','ESPIRITO SANTO') THEN 'ES'
    WHEN UPPER(bsat_user.state) IN ('GO','GOIÁS','GOIAS') THEN 'GO'
    WHEN UPPER(bsat_user.state) IN ('MA','MARANHÃO','MARANHAO') THEN 'MA'
    WHEN UPPER(bsat_user.state) IN ('MT','MATO GROSSO') THEN 'MT'
    WHEN UPPER(bsat_user.state) IN ('MS','MATO GROSSO DO SUL') THEN 'MS'
    WHEN UPPER(bsat_user.state) IN ('MG','MINAS GERAIS') THEN 'MG'
    WHEN UPPER(bsat_user.state) IN ('PA','PARÁ','PARA') THEN 'PA'
    WHEN UPPER(bsat_user.state) IN ('PB','PARAÍBA','PARAIBA') THEN 'PB'
    WHEN UPPER(bsat_user.state) IN ('PR','PARANÁ','PARANA') THEN 'PR'
    WHEN UPPER(bsat_user.state) IN ('PE','PERNAMBUCO') THEN 'PE'
    WHEN UPPER(bsat_user.state
```

**`DeliverTime`**
```sql
let
    Query = 
    "
    select 
        to_timestamp(max(DeliverTime)) AS ultimo_disparo 
    from
        gold.ads.b2b_message 
    where 
        DeliverTime > current_date-3
    ",
    Fonte = Databricks.Query(host, db)(Query),
    #"Tipo Alterado" = Table.TransformColumnTypes(Fonte,{{"ultimo_disparo", type datetime}})
in
    #"Tipo Alterado"
```

**`fFunil_Cadastro`**
```sql
let
Query = "WITH total_usuarios AS (
  SELECT COUNT(DISTINCT id) AS usuarios_total
  FROM bronze.valebonus.users
)

SELECT 
  CASE 
    WHEN gender NOT IN ('M','F') THEN 'OUTROS' 
    ELSE gender 
  END AS genero,
  
  -- status,   -- não utilizado,

  CASE
    WHEN FLOOR(months_between(current_date(), birth_date) / 12) < 18 THEN 'MENOR 18'
    WHEN FLOOR(months_between(current_date(), birth_date) / 12) BETWEEN 18 AND 24 THEN '18-24'
    WHEN FLOOR(months_between(current_date(), birth_date) / 12) BETWEEN 25 AND 34 THEN '25-34'
    WHEN FLOOR(months_between(current_date(), birth_date) / 12) BETWEEN 35 AND 44 THEN '35-44'
    WHEN FLOOR(months_between(current_date(), birth_date) / 12) BETWEEN 45 AND 54 THEN '45-54'
    ELSE '55+'
  END AS faixa_etaria,

  date(u.created_at) Data_Cadastro,

  CASE SUBSTRING(cellphone, 3, 2)
    WHEN '11' THEN 'SP' WHEN '12' THEN 'SP' WHEN '13' THEN 'SP'
    WHEN '14' THEN 'SP' WHEN '15' THEN 'SP' WHEN '16' THEN 'SP'
    WHEN '17' THEN 'SP' WHEN '18' THEN 'SP' WHEN '19' THEN 'SP'
    WHEN '21' THEN 'RJ' WHEN '22' THEN 'RJ' WHEN '24' THEN 'RJ'
    WHEN '27' THEN 'ES' WHEN '28' THEN 'ES'
    WHEN '31' THEN 'MG' WHEN '32' THEN 'MG' WHEN '33' THEN 'MG'
    WHEN '34' THEN 'MG' WHEN '35' THEN 'MG' WHEN '37' THEN 'MG'
    WHEN '38' THEN 'MG'
    WHEN '41' THEN 'PR' WHEN '42' THEN 'PR' WHEN '43' THEN 'PR'
    WHEN '44' THEN 'PR' WHEN '45' THEN 'PR' WHEN '46' THEN 'PR'
    WHEN '47' THEN 'SC' WHEN '48' THEN 'SC' WHEN '49' THEN 'SC'
    WHEN '51' THEN 'RS' WHEN '53' THEN 'RS' WHEN '54' THEN 'RS'
    WHEN '55' THEN 'RS'
    WHEN '61' THEN 'DF'
    WHEN '62' THEN 'GO' WHEN '64' THEN 'GO'
    WHEN '63' THEN 'TO'
    WHEN '65' THEN 'MT' WHEN '66' THEN 'MT'
    WHEN '67' THEN 'MS'
    WHEN '68' THEN 'AC'
    WHEN '69' THEN 'RO'
    WHEN '91' THEN 'PA' WHEN '93' THEN 'PA' WHEN '94' THEN 'PA'
    WHEN '92' THEN 'AM' WHEN '97' THEN 'AM'
    WHEN '95' THEN 'RR'
    WHEN '96' THEN 'AP'
    WHEN '71' THEN 'BA' WHEN '73' THEN 'BA' WHEN '74' THEN 'BA'
    WH
```

**`fFunil_Emissao`**
```sql
let 
    Query = "
    WITH ultima_referencia AS (
        SELECT MAX(reference_text) AS reference_text
        FROM bronze.valebonus.wallet_balances
    )

    SELECT 
        -- DATE(wb.created_at) AS data,

        -- SUM(wb.credited_amount) / 100 AS vlr_Emissao,
        -- SUM(wb.debited_amount) / 100 AS vlr_utilizado,
        -- (SUM(wb.credited_amount) - SUM(wb.debited_amount)) / 100 AS vlr_balance,

        ROUND(
            MEAN(
                CASE 
                    WHEN wb.reference_text = ur.reference_text THEN wb.balance 
                    ELSE NULL 
                END
            ) / 100, 
            2
        ) AS media_credit

    FROM bronze.valebonus.wallet_balances wb
    CROSS JOIN ultima_referencia ur
    GROUP BY DATE(wb.created_at)
    ",
    
    Fonte = Databricks.Query(#"host", #"db")(Query)

in
    Fonte
```

**`fVendas_Totais`**
```sql
let
    Query = "
    SELECT 
      DATE(data_referencia) AS data_referencia,
      codigo_marca,
      marca,
      codigo_loja,
      loja,
      SUM(pedidos)       AS total_pedidos,
      SUM(itens)         AS total_itens,
      SUM(valor_liquido) AS vendas_totais
    FROM silver.giftback.vendas_totais
    GROUP BY ALL
    ",
    Fonte = Databricks.Query(host, db)(Query)
in
    Fonte
```

##### Análise 2 — Medidas DAX (18 medidas)

| Medida | Expressão DAX |
|---|---|
| `% Taxa de Engajamento Total` | `DIVIDE(     CALCULATE(         MAX(fFunil_Resgate[qtde_usuarios_total]),         ALL(fFunil_Resgate)     ),     CALCULATE(         MAX(fFunil_Cadastro[usuarios_total]),         ALL(fFunil_Cadastro)     ) )` |
| `emissao_resgate` | `sum(fFunil_Resgate[resgate])` |
| `Qtd Resgate` | `SUM(fFunil_Resgate[qtde_bonus])` |
| `qtde_cadastro` | `sum(fFunil_Cadastro[usuarios])` |
| `Usuarios Total (fixo)` | `CALCULATE(     MAX(fFunil_Resgate[qtde_usuarios_total]),     ALL(fFunil_Resgate) )` |
| `Qtd Bonus Gerados` | `SUM(fFunil_Resgate[qtde_bonus])` |
| `Qtd Usuarios Resgataram` | `SUM(fFunil_Resgate[usuarios_unicos])` |
| `Valor Total Resgatado` | `SUM(fFunil_Resgate[resgate])` |
| `Funil - Usuarios Cadastrados` | `CALCULATE(     MAX(fFunil_Cadastro[usuarios_total]),     ALL(fFunil_Cadastro) )` |
| `Funil - Bonus Gerados` | `SUM(fFunil_Resgate[qtde_bonus])` |
| `Vendas Totais` | `SUM(fVendas_Totais[vendas_totais])` |
| `Taxa de Conversao Bonus` | `DIVIDE(     SUM(fFunil_Resgate[usuarios_unicos]),     SUM(fFunil_Resgate[qtde_bonus]) )` |
| `Funil - Receita Incremental` | `SUM(fFunil_Resgate[valor_incremental])` |
| `Taxa de Retorno` | `DIVIDE(     SUM(fFunil_Resgate[qtde_bonus_resgatados]),     SUM(fFunil_Resgate[qtde_bonus]) )` |
| `Funil - Bonus Resgatados` | `SUM(fFunil_Resgate[qtde_bonus_resgatados])` |
| `% Conversao Cadastrados p/ Gerados` | `DIVIDE(     SUM(fFunil_Resgate[qtde_bonus]),     CALCULATE(         MAX(fFunil_Cadastro[usuarios_total]),         ALL(fFunil_Cadastro)     ) )` |
| `% Conversao Gerados p/ Resgatados` | `DIVIDE(     SUM(fFunil_Resgate[qtde_bonus_resgatados]),     SUM(fFunil_Resgate[qtde_bonus]) )` |
| `% Representatividade Incremental` | `DIVIDE(     SUM(fFunil_Resgate[valor_incremental]),     SUM(fVendas_Totais[vendas_totais]) )` |

#### 📊 VB - parceiros_interno

---

##### Análise 1 — Fontes (tabelas) utilizadas

| Tabela PBI | Fonte (Databricks) | Tipo | Governança |
|---|---|---|---|
| `dEmissor` | `bronze.valebonus.applications` | Tabela bronze | 🔴 Vermelho |
| `fEmissor` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fEmissor` | `gold.valebonus.emissions_agregado` | Tabela gold | ✅ Verde |
| `fUser` | `bronze.valebonus.consumer` | Tabela bronze | 🔴 Vermelho |
| `fUser` | `bronze.valebonus.device` | Tabela bronze | 🔴 Vermelho |
| `fUser` | `bronze.valebonus.session` | Tabela bronze | 🔴 Vermelho |
| `fUser` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fUser` | `gold.valebonus.usuarios` | Tabela gold | ✅ Verde |
| `fResgate` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fResgate` | `gold.valebonus.transactions_shops` | Tabela gold | ✅ Verde |
| `fResgate_user` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fResgate_user` | `gold.valebonus.transactions_shops` | Tabela gold | ✅ Verde |
| `fResgate_user` | `gold.valebonus.usuarios` | Tabela gold | ✅ Verde |
| `fResgate_unico` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fResgate_unico` | `gold.valebonus.transactions_shops` | Tabela gold | ✅ Verde |
| `fEmissao_total` | `gold.valebonus.pbi_VBO02_Parceiro` | Tabela gold | ✅ Verde |
| `fCohort` | `gold.valebonus.cohort_parceiros` | Tabela gold | ✅ Verde |
| `fEmissor_unico_mes` | `gold.valebonus.emissoes_unicas_mensais` | Tabela gold | ✅ Verde |
| `fResgate_cadastro` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fResgate_cadastro` | `gold.valebonus.transactions_shops` | Tabela gold | ✅ Verde |
| `fResgate_cadastro` | `gold.valebonus.usuarios` | Tabela gold | ✅ Verde |
| `fPrimeira_emissao_mes` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fPrimeira_emissao_semana` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `dOferta` | `gold.valebonus.transactions_shops` | Tabela gold | ✅ Verde |
| `dOferta` | `silver.giftback.bonus` | Tabela silver | 🟡 Amarelo |
| `fResgates_Detalhe` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fResgates_Detalhe` | `gold.valebonus.transactions_shops` | Tabela gold | ✅ Verde |

**Scorecard: ✅ 22 · 🟡 1 · 🔴 4 → 🔴 Crítico**


##### SQL por fonte


**`dEmissor`**
```sql
let
// Emissores imputados a mão por causa dos links das imagens.

    Query =
    "
    with cte as (
      SELECT DISTINCT
        id AS codigo_emissor,
        UPPER(name) AS nome_emissor

        -- grupo_emissor removido

      FROM bronze.valebonus.applications
    )

    select * from cte
    union all
    select 'vivototal'             , '#VIVO TOTAL'
    union all
    select 'crmbonustotal'         , '#CRMBONUS TOTAL'
    union all
    select 'tier_2'                , '#TIER 2'
    union all
    select 'picpaytotal'           , '#PICPAY TOTAL'
    union all
    select 'sabesptotal'           , '#SABESP TOTAL'
    union all
    select 'cartoescarrefourtotal' , '#CARTOES CARREFOUR TOTAL'
    union all
    select 'coritibatotal'         , '#CORITIBA TOTAL'
    union all
    select 'uniasselvitotal'       , '#UNIASSELVI TOTAL'
    union all
    select 'unicesumartotal'       , '#UNICESUMAR TOTAL'
    union all
    select 'vitrutotal'            , '#VITRU TOTAL'
    ",

  Fonte = Databricks.Query(host, db)(Query)
in
  Fonte
```

**`fEmissor`**
```sql
let
    Query =
    "
WITH
  emissor_ref AS (
    SELECT DISTINCT codigo_usuario, UPPER(nome_emissor) as nome_emissor, codigo_emissor
    FROM gold.valebonus.emissions
  ),
  picpay AS (
    SELECT 'picpaytotal' as codigo_emissor, a.data_transacao,
      SUM(a.qtd_emissao) as qtd_emissao,
      SUM(a.qtd_emissao_usuario_unico) as qtd_emissao_usuario_unico,
      SUM(a.valor_emissao) as valor_emissao
    FROM gold.valebonus.emissions_agregado a
    WHERE a.codigo_emissor IN (
      SELECT DISTINCT codigo_emissor FROM emissor_ref
      WHERE nome_emissor LIKE 'PICPAY%'
        AND nome_emissor NOT LIKE '%CARTEIRA%'
        AND nome_emissor NOT LIKE '%PIX%'
    )
    AND a.data_transacao >= '2024-01-01'
    GROUP BY a.data_transacao
  ),
  sabesp AS (
    SELECT 'sabesptotal' as codigo_emissor, a.data_transacao,
      SUM(a.qtd_emissao) as qtd_emissao,
      SUM(a.qtd_emissao_usuario_unico) as qtd_emissao_usuario_unico,
      SUM(a.valor_emissao) as valor_emissao
    FROM gold.valebonus.emissions_agregado a
    WHERE a.codigo_emissor IN (
      SELECT DISTINCT codigo_emissor FROM emissor_ref WHERE nome_emissor LIKE 'SABESP%'
    )
    AND a.data_transacao >= '2024-01-01'
    GROUP BY a.data_transacao
  ),
  carrefour AS (
    SELECT 'cartoescarrefourtotal' as codigo_emissor, a.data_transacao,
      SUM(a.qtd_emissao) as qtd_emissao,
      SUM(a.qtd_emissao_usuario_unico) as qtd_emissao_usuario_unico,
      SUM(a.valor_emissao) as valor_emissao
    FROM gold.valebonus.emissions_agregado a
    WHERE a.codigo_emissor IN (
      SELECT DISTINCT codigo_emissor FROM emissor_ref
      WHERE nome_emissor LIKE 'CART_O ATACAD_O%'
         OR nome_emissor LIKE 'CART_O CARREFOUR%'
         OR nome_emissor LIKE 'CART_O SAM%'
    )
    AND a.data_transacao >= '2024-01-01'
    GROUP BY a.data_transacao
  ),
  coritiba AS (
    SELECT 'coritibatotal' as codigo_emissor, a.data_transacao,
      SUM(a.qtd_emissao) as qtd_emissao,
      SUM(a.qtd_emissao_usuario_unico) as qtd_emissao_usua
```

**`fUser`**
```sql
let
    Query =
    "
        WITH 
            emissor AS (
                SELECT DISTINCT 
                codigo_usuario,
                nome_emissor,
                codigo_emissor
                FROM 
                gold.valebonus.emissions
            ),
            origem AS (
                WITH
                origem as (
                    SELECT
                    MIN(date(s.createdAt)) AS data
                    ,d.consumerId AS usuario
                    ,CASE 
                        WHEN d.type IS NULL THEN 'Sem rastreio'
                        WHEN d.type = 'WEB' THEN 'Web'
                        ELSE 'Aplicativo' END
                        AS origem
                    FROM bronze.valebonus.session s 
                    LEFT JOIN bronze.valebonus.device d ON s.deviceId = d.id
                    WHERE date(s.createdAt) >= '2024-01-01'
                    GROUP BY ALL
                )
                SELECT DISTINCT
                c.id as codigo_usuario,
                MAX(o.origem) as origem
                FROM bronze.valebonus.consumer c
                LEFT JOIN origem o ON c.id = o.usuario AND DATE(c.createdAt) = o.data
                WHERE date(c.createdAt) >= '2024-01-01'
                GROUP BY c.id
            ),
            por_emissor AS (
                SELECT b.codigo_emissor, coalesce(c.origem, 'Sem rastreio') as origem,
                a.data_cadastro, a.genero, a.cidade, a.estado, a.fx_etaria,
                COUNT(DISTINCT a.codigo_usuario) AS qtd_usuarios_total, 
                ABS(MEAN(a.saldo_atual) FILTER (WHERE a.saldo_atual IS NOT NULL OR saldo_atual > 0)) AS media_saldo
                FROM gold.valebonus.usuarios a
                    JOIN emissor b ON a.codigo_usuario = b.codigo_usuario
                    LEFT JOIN origem c ON a.codigo_usuario = c.codigo_usuario
                WHERE a.data_cadastro >= '2024-01-01'
                GROUP BY b.codigo_emissor, coalesce(c.origem, 'Sem rastreio'), a.data_cada
```

**`fResgate`**
```sql
let
    Query =
    "
WITH 
  emissor AS (
    SELECT DISTINCT 
      codigo_usuario,
      nome_emissor,
      codigo_emissor
    FROM 
      gold.valebonus.emissions
  ),
  resgates AS (
    SELECT
      codigo_usuario,
      coalesce(origem, 'Sem rastreio') as origem,
      data_transacao,
      CASE WHEN tipo_resgate IN ('PRODUCT (P4)', 'PRODUCT (P5)') THEN 'PRODUTO' ELSE 'OFERTA' END AS tipo_resgate,
      nome_resgate,
      qtd_resgate,
      valor_resgate,
      valor_faturamento
    FROM gold.valebonus.transactions_shops
  ),
  por_emissor AS (
    SELECT
      b.codigo_emissor,
      a.origem,
      a.data_transacao,
      a.tipo_resgate,
      a.nome_resgate,
      SUM(a.qtd_resgate) as qtd_resgate,
      SUM(a.valor_resgate) as valor_resgate,
      SUM(a.valor_faturamento) as valor_faturamento
    FROM resgates a
      LEFT JOIN emissor b on a.codigo_usuario = b.codigo_usuario
    WHERE a.data_transacao >= '2024-01-01'
    GROUP BY b.codigo_emissor, a.origem, a.data_transacao, a.tipo_resgate, a.nome_resgate
  ),
  crm_total AS (
    SELECT 'crmbonustotal' as codigo_emissor,
      a.origem, a.data_transacao, a.tipo_resgate, a.nome_resgate,
      SUM(a.qtd_resgate), SUM(a.valor_resgate), SUM(a.valor_faturamento)
    FROM resgates a WHERE a.data_transacao >= '2024-01-01'
    GROUP BY a.origem, a.data_transacao, a.tipo_resgate, a.nome_resgate
  ),
  vivo AS (
    SELECT 'vivototal' as codigo_emissor,
      a.origem, a.data_transacao, a.tipo_resgate, a.nome_resgate,
      SUM(a.qtd_resgate), SUM(a.valor_resgate), SUM(a.valor_faturamento)
    FROM resgates a
      JOIN (SELECT DISTINCT codigo_usuario FROM emissor WHERE lower(nome_emissor) LIKE 'vivo%') b ON a.codigo_usuario = b.codigo_usuario
    WHERE a.data_transacao >= '2023-12-07'
    GROUP BY a.origem, a.data_transacao, a.tipo_resgate, a.nome_resgate
  ),
  tier_2 AS (
    WITH tier_2_users AS (
      SELECT DISTINCT codigo_usuario FROM emissor
      WHERE UPPER(nome_emissor) NOT LIKE '%VIVO%' AND UPPER(n
```

**`fResgate_user`**
```sql
let
    Query =
    "
WITH 
  emissor AS (
    SELECT DISTINCT 
      codigo_usuario,
      nome_emissor,
      codigo_emissor
    FROM 
      gold.valebonus.emissions
  ),
  resgates AS (
    SELECT
      coalesce(a.origem, 'Sem rastreio') as origem,
      a.codigo_usuario,
      a.data_transacao,
      b.genero,
      b.fx_etaria,
      b.cidade,
      b.estado
    FROM gold.valebonus.transactions_shops a 
      LEFT JOIN gold.valebonus.usuarios b ON a.codigo_usuario = b.codigo_usuario
  ),
  por_emissor AS (
    SELECT
      b.codigo_emissor,
      coalesce(a.origem, '#GERAL') as origem,
      a.genero,
      a.fx_etaria,
      a.cidade,
      a.estado,
      COUNT( DISTINCT a.codigo_usuario) as qtd_usuarios_resgate
    FROM resgates a
      LEFT JOIN emissor b on a.codigo_usuario = b.codigo_usuario
    WHERE 
      a.data_transacao >= '2024-01-01'
    GROUP BY GROUPING SETS (
      ( b.codigo_emissor, a.origem, a.genero, a.fx_etaria, a.cidade, a.estado ),
      ( b.codigo_emissor, a.genero, a.fx_etaria, a.cidade, a.estado )
    )
  ),
  crm_total AS (
    SELECT
      'crmbonustotal' as codigo_emissor,
      coalesce(a.origem, '#GERAL') as origem,
      a.genero,
      a.fx_etaria,
      a.cidade,
      a.estado,
      COUNT( DISTINCT a.codigo_usuario) as qtd_usuarios_resgate
    FROM resgates a
    WHERE 
      a.data_transacao >= '2024-01-01'
    GROUP BY GROUPING SETS (
      ( a.origem, a.genero, a.fx_etaria, a.cidade, a.estado ),
      ( a.genero, a.fx_etaria, a.cidade, a.estado )
    )
  ),
  vivo AS (
    SELECT
      'vivototal' as codigo_emissor,
      coalesce(a.origem, '#GERAL') as origem,
      a.genero,
      a.fx_etaria,
      a.cidade,
      a.estado,
      COUNT( DISTINCT a.codigo_usuario) as qtd_usuarios_resgate
    FROM resgates a
      JOIN (SELECT DISTINCT codigo_usuario FROM emissor WHERE lower(nome_emissor) like 'vivo%') b on a.codigo_usuario = b.codigo_usuario
    WHERE 
      a.data_transacao >= '2023-12-07'
    GROUP BY GROUPING SETS (
 
```

**`fResgate_unico`**
```sql
let
    Query =
    "
WITH 
  emissor AS (
    SELECT DISTINCT codigo_usuario, nome_emissor, codigo_emissor
    FROM gold.valebonus.emissions
  ),
  resgates AS (
    SELECT
      coalesce(origem, 'Sem rastreio') as origem,
      CASE WHEN tipo_resgate IN ('PRODUCT (P4)', 'PRODUCT (P5)') THEN 'PRODUTO' ELSE 'OFERTA' END AS tipo_resgate,
      codigo_usuario,
      data_transacao
    FROM gold.valebonus.transactions_shops
  ),
  por_emissor AS (
    SELECT
      b.codigo_emissor,
      coalesce(a.tipo_resgate, '#GERAL') as tipo_resgate,
      coalesce(a.origem, '#GERAL') as origem,
      COUNT(DISTINCT a.codigo_usuario) as qtd_usuarios_resgate
    FROM resgates a
      LEFT JOIN emissor b ON a.codigo_usuario = b.codigo_usuario
    WHERE a.data_transacao >= '2024-01-01'
    GROUP BY GROUPING SETS (
      (b.codigo_emissor, a.tipo_resgate, a.origem),
      (b.codigo_emissor, a.tipo_resgate),
      (b.codigo_emissor, a.origem),
      (b.codigo_emissor)
    )
  ),
  crm_total AS (
    SELECT
      'crmbonustotal' as codigo_emissor,
      coalesce(a.tipo_resgate, '#GERAL') as tipo_resgate,
      coalesce(a.origem, '#GERAL') as origem,
      COUNT(DISTINCT a.codigo_usuario) as qtd_usuarios_resgate
    FROM resgates a
    WHERE a.data_transacao >= '2024-01-01'
    GROUP BY GROUPING SETS (
      (a.tipo_resgate, a.origem),
      (a.tipo_resgate),
      (a.origem),
      ()
    )
  ),
  vivo AS (
    SELECT
      'vivototal' as codigo_emissor,
      coalesce(a.tipo_resgate, '#GERAL') as tipo_resgate,
      coalesce(a.origem, '#GERAL') as origem,
      COUNT(DISTINCT a.codigo_usuario) as qtd_usuarios_resgate
    FROM resgates a
      JOIN (SELECT DISTINCT codigo_usuario FROM emissor WHERE lower(nome_emissor) LIKE 'vivo%') b ON a.codigo_usuario = b.codigo_usuario
    WHERE a.data_transacao >= '2023-12-07'
    GROUP BY GROUPING SETS (
      (a.tipo_resgate, a.origem),
      (a.tipo_resgate),
      (a.origem),
      ()
    )
  ),
  tier_2 AS (
    WITH tier_2_users AS (
      SEL
```

**`fEmissao_total`**
```sql
let
    Query =
    "
select * from 
gold.valebonus.pbi_VBO02_Parceiro
    "
    
    ,    Fonte = Databricks.Query(host, db)(Query)
in
    Fonte
```

**`fCohort`**
```sql
let
    Query =
    "
        SELECT *
        FROM gold.valebonus.cohort_parceiros
        WHERE n_cohort <= 24
    ",
    Fonte = Databricks.Query(host, db)(Query)
in
    Fonte
```

**`fEmissor_unico_mes`**
```sql
let
  Query =
  "
    SELECT *
    FROM gold.valebonus.emissoes_unicas_mensais
  ",
  Fonte = Databricks.Query(host, db)(Query),
  Replaced = Table.ReplaceValue(Fonte, "tier2total", "tier_2", Replacer.ReplaceText, {"codigo_emissor"})
in
  Replaced
```

**`fResgate_cadastro`**
```sql
let
  Query =
  "
WITH 
  primeiro_resgate AS (
    SELECT 
      codigo_usuario, 
      MIN(TRUNC(data_transacao, 'MM')) AS dt_pr_resgate
    FROM 
      gold.valebonus.transactions_shops
    WHERE 
      data_transacao >= '2023-12-01'
    GROUP BY
      codigo_usuario
  ),
  resgate AS (
    SELECT 
      codigo_usuario, 
      TRUNC(data_transacao, 'MM') AS data_transacao
    FROM 
      gold.valebonus.transactions_shops
    WHERE 
      data_transacao >= '2023-12-01'
  ),
  cadastro AS (
    SELECT DISTINCT
      codigo_usuario, 
      TRUNC(data_cadastro, 'MM') AS data_cadastro
    FROM 
      gold.valebonus.usuarios
  ),
  emission AS (
    SELECT DISTINCT
      codigo_emissor,
      nome_emissor,
      codigo_usuario
    FROM 
      gold.valebonus.emissions
  ),
  base_final AS (
    SELECT DISTINCT 
        a.codigo_usuario,
        a.data_transacao,
        b.dt_pr_resgate,
        c.data_cadastro,
        CASE 
            WHEN a.data_transacao > b.dt_pr_resgate THEN 'Resgate Recorrente'
            WHEN a.data_transacao = c.data_cadastro THEN 'Resgate no Mês de Cadastro'
            WHEN b.dt_pr_resgate > c.data_cadastro THEN 'Resgate Pós Cadastro'
        END AS descricao_classificacao,
        d.nome_emissor,
        d.codigo_emissor
    FROM resgate a
      LEFT JOIN primeiro_resgate b ON a.codigo_usuario = b.codigo_usuario
      LEFT JOIN cadastro c ON a.codigo_usuario = c.codigo_usuario
      LEFT JOIN emission d ON a.codigo_usuario = d.codigo_usuario
  ),

  por_emissor AS (
    SELECT 
      data_transacao,
      codigo_emissor,
      COUNT(DISTINCT CASE WHEN descricao_classificacao = 'Resgate no Mês de Cadastro' THEN codigo_usuario END) AS resgate_mes_cadastro,
      COUNT(DISTINCT CASE WHEN descricao_classificacao = 'Resgate Pós Cadastro' THEN codigo_usuario END) AS resgate_pos_cadastro,
      COUNT(DISTINCT CASE WHEN descricao_classificacao = 'Resgate Recorrente' THEN codigo_usuario END) AS resgate_recorrente
    FROM base_final
    GROUP BY 
  
```

**`fPrimeira_emissao_mes`**
```sql
let
    Query =
    "
WITH
  retirar AS (
    SELECT DISTINCT codigo_referencia
    FROM gold.valebonus.emissions
    WHERE lower(nome_emissor) like 'vivo%'
      AND valor_transacao = 1
  ),
  base AS (
    SELECT
      codigo_emissor
      ,nome_emissor
      ,codigo_usuario
      ,DATE(data_referencia) as data
    FROM gold.valebonus.emissions
    WHERE 
      DATE(data_referencia) >= TRUNC(add_months(current_date(), -12), 'MM')
      AND codigo_referencia NOT IN (SELECT * FROM retirar)
  ),
  emissor AS (
    WITH 
      emissao AS (
        SELECT 
          codigo_emissor
          ,codigo_usuario
          ,TRUNC(MIN(data), 'MM') as primeira_emissao
        FROM base
        GROUP BY 
          codigo_emissor
          ,codigo_usuario
    )
    SELECT 
      codigo_emissor
      ,primeira_emissao
      ,COUNT( DISTINCT codigo_usuario) as qtd_nova_emissao
    FROM emissao
    GROUP BY 
      codigo_emissor
      ,primeira_emissao
  ),
  crm_total AS (
    WITH 
      emissao AS (
        SELECT 
          codigo_usuario
          ,TRUNC(MIN(data), 'MM') as primeira_emissao
        FROM base
        GROUP BY codigo_usuario
    )
    SELECT 
      'crmbonustotal' AS codigo_emissor
      ,primeira_emissao
      ,COUNT( DISTINCT codigo_usuario) as qtd_nova_emissao
    FROM emissao
    GROUP BY 
      codigo_emissor
      ,primeira_emissao
  ),
  vivo AS (
    WITH 
      emissao AS (
        SELECT 
          codigo_usuario
          ,TRUNC(MIN(data), 'MM') as primeira_emissao
        FROM base
        WHERE UPPER(nome_emissor) LIKE '%VIVO%'
        GROUP BY codigo_usuario
    )
    SELECT 
      'vivototal' AS codigo_emissor
      ,primeira_emissao
      ,COUNT( DISTINCT codigo_usuario) as qtd_nova_emissao
    FROM emissao
    GROUP BY 
      codigo_emissor
      ,primeira_emissao
  ),
  tier_2 AS (
    WITH 
      emissao AS (
        SELECT 
          codigo_usuario
          ,TRUNC(MIN(data), 'MM') as primeira_emissao
        FROM base
        WHERE         
 
```

**`fPrimeira_emissao_semana`**
```sql
let
    Query =
    "
WITH  
  base AS (
    SELECT
      codigo_emissor
      ,nome_emissor
      ,codigo_usuario
      ,DATE(data_referencia) as data
    FROM gold.valebonus.emissions
    WHERE 
      DATE(data_referencia) >= TRUNC(add_months(current_date(), -6), 'MM')
  ),
  emissor AS (
    WITH 
      emissao AS (
        SELECT 
          codigo_emissor
          ,codigo_usuario
          ,MIN(
            DATE_SUB(DATE(data), CASE WHEN dayofweek(data) = 1 THEN 6 ELSE dayofweek(data) - 2 END)
          ) as primeira_emissao
        FROM base
        GROUP BY 
          codigo_emissor
          ,codigo_usuario
    )
    SELECT 
      codigo_emissor
      ,primeira_emissao
      ,COUNT( DISTINCT codigo_usuario) as qtd_nova_emissao
    FROM emissao
    GROUP BY 
      codigo_emissor
      ,primeira_emissao
  ),
  crm_total AS (
    WITH 
      emissao AS (
        SELECT 
          codigo_usuario
          ,MIN(DATE_SUB(DATE(data), CASE WHEN dayofweek(data) = 1 THEN 6 ELSE dayofweek(data) - 2 END )) as primeira_emissao
        FROM base
        GROUP BY
          codigo_usuario
    )
    SELECT 
      'crmbonustotal' AS codigo_emissor
      ,primeira_emissao
      ,COUNT( DISTINCT codigo_usuario) as qtd_nova_emissao
    FROM emissao
    GROUP BY 
      codigo_emissor
      ,primeira_emissao
  ),
  vivo AS (
    WITH 
      emissao AS (
        SELECT 
          codigo_usuario
          ,MIN(DATE_SUB(DATE(data), CASE WHEN dayofweek(data) = 1 THEN 6 ELSE dayofweek(data) - 2 END )) as primeira_emissao
        FROM base
        WHERE 
          UPPER(nome_emissor) LIKE '%VIVO%'
        GROUP BY 
          codigo_usuario
    )
    SELECT 
      'vivototal' AS codigo_emissor
      ,primeira_emissao
      ,COUNT( DISTINCT codigo_usuario) as qtd_nova_emissao
    FROM emissao
    GROUP BY 
      codigo_emissor
      ,primeira_emissao
  ),
  tier_2 AS (
    WITH 
      emissao AS (
        SELECT 
          codigo_usuario
          ,MIN(DATE_SUB(DATE(data), CASE WHEN dayof
```

**`dOferta`**
```sql
let
   
    QueryLojas = "
        SELECT
            UPPER(TRIM(REGEXP_REPLACE(loja, '\\s+', ' '))) AS nome_resgate,
            FIRST(UPPER(TRIM(marca)))                       AS nome_marca
        FROM silver.giftback.bonus
        WHERE loja  IS NOT NULL
          AND marca IS NOT NULL
        GROUP BY UPPER(TRIM(REGEXP_REPLACE(loja, '\\s+', ' ')))
    ",
    FonteLojas = Databricks.Query(host, db)(QueryLojas),

    
    QueryTodos = "
        SELECT
            UPPER(TRIM(REGEXP_REPLACE(nome_resgate, '\\s+', ' '))) AS nome_resgate
        FROM gold.valebonus.transactions_shops
        WHERE nome_resgate IS NOT NULL
          AND data_transacao >= '2024-01-01'
        GROUP BY UPPER(TRIM(REGEXP_REPLACE(nome_resgate, '\\s+', ' ')))
    ",
    FonteTodos = Databricks.Query(host, db)(QueryTodos),

    
    Joined = Table.NestedJoin(
        FonteTodos,  {"nome_resgate"},
        FonteLojas,  {"nome_resgate"},
        "giftback", JoinKind.LeftOuter
    ),
    Expanded = Table.ExpandTableColumn(Joined, "giftback", {"nome_marca"}),

    Final = Table.AddColumn(
        Expanded,
        "nome_marca_final",
        each if [nome_marca] = null or [nome_marca] = "" then [nome_resgate] else [nome_marca],
        type text
    ),

    Resultado = Table.RenameColumns(
        Table.SelectColumns(Final, {"nome_resgate", "nome_marca_final"}),
        {{"nome_marca_final", "nome_marca"}}
    )
in
    Resultado
```

**`fResgates_Detalhe`**
```sql
let
    Query =
    "
WITH 
  emissor AS (
    SELECT DISTINCT 
      codigo_usuario,
      codigo_emissor,
      nome_emissor
    FROM gold.valebonus.emissions
  ),

  resgates_base AS (
    SELECT
      a.codigo_usuario,
      b.codigo_emissor,
      b.nome_emissor AS _nome_emissor,
      a.data_transacao,
      COALESCE(a.origem, 'Sem rastreio') AS origem,
      CASE 
        WHEN a.tipo_resgate IN ('PRODUCT (P4)', 'PRODUCT (P5)') THEN 'PRODUTO'
        ELSE 'OFERTA'
      END AS tipo_resgate
    FROM gold.valebonus.transactions_shops a
    LEFT JOIN emissor b
      ON a.codigo_usuario = b.codigo_usuario
    WHERE a.data_transacao >= '2024-01-01'
    QUALIFY ROW_NUMBER() OVER (
      PARTITION BY a.codigo_usuario, a.data_transacao, a.origem, a.tipo_resgate
      ORDER BY b.codigo_emissor
    ) = 1
  ),

  por_emissor AS (
    SELECT
      codigo_usuario,
      codigo_emissor,
      data_transacao,
      origem,
      tipo_resgate
    FROM resgates_base
  ),

  crm_total AS (
    SELECT
      codigo_usuario,
      'crmbonustotal' AS codigo_emissor,
      data_transacao,
      origem,
      tipo_resgate
    FROM resgates_base
  ),

  vivo AS (
    SELECT
      codigo_usuario,
      'vivototal' AS codigo_emissor,
      data_transacao,
      origem,
      tipo_resgate
    FROM resgates_base
    WHERE UPPER(_nome_emissor) LIKE 'VIVO%'
  ),

  tier_2 AS (
    SELECT
      codigo_usuario,
      'tier_2' AS codigo_emissor,
      data_transacao,
      origem,
      tipo_resgate
    FROM resgates_base
    WHERE UPPER(_nome_emissor) NOT LIKE '%VIVO%'
      AND UPPER(_nome_emissor) NOT LIKE '%SERASA%'
      AND UPPER(_nome_emissor) NOT LIKE '%SABESP%'
      AND UPPER(_nome_emissor) NOT LIKE '%IFOOD%'
      AND UPPER(_nome_emissor) NOT LIKE '%NEON%'
      AND UPPER(_nome_emissor) NOT LIKE '%PICPAY - CARTEIRA%'
      AND UPPER(_nome_emissor) NOT LIKE '%PICPAY - PIX%'
      AND UPPER(_nome_emissor) NOT LIKE '%MASTERCARD%'
      AND UPPER(_nome_emissor) NOT LIKE '%SAFRAPAY%'
  )
```

##### Análise 2 — Medidas DAX (37 medidas)

| Medida | Expressão DAX |
|---|---|
| `Qtd Resgates` | `SUM(fResgate[qtd_resgate])` |
| `Valor Total Resgates` | `SUM(fResgate[valor_resgate])` |
| `Qtd Emissões` | `sum(fEmissor[qtd_emissao])` |
| `Valor Emissoes` | `sum(fEmissor[valor_emissao])` |
| `Saldo Médio Atual em Carteira` | `CALCULATE(     AVERAGE(fUser[media_saldo]),     ALL() )` |
| `% Qtd Resgates (resgates x Emissoes)` | `VAR Calculo = DIVIDE([Qtd Resgates], [Qtd Emissões])   RETURN SWITCH(     TRUE(),     Calculo = BLANK(), "-", Calculo )` |
| `% Valor Resgates (resgates x Emissoes)` | `VAR Calculo = DIVIDE([Valor Total Resgates], [Valor Emissoes])   RETURN SWITCH(     TRUE(),     Calculo = BLANK(), "-", Calculo )` |
| `% Taxa de Engajamento` | `DIVIDE(     [Qtd Resgate únicos],     CALCULATE([Qtd Usuários Cadastrados], ALL(dCalendario)) )` |
| `Qtd Usuários Cadastrados` | `SUM(fUser[qtd_usuarios_total])` |
| `% Variação Emissão Período Atual x Anterior` | `VAR DataRef = MAX(fEmissor[data_transacao]) VAR DataInicioAnt = DATE(YEAR(DataRef), MONTH(DataRef)-1, 01) VAR DataFimAnt = EOMONTH(DataInicioAnt, 0)  -- Corrige o fim do mês anterior VAR ResultMesAnterior =      CALCULATE([Qtd Emissões],         FILTER(ALL(dCalendario),         dCalendario[Data] >= ` |
| `% Variação Cadastros Período Atual x Anterior` | `--- QTD Usuários Cadastrados Mês Anterior VAR DataRef = MAX(fEmissor[data_transacao]) VAR DataInicioAnt = DATE(YEAR(DataRef), MONTH(DataRef)-1, 01) VAR DataFimAnt = EOMONTH(DataInicioAnt, 0)  -- Corrige o fim do mês anterior VAR ResultMesAnterior =      CALCULATE([Qtd Usuários Cadastrados],         ` |
| `% Variação Resgates Período Atual x Anterior` | `--- QTD Resgates Mês Anterior VAR DataRef = MAX(fResgate[data_transacao]) VAR DataInicioAnt = DATE(YEAR(DataRef), MONTH(DataRef)-1, 01) VAR DataFimAnt = EOMONTH(DataInicioAnt, 0) VAR Teste_Data = DATESBETWEEN(dCalendario[Data], DataInicioAnt, DataFimAnt)  VAR ResultMesAnterior =     CALCULATE([Qtd R` |
| `Emissões Usuários Únicos` | `// Só pode ser usado por dia SUM(fEmissor[qtd_emissao_usuario_unico])` |
| `Qtd total de usuários que resgataram Vale Bônus` | `SUM(fResgate_user[qtd_usuarios_resgate])` |
| `Média de Emissões por Usuário` | `DIVIDE([Qtd Emissões Total], [Emissões Usuários Únicos Totais])` |
| `Taxa Emissões únicas` | `DIVIDE(         CALCULATE([Qtd Usuários Cadastrados], ALL(dCalendario))         ,[Emissões Usuários Únicos Totais]     )` |
| `Qtd Resgate únicos` | `VAR origem = ISFILTERED(dOrigem) VAR tipo_resgate = ISFILTERED(dTipo_resgate)  RETURN     SWITCH(         TRUE(),         AND(origem, tipo_resgate), [Usuarios que regataram],         origem, CALCULATE([Usuarios que regataram], fResgate_unico[tipo_resgate] = "#GERAL"),         tipo_resgate, CALCULATE` |
| `Emissões Usuários Únicos Totais` | `SUM(fEmissao_total[qtd_emissao_usuario_unico])` |
| `Qtd Emissões Total` | `SUM(fEmissao_total[qtd_emissao])` |
| `Valor Total Faturamento` | `SUM(fResgate[valor_faturamento])` |
| `Usuarios que regataram` | `SUM(fResgate_unico[qtd_usuarios_resgate])` |
| `Última Atualização` | `-- Retorna a data local considerando o fuso (-3) como última atualização do dashboard     LASTDATE(auxUltima_atualizacao[auxUltima_atualizacao]) - TIME(3,0,0)` |
| `Primeira Emissão Mês` | `SUM(fPrimeira_emissao_mes[qtd_nova_emissao])` |
| `% Variação Resgates (Filtro Data)` | `IF(MAX(pCalendario[pCalendario]) = "Mês", [% Variação Resgates Período Atual x Anterior], BLANK())` |
| `% Variação Cadastros (Filtro Data)` | `IF(MAX(pCalendario[pCalendario]) = "Mês", [% Variação Cadastros Período Atual x Anterior], BLANK())` |
| `Cohort Geral` | `SUM(fCohort[qtd_resgate])` |
| `Cohort App` | `SUM(fCohort[qtd_resgate_app])` |
| `Cohort Web` | `SUM(fCohort[qtd_resgate_web])` |
| `Cohort Acumulado Geral` | `SUM(fCohort[resgate_acumulado])` |
| `Cohort Acumulado App` | `SUM(fCohort[resgate_acumulado_app])` |
| `Cohort Acumulado Web` | `SUM(fCohort[resgate_acumulado_web])` |
| `Cohort cor` | `VAR cohort = MAX(pCohort[pCohort])  RETURN     IF(          MAX(fCohort[n_cohort]) >= 0,          SWITCH(              TRUE(),             cohort = "Cohort Geral", [Cohort Geral],             cohort = "Cohort App", [Cohort App],             cohort = "Cohort Web", [Cohort Web],             cohort = "` |
| `Emissões Únicas pCalendario` | `-- NÃO SOMAR VAR calendario = MAX(pCalendario[pCalendario])  RETURN     SWITCH(         TRUE(),         calendario = "Dia",    [Emissões Usuários Únicos],         calendario = "Semana", [Emissões Usuários Únicos],         calendario = "Mês",   [Emissões Usuários Únicos Mês],         calendario = "An` |
| `Emissões Usuários Únicos Mês` | `// Só pode ser usado por dia SUM(fEmissor_unico_mes[qtd_emissao_usuario_unico])` |
| `Usuários Únicos com Resgates` | `DISTINCTCOUNT(fResgates_Detalhe[codigo_usuario])` |
| `Card Qtd Emissões` | `VAR metrica = MAX(pMetricaEmissao[pMetricaEmissao]) RETURN     SWITCH(         TRUE(),         metrica = "Emissões",        [Qtd Emissões],         metrica = "Emissões Únicas",  [Emissões Usuários Únicos Totais],         metrica = "1ª Emissão Mês",  [Primeira Emissão Mês],         [Qtd Emissões]    ` |
| `Card Valor Emissões` | `VAR metrica = MAX(pMetricaEmissao[pMetricaEmissao]) RETURN     SWITCH(         TRUE(),         metrica = "Emissões",             [Valor Emissoes],         metrica = "Emissões Únicas",             [Valor Emissoes] * DIVIDE([Emissões Usuários Únicos Totais], [Qtd Emissões Total]),         metrica = "1` |

#### 📊 VB - vale_bonus_parceiros

---

##### Análise 1 — Fontes (tabelas) utilizadas

| Tabela PBI | Fonte (Databricks) | Tipo | Governança |
|---|---|---|---|
| `dEmissor` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fUser` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fUser` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fUser` | `gold.valebonus.usuarios` | Tabela gold | ✅ Verde |
| `fResgate` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fResgate` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fResgate` | `gold.valebonus.transactions_shops` | Tabela gold | ✅ Verde |
| `fEmissor_dia` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fEmissor_dia` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `auxRLS_user` | `google_sheets.external` | Fonte externa | 🔴 Vermelho |
| `fCohort` | `gold.valebonus.cohort_parceiros` | Tabela gold | ✅ Verde |
| `fCohort` | `gold.valebonus.emissions_agregado` | Tabela gold | ✅ Verde |
| `fEmissor_unico_geral` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fEmissor_unico_geral` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fEmissor_unico_mes` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fEmissor_unico_mes` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fAcessos_dia` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fAcessos_dia` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fAcessos_dia` | `gold.valebonus.user_acessos` | Tabela gold | ✅ Verde |
| `fAcessos_dia` | `gold.valebonus.usuarios` | Tabela gold | ✅ Verde |
| `fAcessos_dia` | `silver.valebonus.usuarios` | Tabela silver | 🟡 Amarelo |
| `fAcessos_geral` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fAcessos_geral` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fAcessos_geral` | `gold.valebonus.user_acessos` | Tabela gold | ✅ Verde |
| `fAcessos_geral` | `gold.valebonus.usuarios` | Tabela gold | ✅ Verde |
| `fAcessos_geral` | `silver.valebonus.usuarios` | Tabela silver | 🟡 Amarelo |
| `fAcessos_unicos_mes` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fAcessos_unicos_mes` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fAcessos_unicos_mes` | `gold.valebonus.user_acessos` | Tabela gold | ✅ Verde |
| `fAcessos_unicos_mes` | `gold.valebonus.usuarios` | Tabela gold | ✅ Verde |
| `fAcessos_unicos_mes` | `silver.valebonus.usuarios` | Tabela silver | 🟡 Amarelo |
| `fCohort_emissions` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fOptin` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fValor_emissao_por_user` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fResgate_usuarios` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fResgate_usuarios` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fResgate_usuarios` | `gold.valebonus.transactions_shops` | Tabela gold | ✅ Verde |
| `fResgate_usuarios` | `silver.valebonus.usuarios` | Tabela silver | 🟡 Amarelo |
| `fResgate_perfil` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fResgate_perfil` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fResgate_perfil` | `gold.valebonus.transactions_shops` | Tabela gold | ✅ Verde |
| `fResgate_perfil` | `gold.valebonus.usuarios` | Tabela gold | ✅ Verde |
| `fResgate_perfil` | `silver.valebonus.usuarios` | Tabela silver | 🟡 Amarelo |

**Scorecard: ✅ 27 · 🟡 5 · 🔴 11 → 🔴 Crítico**


##### SQL por fonte


**`dEmissor`**
```sql
let
  Query =
  "
    SELECT DISTINCT
      codigo_emissor,
      nome_emissor,
      MIN(DATE(data_referencia)) AS data_referencia
    FROM gold.valebonus.emissions
    WHERE codigo_emissor IN (
      SELECT  codigo_emissor
      FROM gold.valebonus.emissions
      WHERE nome_emissor NOT LIKE 'VIVO%'
      GROUP BY ALL
      HAVING DATE(MAX(data_transacao)) >= add_months(current_date(), -2)
    ) 
    GROUP BY ALL
        UNION ALL
    SELECT 'sabesptotal', '#SABESP TOTAL', DATE('2025-01-01')
  ",
  Fonte = Databricks.Query(host, db)(Query)
in
  Fonte
```

**`fUser`**
```sql
let
  Query =
  "
WITH
  emissores_ativos AS (
    SELECT codigo_emissor
    FROM gold.valebonus.emissions
    WHERE nome_emissor NOT LIKE 'VIVO%'
      AND nome_emissor NOT LIKE '%SABESP%'
    GROUP BY ALL
    HAVING DATE(MAX(data_transacao)) >= add_months(current_date(), -1)
  ),

  sabesp AS (
    SELECT 
      CASE WHEN b.reason = 'Bonus FATURA_DIGITAL' 
        THEN 'b8493d76-87d4-4d0b-9e13-2548dc03e758' 
        ELSE a.codigo_emissor 
      END as codigo_emissor,
      a.codigo_usuario
    FROM gold.valebonus.emissions a
      INNER JOIN bronze.valebonus.pre_charge_transaction b 
        ON a.codigo_pre_recarga = b.id 
        AND b.reason NOT LIKE 'Sabesp - incentivo ao cadastro%'
    WHERE nome_emissor LIKE '%SABESP%'
    GROUP BY ALL
  ),

  emissores AS (
    SELECT DISTINCT 
      a.codigo_usuario,
      a.codigo_emissor
    FROM gold.valebonus.emissions a
      JOIN emissores_ativos b
        ON a.codigo_emissor = b.codigo_emissor

    UNION

    SELECT DISTINCT 
      codigo_usuario,
      codigo_emissor
    FROM sabesp

    UNION

    SELECT DISTINCT 
      codigo_usuario,
      'sabesptotal' AS codigo_emissor
    FROM sabesp
  ),

  usuarios AS (
    SELECT 
      b.codigo_emissor,
      a.origem,
      a.data_cadastro,
      a.genero,
      a.fx_etaria,
      a.estado,
      a.cidade,
      a.resgate,
      AVG(a.saldo_atual) FILTER (WHERE a.saldo_atual > 0) AS saldo_atual,
      COUNT(DISTINCT a.codigo_usuario)                    AS qtd_usuarios
    FROM gold.valebonus.usuarios a
      JOIN emissores b ON a.codigo_usuario = b.codigo_usuario
    GROUP BY 
      b.codigo_emissor,
      a.origem,
      a.data_cadastro,
      a.genero,
      a.fx_etaria,
      a.estado,
      a.cidade,
      a.resgate
  )

SELECT * FROM usuarios
  ",
  Fonte = Databricks.Query(host, db)(Query)
in
  Fonte
```

**`fResgate`**
```sql
let
  Query = 
    "
WITH
  emissores_ativos AS (
      SELECT codigo_emissor
      FROM gold.valebonus.emissions
      WHERE nome_emissor NOT LIKE 'VIVO%'
        AND nome_emissor NOT LIKE '%SABESP%'
      GROUP BY ALL
      HAVING DATE(MAX(data_transacao)) >= add_months(current_date(), -1)
  ),

  sabesp AS (
    SELECT 
      CASE WHEN b.reason = 'Bonus FATURA_DIGITAL'
        THEN 'b8493d76-87d4-4d0b-9e13-2548dc03e758'
        ELSE a.codigo_emissor
      END as codigo_emissor,
      a.codigo_usuario
     
    FROM gold.valebonus.emissions a
      INNER JOIN bronze.valebonus.pre_charge_transaction b 
        ON a.codigo_pre_recarga = b.id 
        AND b.reason NOT LIKE 'Sabesp - incentivo ao cadastro%'
    WHERE nome_emissor LIKE '%SABESP%'
    GROUP BY ALL
  ),

  emissores AS (
    SELECT DISTINCT 
      a.codigo_usuario,
      a.codigo_emissor
      
    FROM gold.valebonus.emissions a
      JOIN emissores_ativos ea ON a.codigo_emissor = ea.codigo_emissor

    UNION           -- ✅ UNION (sem ALL) elimina duplicatas
    SELECT DISTINCT codigo_usuario, codigo_emissor FROM sabesp
    UNION
    SELECT DISTINCT codigo_usuario, 'sabesptotal' AS codigo_emissor FROM sabesp
  ),

  resgates AS (
    SELECT
      b.codigo_emissor,
      CAST(NULL AS STRING) AS reason, 
      a.data_transacao,
      a.tipo_resgate,
      a.origem,
      a.nome_resgate,
      SUM(a.qtd_resgate)       AS qtd_resgate,
      SUM(a.valor_resgate)     AS valor_resgate,
      SUM(a.valor_faturamento) AS valor_faturamento
    FROM gold.valebonus.transactions_shops a  
      JOIN emissores b ON a.codigo_usuario = b.codigo_usuario
    WHERE data_transacao >= '2024-01-01'
    GROUP BY 
      b.codigo_emissor,
      -- ✅ reason fora do GROUP BY: 1 linha por (emissor, data, tipo, origem, loja)
      a.data_transacao, a.tipo_resgate, a.origem, a.nome_resgate
  )

SELECT * FROM resgates
  ",
  Fonte = Databricks.Query(host, db)(Query)
in
  Fonte
```

**`fEmissor_dia`**
```sql
let
  Query =
  "
WITH
  -- Lista de emissores ativos (último mês) usando a base granular
  emissores_ativos AS (
    SELECT
      codigo_emissor
    FROM gold.valebonus.emissions
    WHERE codigo_emissor NOT IN (
      -- CODIGOS VIVO
      'edc044f7-011d-46ec-829c-35914a88fd6e' --VIVO 6
      ,'2763fdd0-9c5e-44e7-965a-f40173eedc4c' --VIVO 3
      ,'c89d6c9d-7b52-4322-90d3-fa024f376e44' --VIVO 1
      ,'434c239b-957e-4748-8e0d-10b490afe48a' --VIVO 7
      ,'62be8eb8-212c-4e88-b94f-8910f4dcd1f0' --VIVO 5
      ,'714b7d81-889e-4710-ac38-e2fa668f3e67' --VIVO 4
      ,'dd3e3ad9-9d9a-44b0-860c-7cc057cc6bbf' --VIVO 2
      ,'crmbonustotal'
      ,'vivototal'
      ,'tier2total'
      ,'b8493d76-87d4-4d0b-9e13-2548dc03e758' -- SABESP - FATURA DIGITAL
      ,'680f7f9c-be0b-4af4-87dd-480435b1c88e' --SABESP - INADIMPLENCIA
      ,'7bcaa6ac-bb66-4e6f-9b33-1ce93ba97cc4' --SABESP - PIX
      ,'08df25aa-e0d7-4a5d-b4e1-311c38f07ead' --SABESP
      ,'3ae7f345-0702-4564-b9c4-8b6f913c585f' --Sabesp - Cartão de Crédito
      ,'b46b07dd-e3d6-420b-b3f3-79f3fab67cfb' --Sabesp - Pix Mensal
    )
    GROUP BY codigo_emissor
    HAVING DATE(MAX(data_referencia)) >= add_months(current_date(), -1)
  ),

  -- Base granular com reason (para todos os emissores ativos)
  base_geral AS (
    SELECT
      DATE(e.data_referencia) AS data_transacao,
      e.codigo_emissor,
      pct.reason,
      e.codigo_referencia,
      e.codigo_usuario,
      e.valor_transacao
    FROM gold.valebonus.emissions e
      JOIN emissores_ativos ea
        ON e.codigo_emissor = ea.codigo_emissor
      LEFT JOIN bronze.valebonus.pre_charge_transaction pct
        ON e.codigo_pre_recarga = pct.id
    WHERE DATE(e.data_referencia) >= '2024-01-01'
      AND e.nome_emissor NOT LIKE 'VIVO%'
  ),

  -- Agregado diário (com reason)
  emissao_geral AS (
    SELECT
      data_transacao,
      codigo_emissor,
      reason,
      COUNT(DISTINCT codigo_referencia) AS qtd_emissao,
      COUNT(DISTINCT codigo_usuario) AS qtd_emissao
```

**`auxRLS_user`**
```sql
let
    Fonte = GoogleSheets.Contents("https://docs.google.com/spreadsheets/d/1qPVey735A0M_ir4qSrEoTXfWsTMnM1wOoGtvutSahDQ/edit?gid=0#gid=0"),
    #"RLS Clientes_Table" = Fonte{[name="RLS Clientes v2",ItemKind="Table"]}[Data],
    #"Cabeçalhos Promovidos" = Table.PromoteHeaders(#"RLS Clientes_Table", [PromoteAllScalars=true]),
    #"Tipo Alterado" = Table.TransformColumnTypes(#"Cabeçalhos Promovidos",{{"nome", type text}, {"e-mail", type text}, {"parceiro", type text}, {"nome_emissor", type text}, {"codigo_emissor", type text}})
in
    #"Tipo Alterado"
```

**`fCohort`**
```sql
let
  Query = 
    "
    WITH
      emissores_ativos AS (
        SELECT codigo_emissor
        FROM gold.valebonus.emissions_agregado
        WHERE codigo_emissor NOT IN (
          -- CODIGOS VIVO
          'edc044f7-011d-46ec-829c-35914a88fd6e' --VIVO 6
          ,'2763fdd0-9c5e-44e7-965a-f40173eedc4c' --VIVO 3
          ,'c89d6c9d-7b52-4322-90d3-fa024f376e44' --VIVO 1
          ,'434c239b-957e-4748-8e0d-10b490afe48a' --VIVO 7
          ,'62be8eb8-212c-4e88-b94f-8910f4dcd1f0' --VIVO 5
          ,'714b7d81-889e-4710-ac38-e2fa668f3e67' --VIVO 4
          ,'dd3e3ad9-9d9a-44b0-860c-7cc057cc6bbf' --VIVO 2
          ,'crmbonustotal'
          ,'vivototal'
        )
        GROUP BY ALL
        HAVING DATE(MAX(data_transacao)) >= add_months(current_date(), -1)
      )
    SELECT 
      codigo_emissor,
      data_cadastro,
      n_cohort,
      cohort,
      qtd_resgate,
      resgate_acumulado
    FROM gold.valebonus.cohort_parceiros
    WHERE codigo_emissor IN (SELECT * FROM emissores_ativos)
      AND n_cohort <= 12
      AND data_cadastro >= TRUNC(add_months(current_date(), -12), 'MM')
    ",
  Fonte = Databricks.Query(host, db)(Query)
in
  Fonte
```

**`fEmissor_unico_geral`**
```sql
let
  Query =
  "
WITH 
  primeiro_emissor AS (
    WITH
      manual AS (
        SELECT 
          '186d69ec-0f11-47ce-bccc-465f8fd7692e' AS codigo_emissor,
          '2025-04-15' AS data_emissao
        UNION ALL SELECT '6251f7cc-fa1f-4b3b-a638-f0390fa85cf1', '2024-06-26'
        UNION ALL SELECT '114c8073-1e9d-4fb7-9e31-9e740f95a7d4', '2025-07-29' -- Serasa
        UNION ALL SELECT '680f7f9c-be0b-4af4-87dd-480435b1c88e', '2025-08-08' -- SABESP - INADIMPLENCIA
        UNION ALL SELECT 'eb101cc7-3210-4b24-b717-8cfe746e0a26', '2025-08-18' -- Clube iFood 
        UNION ALL SELECT 'b48153b2-8757-4b18-a64a-ad6190893cc7', '2025-08-31' -- Mastercard
      ),
      base AS (
        SELECT DISTINCT
          codigo_emissor,
          MIN(DATE(data_referencia)) AS data_emissao
        FROM gold.valebonus.emissions
        WHERE codigo_emissor IN (
          SELECT codigo_emissor
          FROM gold.valebonus.emissions
          WHERE nome_emissor NOT LIKE 'VIVO%'
          GROUP BY ALL
          HAVING DATE(MAX(data_transacao)) >= add_months(current_date(), -1)
        )
        GROUP BY codigo_emissor
      )
      SELECT
        base.codigo_emissor,
        CASE WHEN manual.data_emissao IS NULL THEN base.data_emissao ELSE manual.data_emissao END AS data_emissao
      FROM base
        LEFT JOIN manual ON base.codigo_emissor = manual.codigo_emissor
  ),

  emissao AS (
    SELECT DISTINCT
      em.codigo_emissor,
      CAST(NULL AS STRING) AS reason,
      COUNT(DISTINCT em.codigo_usuario) as qtd_usuario_unicos
    FROM gold.valebonus.emissions em
      JOIN primeiro_emissor pc
        ON em.codigo_emissor = pc.codigo_emissor
       AND DATE(em.data_referencia) >= pc.data_emissao
    WHERE nome_emissor NOT LIKE '%SABESP%'
    GROUP BY ALL
  ),

  sabesp AS (
    WITH base AS (
      SELECT 
        CASE
          WHEN b.reason = 'Bonus FATURA_DIGITAL'
            THEN 'b8493d76-87d4-4d0b-9e13-2548dc03e758'
          ELSE a.codigo_emissor
        END as codigo_emissor,
  
```

**`fEmissor_unico_mes`**
```sql
let
  Query =
  "
WITH
  primeiro_emissor AS (
    WITH
      manual AS (
        SELECT 
          '186d69ec-0f11-47ce-bccc-465f8fd7692e' AS codigo_emissor,
          '2025-04-15' AS data_emissao --Safra Financeira
          UNION ALL SELECT '114c8073-1e9d-4fb7-9e31-9e740f95a7d4', '2025-07-29' -- Serasa
          UNION ALL SELECT '680f7f9c-be0b-4af4-87dd-480435b1c88e', '2025-08-08' -- SABESP - INADIMPLENCIA
          UNION ALL SELECT 'eb101cc7-3210-4b24-b717-8cfe746e0a26', '2025-08-18' -- Clube iFood 
          UNION ALL SELECT 'b48153b2-8757-4b18-a64a-ad6190893cc7', '2025-08-31' -- Mastercard
      ),
      -- Seleciona a data da primeira transação de crédito para cada emissor
      base AS (
        SELECT DISTINCT
          codigo_emissor,
          MIN(DATE(data_referencia)) AS data_emissao
        FROM gold.valebonus.emissions
        WHERE codigo_emissor IN (
          SELECT  codigo_emissor
          FROM gold.valebonus.emissions
          WHERE nome_emissor NOT LIKE 'VIVO%'
          GROUP BY ALL
          HAVING DATE(MAX(data_referencia)) >= add_months(current_date(), -1)
        )
        GROUP BY codigo_emissor
      )
    SELECT
      base.codigo_emissor,
      CASE WHEN manual.data_emissao IS NULL THEN base.data_emissao ELSE manual.data_emissao END AS data_emissao
    FROM base
      LEFT JOIN manual ON base.codigo_emissor = manual.codigo_emissor
  ),

  base_geral AS (
    -- Seleciona as emissões a partir da data da primeira transação de crédito para cada emissor
    SELECT DISTINCT
      TRUNC(DATE(em.data_referencia), 'MM') AS data_transacao,
      em.codigo_emissor,
      pct.reason,
      em.codigo_usuario
    FROM gold.valebonus.emissions em
      JOIN primeiro_emissor pc
        ON em.codigo_emissor = pc.codigo_emissor
       AND DATE(em.data_referencia) >= pc.data_emissao
      LEFT JOIN bronze.valebonus.pre_charge_transaction pct
        ON em.codigo_pre_recarga = pct.id
    WHERE em.valor_transacao <= 20000
      AND em.nome_emissor NOT LIKE
```

**`fAcessos_dia`**
```sql
let
  Query = 
    "    
      WITH
  data_inicio AS (
    SELECT 
      '114c8073-1e9d-4fb7-9e31-9e740f95a7d4' AS codigo_emissor, -- Serasa
      '2025-07-29' AS data_emissao
      UNION ALL SELECT 'b48153b2-8757-4b18-a64a-ad6190893cc7', '2025-08-31' -- Mastercard
      UNION ALL (
        SELECT codigo_emissor, DATE(min(data_referencia))
        FROM gold.valebonus.emissions
        where upper(nome_emissor) like '%SABESP%'
        GROUP BY ALL
      )
      UNION ALL SELECT 'sabesptotal', '2025-01-01' -- Mastercard
  ),
  emissor AS (
    SELECT DISTINCT
      a.codigo_usuario
      ,a.codigo_emissor
      ,a.nome_emissor
    FROM gold.valebonus.emissions a
      INNER JOIN data_inicio b 
        ON a.codigo_emissor = b.codigo_emissor 
        AND DATE(a.data_referencia) >= b.data_emissao
      INNER JOIN gold.valebonus.usuarios c 
        ON a.codigo_usuario = c.codigo_usuario 
        AND c.data_cadastro >= b.data_emissao
    WHERE upper(a.nome_emissor) NOT LIKE '%SABESP%'
  ),
  sabesp AS (
    SELECT 
      CASE WHEN b.reason = 'Bonus FATURA_DIGITAL' THEN 'b8493d76-87d4-4d0b-9e13-2548dc03e758' ELSE a.codigo_emissor END as codigo_emissor,
      a.codigo_usuario
    FROM gold.valebonus.emissions a
      INNER JOIN bronze.valebonus.pre_charge_transaction b 
        ON a.codigo_pre_recarga = b.id 
        AND b.reason NOT LIKE 'Sabesp - incentivo ao cadastro%'
      --INNER JOIN silver.valebonus.usuarios c ON a.codigo_usuario = c.codigo_usuario
    WHERE nome_emissor LIKE '%SABESP%'
    GROUP BY ALL
  ),
  user_emissor AS (
    SELECT DISTINCT
      codigo_usuario,
      codigo_emissor
    FROM emissor
    UNION ALL
    SELECT DISTINCT
      codigo_usuario,
      codigo_emissor
    FROM sabesp
        UNION ALL
    SELECT DISTINCT
      codigo_usuario,
      'sabesptotal' as codigo_emissor
    FROM sabesp
  ),
  sessao AS (
    SELECT
      a.data
      ,a.qtd_sessao
      ,a.codigo_usuario
      ,c.codigo_emissor
      ,coalesce(a.origem, 'Sem rastreio') as orig
```

**`fAcessos_geral`**
```sql
let
  Query = 
    "
        WITH
      data_inicio AS (
        SELECT 
          '114c8073-1e9d-4fb7-9e31-9e740f95a7d4' AS codigo_emissor, -- Serasa
          '2025-07-29' AS data_emissao
          UNION ALL SELECT 'b48153b2-8757-4b18-a64a-ad6190893cc7', '2025-08-31' -- Mastercard
          UNION ALL (
            SELECT codigo_emissor, DATE(min(data_referencia))
            FROM gold.valebonus.emissions
            where upper(nome_emissor) like '%SABESP%'
            GROUP BY ALL
          )
          UNION ALL SELECT 'sabesptotal', '2025-01-01' -- SABESP TOTAL
      ),
  emissor AS (
    SELECT DISTINCT
      a.codigo_usuario
      ,a.codigo_emissor
      ,a.nome_emissor
    FROM gold.valebonus.emissions a
      INNER JOIN data_inicio b 
        ON a.codigo_emissor = b.codigo_emissor 
        AND DATE(a.data_referencia) >= b.data_emissao
      INNER JOIN gold.valebonus.usuarios c 
        ON a.codigo_usuario = c.codigo_usuario 
        AND c.data_cadastro >= b.data_emissao
    WHERE upper(a.nome_emissor) NOT LIKE '%SABESP%'
  ),
  sabesp AS (
    SELECT 
      CASE WHEN b.reason = 'Bonus FATURA_DIGITAL' THEN 'b8493d76-87d4-4d0b-9e13-2548dc03e758' ELSE a.codigo_emissor END as codigo_emissor,
      a.codigo_usuario
    FROM gold.valebonus.emissions a
      INNER JOIN bronze.valebonus.pre_charge_transaction b 
        ON a.codigo_pre_recarga = b.id 
        AND b.reason NOT LIKE 'Sabesp - incentivo ao cadastro%'
      --INNER JOIN silver.valebonus.usuarios c ON a.codigo_usuario = c.codigo_usuario
    WHERE nome_emissor LIKE '%SABESP%'
    GROUP BY ALL
  ),
  user_emissor AS (
    SELECT DISTINCT
      codigo_usuario,
      codigo_emissor
    FROM emissor
    UNION ALL
    SELECT DISTINCT
      codigo_usuario,
      codigo_emissor
    FROM sabesp
        UNION ALL
    SELECT DISTINCT
      codigo_usuario,
      'sabesptotal' as codigo_emissor
    FROM sabesp
  ),
      sessao AS (
        SELECT
          --a.data
          --,a.qtd_sessao
          a.codigo_usuari
```

**`fAcessos_unicos_mes`**
```sql
let
  Query = 
    "
WITH
      data_inicio AS (
        SELECT 
          '114c8073-1e9d-4fb7-9e31-9e740f95a7d4' AS codigo_emissor, -- Serasa
          '2025-07-29' AS data_emissao
          UNION ALL SELECT 'b48153b2-8757-4b18-a64a-ad6190893cc7', '2025-08-31' -- Mastercard
          UNION ALL (
            SELECT codigo_emissor, DATE(min(data_referencia))
            FROM gold.valebonus.emissions
            where upper(nome_emissor) like '%SABESP%'
            GROUP BY ALL
          )
          UNION ALL SELECT 'sabesptotal', '2025-01-01' -- SABESP TOTAL
      ),
  emissor AS (
    SELECT DISTINCT
      a.codigo_usuario
      ,a.codigo_emissor
      ,a.nome_emissor
    FROM gold.valebonus.emissions a
      INNER JOIN data_inicio b 
        ON a.codigo_emissor = b.codigo_emissor 
        AND DATE(a.data_referencia) >= b.data_emissao
      INNER JOIN gold.valebonus.usuarios c 
        ON a.codigo_usuario = c.codigo_usuario 
        AND c.data_cadastro >= b.data_emissao
    WHERE upper(a.nome_emissor) NOT LIKE '%SABESP%'
  ),
  sabesp AS (
    SELECT 
      CASE WHEN b.reason = 'Bonus FATURA_DIGITAL' THEN 'b8493d76-87d4-4d0b-9e13-2548dc03e758' ELSE a.codigo_emissor END as codigo_emissor,
      a.codigo_usuario
    FROM gold.valebonus.emissions a
      INNER JOIN bronze.valebonus.pre_charge_transaction b 
        ON a.codigo_pre_recarga = b.id 
        AND b.reason NOT LIKE 'Sabesp - incentivo ao cadastro%'
      --INNER JOIN silver.valebonus.usuarios c ON a.codigo_usuario = c.codigo_usuario
    WHERE nome_emissor LIKE '%SABESP%'
    GROUP BY ALL
  ),
  user_emissor AS (
    SELECT DISTINCT
      codigo_usuario,
      codigo_emissor
    FROM emissor
    UNION ALL
    SELECT DISTINCT
      codigo_usuario,
      codigo_emissor
    FROM sabesp
        UNION ALL
    SELECT DISTINCT
      codigo_usuario,
      'sabesptotal' as codigo_emissor
    FROM sabesp
  ),
      sessao AS (
        SELECT
          TRUNC(a.data, 'MM') AS data
          --,a.qtd_sessao
          ,a.c
```

**`fCohort_emissions`**
```sql
let
  Query = 
    "
    WITH 
      emissao AS (
          SELECT
              codigo_usuario,
              codigo_emissor,
              DATE(data_referencia) AS data_referencia -- Mantém a data original
          FROM gold.valebonus.emissions
          WHERE codigo_emissor = 'b48153b2-8757-4b18-a64a-ad6190893cc7' -- Mastercard
            AND DATE(data_referencia) >= '2025-08-31'
      ),
      primeira_aparicao AS (
          SELECT
              codigo_usuario,
              MIN(data_referencia) AS primeira_data
          FROM emissao
          GROUP BY codigo_usuario
      ),
      aux_emissao AS (
        SELECT 
          e.codigo_emissor,
          TRUNC(pa.primeira_data, 'MM') AS primeira_data,
          TRUNC(e.data_referencia, 'MM') AS data_referencia,
          COUNT( DISTINCT e.codigo_usuario) AS emissoes_unicas
        FROM emissao e 
          LEFT JOIN primeira_aparicao pa ON e.codigo_usuario = pa.codigo_usuario
        GROUP BY 1,2, 3
      ),
      cohort as (
        SELECT 
          codigo_emissor,
          primeira_data,
          data_referencia,
          DATE_DIFF(MONTH, primeira_data, data_referencia) AS cohort_num_mes,
          'M' || DATE_DIFF(MONTH, primeira_data, data_referencia) AS cohort_mes,
          emissoes_unicas
        FROM aux_emissao
      ),
      qtd_primera_aparicao AS (
        SELECT * 
        FROM cohort
        WHERE cohort_num_mes = 0
      ),
      final AS (
        SELECT
          codigo_emissor,
          primeira_data,
          null as data_referencia,
          -1 as cohort_num_mes,
          'Qde Primeira Emissão' AS cohort_mes,
          null as emissoes_unicas,
          emissoes_unicas as valor_cohort
        FROM qtd_primera_aparicao
        UNION ALL
        SELECT
          c.*,
          ROUND(c.emissoes_unicas / c2.emissoes_unicas, 4) as valor_cohort
        FROM cohort c
          LEFT JOIN qtd_primera_aparicao c2 ON c.codigo_emissor = c2.codigo_emissor AND c.primeira_data = c2.primeira_data
  
```

**`fOptin`**
```sql
let
  Query = 
    "
    WITH
      optin AS (
        SELECT
          codigo_emissor,
          codigo_usuario,
          DATE(MIN(data_referencia)) as data
        FROM gold.valebonus.emissions
        WHERE 
          codigo_emissor IN ('b48153b2-8757-4b18-a64a-ad6190893cc7' )
          AND DATE(data_referencia) >= '2025-08-31'
          AND valor_transacao = 50
        GROUP BY 
          codigo_emissor,
          codigo_usuario
    )
    SELECT 
      codigo_emissor,
      data,
      COUNT(DISTINCT codigo_usuario) AS qtd_usuario_unicos
    FROM optin
    GROUP BY 
      codigo_emissor,
      data
    ",
  Fonte = Databricks.Query(host, db)(Query)
in
  Fonte
```

**`fValor_emissao_por_user`**
```sql
let
  Query = 
    "
    WITH
      emissao AS (
        SELECT
          TRUNC(DATE(data_referencia), 'MM') AS data_transacao,
          codigo_usuario,
          --SUM(valor_transacao) as valor_emissão,
          CASE
            WHEN SUM(valor_transacao) <= 25 THEN '0-25'
            WHEN SUM(valor_transacao) <= 50 THEN '25-50'
            WHEN SUM(valor_transacao) <= 75 THEN '50-75'
            WHEN SUM(valor_transacao) <= 100 THEN '75-100'
            WHEN SUM(valor_transacao) <= 125 THEN '100-125'
            WHEN SUM(valor_transacao) <= 150 THEN '125-150'
            WHEN SUM(valor_transacao) <= 175 THEN '150-175'
            WHEN SUM(valor_transacao) <= 200 THEN '175-200'
            WHEN SUM(valor_transacao) <= 225 THEN '200-225'
            WHEN SUM(valor_transacao) <= 250 THEN '225-250'
            ELSE '>250'
          END AS faixa_valor
        FROM gold.valebonus.emissions
        WHERE valor_transacao <= 20000
          AND codigo_emissor = 'b48153b2-8757-4b18-a64a-ad6190893cc7' --Mastercard Surpreenda
          AND DATE(data_referencia) >= '2025-08-31'
        GROUP BY 1, 2
      )

    SELECT 
      data_transacao,
      faixa_valor,
      COUNT(DISTINCT codigo_usuario) as qtd_usuarios
    FROM emissao
    GROUP BY GROUPING SETS (
      (data_transacao, faixa_valor),
      (faixa_valor)
    )
    ",
  Fonte = Databricks.Query(host, db)(Query)
in
  Fonte
```

**`fResgate_usuarios`**
```sql
let
  Query = 
    "
WITH
  primeiro_emissor AS (
    WITH
      emissores_ativos AS (
        SELECT codigo_emissor
        FROM gold.valebonus.emissions
        WHERE nome_emissor NOT LIKE 'VIVO%'
          --AND nome_emissor NOT LIKE '%SABESP%'
        GROUP BY ALL
        HAVING DATE(MAX(data_transacao)) >= add_months(current_date(), -2)
      ),
      manual AS (
        SELECT 
          '186d69ec-0f11-47ce-bccc-465f8fd7692e' AS codigo_emissor,
          '2025-04-15' AS data_emissao --Safra Financeira
          UNION ALL SELECT '114c8073-1e9d-4fb7-9e31-9e740f95a7d4', '2025-07-29' -- Serasa
          UNION ALL SELECT '680f7f9c-be0b-4af4-87dd-480435b1c88e', '2025-08-08' -- SABESP - INADIMPLENCIA
          UNION ALL SELECT 'eb101cc7-3210-4b24-b717-8cfe746e0a26', '2025-08-18' -- Clube iFood 
          UNION ALL SELECT 'b48153b2-8757-4b18-a64a-ad6190893cc7', '2025-08-31' -- Mastercard
      ),
      -- Seleciona a data da primeira transação de crédito para cada emissor
      base AS (
        SELECT DISTINCT
          a.codigo_emissor,
          MIN(DATE(a.data_referencia)) AS data_emissao
        FROM gold.valebonus.emissions a
          JOIN emissores_ativos b ON a.codigo_emissor = b.codigo_emissor
        GROUP BY a.codigo_emissor
      )
    SELECT
      base.codigo_emissor,
      CASE WHEN manual.data_emissao IS NULL THEN base.data_emissao ELSE manual.data_emissao END AS data_emissao
    FROM base
      LEFT JOIN manual ON  base.codigo_emissor = manual.codigo_emissor

    UNION ALL SELECT 'sabesptotal', '2025-01-01' -- sabesptotal
  ),
  sabesp AS (
    SELECT 
      CASE WHEN b.reason = 'Bonus FATURA_DIGITAL' THEN 'b8493d76-87d4-4d0b-9e13-2548dc03e758' ELSE a.codigo_emissor END as codigo_emissor,
      a.codigo_usuario
    FROM gold.valebonus.emissions a
      INNER JOIN bronze.valebonus.pre_charge_transaction b 
        ON a.codigo_pre_recarga = b.id 
        AND b.reason NOT LIKE 'Sabesp - incentivo ao cadastro%'
      --INNER JOIN silver.valebonus.usuarios
```

**`fResgate_perfil`**
```sql
let
  Query = 
    "
    WITH
      primeiro_emissor AS (
        WITH
          emissores_ativos AS (
            SELECT codigo_emissor
            FROM gold.valebonus.emissions
            WHERE nome_emissor NOT LIKE 'VIVO%'
            GROUP BY ALL
            HAVING DATE(MAX(data_transacao)) >= add_months(current_date(), -2)
          ),
          manual AS (
            SELECT 
              '186d69ec-0f11-47ce-bccc-465f8fd7692e' AS codigo_emissor,
              '2025-04-15' AS data_emissao --Safra Financeira
              UNION ALL SELECT '114c8073-1e9d-4fb7-9e31-9e740f95a7d4', '2025-07-29' -- Serasa
              UNION ALL SELECT '680f7f9c-be0b-4af4-87dd-480435b1c88e', '2025-08-08' -- SABESP - INADIMPLENCIA
              UNION ALL SELECT 'eb101cc7-3210-4b24-b717-8cfe746e0a26', '2025-08-18' -- Clube iFood 
              UNION ALL SELECT 'b48153b2-8757-4b18-a64a-ad6190893cc7', '2025-08-31' -- Mastercard
          ),
          -- Seleciona a data da primeira transação de crédito para cada emissor
          base AS (
            SELECT DISTINCT
              a.codigo_emissor,
              MIN(DATE(a.data_referencia)) AS data_emissao
            FROM gold.valebonus.emissions a
              JOIN emissores_ativos b ON a.codigo_emissor = b.codigo_emissor
            GROUP BY a.codigo_emissor
          )
        SELECT
          base.codigo_emissor,
          CASE WHEN manual.data_emissao IS NULL THEN base.data_emissao ELSE manual.data_emissao END AS data_emissao
        FROM base
          LEFT JOIN manual ON  base.codigo_emissor = manual.codigo_emissor

        UNION ALL SELECT 'sabesptotal', '2025-01-01' -- sabesptotal
      ),
  sabesp AS (
    SELECT 
      CASE WHEN b.reason = 'Bonus FATURA_DIGITAL' THEN 'b8493d76-87d4-4d0b-9e13-2548dc03e758' ELSE a.codigo_emissor END as codigo_emissor,
      a.codigo_usuario
    FROM gold.valebonus.emissions a
      INNER JOIN bronze.valebonus.pre_charge_transaction b 
        ON a.codigo_pre_recarga = b.id 
        AND b.rea
```

##### Análise 2 — Medidas DAX (62 medidas)

| Medida | Expressão DAX |
|---|---|
| `Qtd Usuários Cadastrados` | `-- Calcula os codigos distintos de usuarios -- Considera a data que o usuário se cadastrou CALCULATE(     SUM(fUser[qtd_usuarios]),     -- Filtro usado para considerar a data que o emissor gerou o primeiro crédito     FILTER(         dCalendario,         dCalendario[Data] >= [Primeira Data de Credit` |
| `Primeira Data de Credito` | `// VAR data_credito = DATEVALUE(MIN(dEmissor[data_referencia])) VAR data_credito = MIN(dEmissor[data_referencia]) // VAR data_manual = DATEVALUE(MIN(auxData_inicio[data_inicio_dados])) VAR data_manual = MIN(auxData_inicio[data_inicio_dados]) VAR filtro_emissor = ISFILTERED(dEmissor)  RETURN -- Retor` |
| `Média Saldo em Carteira` | `-- Saldo médio em carteiro -- Criado uma tabela temp para retirar saldos zerados e em branco     CALCULATE(         AVERAGE(fUser[saldo_atual]),         fUser[saldo_atual] > 0     )` |
| `Nome Emissor | Cadastro` | `-- Titulo da página correspondente ao titulo da fórmula [Nome Emissor] & " \| " & "CADASTROS"` |
| `Resgates` | `-- Soma a tabela de qtd_resgate da tabela fResgate, que faz a contagem distinta do codigo_referencia da tabela  CALCULATE(     SUM(fResgate[qtd_resgate]),     -- Filtro usado para considerar a data que o emissor gerou o primeiro crédito     FILTER(         DISTINCT( dCalendario[Data]),         dCale` |
| `Emissões` | `CALCULATE(     SUM(fEmissor_dia[qtd_emissao]),     -- Filtro usado para considerar a data que o emissor gerou o primeiro crédito     FILTER(         DISTINCT( dCalendario[Data] ),         dCalendario[Data] >= [Primeira Data de Credito]     ) )` |
| `Emissões Únicas Mês` | `VAR primeiro_dia_mes = DATE(YEAR([Primeira Data de Credito]), MONTH([Primeira Data de Credito]), 1)  RETURN     SUMX(         FILTER(             fEmissor_unico_mes,             fEmissor_unico_mes[data_transacao] >= primeiro_dia_mes         ), fEmissor_unico_mes[qtd_emissao_usuario_unico]     )` |
| `Valor Emissões` | `-- Soma o valor das emissões  -- A coluna vem de uma query agregada  CALCULATE(     SUM(fEmissor_dia[valor_emissao]),     -- Filtro usado para considerar a data que o emissor gerou o primeiro crédito         FILTER(         DISTINCT( dCalendario[Data] ),         dCalendario[Data] >= [Primeira Data d` |
| `Última Atualização` | `-- Retorna a data local considerando o fuso (-3) como última atualização do dashboard     LASTDATE(auxUltima_atualizacao[auxUltima_atualizacao]) - TIME(3,0,0)` |
| `%Taxa Emissores Únicas` | `-- Taxa usada no Funil de Conversão -- Calcula as emissões unicas sobre o total de emissões que foram emitidas     DIVIDE(          [Emissões Unicas Geral - TemFiltroData],          [Emissões]     )` |
| `% Taxa Cadastro` | `-- Taxa usada no Funil de Conversão -- Considera as total e usuarios cadastrados sobre as emissões unicas     DIVIDE(         [Base Total de Usuários Cadastrados (Sem Filtro de Data)],          [Emissões Únicas Geral]     )` |
| `Resgates Usuários Únicos Mês` | `-- Soma a tabela de qtd_resgate da tabela fResgate, que faz a contagem distinta do codigo_referencia da tabela SUM(fResgate_usuarios[qtd_usuarios])` |
| `Valor Resgates` | `CALCULATE(     SUM(fResgate[valor_resgate]),     -- Filtro usado para considerar a data que o emissor gerou o primeiro crédito     FILTER(         DISTINCT( dCalendario[Data] ),         dCalendario[Data] >= [Primeira Data de Credito]     ) )` |
| `% Taxa Resgate` | `-- Taxa usada no Funil de Conversão -- Calcula a quantidade de usuarios cadastrados que fizeram resgate --- A partir dos usuarios que resgataram e toda a base cadastrada     DIVIDE(         [Resgates Usuários Únicos Geral - TemFiltroData],         [Funil Usuários Cadastrados]     )` |
| `Nome Emissor` | `-- Retornar o nome do emissor para retornar nos titulos -- Uma possibilidade é retornar o titulo na planilha de RLS Cliente UPPER(MAX(auxRLS_user[parceiro]))` |
| `Nome Emissor | Emissões` | `-- Titulo da página correspondente ao titulo da fórmula [Nome Emissor] & " \| " & "EMISSÕES"` |
| `Nome Emissor | Resgates` | `-- Titulo da página correspondente ao titulo da fórmula [Nome Emissor] & " \| " & "RESGATES"` |
| `Nome Emissor | VBShop` | `-- Titulo da página correspondente ao titulo da fórmula [Nome Emissor] & " \| " & "VB SHOP"` |
| `Nome Emissor | Resultados Gerais` | `-- Titulo da página correspondente ao titulo da fórmula [Nome Emissor] & " \| " & "RESULTADOS GERAIS"` |
| `Email` | `-- Retorna o e-mail que está visualizando o dashbaord --- usado para filtrar a partir da tabela RLS Cliente     USERNAME()     -- USERPRINCIPALNAME()` |
| `Funil Usuários Cadastrados` | `-- Usado para filtar o MÊS no FUNIL IF(     ISFILTERED(dCalendario[MesAno]),     [Qtd Usuários Cadastrados],     [Base Total de Usuários Cadastrados (Sem Filtro de Data)] )` |
| `% Engajamento (Resgates x Novos Usuários)` | `// INDICADOR COM FILTRO VAR cadastrados_resgataram =      CALCULATE(         [Qtd Usuários Cadastrados],         fUser[resgate] = "Resgatou"     )  RETURN     DIVIDE(         cadastrados_resgataram,         [Qtd Usuários Cadastrados]     )` |
| `%Cohort` | `VAR cadastrados = [Qtd Usuários Cadastrados]  RETURN     IF(          MAX(fCohort[n_cohort]) >= 0,         SUM(fCohort[qtd_resgate]),         cadastrados     )` |
| `Cohort cor` | `IF(      MAX(fCohort[n_cohort]) >= 0,      IF( MAX(pCohort[pCohort]) = "%Cohort Acumulado",         SUM(fCohort[resgate_acumulado]),         SUM(fCohort[qtd_resgate])     ),     BLANK() )` |
| `Nome Emissor | Cohort` | `-- Titulo da página correspondente ao titulo da fórmula [Nome Emissor] & " \| " & "COHORT"` |
| `%Cohort Acumulado` | `VAR cadastrados = [Qtd Usuários Cadastrados]  RETURN     IF(          MAX(fCohort[n_cohort]) >= 0,         SUM(fCohort[resgate_acumulado]),         cadastrados     )` |
| `Filtro Data` | `[Resgates] + [Qtd Usuários Cadastrados]` |
| `Emissões Únicas Geral` | `SUM(fEmissor_unico_geral[qtd_usuario_unicos])` |
| `Base Total de Usuários Cadastrados (Sem Filtro de Data)` | `-- Possui somente o filtro de emissor, não filtra por data de cadastro     CALCULATE(         SUM(fUser[qtd_usuarios]),         ALL(dCalendario)     )` |
| `Emissões Unicas Geral - TemFiltroData` | `IF(         ISFILTERED(dCalendario[MesAno]),          [Emissões Únicas Mês],          [Emissões Únicas Geral]     )` |
| `% Taxa Cadastro - TemFiltroData` | `-- Taxa usada no Funil de Conversão -- Considera as total e usuarios cadastrados sobre as emissões unicas     DIVIDE(         [Funil Usuários Cadastrados],          -- Emissõe únicas por mÊs         [Emissões Unicas Geral - TemFiltroData]     )` |
| `Acessos únicos dia` | `SUM(fAcessos_dia[qt_usuarios])` |
| `Acessos únicos Geral` | `SUM(fAcessos_geral[qt_usuarios])` |
| `Acessos` | `SUM(fAcessos_dia[acessos])` |
| `Acessos únicos mês` | `SUM(fAcessos_unicos_mes[qt_usuarios])` |
| `Nome Emissor | Indicadores` | `-- Titulo da página correspondente ao titulo da fórmula [Nome Emissor] & " \| " & "INDICADORES"` |
| `Acessos únicos filtro data` | `VAR pData = MAX(pCalendario[pCalendario])  RETURN      SWITCH(         TRUE(),         pData = "Dia", [Acessos únicos dia],         pData = "Mês/Ano", [Acessos únicos mês],         BLANK()     )` |
| `Frequência de Acesso` | `DIVIDE(         CALCULATE(             [Acessos],             ALL(dCalendario)         ),         [Acessos únicos Geral]     )` |
| `Valor Pagamento` | `CALCULATE(     SUM(fResgate[valor_faturamento]),     -- Filtro usado para considerar a data que o emissor gerou o primeiro crédito     FILTER(         DISTINCT( dCalendario[Data] ),         dCalendario[Data] >= [Primeira Data de Credito]     ) )  // Medida Anterior // CALCULATE( //     SUMX( //     ` |
| `Média de Emissões por Usuário` | `DIVIDE(         [Emissões],         [Emissões Únicas Geral]     )` |
| `% Taxa Cadastro - TemFiltroData - pCadastro` | `IF(         MAX(pCadastros[pCadastros]) = "Novos Usuários",          [% Taxa Cadastro Novos Usuários],         [% Taxa Cadastro - TemFiltroData]     )` |
| `% Taxa Resgate - pCadastro` | `IF(         OR(             MAX(pCadastros[pCadastros]) = "Novos Usuários",             ISFILTERED(dCalendario[MesAno])         ),          DIVIDE(             [Resgates únicos Funil],             [Qtd Usuários Cadastrados]         ),         [% Taxa Resgate]     )` |
| `%Cohort Emission` | `SUM(fCohort_emissions[valor_cohort])` |
| `Cohort Emission cor` | `IF(      MAX(fCohort[n_cohort]) >= 0,      [%Cohort Emission],     BLANK() )` |
| `Resgates Novos Usuários` | `-- Considera somente os Novos usuarios CALCULATE(     [Resgates],     FILTER(         DISTINCT( fUser),         fUser[data_cadastro] >= [Primeira Data de Credito]     ) )` |
| `Usuários Resgates Novos Usuários` | `-- Considera somente os Novos usuarios CALCULATE(     [Resgates Usuários Únicos Mês],     FILTER(         DISTINCT( fUser),         fUser[data_cadastro] >= [Primeira Data de Credito]     ) )` |
| `Emissões Únicas Dia` | `CALCULATE(     SUM(fEmissor_dia[qtd_emissao_usuario_unico]),     -- Filtro usado para considerar a data que o emissor gerou o primeiro crédito     FILTER(         DISTINCT( dCalendario[Data] ),         dCalendario[Data] >= [Primeira Data de Credito]     ) )` |
| `Emissões Únicas pCalendario` | `IF(         MAX(pCalendario[pCalendario]) = "Dia",         [Emissões Únicas Dia],         [Emissões Únicas Mês]     )` |
| `Resgates Funil` | `IF(         OR(             MAX(pCadastros[pCadastros]) = "Novos Usuários",             ISFILTERED(dCalendario[MesAno])         ),         [Resgates Novos Usuários],         [Resgates]     )` |
| `Resgates únicos Funil` | `IF(         OR(             MAX(pCadastros[pCadastros]) = "Novos Usuários",             ISFILTERED(dCalendario[MesAno])         ),         [Usuários Resgates Novos Usuários],         [Resgates Usuários Únicos Geral]     )` |
| `% Taxa Cadastro Novos Usuários` | `-- CUIDADO -- -- [Qtd Usuários Cadastrados] tem filtro de data     DIVIDE(         [Qtd Usuários Cadastrados],          [Emissões Únicas Geral]     )` |
| `% Engajamento (Resgates / Acessos)` | `VAR acesso =      CALCULATE(         [Acessos],         dOrigem[Origem] <> "#Geral"     )  //    IF( //        ISFILTERED(dOrigem[Origem]), //        [Acessos], //        CALCULATE([Acessos únicos Geral], dOrigem[Origem] = "#Geral") //    )  RETURN     DIVIDE(         [Resgates],         acesso     ` |
| `% de Novos Usuários` | `DIVIDE(         [Qtd Usuários Cadastrados],          [Base Total de Usuários Cadastrados (Sem Filtro de Data)]     )` |
| `Valor Emissão Cadastrados` | `SUM(fEmissoes_cadastrados[valor_emissao])` |
| `Emissões Unicas Cdastrados / Emissões` | `DIVIDE(         SUM(fEmissoes_cadastrados[qtd_usuario_unicos]),         [Emissões]     )` |
| `Resgates / Emissões Unicas Cadastrados` | `DIVIDE(         [Resgates],         SUM(fEmissoes_cadastrados[qtd_usuario_unicos])     )` |
| `Valor Emissão Cadastrados / Valor Emissão` | `DIVIDE(         [Valor Emissão Cadastrados],         [Valor Emissões]     )` |
| `Valor resgate / Valor Emissão Cadastrados` | `DIVIDE(         [Valor Resgates],         [Valor Emissão Cadastrados]     )` |
| `Optin` | `SUM(fOptin[qtd_usuario_unicos])` |
| `Resgates Usuários Únicos Perfil` | `SUM(fResgate_perfil[qtd_usuarios])` |
| `Resgates Usuários Únicos Geral` | `CALCULATE(         SUM(fResgate_usuarios[qtd_usuarios]),          fResgate_usuarios[origem] = "#GERAL",         fResgate_usuarios[data_transacao] = DATE(1900, 01, 01),         ALL(dCalendario[Data])     )` |
| `Resgates Usuários Únicos Geral - TemFiltroData` | `IF(         ISFILTERED(dCalendario[MesAno]),          [Resgates Usuários Únicos Mês],          [Resgates Usuários Únicos Geral]     )` |

#### 📊 VB - vivo

---

##### Análise 1 — Fontes (tabelas) utilizadas

| Tabela PBI | Fonte (Databricks) | Tipo | Governança |
|---|---|---|---|
| `fresgates` | `bronze.valebonus.campaign` | Tabela bronze | 🔴 Vermelho |
| `fresgates` | `bronze.valebonus.offer` | Tabela bronze | 🔴 Vermelho |
| `fresgates` | `silver.giftback.bonus` | Tabela silver | 🟡 Amarelo |
| `fresgates` | `silver.valebonus.transactions` | Tabela silver | 🟡 Amarelo |
| `fusuarios` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fusuarios` | `silver.valebonus.usuarios` | Tabela silver | 🟡 Amarelo |
| `dEmissor` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `dEmissor` | `silver.valebonus.usuarios` | Tabela silver | 🟡 Amarelo |
| `d_aux_ultimo_emissor` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `d_aux_ultimo_emissor` | `silver.valebonus.usuarios` | Tabela silver | 🟡 Amarelo |
| `fPreCharge` | `gold.valebonus.emissoes_vivo_bi` | Tabela gold | ✅ Verde |
| `f_aux_numeros_validados` | `bronze.valebonus.cellphone` | Tabela bronze | 🔴 Vermelho |
| `f_aux_numeros_validados` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fVBShop` | `silver.valebonus.p5` | Tabela silver | 🟡 Amarelo |
| `fVBShop` | `silver.valebonus.transactions` | Tabela silver | 🟡 Amarelo |
| `fPreRecargaDt` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fSemCadastro` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fSemCadastro` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fSemCadastro` | `silver.valebonus.usuarios` | Tabela silver | 🟡 Amarelo |
| `fEmissao` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fEmissaoMes` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |

**Scorecard: ✅ 6 · 🟡 8 · 🔴 7 → 🔴 Crítico**


##### SQL por fonte


**`fresgates`**
```sql
let
 
Query =
    "
WITH creditos_vivo AS (
    SELECT
        usuario_referencia AS codigo_usuario,
        nome_emissor,
        codigo_emissor,
        data_transacao,
        data_hora_transacao
    FROM silver.valebonus.transactions
    WHERE tipo_transacao = 'CRÉDITO'
      AND nome_emissor LIKE 'VIVO%'
),

campanha_info AS (
    SELECT 
        o.id AS codigo_oferta,
        c.title AS campanha,
        c.id AS codigo_campanha,
        c.price AS valor_campanha
    FROM bronze.valebonus.offer o
    LEFT JOIN bronze.valebonus.campaign c 
        ON o.id = c.offerId
),

ranked_transactions AS (
    SELECT 
        re.codigo_referencia AS codigo_transacao,
        re.usuario_referencia AS codigo_usuario,
        re.nome_resgate,
        ABS(re.valor_transacao) AS valor_transacao,
        re.data_transacao,
        re.valor_pagamento,

        cr.nome_emissor,

        b.marca,
        re.tipo_resgate,
        ci.campanha,
        ci.valor_campanha,

        cr.data_transacao AS data_credito_vivo,
        cr.data_hora_transacao AS data_hora_credito_vivo,

        ROW_NUMBER() OVER (
            PARTITION BY re.codigo_referencia
            ORDER BY
                cr.data_hora_transacao DESC,
                cr.data_transacao DESC,
                cr.nome_emissor DESC,
                cr.codigo_emissor DESC,
                re.data_hora_transacao DESC
        ) AS rn

    FROM silver.valebonus.transactions re

    INNER JOIN creditos_vivo cr 
        ON re.usuario_referencia = cr.codigo_usuario
       AND cr.data_hora_transacao <= re.data_hora_transacao

    LEFT JOIN campanha_info ci 
        ON re.campaignId = ci.codigo_campanha 

    LEFT JOIN silver.giftback.bonus b 
        ON b.bonus_id = re.codigo_bonus

    WHERE re.tipo_transacao = 'DÉBITO'
      AND re.situacao_checkout = 'APPROVED'
      AND re.tipo_resgate = 'OFFER'
      AND re.data_transacao >= DATE('2023-12-01')
)

SELECT 
    codigo_transacao,
    codigo_usuario,
    nome_resgate,
    valor_transa
```

**`fusuarios`**
```sql
let
 
Query =
    "
    select distinct 
       us.codigo_usuario
      ,us.tipo_genero
      ,us.data_cadastro      
      --,us.saldo_atual
      --,upper(us.nome_cidade) as nome_cidade
      --,upper(us.nome_estado) as nome_estado
      --,us.flag_excluido
from silver.valebonus.usuarios us
join gold.valebonus.emissions  em on us.codigo_usuario = em.codigo_usuario
where em.nome_emissor like 'VIVO%'
AND
us.data_cadastro >= '2025-01-01'
    ",
Fonte = Databricks.Query(host, db) ( Query)
 
in
Fonte
```

**`dEmissor`**
```sql
let
 
Query =
    "
     with cte as (
    select
      nome_emissor,
      data_referencia,
      codigo_usuario,
      valor_transacao,
      codigo_emissor,
      data_hora_transacao
    from gold.valebonus.emissions
    where nome_emissor like 'VIVO%'
    ), cte2 as (
        select
        codigo_usuario,max(data_hora_transacao) as data_hora_transacao
        from cte
        group by codigo_usuario
        )
        select distinct c.codigo_usuario
        ,c.nome_emissor
        --,t.data_hora_transacao        
        from cte   c
        join cte2  t   on c.codigo_usuario = t.codigo_usuario 
        and c.data_hora_transacao = t.data_hora_transacao
        LEFT JOIN silver.valebonus.usuarios u on c.codigo_usuario = u.codigo_usuario
    ",
Fonte = Databricks.Query(host, db) ( Query)
 
in
Fonte
```

**`d_aux_ultimo_emissor`**
```sql
let
 
Query =
    "
    -- CTE para selecionar todas as transações de emissões da VIVO
WITH cte AS (
    SELECT 
        nome_emissor, 
        data_transacao,  
        codigo_usuario, 
        valor_transacao, 
        codigo_emissor, 
        data_hora_transacao
    FROM gold.valebonus.emissions
    WHERE nome_emissor LIKE 'VIVO %'
),

-- CTE para selecionar a última data e hora de transação para cada usuário antes da data de cadastro
cte2 AS (
    SELECT
        c.codigo_usuario,
        MAX(c.data_hora_transacao) AS data_hora_transacao
    FROM cte c
    JOIN silver.valebonus.usuarios u ON c.codigo_usuario = u.codigo_usuario
    WHERE c.data_hora_transacao < u.data_cadastro
    GROUP BY c.codigo_usuario
),

-- CTE para selecionar a data e hora de transação mais próxima para cada usuário, caso não haja uma antes da data de cadastro
cte3 AS (
    SELECT
        c.codigo_usuario,
        MIN(c.data_hora_transacao) AS data_hora_transacao
    FROM cte c
    JOIN silver.valebonus.usuarios u ON c.codigo_usuario = u.codigo_usuario
    WHERE c.data_hora_transacao >= u.data_cadastro
    GROUP BY c.codigo_usuario
),

-- CTE para combinar as duas lógicas anteriores
cte_final AS (
    SELECT
        COALESCE(t2.codigo_usuario, t3.codigo_usuario) AS codigo_usuario,
        COALESCE(t2.data_hora_transacao, t3.data_hora_transacao) AS data_hora_transacao
    FROM cte2 t2
    FULL OUTER JOIN cte3 t3 ON t2.codigo_usuario = t3.codigo_usuario
),

-- CTE para selecionar informações de usuários e suas últimas transações
cte_final_info AS (
    SELECT DISTINCT 
        c.codigo_usuario,
        c.nome_emissor,
        u.nome_cidade,
        u.nome_estado
    FROM cte c
    JOIN cte_final t ON c.codigo_usuario = t.codigo_usuario AND c.data_hora_transacao = t.data_hora_transacao
    LEFT JOIN silver.valebonus.usuarios u ON c.codigo_usuario = u.codigo_usuario
),
CTE_Distinta AS (

-- Query principal para selecionar informações de usuários únicos
SELECT 
    codigo_usuario,
    nome_emiss
```

**`fPreCharge`**
```sql
let
 
Query =
    "
     SELECT
  --mailingId,
  cast(data as date) as data,
  cpfs_pr_emissao
  --cpfs_unicos,
  --emissoes,
  --telefones_enviados
FROM gold.valebonus.emissoes_vivo_bi
    ",
Fonte = Databricks.Query(host, db) ( Query)
 
in
Fonte
```

**`f_aux_numeros_validados`**
```sql
let
 
Query =
    "
with emissor as (
 SELECT
   codigo_usuario,
   codigo_emissor,
   nome_emissor
 FROM gold.valebonus.emissions
 )
  SELECT
    cast(cp.createdAt as date) as data,
    COUNT(distinct cp.number) AS qtd_numeros_validados
  FROM bronze.valebonus.cellphone cp
  LEFT JOIN emissor em ON em.codigo_usuario = cp.consumerId
  WHERE nome_emissor LIKE 'VIVO%'
  group by 1
    ",
Fonte = Databricks.Query(host, db) ( Query)
 
in
Fonte
```

**`fVBShop`**
```sql
let
 
Query =
    "
     select 
    codigo_usuario,
  --codigo_refrencia ,
  date(data_transacao) as data_transacao,
  -valor_transacao as valor_transacao,  
  nome_resgate as nome_resgate,
  valor_pagamento as valor_pagamento,  
  situacao_checkout as situacao_checkout,
  'DÉBITO' as tipo_transacao,  
  nome_aplicacao
from silver.valebonus.p5
where nome_aplicacao like '%Vivo%'

UNION ALL

SELECT 
    usuario_referencia as codigo_usuario,
  --codigo_referencia,
  data_transacao,
  valor_transacao,
  nome_resgate,
  valor_pagamento,   
  situacao_checkout,
  tipo_transacao,  
  nome_aplicacao
FROM silver.valebonus.transactions 
WHERE  data_transacao >= '2025-01-01'
and tipo_transacao = 'DÉBITO'
and lower(nome_aplicacao) like '%vivo%'
and tipo_resgate = 'PRODUCT (P4)'
    ",
Fonte = Databricks.Query(host, db) ( Query)
 
in
Fonte
```

**`fPreRecargaDt`**
```sql
let
 
Query =
    "
     WITH cte as (   
   SELECT 
    additionalReferenceKey, 
    MIN(CAST(createdAt AS DATE)) AS dt_min 
  FROM 
    bronze.valebonus.pre_charge_transaction 
  WHERE
    type ILIKE '%VIVO%'
    AND DATE(createdAt) >= '2025-01-01'
    
  GROUP BY 
    additionalReferenceKey
)
select count(additionalReferenceKey) as qtd_emissao,
dt_min
from cte
GROUP BY 2
    ",
Fonte = Databricks.Query(host, db) ( Query)
 
in
Fonte
```

**`fSemCadastro`**
```sql
let
 
Query =
    "
    with cadastro as (
select
 distinct pr.additionalReferenceKey
from gold.valebonus.emissions e
join silver.valebonus.usuarios us on us.codigo_usuario = e.codigo_usuario
join bronze.valebonus.pre_charge_transaction pr ON pr.walletTransactionId = e.codigo_transacao
where nome_emissor ilike 'vivo%'
and us.data_cadastro >= '2025-01-01'
)
, sem_cadastro as (
  select 
    distinct pr.additionalReferenceKey,
    MIN(CAST(pr.createdAt AS DATE)) AS dt_min 
  from bronze.valebonus.pre_charge_transaction pr
  left join cadastro c on pr.additionalReferenceKey = c.additionalReferenceKey
  where c.additionalReferenceKey is null
  and pr.type LIKE 'VIVO'
  group by 1
)
select
  count(additionalReferenceKey) as qtd_emissao,
  dt_min
from sem_cadastro
group by 2
    ",
Fonte = Databricks.Query(host, db) ( Query)
 
in
Fonte
```

**`fEmissao`**
```sql
let
 
Query =
    "
     with cte as (SELECT 
    additionalReferenceKey, 
    key as TERMINAL,
    CAST(createdAt AS DATE) AS data ,
    mailingId
  FROM 
    bronze.valebonus.pre_charge_transaction 
  WHERE
    type = 'VIVO'
    AND DATE(createdAt) >= '2025-01-01'
    and amount > 1
  
), resultado as (
select
  additionalReferenceKey,
  CONCAT_WS(', ', COLLECT_LIST(TERMINAL)) AS TERMINAIS,
  data 
  from cte
  group by 1, 3
)
select 
  count(additionalReferenceKey) as qtd_emissao,
  data  
  from resultado
  group by 2
    ",
Fonte = Databricks.Query(host, db) ( Query)
 
in
Fonte
```

**`fEmissaoMes`**
```sql
// let
//     Source = Value.NativeQuery(Databricks.Catalogs("adb-7976517966788827.7.azuredatabricks.net", "/sql/1.0/warehouses/1042454831133080", [Catalog="bronze", Database=null, EnableAutomaticProxyDiscovery=null]){[Name="bronze",Kind="Database"]}[Data], "WITH cte AS (#(lf)  SELECT #(lf)    additionalReferenceKey, #(lf)    CASE #(lf)      WHEN DATE_TRUNC('month', CAST(createdAt AS DATE)) = '2023-12-01' THEN '2023-12-07'#(lf)      ELSE DATE_TRUNC('month', CAST(createdAt AS DATE))#(lf)    END AS month_data#(lf)  FROM #(lf)    bronze.valebonus.pre_charge_transaction #(lf)  WHERE#(lf)    type ILIKE '%VIVO%'#(lf)    AND DATE(createdAt) >= '2023-12-07'#(lf)    AND amount > 1#(lf))#(lf)SELECT #(lf)  COUNT(DISTINCT additionalReferenceKey) AS distinct_additionalReferenceKey,#(lf)  CAST(month_data AS DATE)#(lf)FROM #(lf)  cte#(lf)GROUP BY #(lf)  month_data", null, [EnableFolding=true])
// in
//     Source



let
 
Query =
    "
     WITH cte AS (
  SELECT 
    additionalReferenceKey, 
    CASE 
      WHEN DATE_TRUNC('month', CAST(createdAt AS DATE)) = '2025-01-01' THEN '2025-01-01'
      ELSE DATE_TRUNC('month', CAST(createdAt AS DATE))
    END AS month_data
  FROM 
    bronze.valebonus.pre_charge_transaction 
  WHERE
    type ILIKE '%VIVO%'
    AND DATE(createdAt) >= '2025-01-01'
    AND amount > 1
)
SELECT 
  COUNT(DISTINCT additionalReferenceKey) AS distinct_additionalReferenceKey,
  CAST(month_data AS DATE)
FROM 
  cte
GROUP BY 
  month_data
    ",
Fonte = Databricks.Query(host, db) ( Query)
 
in
Fonte
```

##### Análise 2 — Medidas DAX (25 medidas)

| Medida | Expressão DAX |
|---|---|
| `Qtd usuários` | `DISTINCTCOUNT ( fVBShop[codigo_usuario] )` |
| `Qtd Pedidos` | `COUNTROWS( fVBShop )` |
| `Qtd Resgates` | `CALCULATE (     COUNTROWS( fVBShop ),     fVBShop[tipo_transacao] = "DÉBITO",     fVBShop[situacao_checkout] IN { "APPROVED", "PAGO" } )` |
| `Valor_Pagamento total` | `CALCULATE (     SUM ( fVBShop[valor_pagamento] ),     fVBShop[tipo_transacao] = "DÉBITO",     fVBShop[situacao_checkout] IN { "APPROVED", "PAGO" } )` |
| `Valor Resgates Total` | `CALCULATE (     ABS ( SUM ( fVBShop[valor_transacao] ) ),     fVBShop[tipo_transacao] = "DÉBITO",     fVBShop[situacao_checkout] IN { "APPROVED", "PAGO" } )` |
| `Qtd Resgates total` | `CALCULATE (     COUNTROWS( fVBShop ),     fVBShop[tipo_transacao] = "DÉBITO",     fVBShop[situacao_checkout] = "APPROVED" )` |
| `media emissao/usuarios` | `DIVIDE ( [_qtd_emissao], [pre recarga unico] )` |
| `pre recarga unico` | `SUM ( fPreRecargaDt[qtd_emissao] )` |
| `_qtdeCadastros` | `DISTINCTCOUNT ( fusuarios[codigo_usuario] )` |
| `_SemCadastro` | `SUM ( fSemCadastro[qtd_emissao] )` |
| `_engajamento` | `DIVIDE ( [_qtdeUsuariosResgates], [_qtdeCadastros], 0 )` |
| `_qtdeResgates` | `CALCULATE (     //DISTINCTCOUNT ( fresgates[codigo_transacao] ),     COUNTROWS(fresgates),     NOT ( fresgates[nome_resgate] IN { "TESTE ONBOARDING 2", "Loja Teste" } ),     NOT ( fresgates[nome_emissor] IN { "VIVO", "VIVO GO" } ),     NOT ( ISBLANK ( fresgates[nome_resgate] ) ) )` |
| `_qtdeResgatesOferta` | `--DISTINCTCOUNT ( fresgates[codigo_transacao] ) COUNTROWS(fresgates)` |
| `Média de Resgates p User` | `DIVIDE ( [_qtdeResgates], [_qtdeUsuariosResgates] )` |
| `_valorTotal` | `SUM ( fresgates[valor_transacao] )` |
| `_qtd_emissao_mes1` | `SUM ( fEmissaoMes[distinct_additionalReferenceKey] )` |
| `_qtd_emissao` | `SUM ( fEmissao[qtd_emissao] )` |
| `Valor Resgate` | `ABS ( SUM ( fresgates[valor_transacao] ) )` |
| `telefone_valido` | `SUM ( f_aux_numeros_validados[qtd_numeros_validados] )` |
| `prim_emissao` | `SUM ( fPreCharge[cpfs_pr_emissao] )` |
| `_qtdeUsuariosResgates` | `DISTINCTCOUNT ( fresgates[codigo_usuario] )` |
| `_qtdeResgatesEmissor` | `CALCULATE (     DISTINCTCOUNT ( fresgates[codigo_transacao] ),     --COUNTROWS(fresgates),     FILTER (         fresgates,         NOT ( fresgates[nome_resgate] IN { "TESTE ONBOARDING 2", "Loja Teste" } )             && NOT ( fresgates[nome_emissor] IN { "VIVO", "VIVO GO" } )                 && NOT ` |
| `Valor Campanha` | `SUM(fresgates[valor_campanha])` |
| `Validacao_resgates_tier_modelo` | `CALCULATETABLE (     SUMMARIZECOLUMNS (         fresgates[nome_emissor],         "Abr_2026",             DISTINCTCOUNT ( fresgates[codigo_transacao] )     ),     fresgates[data_transacao] >= DATE ( 2026, 4, 1 ),     fresgates[data_transacao] <= DATE ( 2026, 4, 30 ),     fresgates[nome_emissor] IN { ` |
| `Validacao_resgates_tier_abril` | `CALCULATETABLE (     SUMMARIZECOLUMNS (         fresgates[nome_emissor],         "Qtd_Resgates_Emissor",             DISTINCTCOUNT ( fresgates[codigo_transacao] )     ),     fresgates[data_transacao] >= DATE ( 2026, 4, 1 ),     fresgates[data_transacao] <= DATE ( 2026, 4, 30 ),     fresgates[nome_em` |

#### 📊 VB - vivo_acompanhamento_de_meta

---

##### Análise 1 — Fontes (tabelas) utilizadas

| Tabela PBI | Fonte (Databricks) | Tipo | Governança |
|---|---|---|---|
| `fEmissoes_unicas_acumuladas` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fEmissoes_unicas_acumuladas` | `silver.ext_bases.vivo_vale_bonus_usuarios_extras` | Tabela silver | 🟡 Amarelo |
| `fResgates_acumulados` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fResgates_acumulados` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fResgates_acumulados` | `silver.ext_bases.vivo_vale_bonus_usuarios_extras` | Tabela silver | 🟡 Amarelo |
| `fResgates_acumulados` | `silver.valebonus.p5` | Tabela silver | 🟡 Amarelo |
| `fResgates_acumulados` | `silver.valebonus.transactions` | Tabela silver | 🟡 Amarelo |
| `fResgates_tier` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fResgates_tier` | `gold.valebonus.emissions` | Tabela gold | ✅ Verde |
| `fResgates_tier` | `silver.ext_bases.vivo_vale_bonus_usuarios_extras` | Tabela silver | 🟡 Amarelo |
| `fResgates_tier` | `silver.valebonus.p5` | Tabela silver | 🟡 Amarelo |
| `fResgates_tier` | `silver.valebonus.transactions` | Tabela silver | 🟡 Amarelo |
| `fEmissoes_acumuladas` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fEmissoes_acumuladas` | `silver.ext_bases.vivo_vale_bonus_usuarios_extras` | Tabela silver | 🟡 Amarelo |
| `fMailing` | `bronze.valebonus.pre_charge_transaction` | Tabela bronze | 🔴 Vermelho |
| `fMailing` | `bronze.valebonus.wallet_transaction` | Tabela bronze | 🔴 Vermelho |
| `fMailing` | `silver.valebonus.usuarios` | Tabela silver | 🟡 Amarelo |

**Scorecard: ✅ 2 · 🟡 9 · 🔴 6 → 🔴 Crítico**


##### SQL por fonte


**`fEmissoes_unicas_acumuladas`**
```sql
let
    Query = 
    "
    WITH 
        emissao AS (
            SELECT DISTINCT
            pr.additionalReferenceKey AS CPF, 
            --pr.walletTransactionId,
            --TRUNC(pr.createdAt, 'MM') AS mes
            TRUNC(
                CASE WHEN dataMalingVivo LIKE '%/%' THEN to_date(dataMalingVivo, 'dd/M/yyyy') ELSE DATE(dataMalingVivo) END
                , 'MM') AS mes
            FROM bronze.valebonus.pre_charge_transaction pr
            WHERE pr.applicationId IN (
                'c89d6c9d-7b52-4322-90d3-fa024f376e44', --  VIVO 1
                'dd3e3ad9-9d9a-44b0-860c-7cc057cc6bbf', --  VIVO 2
                '2763fdd0-9c5e-44e7-965a-f40173eedc4c', -- VIVO 3
                '714b7d81-889e-4710-ac38-e2fa668f3e67', -- VIVO 4
                '62be8eb8-212c-4e88-b94f-8910f4dcd1f0' -- VIVO 5
            )
            AND pr.amount > 1
            AND LOWER(pr.reason) LIKE '%vencimento%'
            AND TRUNC(pr.createdAt, 'MM') >= '2025-01-01'
            AND pr.additionalReferenceKey NOT IN (SELECT CPF FROM silver.ext_bases.vivo_vale_bonus_usuarios_extras)
        ),
        datas_analise AS (
            -- Gera os meses de análise (últimos N meses, aqui como exemplo últimos 6 meses)
            SELECT DISTINCT
            mes
            ,add_months(mes, -2) AS mes_inicio
            --,add_months(mes, -1) AS mes_fim
            FROM emissao
        )
        SELECT
        d.mes,
        COUNT(DISTINCT CPF) AS qtd_emissao_unica
        FROM datas_analise d
        LEFT JOIN emissao e ON e.mes BETWEEN d.mes_inicio AND d.mes
        WHERE d.mes >= '2025-04-01'
        GROUP BY 1
    ",
  Fonte = Databricks.Query(host, db)(Query)
in
  Fonte
```

**`fResgates_acumulados`**
```sql
let
    Query =
        "
        -- Quantidade de usuários que resgataram no mês atual que receberam emissões nos mes atual e nos últimos 3 meses
        -- Quantidade total de resgates de usuários que resgataram no mês atual que receberam emissões nos mes atual e nos últimos 3 meses

        WITH 
        --  USUARIOS QUE RECEBERAM EMISSÃO NOS ÚLTIMOS 3 MESES (REFERENCIA AO MES SELECIONADO)
        user_emissao AS (
        WITH
        emissao AS (
            SELECT 
            pr.additionalReferenceKey AS CPF, 
            pr.walletTransactionId,
            --TRUNC(pr.createdAt, 'MM') AS mes
            TRUNC(
                CASE WHEN dataMalingVivo LIKE '%/%' THEN to_date(dataMalingVivo, 'dd/M/yyyy') ELSE DATE(dataMalingVivo) END
                , 'MM') AS mes
            FROM bronze.valebonus.pre_charge_transaction pr
            WHERE pr.applicationId IN (
                'c89d6c9d-7b52-4322-90d3-fa024f376e44', --  VIVO 1
                'dd3e3ad9-9d9a-44b0-860c-7cc057cc6bbf', --  VIVO 2
                '2763fdd0-9c5e-44e7-965a-f40173eedc4c', -- VIVO 3
                '714b7d81-889e-4710-ac38-e2fa668f3e67', -- VIVO 4
                '62be8eb8-212c-4e88-b94f-8910f4dcd1f0' -- VIVO 5
            )
            AND pr.amount > 1
            AND pr.additionalReferenceKey IS NOT NULL
            AND LOWER(pr.reason) LIKE '%vencimento%'
            AND TRUNC(pr.createdAt, 'MM') >= '2025-01-01'
            AND pr.additionalReferenceKey NOT IN (SELECT CPF FROM silver.ext_bases.vivo_vale_bonus_usuarios_extras)
        ),
        datas_analise AS (
            -- Gera os meses de análise (últimos N meses, aqui como exemplo últimos 6 meses)
            SELECT DISTINCT
            mes AS mes_base
            FROM emissao
        ),
        emissoes_agregado AS (
            SELECT *
            FROM datas_analise d
            LEFT JOIN emissao e ON e.mes BETWEEN add_months(d.mes_base, -2) AND mes_base
        )
        SELECT DISTINCT
            eg.mes_base,
        
```

**`fResgates_tier`**
```sql
let
    Query =
        "
        -- Quantidade de usuários que resgataram (total)
        WITH 
            tier AS (
            WITH
                users AS (
                SELECT DISTINCT
                    e.codigo_usuario,
                    MAX(e.data_hora_transacao) as data
                FROM gold.valebonus.emissions e   
                    JOIN bronze.valebonus.pre_charge_transaction pct on e.codigo_transacao = pct.walletTransactionId
                WHERE e.codigo_emissor in (
                    'c89d6c9d-7b52-4322-90d3-fa024f376e44', -- VIVO 1
                    'dd3e3ad9-9d9a-44b0-860c-7cc057cc6bbf', -- VIVO 2
                    '2763fdd0-9c5e-44e7-965a-f40173eedc4c', -- VIVO 3
                    '714b7d81-889e-4710-ac38-e2fa668f3e67', -- VIVO 4
                    '62be8eb8-212c-4e88-b94f-8910f4dcd1f0' -- VIVO 5
                )
                    AND pct.additionalReferenceKey IS NOT NULL
                    AND LOWER(pct.reason) LIKE '%vencimento%'
                    AND pct.additionalReferenceKey NOT IN (SELECT CPF FROM silver.ext_bases.vivo_vale_bonus_usuarios_extras)
                GROUP BY 1
                )
            SELECT DISTINCT
                u.codigo_usuario,
                e.nome_emissor AS tier
            FROM users u
                --LEFT JOIN bronze.valebonus.pre_charge_transaction pct ON u.CPF = pct.additionalReferenceKey
                JOIN gold.valebonus.emissions e ON e.codigo_usuario = u.codigo_usuario AND u.data = e.data_hora_transacao
            WHERE e.codigo_emissor in (
                    'c89d6c9d-7b52-4322-90d3-fa024f376e44', -- VIVO 1
                    'dd3e3ad9-9d9a-44b0-860c-7cc057cc6bbf', -- VIVO 2
                    '2763fdd0-9c5e-44e7-965a-f40173eedc4c', -- VIVO 3
                    '714b7d81-889e-4710-ac38-e2fa668f3e67', -- VIVO 4
                    '62be8eb8-212c-4e88-b94f-8910f4dcd1f0' -- VIVO 5
                )
            ),
            base_resgates as (
            SELECT  
      
```

**`fEmissoes_acumuladas`**
```sql
let
    Query =
        "
        WITH 
            emissao AS (
                SELECT 
                --pr.additionalReferenceKey AS CPF, 
                pr.walletTransactionId,
                --TRUNC(pr.createdAt, 'MM') AS mes
                TRUNC(
                    CASE WHEN dataMalingVivo LIKE '%/%' THEN to_date(dataMalingVivo, 'dd/M/yyyy') ELSE DATE(dataMalingVivo) END
                    , 'MM') AS mes
                FROM bronze.valebonus.pre_charge_transaction pr
                WHERE pr.applicationId IN (
                    'c89d6c9d-7b52-4322-90d3-fa024f376e44', --  VIVO 1
                    'dd3e3ad9-9d9a-44b0-860c-7cc057cc6bbf', --  VIVO 2
                    '2763fdd0-9c5e-44e7-965a-f40173eedc4c', -- VIVO 3
                    '714b7d81-889e-4710-ac38-e2fa668f3e67', -- VIVO 4
                    '62be8eb8-212c-4e88-b94f-8910f4dcd1f0' -- VIVO 5
                )
                AND pr.amount > 1
                AND LOWER(pr.reason) LIKE '%vencimento%'
                AND TRUNC(pr.createdAt, 'MM') >= '2025-01-01'
            AND pr.additionalReferenceKey NOT IN (SELECT CPF FROM silver.ext_bases.vivo_vale_bonus_usuarios_extras)
            ),
            datas_analise AS (
                -- Gera os meses de análise (últimos N meses, aqui como exemplo últimos 6 meses)
                SELECT DISTINCT
                mes
                ,add_months(mes, -2) AS mes_inicio
                --,add_months(mes, -1) AS mes_fim
                FROM emissao
            ),
            qtd_emissao AS (
                SELECT
                mes,
                COUNT(*) AS qtd_emissao
                FROM emissao
                GROUP BY 1
            ),
            emissoes_agregado AS (
                SELECT
                d.mes,
                SUM(qtd_emissao) AS qtd_emissao
                FROM datas_analise d
                LEFT JOIN qtd_emissao e ON e.mes BETWEEN d.mes_inicio AND d.mes
                GROUP BY 1
            )
            SELECT *
   
```

**`fMailing`**
```sql
let
  Query =
    "
    WITH
      emissao AS (
        SELECT 
          additionalReferenceKey,
          walletTransactionId,
          dataMalingVivo,
          reason
        FROM bronze.valebonus.pre_charge_transaction
        WHERE
          applicationId in (
              'c89d6c9d-7b52-4322-90d3-fa024f376e44',
              'dd3e3ad9-9d9a-44b0-860c-7cc057cc6bbf',
              '2763fdd0-9c5e-44e7-965a-f40173eedc4c',
              '714b7d81-889e-4710-ac38-e2fa668f3e67',
              '62be8eb8-212c-4e88-b94f-8910f4dcd1f0',
              'edc044f7-011d-46ec-829c-35914a88fd6e',
              '434c239b-957e-4748-8e0d-10b490afe48a'
          )
      ),
      carterias AS (
        SELECT 
          a.additionalReferenceKey,
          MAX(c.codigo_usuario) AS codigo_usuario,
          MAX(c.data_cadastro)  AS data_cadastro
        FROM 
          emissao                                   a
          JOIN bronze.valebonus.wallet_transaction  b ON a.walletTransactionId = b.id
          JOIN silver.valebonus.usuarios            c ON b.walletId = c.codigo_carteira
        GROUP BY 
          a.additionalReferenceKey
      ),
      base AS (
          SELECT 
            try_to_date(a.dataMalingVivo , 'd/M/yyyy')  AS data_de_recarga, 
            a.reason, 
            a.additionalReferenceKey,
            b.data_cadastro,
            a.walletTransactionId
        FROM emissao a
          LEFT JOIN carterias b ON a.additionalReferenceKey = b.additionalReferenceKey
      ),
      cadastro AS (
        SELECT
          additionalReferenceKey,
          data_cadastro,
          data_de_recarga,
          reason,
          ROW_NUMBER() OVER (
              PARTITION BY additionalReferenceKey, data_cadastro
              ORDER BY data_de_recarga DESC
          ) AS rn
        FROM base
        WHERE data_de_recarga <= data_cadastro
      ),
      metricas AS (
        SELECT 
          a.data_de_recarga, 
          a.reason, 
          COUNT(DISTINCT a.additionalReference
```

##### Análise 2 — Medidas DAX (4 medidas)

| Medida | Expressão DAX |
|---|---|
| `Engajamento` | `DIVIDE(         SUM(fResgates_acumulados[qtd_resgates_unicos]),         SUM(fEmissoes_unicas_acumuladas[qtd_emissao_unica])     )` |
| `Indicador` | `VAR perc = 	DIVIDE( 		[Engajamento], 		MAX(dIndicador[Indicador]) 	)  RETURN 	IF( perc > 1, 1, perc) * 100` |
| `Última Atualização` | `-- Retorna a data local considerando o fuso (-3) como última atualização do dashboard VAR Data = LASTDATE(auxUltima_atualizacao[auxUltima_atualizacao]) - TIME(3,0,0) VAR Data_txt = FORMAT(Data, "dd/mm/yyyy hh:mm:ss")  RETURN  "Última Atualização: " & Data_txt` |
| `Aux_indicador` | `1` |

#### 📊 Scorecard ValeBonus

| Dashboard | Nível | ✅ Verde | 🟡 Amarelo | 🔴 Vermelho |
|---|---|---|---|---|
| VB - Ifood Embedded | 🔴 Crítico | 2 | 4 | 4 |
| VB - parceiros_interno | 🔴 Crítico | 22 | 1 | 4 |
| VB - vale_bonus_parceiros | 🔴 Crítico | 27 | 5 | 11 |
| VB - vivo | 🔴 Crítico | 6 | 8 | 7 |
| VB - vivo_acompanhamento_de_meta | 🔴 Crítico | 2 | 9 | 6 |

## Governança das Fontes dos Metric Views

> Análise das fontes reais de cada Metric View listado na seção anterior. Views e CTEs foram rastreadas até sua tabela de origem para classificação real. Coleta via `DESCRIBE EXTENDED` + `SHOW CREATE TABLE` no Databricks (2026-06-06).

**Critérios:**
- ✅ **Verde** — fonte é tabela gold, acesso direto (ideal)
- 🟡 **Amarelo** — fonte silver, ou gold via view intermediária (aceitável com ressalva)
- 🔴 **Vermelho** — fonte bronze, ou cadeia que inclui bronze (ação necessária)

---

### 📊 Scorecard por Avenida

| Avenida | Total MVs | ✅ Verde | 🟡 Amarelo | 🔴 Vermelho | Nível |
|---|---|---|---|---|---|
| ads | 5 | 3 | 1 | 1 | 🟠 Moderado |
| crmback | 3 | 3 | 0 | 0 | ✅ Ideal |
| giftback | 10 | 0 | 9 | 1 | 🟡 Atenção |
| presenteia | 15 | 0 | 0 | 15 | 🔴 Crítico |
| valebonus | 5 | 3 | 1 | 1 | 🟠 Moderado |
| **Total** | **38** | **9** | **11** | **18** | 🔴 Crítico |

---

### 🔍 ADS — Governança das Fontes

| Metric View | Tipo de Acesso | Fonte Primária | Fonte Real (rastreada) | Governança | Ação |
|---|---|---|---|---|---|
| `mv_ads_bonification` | Tabela direta | `gold.ads.b2b_bonification` | — | ✅ Verde | Nenhuma |
| `mv_ads_message` | Tabela direta | `gold.ads.b2b_message` | — | ✅ Verde | Nenhuma |
| `mv_ads_offer` | Tabela direta | `gold.ads.b2b_offer` | — | ✅ Verde | Nenhuma |
| `mv_ads_resgate` | View gold | `gold.ads.vw_ads_resgate` | `gold.ads.b2b_bonification` + `silver.giftback.bonus` | 🟡 Amarelo | Materializar `vw_ads_resgate` como tabela gold pura; eliminar dependência de `silver.giftback.bonus` |
| `mv_ads_receita_ads` | SQL / CTE | — | `gold.ads.cpl_cpi_cpa`, `gold.ads.b2b_offer`, `gold.ads.b2b_bonification`, `gold.ads.arm_receita_incremental` + ⚠️ `bronze.giftback.bonus`, `bronze.giftback.brands`, `bronze.giftback.orders`, `silver.giftback.bonus` | 🔴 Vermelho | Promover `bronze.giftback.bonus/brands/orders` para gold. Reescrever CTE usando fontes gold |

---

### 🔍 CRMBACK — Governança das Fontes

| Metric View | Tipo de Acesso | Fonte Primária | Fonte Real (rastreada) | Governança | Ação |
|---|---|---|---|---|---|
| `mv_carrinhos` | Tabela direta | `gold.crmback.tb_carrinhos` | — | ✅ Verde | Nenhuma |
| `mv_pedidos` | Tabela direta | `gold.crmback.tb_pedidos` | — | ✅ Verde | Nenhuma |
| `mv_produtos_visualizados` | Tabela direta | `gold.crmback.tb_produtos_visualizados` | — | ✅ Verde | Nenhuma |

> 💚 CRMBACK é a única avenida com 100% dos MVs em gold e acesso direto — referência de boas práticas.

---

### 🔍 GIFTBACK — Governança das Fontes

| Metric View | Tipo de Acesso | Fonte Primária | Fonte Real (rastreada) | Governança | Ação |
|---|---|---|---|---|---|
| `mv_bonus_gerado_marca` | Tabela direta | `silver.giftback.bonus` | — | 🟡 Amarelo | Promover `silver.giftback.bonus` para gold |
| `mv_bonus_gerado_master` | View gold | `gold.giftback.vw_bonus_var_gerado` | `silver.giftback.bonus` + `silver.giftback.masters` | 🟡 Amarelo | Materializar view como tabela gold; promover fontes silver |
| `mv_bonus_perdido_marca` | Tabela direta | `silver.giftback.bonus` | — | 🟡 Amarelo | Promover `silver.giftback.bonus` para gold |
| `mv_bonus_perdido_master` | View gold | `gold.giftback.vw_bonus_var_perdido` | `silver.giftback.bonus` + `silver.giftback.masters` + ⚠️ `bronze.giftback.bonus` | 🔴 Vermelho | View contém `bronze.giftback.bonus`. Prioridade alta: reescrever view sem bronze |
| `mv_bonus_resgate_marca` | View gold | `gold.giftback.vw_bonus_var_resgate_marca` | `silver.giftback.bonus` | 🟡 Amarelo | Materializar view; promover `silver.giftback.bonus` para gold |
| `mv_bonus_resgate_master` | View gold | `gold.giftback.vw_bonus_var_resgate` | `silver.giftback.bonus` + `silver.giftback.masters` | 🟡 Amarelo | Materializar view; promover fontes silver |
| `mv_bonus_vendatotal_marca` | Tabela direta | `silver.giftback.vendas_totais` | — | 🟡 Amarelo | Promover `silver.giftback.vendas_totais` para gold |
| `mv_bonus_vendatotal_master` | View gold | `gold.giftback.vw_bonus_var_vendatotal` | `silver.giftback.vendas_totais` + `silver.giftback.masters` | 🟡 Amarelo | Materializar view; promover fontes silver |
| `mv_engajamento_marca` | SQL / CTE | — | `silver.giftback.vendas_totais` + `silver.giftback.bonus` | 🟡 Amarelo | Reescrever CTE usando fontes gold quando promovidas |
| `mv_engajamento_master` | SQL / CTE | — | `silver.giftback.vendas_totais` + `silver.giftback.masters` + `silver.giftback.bonus` | 🟡 Amarelo | Reescrever CTE usando fontes gold quando promovidas |

> ⚠️ Padrão crítico: todos os MVs giftback dependem de `silver.giftback.bonus` e/ou `silver.giftback.masters`. A promoção dessas duas tabelas para gold resolveria 9 dos 10 MVs de uma vez.

---

### 🔍 PRESENTEIA — Governança das Fontes

| Metric View | Tipo de Acesso | Fonte Primária | Fontes Reais | Governança | Ação |
|---|---|---|---|---|---|
| `mv_atendimentos` | SQL / CTE | — | `bronze.zendesk.organizations` + `silver.zendesk.users` + `silver.zendesk.tickets` | 🔴 Vermelho | Criar camada gold para dados Zendesk |
| `mv_contatos` | SQL / CTE | — | `bronze.presenteia.users`, `bronze.presenteia.user_contacts`, `bronze.presenteia.user_checkins`, `bronze.presenteia.user_events` | 🔴 Vermelho | Criar tabelas gold para entidades base do Presenteia |
| `mv_convites` | Tabela direta | `bronze.presenteia.invite_sends` | — | 🔴 Vermelho | Criar `gold.presenteia.invite_sends` |
| `mv_custo_meta` | Tabela direta | `bronze.presenteia.meta_template_analytics` | — | 🔴 Vermelho | Criar `gold.presenteia.meta_template_analytics` |
| `mv_enderecos` | SQL / CTE | — | `bronze.presenteia.user_addresses` | 🔴 Vermelho | Criar `gold.presenteia.user_addresses` |
| `mv_kids` | SQL / CTE | — | `bronze.presenteia.user_contacts` | 🔴 Vermelho | Criar `gold.presenteia.user_contacts` |
| `mv_lembretes_criados` | Tabela direta | `bronze.presenteia.user_events` | — | 🔴 Vermelho | Criar `gold.presenteia.user_events` |
| `mv_lembretes_criados_2` | Tabela direta | `bronze.presenteia.user_events` | — | 🔴 Vermelho | Mesmo que acima — MVs duplicados na mesma fonte |
| `mv_lembretes_enviados` | SQL / CTE | — | `bronze.presenteia.orders`, `bronze.presenteia.users`, `bronze.presenteia.user_contacts`, `bronze.presenteia.user_events`, `bronze.presenteia.user_event_sends` | 🔴 Vermelho | Criar camada gold para todo o schema presenteia |
| `mv_numeros` | Tabela direta | `bronze.presenteia.meta_phone_numbers` | — | 🔴 Vermelho | Criar `gold.presenteia.meta_phone_numbers` |
| `mv_pedidos` | SQL / CTE | — | `bronze.presenteia.orders` | 🔴 Vermelho | Criar `gold.presenteia.orders` |
| `mv_pedidos_2` | SQL / CTE | — | `bronze.presenteia.orders` | 🔴 Vermelho | Mesmo que acima — MVs duplicados na mesma fonte |
| `mv_take_rate` | SQL / CTE | — | `bronze.presenteia.orders` | 🔴 Vermelho | Mesmo que acima |
| `mv_usuario_com_engajamento` | Tabela direta | `bronze.presenteia.user_events` | — | 🔴 Vermelho | Criar `gold.presenteia.user_events` |
| `mv_usuarios` | SQL / CTE | — | `bronze.presenteia.users` | 🔴 Vermelho | Criar `gold.presenteia.users` |

> 🚨 PRESENTEIA é a avenida mais crítica: **100% dos MVs (15/15) consomem bronze diretamente**. A lista de tabelas bronze a promover se resume a poucas entidades centrais:
> - `bronze.presenteia.orders` (usada por 5 MVs)
> - `bronze.presenteia.user_events` (usada por 4 MVs)
> - `bronze.presenteia.users` (usada por 3 MVs)
> - `bronze.presenteia.user_contacts` (usada por 3 MVs)
> - Demais bronze individuais

---

### 🔍 VALEBONUS — Governança das Fontes

| Metric View | Tipo de Acesso | Fonte Primária | Fonte Real (rastreada) | Governança | Ação |
|---|---|---|---|---|---|
| `mv_emissoes` | Tabela direta | `gold.valebonus.emissions` | — | ✅ Verde | Nenhuma |
| `mv_resgates` | Tabela direta | `gold.valebonus.transactions_shops` | — | ✅ Verde | Nenhuma |
| `mv_valebonus_resgate_usuarios` | Tabela direta | `gold.valebonus.transactions_shops` | — | ✅ Verde | Nenhuma |
| `mv_valebonus_cadastro` | Tabela direta | `silver.valebonus.usuarios` | — | 🟡 Amarelo | Promover `silver.valebonus.usuarios` para gold |
| `mv_resgate_ifood_embedded` | SQL / CTE | — | `bronze.valebonus.redemptions` + `silver.giftback.bonus` | 🔴 Vermelho | Criar `gold.valebonus.redemptions`; resolver dependência de `silver.giftback.bonus` |

---

### 📋 Mapa de Ação para Engenharia de Dados

#### Tabelas Bronze/Silver a promover (por prioridade de impacto)

| Tabela a Promover | MVs Afetados | Impacto | Prioridade |
|---|---|---|---|
| `silver.giftback.bonus` | mv_bonus_gerado_marca, mv_bonus_perdido_marca, mv_bonus_resgate_marca, mv_engajamento_marca, + via views | 6+ MVs | 🔴 Alta |
| `silver.giftback.masters` | mv_bonus_gerado_master, mv_bonus_resgate_master, mv_engajamento_master, + via views | 5+ MVs | 🔴 Alta |
| `bronze.presenteia.orders` | mv_pedidos, mv_pedidos_2, mv_take_rate, mv_lembretes_enviados | 4+ MVs | 🔴 Alta |
| `bronze.presenteia.user_events` | mv_lembretes_criados, mv_lembretes_criados_2, mv_usuario_com_engajamento, mv_contatos | 4 MVs | 🔴 Alta |
| `bronze.presenteia.users` | mv_usuarios, mv_contatos, mv_lembretes_enviados | 3 MVs | 🟡 Média |
| `bronze.presenteia.user_contacts` | mv_kids, mv_contatos, mv_lembretes_enviados | 3 MVs | 🟡 Média |
| `bronze.giftback.bonus` | mv_bonus_perdido_master (via `vw_bonus_var_perdido`), mv_ads_receita_ads | 2 MVs | 🔴 Alta |
| `silver.giftback.vendas_totais` | mv_bonus_vendatotal_marca, mv_engajamento_marca, mv_engajamento_master | 3 MVs | 🟡 Média |
| `silver.valebonus.usuarios` | mv_valebonus_cadastro | 1 MV | 🟡 Média |
| `bronze.valebonus.redemptions` | mv_resgate_ifood_embedded | 1 MV | 🟡 Média |
| `bronze.zendesk.*` | mv_atendimentos | 1 MV | 🟡 Média |

#### Views gold a materializar como tabelas

| View | Depende de | MVs Afetados | Ação |
|---|---|---|---|
| `gold.ads.vw_ads_resgate` | gold + silver | `mv_ads_resgate` | Materializar; eliminar dependência de silver |
| `gold.giftback.vw_bonus_var_gerado` | silver | `mv_bonus_gerado_master` | Materializar após promoção das fontes silver |
| `gold.giftback.vw_bonus_var_perdido` | silver + **bronze** | `mv_bonus_perdido_master` | ⚠️ Prioridade: remover `bronze.giftback.bonus` da view |
| `gold.giftback.vw_bonus_var_resgate_marca` | silver | `mv_bonus_resgate_marca` | Materializar após promoção das fontes silver |
| `gold.giftback.vw_bonus_var_resgate` | silver | `mv_bonus_resgate_master` | Materializar após promoção das fontes silver |
| `gold.giftback.vw_bonus_var_vendatotal` | silver | `mv_bonus_vendatotal_master` | Materializar após promoção das fontes silver |


---

---

## Governança dos Agents

> Análise das fontes de dados utilizadas pelos Genies (AI/BI Agents no Databricks). Tabelas identificadas via histórico de conversas + consultas diretas. Classificação: ✅ Verde (Metric View ou tabela gold), 🟡 Amarelo (silver), 🔴 Vermelho (bronze ou camada não governada). Coletado em 2026-06-07.

---

### 1. Cobertura por Avenida

| Avenida | Genie Dedicado | Status |
|---|---|---|
| 🎁 Giftback | Clara - Extração de Informações | ✅ Coberto |
| 🎀 Presenteia | Orion - Análise de Pedidos do PresenteIA | ✅ Coberto |
| 🎫 ValeBonus | VB Insights + Ifood Embedded | ✅ Coberto (2 Genies) |
| 🛒 CRMBACK | CRMBACK Analysis | ✅ Coberto |
| 📢 ADS | — | ❌ **Sem Genie dedicado** |

> ⚠️ **ADS não possui Genie** entre os 5 analisados. É a avenida com maior volume de dashboards e todos os 5 Metric Views já estão em gold — a infraestrutura está pronta para criar um Genie ADS imediatamente.

---

### 2. Fontes por Genie — Classificação de Governança

#### 🎫 VB Insights — ValeBonus

| Tabela | Tipo | Governança | Observação |
|---|---|---|---|
| `gold.valebonus.mv_emissoes` | Metric View | ✅ Verde | Ideal |
| `gold.valebonus.mv_resgates` | Metric View | ✅ Verde | Ideal |
| `gold.valebonus.mv_valebonus_resgate_usuarios` | Metric View | ✅ Verde | Ideal |
| `gold.valebonus.emissions` | Tabela gold | ✅ Verde | Redundante com `mv_emissoes` — unificar |
| `gold.valebonus.transactions_shops` | Tabela gold | ✅ Verde | Redundante com `mv_resgates` — unificar |
| `silver.valebonus.usuarios` | Tabela silver | 🟡 Amarelo | Não existe MV de usuários VB — criar `mv_valebonus_usuarios` |
| `bronze.valebonus.applications` | Tabela bronze | 🔴 Vermelho | Consulta direta ao bronze. Criar gold/MV equivalente |

**Scorecard: ✅ 5 · 🟡 1 · 🔴 1 → 🟠 Moderado**

---

#### 🎫 Ifood Embedded — ValeBonus

| Tabela | Tipo | Governança | Observação |
|---|---|---|---|
| `datalab.valebonus.prd_ifood_embeded_resgates` | Staging (datalab) | 🔴 Vermelho | Camada `datalab` é área de trabalho não governada. O MV `gold.valebonus.mv_resgate_ifood_embedded` **já existe** e não está sendo usado |

**Scorecard: ✅ 0 · 🟡 0 · 🔴 1 → 🔴 Crítico**

> 🚨 **Achado crítico:** O MV foi criado exatamente para este caso de uso e está sendo ignorado. O Genie consulta diretamente o staging em vez da camada semântica gold. Reconfigurar urgente.

---

#### 🎁 Clara - Extração de Informações — Giftback

| Tabela | Tipo | Governança | Observação |
|---|---|---|---|
| `gold.giftback.mv_bonus_gerado_marca` | Metric View | ✅ Verde | Ideal |
| `gold.giftback.mv_bonus_gerado_master` | Metric View | ✅ Verde | Ideal |
| `gold.giftback.mv_bonus_perdido_marca` | Metric View | ✅ Verde | Ideal |
| `gold.giftback.mv_bonus_perdido_master` | Metric View | ✅ Verde | ⚠️ Este MV usa `bronze.giftback.bonus` internamente (ver Governança dos MVs) |
| `gold.giftback.mv_bonus_resgate_marca` | Metric View | ✅ Verde | Ideal |
| `gold.giftback.mv_bonus_resgate_master` | Metric View | ✅ Verde | Ideal |
| `gold.giftback.mv_bonus_vendatotal_marca` | Metric View | ✅ Verde | Ideal |
| `gold.giftback.mv_engajamento_master` | Metric View | ✅ Verde | ⚠️ MV do tipo SQL/CTE — usa `silver.giftback.bonus` + `silver.giftback.masters` internamente |
| `silver.giftback.bonus` | Tabela silver | 🟡 Amarelo | Genie contorna os MVs e acessa silver diretamente em certas consultas |

**Scorecard: ✅ 8 · 🟡 1 · 🔴 0 → 🟢 Bom**

> 💡 Clara é o Genie com mais MVs e melhor cobertura semântica. Ponto de atenção: ainda escapa para `silver.giftback.bonus` em algumas perguntas. Restringir o Genie para usar exclusivamente MVs.

---

#### 🎀 Orion - Análise de Pedidos do PresenteIA

| Tabela | Tipo | Governança | Observação |
|---|---|---|---|
| `silver.presenteia.tb_conversas` | Tabela silver | 🟡 Amarelo | Sem MV equivalente disponível em gold |
| `silver.presenteia.tb_entradas` | Tabela silver | 🟡 Amarelo | Sem MV equivalente disponível em gold |
| `silver.presenteia.tb_eventos` | Tabela silver | 🟡 Amarelo | Sem MV equivalente disponível em gold |
| `silver.presenteia.tb_pedidos` | Tabela silver | 🟡 Amarelo | `gold.presenteia.mv_pedidos` existe, mas usa `bronze.presenteia.orders` internamente |
| `silver.presenteia.tb_usuarios` | Tabela silver | 🟡 Amarelo | `gold.presenteia.mv_usuarios` existe, mas usa `bronze.presenteia.users` internamente |

**Scorecard: ✅ 0 · 🟡 5 · 🔴 0 → 🟡 Atenção**

> ⚠️ Orion não usa nenhum Metric View. Os MVs do Presenteia existem mas têm bronze nas fontes — o Genie usa silver porque é a melhor camada disponível hoje. A solução está na Engenharia: promover as tabelas bronze do Presenteia para gold desbloqueará tanto os MVs quanto o Orion.

---

#### 🛒 CRMBACK Analysis

| Tabela | Tipo | Governança | Observação |
|---|---|---|---|
| `gold.crmback.mv_carrinhos` | Metric View | ✅ Verde | Ideal |
| `gold.crmback.mv_pedidos` | Metric View | ✅ Verde | Ideal |
| `gold.crmback.mv_produtos_visualizados` | Metric View | ✅ Verde | Ideal |
| `gold.crmback.tb_carrinhos` | Tabela gold | ✅ Verde | Gold direto — usa tanto o MV quanto a tabela. Unificar para só usar `mv_carrinhos` |

**Scorecard: ✅ 4 · 🟡 0 · 🔴 0 → ✅ Ideal**

> 💚 CRMBACK Analysis é o Genie com melhor governança absoluta — todos os acessos são gold ou MV. Referência de como os outros Genies deveriam ser configurados.

---

### 3. Scorecard Consolidado dos Genies

| Genie | Avenida | ✅ Verde | 🟡 Amarelo | 🔴 Vermelho | Total fontes | Nível |
|---|---|---|---|---|---|---|
| CRMBACK Analysis | CRMBACK | 4 | 0 | 0 | 4 | ✅ Ideal |
| Clara - Extração | Giftback | 8 | 1 | 0 | 9 | 🟢 Bom |
| VB Insights | ValeBonus | 5 | 1 | 1 | 7 | 🟠 Moderado |
| Orion - PresenteIA | Presenteia | 0 | 5 | 0 | 5 | 🟡 Atenção |
| Ifood Embedded | ValeBonus | 0 | 0 | 1 | 1 | 🔴 Crítico |
| **— (ausente)** | **ADS** | — | — | — | — | ❌ Sem Genie |

---

### 4. Insights para o Time de Engenharia de Dados

#### 🔴 Ações imediatas

| # | Ação | Impacto | Genie |
|---|---|---|---|
| 1 | **Reconfigurar Ifood Embedded** para usar `gold.valebonus.mv_resgate_ifood_embedded` em vez de `datalab.valebonus.prd_ifood_embeded_resgates` | MV já existe, mudança só de configuração | Ifood Embedded |
| 2 | **Criar Genie ADS** conectado aos 5 MVs gold: `mv_ads_bonification`, `mv_ads_message`, `mv_ads_offer`, `mv_ads_resgate`, `mv_ads_receita_ads` | Fecha o único gap de cobertura por avenida | — (novo) |
| 3 | **Promover `bronze.giftback.bonus` para gold** | Remove a única fonte bronze da cadeia do `mv_bonus_perdido_master` (usado pela Clara) | Clara |

#### 🟡 Ações de médio prazo

| # | Ação | Impacto | Genie |
|---|---|---|---|
| 4 | **Promover tabelas bronze do Presenteia para gold** (`orders`, `user_events`, `users`, `user_contacts`) | Desbloqueia os MVs do Presenteia → Orion sobe de silver para gold | Orion |
| 5 | **Criar `gold.valebonus.mv_valebonus_usuarios`** a partir de `silver.valebonus.usuarios` | Elimina o silver do VB Insights | VB Insights |
| 6 | **Promover `bronze.valebonus.applications` para gold** ou criar MV | Elimina o único bronze do VB Insights | VB Insights |
| 7 | **Restringir Clara para usar só MVs** — remover acesso direto a `silver.giftback.bonus` | Garante semântica consistente em 100% das consultas | Clara |
| 8 | **Unificar VB Insights**: remover `gold.valebonus.emissions` e `transactions_shops` diretos, usar apenas os MVs equivalentes | Elimina redundância, força uso da camada semântica | VB Insights |
| 9 | **Unificar CRMBACK Analysis**: remover `tb_carrinhos`, usar só `mv_carrinhos` | Padronização total em MVs | CRMBACK Analysis |

#### 💡 Observação estrutural

> O padrão é claro: **Genies que usam Metric Views têm melhor governança e semântica mais consistente**. CRMBACK (4/4 MVs) e Clara (8/9 MVs) são os exemplos a seguir. O caminho para melhorar Orion, VB Insights e Ifood Embedded passa menos pela reconfiguração dos Genies e mais pela **promoção das tabelas bronze/silver para gold pela engenharia de dados** — os Genies estão consumindo o melhor que a arquitetura oferece hoje.
