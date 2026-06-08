---
tipo: resumo-executivo
origem: "[[Governança de Dados - Camada MV, DASHs e Agents]]"
plano: "[[Plano de Engenharia de Dados — Governança MV]]"
data: 2026-06-08
publico: diretoria
regra: "Silver é considerado verde (camada modelada e governada). Apenas bronze/externo é vermelho."
---

# 📊 Governança de Dados — Resumo Executivo

> **Pergunta:** os dados que alimentam nossas decisões vêm de uma camada **modelada e governada**?
> **Padrão:** métrica → **Metric View** · fonte → **gold (ideal)** ou **silver (aceitável)** — o que **não** pode é bronze/planilha.

---

## 🎨 O que significa cada cor

| Cor | Camada do dado | O que é | Situação |
|:---:|---|---|---|
| 🟩 **Verde** | **Gold, Silver ou Metric View** | Dado **modelado e governado** (gold = ideal · silver = tratado/aceitável) | ✅ OK |
| 🟥 **Vermelho** | **Bronze** ou fonte externa | Dado **cru / planilha** — sem tratamento, risco de erro e divergência | ❌ Ação necessária |

> **Mudança de régua:** o **silver agora conta como verde**. Silver é uma camada já modelada — o problema de governança real é o consumo de **bronze/externo**. As barras abaixo mostram quanto de cada avenida já está em camada governada (🟩) vs. dado cru (🟥).

---

## 🚦 Painel Consolidado por Avenida

| Avenida | 📐 Metric Views | 🤖 Agentes | 📈 Dashboards | Geral |
|---|:---:|:---:|:---:|:---:|
| 🛒 **CRMBACK** | 🟩 100% | 🟩 100% | — | 🟩 **Referência** |
| 🎁 **Giftback** | 🟩 90% | 🟩 100% (Clara) | 🟧 55% | 🟢 **Bom** (dashs a migrar) |
| 🎫 **ValeBonus** | 🟩 80% | 🟧 Ifood crítico | 🟩 73% | 🟧 **Moderado** |
| 📢 **ADS** | 🟩 80% | 🟥 Sem Genie | 🟧 60% | 🟧 **Atenção** |
| 🎀 **Presenteia** | 🟥 0% (100% bronze) | 🟩 100% (Orion) | — | 🟥 **Crítico (MVs)** |

> Com a nova régua, **a maioria das avenidas já está majoritariamente em verde**. Os focos vermelhos se concentram em poucos pontos claros (detalhados abaixo).

---

## 1️⃣ Metric Views — % em camada governada vs bronze

**38 Metric Views · 53% já em camada governada (gold+silver) · 47% ainda em bronze**

| Avenida | Distribuição (🟩 governado → 🟥 bronze) | 🟩 Verde | 🟥 Bronze |
|---|---|:---:|:---:|
| 🛒 CRMBACK | 🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩 | **100%** | 0% |
| 🎁 Giftback | 🟩🟩🟩🟩🟩🟩🟩🟩🟩🟥 | **90%** | 10% |
| 📢 ADS | 🟩🟩🟩🟩🟩🟩🟩🟩🟥🟥 | **80%** | 20% |
| 🎫 ValeBonus | 🟩🟩🟩🟩🟩🟩🟩🟩🟥🟥 | **80%** | 20% |
| 🎀 Presenteia | 🟥🟥🟥🟥🟥🟥🟥🟥🟥🟥 | **0%** | 100% |
| **TOTAL** | 🟩🟩🟩🟩🟩🟥🟥🟥🟥🟥 | **53%** | 47% |

> Do verde total (20 MVs): 9 em **gold** (ideal) e 11 em **silver** (aceitável).

**📌 Leitura:** quase todas as avenidas já têm MVs em camada governada. O vermelho é **quase todo Presenteia** (15 MVs, 100% bronze) — é onde está concentrado o esforço.

---

## 2️⃣ Agentes (Genies) — % em camada governada + % via Metric View

| Genie | Avenida | Distribuição | 🟩 Verde | 🟥 Vermelho | **% via Metric View** |
|---|---|---|:---:|:---:|:---:|
| CRMBACK Analysis | 🛒 CRMBACK | 🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩 | 100% | 0% | **75%** |
| Clara | 🎁 Giftback | 🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩 | 100% | 0% | **89%** |
| Orion | 🎀 Presenteia | 🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩 | 100% | 0% | **0%** (usa silver) |
| VB Insights | 🎫 ValeBonus | 🟩🟩🟩🟩🟩🟩🟩🟩🟥 | 86% | 🟥 14% | **43%** |
| Ifood Embedded | 🎫 ValeBonus | 🟥🟥🟥🟥🟥🟥🟥🟥🟥🟥 | 0% | 🟥 100% | **0%** |
| — ausente — | 📢 **ADS** | — | — | — | **sem agente** |

**📌 Leitura:** com silver valendo como verde, **4 dos 5 agentes estão verdes** (o Orion sobe de "atenção" para 100% — ele usa silver, que agora é aceitável). Restam **2 focos**: o **Ifood Embedded** (usa staging não-governado, mesmo havendo MV pronto) e a **ausência de Genie no ADS**.

---

## 3️⃣ Dashboards — % em camada governada vs bronze/planilha

**15 dashboards · ainda é onde mais se consome dado cru**

| Avenida | Distribuição (🟩 governado → 🟥 bronze/ext) | 🟩 Verde | 🟥 Bronze/externo |
|---|---|:---:|:---:|
| 🎫 ValeBonus (5 dashs) | 🟩🟩🟩🟩🟩🟩🟩🟥🟥🟥 | **73%** | 27% |
| 📢 ADS (6 dashs) | 🟩🟩🟩🟩🟩🟩🟥🟥🟥🟥 | **60%** | 40% |
| 🎁 Giftback (4 dashs) | 🟩🟩🟩🟩🟩🟩🟥🟥🟥🟥 | **55%** | 45% |

