---
tipo: plano-engenharia
origem: "[[Governança de Dados - Camada MV, DASHs e Agents]]"
data: 2026-06-07
estado_alvo: "Toda métrica via Metric View; todo dado não-métrico via tabela gold; todo MV com fonte gold ou silver (nunca bronze/externo)"
---

# 🛠️ Plano de Engenharia de Dados — Governança MV

> Plano de tarefas derivado da investigação em [[Governança de Dados - Camada MV, DASHs e Agents]].
> **Objetivo:** levar Dashboards e Genies ao estado em que **toda métrica vem de um Metric View** e **todo dado não-métrico vem de uma tabela gold modelada** — com os Metric Views consumindo apenas **gold (ideal)** ou **silver (tolerável)**, nunca bronze ou fontes externas.

---

## 1. Sumário Executivo

### Estado atual

| Dimensão | Total | ✅ Verde | 🟡 Amarelo | 🔴 Vermelho | Nível |
|---|---|---|---|---|---|
| **Fontes dos Metric Views** | 38 MVs | 9 | 11 | 18 | 🔴 Crítico |
| **Dashboards** | 15 dashs | — | — | maioria 🔴 | 🔴 Crítico |
| **Genies (Agents)** | 5 genies | 2 ideais | 2 atenção | 1 crítico | 🟠 Moderado |

### Diagnóstico em uma frase
A camada semântica (MVs) existe e cobre boa parte das métricas, mas **18 dos 38 MVs ainda bebem de bronze/silver** e **a maioria dos dashboards e parte dos Genies contorna os MVs** consumindo tabelas cruas. O gargalo está majoritariamente nas **tabelas bronze consumidas direto** (Eixo A) — saneá-las destrava MVs, dashboards e Genies de uma vez.

### Os 4 eixos de trabalho
- **Eixo A — Saneamento de tabelas bronze** (verificar → trocar por silver existente ou remodelar): a raiz do problema.
- **Eixo B — Materialização de Views gold** (`vw_*` que escondem silver/bronze).
- **Eixo C — Métricas a criar** (novos Metric Views para tabelas gold hoje sem MV).
- **Eixo D — Reconfiguração de Consumo** (apontar Dashs/Genies para os MVs).

### Estado-alvo por avenida

| Avenida | Hoje | Maior alavanca | Estado-alvo |
|---|---|---|---|
| 🛒 CRMBACK | ✅ Ideal (3/3 MVs gold) | — (referência) | Manter; unificar consumo no MV |
| 📢 ADS | 🟠 Moderado (3✅/1🟡/1🔴) | Sanear bronze.giftback (motor ADS) + criar Genie | 5 MVs gold + Genie ADS |
| 🎫 ValeBonus | 🟠 Moderado MVs / 🔴 dashs | Sanear bronze.valebonus (trocar/remodelar) | MVs gold + Genies só-MV |
| 🎁 Giftback | 🟡 Atenção (0✅/9🟡/1🔴) | Trocar bronze.giftback.{bonus,masters} → silver | 10 MVs gold |
| 🎀 Presenteia | 🔴 Crítico (15/15 bronze) | Trocar bronze.presenteia.* → silver `tb_*` existentes | 15 MVs gold + Orion sobe p/ gold |

---

## 2. Eixo A — Saneamento das Tabelas Bronze

> **Nova abordagem (raiz da governança).** Nenhum Metric View, agente ou dashboard deve consumir **bronze**. Para **cada tabela bronze** consumida hoje, o processo é:
>
> 1. **Mapear** quem consome (MV / agente / dashboard) — já feito no inventário abaixo.
> 2. **Verificar no Unity Catalog** se já existe uma tabela **silver equivalente**.
> 3. **Se existe** → validar a equivalência de schema/grão e **trocar o consumo para a silver** (e avaliar promovê-la a gold em seguida).
> 4. **Se não existe** → **entender o uso real** (campos, grão, regra de negócio) e **remodelar** (criar a silver/gold adequada).
>
> ⚠️ A coluna **"Silver candidata"** no inventário é uma **hipótese por correspondência de nome**, a confirmar no catálogo — ✅ = silver já existe no doc · ❓ = provável, validar · — = não há.

