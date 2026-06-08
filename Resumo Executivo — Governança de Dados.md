---
tipo: resumo-executivo
origem: "[[Governança de Dados - Camada MV, DASHs e Agents]]"
plano: "[[Plano de Engenharia de Dados — Governança MV]]"
data: 2026-06-07
publico: diretoria
---

# 📊 Governança de Dados — Resumo Executivo

> **Pergunta:** os dados que alimentam nossas decisões vêm de uma fonte confiável e padronizada?
> **Padrão-ouro:** métrica → **Metric View** (camada semântica única) · fonte do MV → **gold** (dado tratado).

---

## 🎨 O que significa cada cor

| Cor | Camada do dado | O que é | Confiança |
|:---:|---|---|---|
| 🟩 **Verde** | **Gold** (ou Metric View) | Dado **tratado, validado e modelado** — padrão oficial | ✅ Confiável |
| 🟨 **Amarelo** | **Silver** | Dado **semi-tratado** — serve, mas sem garantia total de qualidade | ⚠️ Aceitável com ressalva |
| 🟥 **Vermelho** | **Bronze** ou fonte externa | Dado **cru / planilha** — sem tratamento, risco de erro e divergência | ❌ Ação necessária |

> As barras abaixo mostram **quanto de cada cor** existe em cada avenida. Quanto mais verde, mais saudável.

---

## 🚦 Painel Consolidado por Avenida

| Avenida | 📐 Metric Views | 🤖 Agentes | 📈 Dashboards | Geral |
|---|:---:|:---:|:---:|:---:|
| 🛒 **CRMBACK** | 🟩 Ideal | 🟩 Ideal | — | 🟩 **Referência** |
| 📢 **ADS** | 🟧 Moderado | 🟥 Sem Genie | 🟥 Crítico | 🟧 **Atenção** |
| 🎫 **ValeBonus** | 🟧 Moderado | 🟧 Moderado | 🟥 Crítico | 🟥 **Crítico** |
| 🎁 **Giftback** | 🟨 Atenção | 🟢 Bom | 🟥 Crítico | 🟨 **Atenção** |
| 🎀 **Presenteia** | 🟥 Crítico | 🟨 Atenção | — | 🟥 **Crítico** |

---

## 1️⃣ Metric Views — % de fonte Gold / Silver / Bronze

**38 Metric Views · só 24% têm fonte 100% confiável (gold)**

| Avenida | Distribuição da fonte | 🟩 Gold | 🟨 Silver | 🟥 Bronze |
|---|---|:---:|:---:|:---:|
| 🛒 CRMBACK | 🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩 | **100%** | 0% | 0% |
| 📢 ADS | 🟩🟩🟩🟩🟩🟩🟨🟨🟥🟥 | **60%** | 20% | 20% |
| 🎫 ValeBonus | 🟩🟩🟩🟩🟩🟩🟨🟨🟥🟥 | **60%** | 20% | 20% |
| 🎁 Giftback | 🟨🟨🟨🟨🟨🟨🟨🟨🟨🟥 | **0%** | 90% | 10% |
| 🎀 Presenteia | 🟥🟥🟥🟥🟥🟥🟥🟥🟥🟥 | **0%** | 0% | 100% |
| **TOTAL** | 🟩🟩🟨🟨🟨🟥🟥🟥🟥🟥 | **24%** | 29% | 47% |

**📌 Leitura:** a camada semântica existe, mas **47% dos Metric Views ainda bebem de dado cru (bronze)**. Presenteia (100% bronze) e Giftback (90% silver, 0% gold) são os pontos frágeis.

---

## 2️⃣ Agentes (Genies) — Governança + % que realmente usa Metric View

**O agente deveria responder sempre a partir do Metric View. Onde isso não acontece, ele busca dado cru.**

