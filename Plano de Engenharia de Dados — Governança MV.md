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
A camada semântica (MVs) existe e cobre boa parte das métricas, mas **18 dos 38 MVs ainda bebem de bronze/silver** e **a maioria dos dashboards e parte dos Genies contorna os MVs** consumindo tabelas cruas. O gargalo está majoritariamente na **modelagem de tabelas gold** (Eixo A) — resolvê-lo destrava MVs, dashboards e Genies de uma vez.

### Os 4 eixos de trabalho
- **Eixo A — Modelagem de Tabelas** (bronze/silver → gold): a raiz do problema.
- **Eixo B — Materialização de Views gold** (`vw_*` que escondem silver/bronze).
- **Eixo C — Métricas a criar** (novos Metric Views para tabelas gold hoje sem MV).
- **Eixo D — Reconfiguração de Consumo** (apontar Dashs/Genies para os MVs).

### Estado-alvo por avenida

| Avenida | Hoje | Maior alavanca | Estado-alvo |
|---|---|---|---|
| 🛒 CRMBACK | ✅ Ideal (3/3 MVs gold) | — (referência) | Manter; unificar consumo no MV |
| 📢 ADS | 🟠 Moderado (3✅/1🟡/1🔴) | Promover bronze.giftback + criar Genie | 5 MVs gold + Genie ADS |
| 🎫 ValeBonus | 🟠 Moderado MVs / 🔴 dashs | Promover bronze.valebonus | MVs gold + Genies só-MV |
| 🎁 Giftback | 🟡 Atenção (0✅/9🟡/1🔴) | Promover silver.giftback.{bonus,masters} | 10 MVs gold |
| 🎀 Presenteia | 🔴 Crítico (15/15 bronze) | Criar camada gold presenteia | 15 MVs gold + Orion sobe p/ gold |

---

## 2. Eixo A — Modelagem de Tabelas (bronze/silver → gold)

> **Raiz da governança.** Ordenado por nº de consumidores (MVs + dashboards + Genies) desbloqueados. Cada item é uma tabela a **promover/modelar em gold**.

- [ ] **A1 · 🔴 P0 · `silver.giftback.bonus` → gold** _(maior alavanca Giftback)_
  Promover a tabela gold modelada (ou MV de base). **Destrava:** `mv_bonus_gerado_marca`, `mv_bonus_perdido_marca`, `mv_bonus_resgate_marca`, `mv_engajamento_marca` + 5 MVs via views + dashboards GB (natura, vivara, cs_giftback, cs_acoes) + escape do Genie Clara. **Impacto: 6+ MVs, 4 dashs, 1 Genie.**

- [ ] **A2 · 🔴 P0 · `silver.giftback.masters` → gold**
  Promover a gold. **Destrava:** `mv_bonus_gerado_master`, `mv_bonus_resgate_master`, `mv_engajamento_master` + views correlatas. **Impacto: 5+ MVs.** _(A1+A2 juntas resolvem 9 dos 10 MVs de Giftback.)_

- [ ] **A3 · 🔴 P0 · `bronze.giftback.bonus` → gold**
  Promover. É a **única fonte bronze** na cadeia do `mv_bonus_perdido_master` (via `vw_bonus_var_perdido`) e também alimenta `mv_ads_receita_ads`. **Impacto: 2 MVs, Genie Clara.**

- [ ] **A4 · 🔴 P0 · `bronze.presenteia.orders` → `gold.presenteia.orders`**
  Modelar tabela gold. **Destrava:** `mv_pedidos`, `mv_pedidos_2`, `mv_take_rate`, `mv_lembretes_enviados` + Genie Orion. **Impacto: 4+ MVs, 1 Genie.**

- [ ] **A5 · 🔴 P0 · `bronze.presenteia.user_events` → `gold.presenteia.user_events`**
  Modelar gold. **Destrava:** `mv_lembretes_criados`, `mv_lembretes_criados_2`, `mv_usuario_com_engajamento`, `mv_contatos`. **Impacto: 4 MVs.**