### A1 · 🔴 P0 · Inventário e verificação no Unity Catalog
- [ ] **A1** — Para cada tabela do inventário (final desta seção), rodar `DESCRIBE EXTENDED` / `SHOW TABLES IN silver.<schema>` e confirmar: (a) existe silver equivalente? (b) schema/grão batem? Registrar a decisão final (Trocar / Confirmar / Remodelar). **É o passo que destrava todos os demais.**

### A2 · 🟢 Trocar para silver existente (equivalência provável)
> Silver já existe no catálogo; ação = validar e redirecionar o consumo do bronze para a silver.
- [ ] **A2.1 · 🔴 P0 · `bronze.giftback.bonus` → `silver.giftback.bonus`** — maior alavanca. Consumida por `mv_ads_receita_ads`, `mv_bonus_perdido_master` (via `vw_bonus_var_perdido`) e 6 dashboards (ADS Monitoramento/Sem Parar/ARM + GB natura/vivara/cs_acoes). A silver já alimenta outros MVs Giftback → equivalência alta.
- [ ] **A2.2 · 🔴 P0 · `bronze.giftback.masters` → `silver.giftback.masters`** — dash GB cs_giftback. Silver já consumida pelos MVs *master*.
- [ ] **A2.3 · 🟡 P1 · `bronze.giftback.total_sales` → `silver.giftback.vendas_totais`** — dashs GB natura/cs_giftback. Validar se `vendas_totais` cobre `total_sales`.
- [ ] **A2.4 · 🔴 P0 · `bronze.presenteia.orders` → `silver.presenteia.tb_pedidos`** — base de 4 MVs (`mv_pedidos`, `mv_pedidos_2`, `mv_take_rate`, `mv_lembretes_enviados`). Orion já usa `tb_pedidos`.
- [ ] **A2.5 · 🔴 P0 · `bronze.presenteia.users` → `silver.presenteia.tb_usuarios`** — base de 3 MVs (`mv_usuarios`, `mv_contatos`, `mv_lembretes_enviados`). Orion já usa `tb_usuarios`.
- [ ] **A2.6 · 🔴 P0 · `bronze.presenteia.user_events` → `silver.presenteia.tb_eventos`** — base de **5 MVs** (maior nº do Presenteia). Orion já usa `tb_eventos`.

### A3 · 🟡 Confirmar equivalência incerta, depois trocar
> Há silver com nome/domínio próximo, mas a equivalência precisa ser validada campo a campo. Se não bater, cai para remodelagem (A4).
- [ ] **A3.1 · 🟡 P1 · `bronze.giftback.customer_masters` → `silver.giftback.masters`?** — confirmar se `masters` cobre `customer_masters` (dash cs_giftback).
- [ ] **A3.2 · 🟡 P1 · `bronze.presenteia.user_contacts` → `silver.presenteia.tb_conversas`/`tb_entradas`?** — base de 3 MVs (`mv_contatos`, `mv_kids`, `mv_lembretes_enviados`). Identificar qual silver corresponde; senão, remodelar.
- [ ] **A3.3 · 🟡 P1 · `bronze.valebonus.pre_charge_transaction` → `silver.valebonus.transactions`?** — fonte bronze mais recorrente dos dashs Vivo (3 dashboards). Alto impacto — validar equivalência.
- [ ] **A3.4 · 🟡 P2 · `bronze.valebonus.redemptions` → `silver.valebonus.transactions`?** — base de `mv_resgate_ifood_embedded` + dash Ifood. Confirmar se `transactions` cobre `redemptions`.
- [ ] **A3.5 · 🟡 P2 · `bronze.valebonus.{users, consumer, device, session}` → `silver.valebonus.usuarios`?** — perfil de usuário VB (dashs parceiros_interno, Ifood). Confirmar cobertura de `usuarios`; o que faltar, remodelar.
- [ ] **A3.6 · 🟡 P2 · `bronze.zendesk.organizations` → `silver.zendesk.*`** — `silver.zendesk.users` e `silver.zendesk.tickets` já existem; falta `silver.zendesk.organizations`. Base do `mv_atendimentos`. Criar a silver que falta para completar o domínio.