| Genie | Avenida | Distribuição | 🟩 Verde | 🟨 / 🟥 | **% via Metric View** |
|---|---|---|:---:|:---:|:---:|
| CRMBACK Analysis | 🛒 CRMBACK | 🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩 | 100% | 0% | **75%** |
| Clara | 🎁 Giftback | 🟩🟩🟩🟩🟩🟩🟩🟩🟩🟨 | 89% | 🟨 11% | **89%** |
| VB Insights | 🎫 ValeBonus | 🟩🟩🟩🟩🟩🟩🟩🟨🟥 | 71% | 🟨 14% · 🟥 14% | **43%** |
| Orion | 🎀 Presenteia | 🟨🟨🟨🟨🟨🟨🟨🟨🟨🟨 | 0% | 🟨 100% | **0%** |
| Ifood Embedded | 🎫 ValeBonus | 🟥🟥🟥🟥🟥🟥🟥🟥🟥🟥 | 0% | 🟥 100% | **0%** |
| — ausente — | 📢 **ADS** | — | — | — | **sem agente** |

**📌 Leitura:** quem usa Metric View tem a melhor governança (Clara 89%, CRMBACK 75%). **Orion e Ifood Embedded usam 0% de Metric View** — e no caso do Ifood, **o Metric View certo já existe e está sendo ignorado** (só reconfigurar). ADS, a maior avenida, **não tem agente**.

---

## 3️⃣ Dashboards — % de fonte Gold/MV vs Silver vs Bronze

**15 dashboards · a maioria puxa dado cru e planilha direto, contornando a camada oficial**

| Avenida | Distribuição da fonte | 🟩 Gold/MV | 🟨 Silver | 🟥 Bronze/externo |
|---|---|:---:|:---:|:---:|
| 📢 ADS (6 dashs) | 🟩🟩🟩🟩🟩🟨🟥🟥🟥🟥 | **52%** | 8% | 40% |
| 🎫 ValeBonus (5 dashs) | 🟩🟩🟩🟩🟩🟨🟨🟥🟥🟥 | **50%** | 23% | 27% |
| 🎁 Giftback (4 dashs) | 🟩🟩🟩🟨🟨🟨🟥🟥🟥🟥 | **27%** | 27% | 45% |

> ⚠️ **Atenção:** mesmo o "verde" dos dashboards é, em grande parte, **tabela gold consumida direto — não o Metric View**. Ou seja, a adoção real da camada semântica nos dashboards é ainda menor que o verde sugere.

**📌 Leitura:** é o ponto **mais crítico**. Dashboards de negócio consomem bronze/planilha diretamente (Giftback 45%, ADS 40%), criando risco de **números divergentes** entre relatórios e a fonte oficial.

---

## 4️⃣ Tabelas Críticas — candidatas a documentação + monitoramento

> Estas são as tabelas **mais reutilizadas** em todo o ecossistema (somando dashboards + Metric Views + agentes). São a "visão de dados" que mais chega ao cliente — logo, **as que mais valem documentação caprichada e monitoramento de qualidade**. Quanto mais consumidores, maior o "raio de impacto" se a tabela falhar.

### 🥇 Núcleo central — usadas pelos 3 tipos de consumidor (dash + MV + agente)

| Tabela | Camada | 📈 Dash | 📐 MV | 🤖 Agente | **Total** | Risco |
|---|:---:|:---:|:---:|:---:|:---:|---|
| `silver.giftback.bonus` | 🟨 Silver | 11 | 11 | 1 | **23** | 🔴 **a mais usada de todas** — e ainda é silver |
| `gold.valebonus.emissions` | 🟩 Gold | 4 | 1 | 1 | **6** | ✅ já é gold — só documentar/monitorar |
| `gold.valebonus.transactions_shops` | 🟩 Gold | 2 | 2 | 1 | **5** | ✅ já é gold |
| `silver.valebonus.usuarios` | 🟨 Silver | 3 | 1 | 1 | **5** | 🟡 promover a gold |

### 🥈 Espinha dorsal — usadas por 2 tipos, alto volume