- [ ] **A6 · 🟡 P1 · `bronze.presenteia.users` → `gold.presenteia.users`**
  Modelar gold. **Destrava:** `mv_usuarios`, `mv_contatos`, `mv_lembretes_enviados`. **Impacto: 3 MVs, Genie Orion.**

- [ ] **A7 · 🟡 P1 · `bronze.presenteia.user_contacts` → `gold.presenteia.user_contacts`**
  Modelar gold. **Destrava:** `mv_kids`, `mv_contatos`, `mv_lembretes_enviados`. **Impacto: 3 MVs.**

- [ ] **A8 · 🟡 P1 · `silver.giftback.vendas_totais` → gold**
  Promover. **Destrava:** `mv_bonus_vendatotal_marca`, `mv_engajamento_marca`, `mv_engajamento_master` + dashboards GB (`fvendas`). **Impacto: 3 MVs.**

- [ ] **A9 · 🔴 P1 · `bronze.valebonus.pre_charge_transaction` → gold**
  Modelar gold. Fonte bronze mais recorrente nos dashboards Vivo (`fEmissao`, `fResgates_*`, `fMailing`, `fPreCharge`). **Impacto: dashs VB vivo + vivo_acompanhamento (núcleo do scorecard 🔴).**

- [ ] **A10 · 🔴 P1 · `bronze.valebonus.applications` → gold (ou MV)**
  Modelar. Usada pelo Genie VB Insights (consulta direta a bronze) e dashboards `dEmissor`. **Impacto: 1 Genie, dashs VB.**

- [ ] **A11 · 🟡 P1 · `bronze.valebonus.redemptions` → `gold.valebonus.redemptions`**
  Modelar gold. **Destrava:** `mv_resgate_ifood_embedded` (hoje 🔴 por bronze + silver.giftback.bonus). **Impacto: 1 MV, Genie Ifood Embedded.**

- [ ] **A12 · 🟡 P2 · `silver.valebonus.usuarios` → gold**
  Promover. **Destrava:** `mv_valebonus_cadastro` (sobe 🟡→✅) + dashs VB (`fUser`, `fResgate_perfil`). **Impacto: 1 MV, dashs VB, Genie VB Insights.**

- [ ] **A13 · 🟡 P2 · Demais bronze ValeBonus → gold**
  `consumer`, `device`, `session`, `cellphone`, `campaign`, `offer`, `wallet_transaction`, `pre_charge_transaction` (cobertos em A9). Modelar dims/fatos gold para zerar bronze nos dashs `parceiros_interno`, `vale_bonus_parceiros`, `vivo`. **Impacto: dashs VB.**

- [ ] **A14 · 🟡 P2 · Camada gold Zendesk (`bronze.zendesk.*` + `silver.zendesk.*`)**
  Modelar gold para atendimento. **Destrava:** `mv_atendimentos` (Presenteia). **Impacto: 1 MV.**

- [ ] **A15 · 🔴 P2 · Bronze Giftback de dashboards → gold/dims**
  `customers`, `customer_configs`, `customer_users`, `orders`, `total_sales`, `sms_mgms`, `ads_activities`, `ads_offers`, `brands`. Criar dims/fatos gold (`dim_customers`, `dim_customer_users`, `gold.giftback.orders`, `dim_brands`). **Destrava:** dashs GB (natura, vivara) + ADS (Acompanhamento Motor, Gerencial). **Impacto: dashs ADS+GB.**

- [ ] **A16 · 🟡 P2 · `silver.ext_bases.vivo_vale_bonus_usuarios_extras`**
  Avaliar promoção/governança da base externa Vivo. Usada nos dashs `vivo_acompanhamento_de_meta`. **Impacto: dashs VB Vivo.**

---