### A4 · 🔴 Remodelar — sem silver hoje (entender uso e criar)
> Não há silver equivalente. Entender o uso real e criar a camada silver/gold. Agrupado por domínio.
- [ ] **A4.1 · 🔴 P1 · Domínio motor de ofertas ADS (Giftback)** — `brands`, `ads_activities`, `ads_offers`, `ads_publisher_squares`, `bonus_b2b_campaigns`, `bonus_b2b_logs`, `prebonus`, `prebonus_file`. Consumidas por `mv_ads_receita_ads` (`brands`) e dashs ADS (Acompanhamento Motor, Monitoramento, Sem Parar, ARM, quartou). Modelar dims/fatos do motor ADS.
- [ ] **A4.2 · 🔴 P2 · Domínio cadastro Giftback** — `customers`, `customer_configs`, `customer_users`, `orders`, `sms_mgms`. Dashs GB (natura, vivara, cs_acoes, quartou). Modelar `dim_customers`, `dim_customer_users`, fato `orders`/MGM.
- [ ] **A4.3 · 🔴 P2 · Domínio Becon / WhatsApp (ADS Sem Parar)** — `bronze.becon.{w_message, w_message_error, w_message_label, w_company_contact, w_company_label, w_company_chat_flow_message, w_company_chat_flow_session}` (7 tabelas). Sem silver. Entender uso no dash Sem Parar e modelar.
- [ ] **A4.4 · 🟡 P2 · Domínio Presenteia (entidades sem silver)** — `user_checkins`, `user_addresses`, `user_event_sends`, `invite_sends`, `meta_phone_numbers`, `meta_template_analytics`. Base de `mv_contatos`, `mv_enderecos`, `mv_lembretes_enviados`, `mv_convites`, `mv_numeros`, `mv_custo_meta`. Modelar silver/gold.
- [ ] **A4.5 · 🔴 P1 · Domínio ValeBonus (sem silver)** — `applications` (Genie VB Insights + dash parceiros), `campaign`, `offer`, `cellphone`, `wallet_balances`, `wallet_transaction`. Dashs VB vivo, parceiros, Ifood, vivo_acompanhamento. Modelar dims/fatos silver/gold.

### A5 · 🟥 Caso especial — staging não governado
- [ ] **A5 · 🔴 P0 · `datalab.valebonus.prd_ifood_embeded_resgates`** — não é bronze, é **staging (datalab)** consumido pelo Genie Ifood Embedded. O MV `gold.valebonus.mv_resgate_ifood_embedded` **já existe** → não remodelar, apenas **reapontar o Genie** (ver **D1**).