> ⚠️ Mesmo o verde aqui é, em boa parte, **tabela consumida direto (gold/silver) — não o Metric View**. A adoção da camada semântica nos dashboards segue sendo um ponto a evoluir.

**📌 Leitura:** os dashboards continuam sendo o ponto **mais vermelho** — Giftback (45%) e ADS (40%) ainda puxam bronze/planilha. Aqui o ganho vem de **trocar o bronze pela silver/gold equivalente** (a maior parte já existe).

---

## 4️⃣ Tabelas Críticas — candidatas a documentação + monitoramento

> As tabelas **mais reutilizadas** no ecossistema (dashboards + MVs + agentes) — a "visão de dados" que mais chega ao cliente. Independente da camada, merecem documentação e monitoramento. As 🟥 bronze são também as que precisam de saneamento.

| Tabela | Camada | 📈 Dash | 📐 MV | 🤖 Agente | **Total** | Nota |
|---|:---:|:---:|:---:|:---:|:---:|---|
| `silver.giftback.bonus` | 🟩 Silver | 11 | 11 | 1 | **23** | ✅ governada — **a mais usada de todas**, monitorar a fundo |
| `bronze.giftback.bonus` | 🟥 Bronze | 6 | 2 | — | **8** | 🔴 trocar pela silver acima |
| `gold.ads.b2b_bonification` | 🟩 Gold | 5 | 3 | — | **8** | ✅ núcleo ADS |
| `silver.giftback.masters` | 🟩 Silver | 3 | 5 | — | **8** | ✅ governada |
| `gold.ads.b2b_message` | 🟩 Gold | 6 | 1 | — | **7** | ✅ núcleo ADS |
| `gold.ads.b2b_offer` | 🟩 Gold | 5 | 2 | — | **7** | ✅ núcleo ADS |
| `bronze.giftback.orders` | 🟥 Bronze | 6 | 1 | — | **7** | 🔴 remodelar |
| `silver.giftback.vendas_totais` | 🟩 Silver | 2 | 4 | — | **6** | ✅ governada |
| `bronze.giftback.brands` | 🟥 Bronze | 5 | 1 | — | **6** | 🔴 remodelar (cadastro de marcas) |
| `gold.ads.cpl_cpi_cpa` | 🟩 Gold | 4 | 1 | — | **5** | ✅ parâmetros de receita |
| `silver.valebonus.usuarios` | 🟩 Silver | 3 | 1 | 1 | **5** | ✅ governada |
| `bronze.presenteia.user_events` | 🟥 Bronze | — | 5 | — | **5** | 🔴 trocar por `silver.presenteia.tb_eventos` |
| `bronze.presenteia.orders` | 🟥 Bronze | — | 4 | — | **4** | 🔴 trocar por `silver.presenteia.tb_pedidos` |

**📌 Leitura:** as tabelas mais usadas já são, em boa parte, **verdes (gold/silver)** — boa notícia. As 🟥 bronze recorrentes (`giftback.bonus/orders/brands`, núcleo `presenteia`) são as que faltam sanear, e na maioria **já têm uma silver equivalente para trocar**.

> 💡 **Recomendação:** definir um grupo de **"golden tables"** (as ~13 acima) com dono nomeado, dicionário de dados, contrato (schema + SLA de frescor) e **monitoramento de qualidade** — incluindo as silver mais usadas, que agora são camada oficial.

---

## 🎯 A Mensagem Central

> Com o **silver reconhecido como camada governada**, o cenário é mais saudável do que parecia: **a maioria das métricas, agentes e dashboards já está em verde**.
>
> O problema de governança se **isola no consumo de bronze**, concentrado em pontos claros:
> - 🎀 **Presenteia** — os 15 MVs leem bronze direto (e a silver equivalente já existe para a maioria);
> - 📈 **Dashboards** Giftback/ADS — 40–45% ainda em bronze/planilha;
> - 🎫 **Ifood Embedded** — agente usa staging não-governado, ignorando o MV pronto.
>
> **O trabalho deixou de ser "promover silver para gold" e passou a ser "tirar o bronze do caminho"** — na maioria dos casos, apenas **trocar o bronze pela silver que já existe**.

---

## ✅ Plano de Ação — foco no que ainda é vermelho

| Frente | O que é | Onde dói mais |
|---|---|---|
| **A — Sanear bronze** | trocar bronze pela silver existente; remodelar onde não há | 🎀 Presenteia (MVs) e 📈 dashs Giftback/ADS |
| **D — Reapontar consumo** | dashs/agentes → usar MV/silver/gold | Ifood, dashs em geral |
| **C — Criar métricas faltantes** | tabelas sem Metric View | 📢 ADS, 🎫 ValeBonus |

### 🚀 3 vitórias rápidas (alto impacto, baixo esforço)
1. **Reconfigurar o agente Ifood Embedded** → usar o Metric View que já existe _(0% → 100% verde, custo ~zero)_.
2. **Criar o Genie de ADS** → fecha o único vazio de cobertura por avenida (5 MVs prontos).
3. **Trocar o bronze do Presenteia pelas silver `tb_*` que já existem** → tira ~8 MVs do vermelho **sem precisar modelar nada novo** (a silver já está pronta e governada).

---

_Detalhamento técnico em [[Plano de Engenharia de Dados — Governança MV]] · tabelas silver em uso em [[Mapa de Tabelas Silver em Uso]] · base de investigação em [[Governança de Dados - Camada MV, DASHs e Agents]]._