## 3. Eixo B — Materialização de Views gold

> Views `gold.*.vw_*` que **aparentam ser gold mas leem silver/bronze por baixo**. Materializar como tabela gold (após as fontes do Eixo A serem promovidas).

- [ ] **B1 · 🔴 P0 · `gold.giftback.vw_bonus_var_perdido`**
  Contém `bronze.giftback.bonus` (além de silver). **Prioridade:** reescrever sem bronze (depende de A3) e materializar. Afeta `mv_bonus_perdido_master` (usado pela Clara).

- [ ] **B2 · 🟡 P1 · `gold.ads.vw_ads_resgate`**
  Lê `gold.ads.b2b_bonification` + `silver.giftback.bonus`. Materializar como tabela gold pura, eliminando o silver. Afeta `mv_ads_resgate`.

- [ ] **B3 · 🟡 P1 · `gold.giftback.vw_bonus_var_gerado`**
  Lê silver. Materializar após A1/A2. Afeta `mv_bonus_gerado_master`.

- [ ] **B4 · 🟡 P1 · `gold.giftback.vw_bonus_var_resgate_marca`**
  Lê `silver.giftback.bonus`. Materializar após A1. Afeta `mv_bonus_resgate_marca`.

- [ ] **B5 · 🟡 P1 · `gold.giftback.vw_bonus_var_resgate`**
  Lê silver. Materializar após A1/A2. Afeta `mv_bonus_resgate_master`.

- [ ] **B6 · 🟡 P1 · `gold.giftback.vw_bonus_var_vendatotal`**
  Lê silver. Materializar após A8. Afeta `mv_bonus_vendatotal_master`.

> ℹ️ Os MVs `mv_engajamento_*` são SQL/CTE direto sobre silver — **reescrever a CTE** para apontar às fontes gold quando A1/A2/A8 estiverem prontas (tratado em D8).

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
- [ ] **C6 · 🟡 P2 · `mv_valebonus_usuarios`** — a partir de `silver.valebonus.usuarios` (após A12). Hoje não há MV de usuários VB; usado por VB Insights e dashs.
- [ ] **C7 · 🟡 P2 · MVs para fatos VB hoje soltos em gold** — avaliar criar MVs sobre `gold.valebonus.{emissions_agregado, cohort_parceiros, emissoes_unicas_mensais, emissoes_vivo_bi, pbi_VBO02_Parceiro}` usados direto em dashs sem camada semântica.

### 🎀 Presenteia
- [ ] **C8 · 🟡 P2 · MVs de conversas/eventos para o Orion** — após Eixo A, criar MVs gold equivalentes a `silver.presenteia.{tb_conversas, tb_entradas, tb_eventos}` (hoje sem MV; Orion usa silver por falta de opção). Consolidar os MVs duplicados (`mv_pedidos`/`mv_pedidos_2`, `mv_lembretes_criados`/`_2`) numa única definição.

### 🎫 VB Insights — bronze sem MV
- [ ] **C9 · 🔴 P2 · MV para `bronze.valebonus.applications`** — criar gold/MV equivalente (liga-se a A10) para eliminar consulta bronze do Genie.

---

## 5. Eixo D — Reconfiguração de Consumo (Dashboards & Genies)

> Não exige modelagem nova — é **apontar o consumidor para o MV/gold** já existente. Várias são ganho rápido.