### 📋 Inventário das tabelas bronze (base da verificação)
| Tabela bronze | Consumidores | Silver candidata | Decisão inicial |
|---|---|---|---|
| `bronze.giftback.bonus` | 2 MV · 6 dash | `silver.giftback.bonus` ✅ | 🟢 Trocar (A2.1) |
| `bronze.giftback.masters` | 1 dash | `silver.giftback.masters` ✅ | 🟢 Trocar (A2.2) |
| `bronze.giftback.total_sales` | 2 dash | `silver.giftback.vendas_totais` ✅ | 🟢 Trocar (A2.3) |
| `bronze.giftback.customer_masters` | 1 dash | `silver.giftback.masters` ❓ | 🟡 Confirmar (A3.1) |
| `bronze.giftback.brands` | 1 MV · 5 dash | — | 🔴 Remodelar (A4.1) |
| `bronze.giftback.orders` | 1 MV · 6 dash | — | 🔴 Remodelar (A4.2) |
| `bronze.giftback.customers` | 3 dash | — | 🔴 Remodelar (A4.2) |
| `bronze.giftback.customer_configs` | 1 dash | — | 🔴 Remodelar (A4.2) |
| `bronze.giftback.customer_users` | 1 dash | — | 🔴 Remodelar (A4.2) |
| `bronze.giftback.sms_mgms` | 1 dash | — | 🔴 Remodelar (A4.2) |
| `bronze.giftback.ads_activities` | 2 dash | — | 🔴 Remodelar (A4.1) |
| `bronze.giftback.ads_offers` | 2 dash | — | 🔴 Remodelar (A4.1) |
| `bronze.giftback.ads_publisher_squares` | 1 dash | — | 🔴 Remodelar (A4.1) |
| `bronze.giftback.bonus_b2b_campaigns` | 1 dash | — | 🔴 Remodelar (A4.1) |
| `bronze.giftback.bonus_b2b_logs` | 1 dash | — | 🔴 Remodelar (A4.1) |
| `bronze.giftback.prebonus` | 1 dash | — | 🔴 Remodelar (A4.1) |
| `bronze.giftback.prebonus_file` | 1 dash | — | 🔴 Remodelar (A4.1) |
| `bronze.becon.*` (7 tabelas) | 1 dash (Sem Parar) | — | 🔴 Remodelar (A4.3) |
| `bronze.presenteia.orders` | 4 MV | `silver.presenteia.tb_pedidos` ✅ | 🟢 Trocar (A2.4) |
| `bronze.presenteia.users` | 3 MV | `silver.presenteia.tb_usuarios` ✅ | 🟢 Trocar (A2.5) |
| `bronze.presenteia.user_events` | 5 MV | `silver.presenteia.tb_eventos` ✅ | 🟢 Trocar (A2.6) |
| `bronze.presenteia.user_contacts` | 3 MV | `silver.presenteia.tb_conversas/entradas` ❓ | 🟡 Confirmar (A3.2) |
| `bronze.presenteia.user_checkins` | 1 MV | — | 🔴 Remodelar (A4.4) |
| `bronze.presenteia.user_addresses` | 1 MV | — | 🔴 Remodelar (A4.4) |
| `bronze.presenteia.user_event_sends` | 1 MV | — | 🔴 Remodelar (A4.4) |
| `bronze.presenteia.invite_sends` | 1 MV | — | 🔴 Remodelar (A4.4) |
| `bronze.presenteia.meta_phone_numbers` | 1 MV | — | 🔴 Remodelar (A4.4) |
| `bronze.presenteia.meta_template_analytics` | 1 MV | — | 🔴 Remodelar (A4.4) |
| `bronze.valebonus.pre_charge_transaction` | 3 dash | `silver.valebonus.transactions` ❓ | 🟡 Confirmar (A3.3) |
| `bronze.valebonus.redemptions` | 1 MV · 1 dash | `silver.valebonus.transactions` ❓ | 🟡 Confirmar (A3.4) |
| `bronze.valebonus.users` | 1 dash | `silver.valebonus.usuarios` ❓ | 🟡 Confirmar (A3.5) |
| `bronze.valebonus.consumer` | 1 dash | `silver.valebonus.usuarios` ❓ | 🟡 Confirmar (A3.5) |
| `bronze.valebonus.device` | 1 dash | `silver.valebonus.usuarios` ❓ | 🟡 Confirmar (A3.5) |
| `bronze.valebonus.session` | 1 dash | `silver.valebonus.usuarios` ❓ | 🟡 Confirmar (A3.5) |
| `bronze.valebonus.applications` | 1 agente · 1 dash | — | 🔴 Remodelar (A4.5) |
| `bronze.valebonus.campaign` | 1 dash | — | 🔴 Remodelar (A4.5) |
| `bronze.valebonus.offer` | 1 dash | — | 🔴 Remodelar (A4.5) |
| `bronze.valebonus.cellphone` | 1 dash | — | 🔴 Remodelar (A4.5) |
| `bronze.valebonus.wallet_balances` | 1 dash | — | 🔴 Remodelar (A4.5) |
| `bronze.valebonus.wallet_transaction` | 1 dash | — | 🔴 Remodelar (A4.5) |
| `bronze.zendesk.organizations` | 1 MV | silver.zendesk (parcial) ❓ | 🟡 Criar silver faltante (A3.6) |
| `datalab.valebonus.prd_ifood_embeded_resgates` | 1 agente | **MV já existe!** | 🟥 Reapontar Genie (A5 / D1) |

---

## 3. Eixo B — Materialização de Views gold

> Views `gold.*.vw_*` que **aparentam ser gold mas leem silver/bronze por baixo**. Materializar como tabela gold (após as fontes do Eixo A serem promovidas).

- [ ] **B1 · 🔴 P0 · `gold.giftback.vw_bonus_var_perdido`**
  Contém `bronze.giftback.bonus` (além de silver). **Prioridade:** reescrever sem bronze (depende de A2.1) e materializar. Afeta `mv_bonus_perdido_master` (usado pela Clara).