| Tabela | Camada | 📈 Dash | 📐 MV | 🤖 Agente | **Total** | Risco |
|---|:---:|:---:|:---:|:---:|:---:|---|
| `gold.ads.b2b_bonification` | 🟩 Gold | 5 | 3 | — | **8** | ✅ núcleo ADS |
| `bronze.giftback.bonus` | 🟥 Bronze | 6 | 2 | — | **8** | 🔴 dado cru muito consumido |
| `silver.giftback.masters` | 🟨 Silver | 3 | 5 | — | **8** | 🟡 promover a gold |
| `gold.ads.b2b_message` | 🟩 Gold | 6 | 1 | — | **7** | ✅ núcleo ADS |
| `gold.ads.b2b_offer` | 🟩 Gold | 5 | 2 | — | **7** | ✅ núcleo ADS |
| `bronze.giftback.orders` | 🟥 Bronze | 6 | 1 | — | **7** | 🔴 dado cru muito consumido |
| `bronze.giftback.brands` | 🟥 Bronze | 5 | 1 | — | **6** | 🔴 cadastro de marcas cru |
| `silver.giftback.vendas_totais` | 🟨 Silver | 2 | 4 | — | **6** | 🟡 promover a gold |
| `gold.ads.cpl_cpi_cpa` | 🟩 Gold | 4 | 1 | — | **5** | ✅ parâmetros de receita |

### 🎀 Núcleo Presenteia — base de muitos MVs (avenida 100% bronze)

| Tabela | Camada | 📈 Dash | 📐 MV | 🤖 Agente | **Total** | Risco |
|---|:---:|:---:|:---:|:---:|:---:|---|
| `bronze.presenteia.user_events` | 🟥 Bronze | — | 5 | — | **5** | 🔴 base de 5 MVs |
| `bronze.presenteia.orders` | 🟥 Bronze | — | 4 | — | **4** | 🔴 base de 4 MVs |
| `bronze.presenteia.users` | 🟥 Bronze | — | 3 | — | **3** | 🔴 base de 3 MVs |

**📌 Leitura:** **`silver.giftback.bonus` é, isolada, a tabela mais crítica do ecossistema** — alimenta 11 dashboards, ~todos os Metric Views de Giftback e o agente Clara. Um erro nela impacta quase toda a avenida. E há um padrão preocupante: **boa parte das tabelas mais usadas ainda é silver ou bronze** (raio de impacto alto + confiança baixa) — exatamente as que mais precisam de documentação e monitoramento **agora**.

> 💡 **Recomendação:** definir um **grupo "tabelas críticas / golden tables"** (as ~12 acima) com: dono nomeado, dicionário de dados documentado, contrato de dados (schema + SLA de atualização) e **monitoramento de qualidade/frescor**. Priorizar as 🔴 silver/bronze — são as mais usadas e as menos confiáveis.

---

## 🎯 A Mensagem Central

> A **camada semântica (Metric Views) já existe** e cobre as métricas. O problema **não é falta de modelo** — é que **47% dos MVs ainda têm fonte de dado cru** e dashboards/agentes **contornam o modelo** indo direto à fonte.
>
> **80% da solução está numa única frente: a Engenharia tratar as tabelas-base (bronze/silver → gold).** Isso conserta Metric Views, dashboards e agentes de uma vez.

---

## ✅ Plano de Ação — 4 frentes

| Frente | O que é | Onde dói mais |
|---|---|---|
| **A — Tratar tabelas-base** | bronze/silver → gold | 🎀 Presenteia (100% bronze) e 🎁 Giftback (90% silver) |
| **B — Materializar views** | views que escondem dado cru | 🎁 Giftback |
| **C — Criar métricas faltantes** | tabelas gold ainda sem Metric View | 📢 ADS, 🎫 ValeBonus |
| **D — Reapontar consumo** | dashs/agentes → usar o Metric View | todas |

### 🚀 3 vitórias rápidas (alto impacto, baixo esforço)
1. **Reconfigurar o agente Ifood Embedded** → usar o Metric View que já existe _(0% → 100% MV, custo ~zero)_.
2. **Criar o Genie de ADS** → fecha o único vazio de cobertura por avenida (5 MVs já prontos).
3. **Tratar 2 tabelas de Giftback** (`bonus` + `masters`) → conserta **9 dos 10 Metric Views** da avenida de uma só vez (0% → ~90% gold).

---

_Detalhamento técnico em [[Plano de Engenharia de Dados — Governança MV]] · base de investigação em [[Governança de Dados - Camada MV, DASHs e Agents]]._