### Genies (Agents)
- [ ] **D1 · 🔴 P0 · Reconfigurar Genie "Ifood Embedded"** — trocar `datalab.valebonus.prd_ifood_embeded_resgates` (staging não governado) por `gold.valebonus.mv_resgate_ifood_embedded`. **🚨 O MV já existe e está sendo ignorado** — mudança apenas de configuração.
- [ ] **D2 · 🔴 P0 · Criar Genie ADS** — conectar aos 5 MVs gold prontos (`mv_ads_bonification`, `mv_ads_message`, `mv_ads_offer`, `mv_ads_resgate`, `mv_ads_receita_ads`). É a **única avenida sem Genie** e a infraestrutura já está pronta.
- [ ] **D3 · 🟡 P1 · Restringir Genie "Clara" a só-MVs** — remover acesso direto a `silver.giftback.bonus`; forçar uso exclusivo dos 8 MVs. Garante semântica consistente em 100% das consultas.
- [ ] **D4 · 🟡 P2 · Unificar Genie "VB Insights"** — remover `gold.valebonus.emissions` e `transactions_shops` diretos (redundantes com `mv_emissoes`/`mv_resgates`); usar só os MVs. Após A12, remover silver.
- [ ] **D5 · 🟡 P2 · Unificar Genie "CRMBACK Analysis"** — remover `gold.crmback.tb_carrinhos` direto; usar só `mv_carrinhos`. Padronização total.
- [ ] **D6 · 🟡 P2 · Reapontar Genie "Orion"** — após A4–A7, migrar de `silver.presenteia.*` para os MVs gold do Presenteia.

### Dashboards
- [ ] **D7 · 🟡 P1 · Migrar datasets PBI dos dashs ADS para MVs** — onde o dash usa SQL direto sobre bronze/gold (`f_ads_geral_motor`, `brand`, `Brand_Offer`, etc.), reapontar para os MVs e dims gold à medida que A15/C1–C5 ficam prontos.
- [ ] **D8 · 🟡 P1 · Reescrever CTEs dos MVs SQL/CTE** — `mv_engajamento_marca/master`, `mv_ads_receita_ads` e demais MVs definidos por CTE sobre silver/bronze: trocar fontes pelas tabelas gold dos Eixos A/B.
- [ ] **D9 · 🟡 P2 · Migrar dashs Giftback para MVs/gold** — natura, vivara, cs_giftback, cs_acoes: trocar `bronze.giftback.*` (A15) e `silver.giftback.bonus` (A1) pelos MVs/dims gold.
- [ ] **D10 · 🟡 P2 · Migrar dashs ValeBonus para MVs/gold** — parceiros_interno, vale_bonus_parceiros, vivo, vivo_acompanhamento: substituir `bronze.valebonus.*` (A9/A13) e silver pelos MVs/gold.

---

## 6. Matriz de Priorização — Onde atuar primeiro

> Sequência recomendada: as tarefas P0 destravam o maior número de consumidores com menor esforço relativo.

### 🚀 P0 — Faça primeiro (alto impacto)
| ID | Tarefa | Tipo | Destrava |
|---|---|---|---|
| D1 | Reconfigurar Ifood Embedded → MV existente | Config | 1 Genie (ganho imediato, esforço ~zero) |
| D2 | Criar Genie ADS | Config | Fecha o único gap de cobertura por avenida |
| A1+A2 | `silver.giftback.{bonus,masters}` → gold | Modelagem | 9/10 MVs Giftback + 4 dashs + Clara |
| A3 | `bronze.giftback.bonus` → gold | Modelagem | 2 MVs + Clara |
| A4+A5 | `bronze.presenteia.{orders,user_events}` → gold | Modelagem | 8 MVs Presenteia + Orion |
| B1 | Materializar `vw_bonus_var_perdido` (sem bronze) | View | `mv_bonus_perdido_master` |

### 🔧 P1 — Em seguida (consolida o ganho)
A6, A7, A8, A9, A10, A11 · B2–B6 · C1 · D3, D7, D8

### 🧹 P2 — Refinamento e cauda longa
A12–A16 · C2–C9 · D4, D5, D6, D9, D10

### Dependências-chave
- **Views (Eixo B)** dependem da promoção das fontes (Eixo A) — materializar só depois.
- **Novos MVs Presenteia (C8)** dependem de A4–A7.
- **Migração de dashs/Genies (D6–D10)** depende do gold correspondente já existir.
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