- [ ] **B2 · 🟡 P1 · `gold.ads.vw_ads_resgate`**
  Lê `gold.ads.b2b_bonification` + `silver.giftback.bonus`. Materializar como tabela gold pura, eliminando o silver. Afeta `mv_ads_resgate`.

- [ ] **B3 · 🟡 P1 · `gold.giftback.vw_bonus_var_gerado`**
  Lê silver. Materializar após A2.1/A2.2. Afeta `mv_bonus_gerado_master`.

- [ ] **B4 · 🟡 P1 · `gold.giftback.vw_bonus_var_resgate_marca`**
  Lê `silver.giftback.bonus`. Materializar após A2.1. Afeta `mv_bonus_resgate_marca`.

- [ ] **B5 · 🟡 P1 · `gold.giftback.vw_bonus_var_resgate`**
  Lê silver. Materializar após A2.1/A2.2. Afeta `mv_bonus_resgate_master`.

- [ ] **B6 · 🟡 P1 · `gold.giftback.vw_bonus_var_vendatotal`**
  Lê silver. Materializar após A2.3. Afeta `mv_bonus_vendatotal_master`.

> ℹ️ Os MVs `mv_engajamento_*` são SQL/CTE direto sobre silver — **reescrever a CTE** para apontar às fontes saneadas quando A2.1/A2.2/A2.3 estiverem prontas (tratado em D8).

---

## 4. Eixo C — Métricas a criar (novos Metric Views)

> Tabelas **gold já usadas em dashboards sem MV correspondente** = a métrica existe, mas fora da camada semântica. Cada item é um **MV novo a modelar**. (Onde a fonte ainda é bronze, depende do Eixo A.)

### 📢 ADS
- [ ] **C1 · 🟡 P1 · `mv_ads_patrocinado`** — a partir de `gold.ads.ads_patrocinado` (hoje gold sem MV; tabela `patrocinado` no dash Gerencial).
- [ ] **C2 · 🟡 P2 · `mv_ads_emissao_quartou`** — a partir de `gold.ads.emissao_quartou` (dash Quartou, `fEmisao`).
- [ ] **C3 · 🟡 P2 · `mv_ads_resgate_quartou`** — a partir de `gold.ads.resgate_quartou` (dash Quartou, `fResgate`).
- [ ] **C4 · 🟡 P2 · `mv_ads_usuario_quartou`** — a partir de `gold.ads.resgate_usuario_quartou` (dash Quartou, `fNovos_marca_tipo`, `fRetorno`).
- [ ] **C5 · 🟡 P2 · Dimensões ADS nos MVs** — incorporar `gold.ads.cpl_cpi_cpa` (taxas CPL/CPI/CPA) como `dim_taxas` no `mv_ads_receita_ads`; `gold.ads.b2b_marca_cs` como `dim_marca`; avaliar publisher/praça de `b2b_message` como dimensão dos MVs existentes.

### 🎫 ValeBonus
- [ ] **C6 · 🟡 P2 · `mv_valebonus_usuarios`** — a partir de `silver.valebonus.usuarios` (após consolidar o perfil VB em silver, A3.5). Hoje não há MV de usuários VB; usado por VB Insights e dashs.
- [ ] **C7 · 🟡 P2 · MVs para fatos VB hoje soltos em gold** — avaliar criar MVs sobre `gold.valebonus.{emissions_agregado, cohort_parceiros, emissoes_unicas_mensais, emissoes_vivo_bi, pbi_VBO02_Parceiro}` usados direto em dashs sem camada semântica.

### 🎀 Presenteia
- [ ] **C8 · 🟡 P2 · MVs de conversas/eventos para o Orion** — após Eixo A, criar MVs gold equivalentes a `silver.presenteia.{tb_conversas, tb_entradas, tb_eventos}` (hoje sem MV; Orion usa silver por falta de opção). Consolidar os MVs duplicados (`mv_pedidos`/`mv_pedidos_2`, `mv_lembretes_criados`/`_2`) numa única definição.

### 🎫 VB Insights — bronze sem MV
- [ ] **C9 · 🔴 P2 · MV para `bronze.valebonus.applications`** — criar gold/MV equivalente (liga-se a A4.5) para eliminar consulta bronze do Genie.

---

## 5. Eixo D — Reconfiguração de Consumo (Dashboards & Genies)

> Não exige modelagem nova — é **apontar o consumidor para o MV/gold** já existente. Várias são ganho rápido.

### Genies (Agents)
- [ ] **D1 · 🔴 P0 · Reconfigurar Genie "Ifood Embedded"** — trocar `datalab.valebonus.prd_ifood_embeded_resgates` (staging não governado) por `gold.valebonus.mv_resgate_ifood_embedded`. **🚨 O MV já existe e está sendo ignorado** — mudança apenas de configuração.
- [ ] **D2 · 🔴 P0 · Criar Genie ADS** — conectar aos 5 MVs gold prontos (`mv_ads_bonification`, `mv_ads_message`, `mv_ads_offer`, `mv_ads_resgate`, `mv_ads_receita_ads`). É a **única avenida sem Genie** e a infraestrutura já está pronta.
- [ ] **D3 · 🟡 P1 · Restringir Genie "Clara" a só-MVs** — remover acesso direto a `silver.giftback.bonus`; forçar uso exclusivo dos 8 MVs. Garante semântica consistente em 100% das consultas.
- [ ] **D4 · 🟡 P2 · Unificar Genie "VB Insights"** — remover `gold.valebonus.emissions` e `transactions_shops` diretos (redundantes com `mv_emissoes`/`mv_resgates`); usar só os MVs. Após A3.5, remover silver.
- [ ] **D5 · 🟡 P2 · Unificar Genie "CRMBACK Analysis"** — remover `gold.crmback.tb_carrinhos` direto; usar só `mv_carrinhos`. Padronização total.
- [ ] **D6 · 🟡 P2 · Reapontar Genie "Orion"** — após A2.4–A2.6 / A3.2, migrar de `silver.presenteia.*` para os MVs gold do Presenteia.

### Dashboards
- [ ] **D7 · 🟡 P1 · Migrar datasets PBI dos dashs ADS para MVs** — onde o dash usa SQL direto sobre bronze/gold (`f_ads_geral_motor`, `brand`, `Brand_Offer`, etc.), reapontar para os MVs e dims gold à medida que A4.1/A4.2 e C1–C5 ficam prontos.
- [ ] **D8 · 🟡 P1 · Reescrever CTEs dos MVs SQL/CTE** — `mv_engajamento_marca/master`, `mv_ads_receita_ads` e demais MVs definidos por CTE sobre silver/bronze: trocar fontes pelas tabelas gold dos Eixos A/B.
- [ ] **D9 · 🟡 P2 · Migrar dashs Giftback para MVs/gold** — natura, vivara, cs_giftback, cs_acoes: trocar `bronze.giftback.bonus` (→ silver, A2.1) e os demais `bronze.giftback.*` (A4.1/A4.2) pelos MVs/dims gold.
- [ ] **D10 · 🟡 P2 · Migrar dashs ValeBonus para MVs/gold** — parceiros_interno, vale_bonus_parceiros, vivo, vivo_acompanhamento: substituir `bronze.valebonus.pre_charge_transaction` (A3.3) e os demais `bronze.valebonus.*` (A4.5) pelos MVs/gold.

---

## 6. Matriz de Priorização — Onde atuar primeiro

> Sequência recomendada: as tarefas P0 destravam o maior número de consumidores com menor esforço relativo.

### 🚀 P0 — Faça primeiro (alto impacto)
| ID | Tarefa | Tipo | Destrava |
|---|---|---|---|
| A1 | Inventário + verificação de silver no Unity Catalog | Verificação | define toda a decisão do Eixo A (trocar vs remodelar) |
| D1 | Reconfigurar Ifood Embedded → MV existente | Config | 1 Genie (ganho imediato, esforço ~zero) |
| D2 | Criar Genie ADS | Config | Fecha o único gap de cobertura por avenida |
| A2.1+A2.2 | `bronze.giftback.{bonus,masters}` → silver existente | Saneamento | maioria dos MVs/dashs Giftback + Clara |
| A2.4–A2.6 | `bronze.presenteia.{orders,users,user_events}` → silver existente | Saneamento | 12 usos de MV no Presenteia + Orion |
| B1 | Materializar `vw_bonus_var_perdido` (sem bronze) | View | `mv_bonus_perdido_master` |

### 🔧 P1 — Em seguida (consolida o ganho)
A2.3 · A3.1, A3.2, A3.3 · A4.1, A4.5 · B2–B6 · C1 · D3, D7, D8

### 🧹 P2 — Refinamento e cauda longa
A3.4, A3.5, A3.6 · A4.2, A4.3, A4.4 · C2–C9 · D4, D5, D6, D9, D10

### Dependências-chave
- **A1 (verificação no catálogo) vem antes de tudo no Eixo A** — é ela que classifica cada bronze em "trocar por silver" (A2/A3) ou "remodelar" (A4).
- **Trocas (A2/A3)** são mais baratas que remodelagens (A4) — priorizar onde a silver já existe.
- **Views (Eixo B)** dependem do saneamento das fontes (Eixo A) — materializar só depois.
- **Novos MVs Presenteia (C8)** dependem de A2.4–A2.6 / A3.2.
- **Migração de dashs/Genies (D6–D10)** depende do gold/silver correspondente já existir.
- **MV `mv_resgate_ifood_embedded`** já existe → D1 é independente do Eixo A.

---

## 7. Anexo — Scorecards Consolidados

### Fontes dos Metric Views (38 MVs)
| Avenida | Total | ✅ | 🟡 | 🔴 | Nível |
|---|---|---|---|---|---|
| ads | 5 | 3 | 1 | 1 | 🟠 Moderado |
| crmback | 3 | 3 | 0 | 0 | ✅ Ideal |
| giftback | 10 | 0 | 9 | 1 | 🟡 Atenção |
| presenteia | 15 | 0 | 0 | 15 | 🔴 Crítico |
| valebonus | 5 | 3 | 1 | 1 | 🟠 Moderado |
| **Total** | **38** | **9** | **11** | **18** | 🔴 Crítico |

### Dashboards
| Dashboard | Avenida | Nível |
|---|---|---|
| Ads - Gerencial | ADS | 🟠 Moderado |
| Ads - Acompanhamento Motor | ADS | 🔴 Crítico |
| Ads - Monitoramento | ADS | 🔴 Crítico |
| Ads - quartou | ADS | 🔴 Crítico |
| Ads - Sem Parar ARM | ADS | 🔴 Crítico |
| Ads - Sem Parar | ADS | 🔴 Crítico |
| GB - cliente_natura | Giftback | 🔴 Crítico |
| GB - cliente_vivara | Giftback | 🟠 Moderado |
| GB - cs_giftback | Giftback | 🔴 Crítico |
| GB - cs_acoes | Giftback | 🔴 Crítico |
| VB - Ifood Embedded | ValeBonus | 🔴 Crítico |
| VB - parceiros_interno | ValeBonus | 🔴 Crítico |
| VB - vale_bonus_parceiros | ValeBonus | 🔴 Crítico |
| VB - vivo | ValeBonus | 🔴 Crítico |
| VB - vivo_acompanhamento_de_meta | ValeBonus | 🔴 Crítico |

### Genies (Agents)
| Genie | Avenida | ✅ | 🟡 | 🔴 | Nível |
|---|---|---|---|---|---|
| CRMBACK Analysis | CRMBACK | 4 | 0 | 0 | ✅ Ideal |
| Clara - Extração | Giftback | 8 | 1 | 0 | 🟢 Bom |
| VB Insights | ValeBonus | 5 | 1 | 1 | 🟠 Moderado |
| Orion - PresenteIA | Presenteia | 0 | 5 | 0 | 🟡 Atenção |
| Ifood Embedded | ValeBonus | 0 | 0 | 1 | 🔴 Crítico |
| — (ausente) | ADS | — | — | — | ❌ Sem Genie |

---

### 💡 Princípio orientador
> Genies e dashboards que usam **Metric Views** têm a melhor governança — CRMBACK (4/4) e Clara (8/9) são a referência. O caminho para os demais passa **menos por reconfigurar o consumidor e mais por promover bronze/silver para gold na engenharia**: hoje os consumidores já usam o melhor que a arquitetura oferece. Resolva o Eixo A e os Eixos C/D caem por consequência.

_Gerado a partir de [[Governança de Dados - Camada MV, DASHs e Agents]] em 2026-06-07._
