---
tipo: mapa-tabelas
origem: "[[Governança de Dados - Camada MV, DASHs e Agents]]"
plano: "[[Plano de Engenharia de Dados — Governança MV]]"
data: 2026-06-08
escopo: "Todas as tabelas silver efetivamente consumidas por Metric Views, Agentes e Dashboards"
---

# 🟨 Mapa das Tabelas Silver em Uso

> Levantamento de **todas as tabelas `silver.*` realmente consumidas** por Metric Views, Agentes (Genies) e Dashboards, extraído de [[Governança de Dados - Camada MV, DASHs e Agents]].
> Objetivo: mapear **o que está de fato em uso** na camada silver — base para decidir o que promover a gold.
>
> 🟨 Silver = dado semi-tratado (aceitável com ressalva). O estado-alvo é que MVs consumam **gold**; silver é tolerado temporariamente.

## 📊 Resumo

- **18 tabelas silver** distintas em uso, em **7 schemas**.
- Concentração altíssima: **`silver.giftback.bonus` sozinha é consumida por 23 objetos** (11 MVs + 11 dashboards + 1 agente).
- 5 tabelas alimentam **Metric Views** (são as prioritárias para promoção a gold).
- O schema `silver.presenteia.tb_*` existe e é usado **só pelo Orion** — são as silver que devem substituir o bronze do Presenteia (ver plano, A2.4–A2.6 / A3.2).

---

## 🏆 Ranking por uso (todas as silver)

| # | Tabela silver | 📐 MV | 🤖 Agente | 📈 Dash | **Total** | Alimenta MV? |
|---|---|:---:|:---:|:---:|:---:|:---:|
| 1 | `silver.giftback.bonus` | 11 | 1 | 11 | **23** | ✅ sim (11) |
| 2 | `silver.giftback.masters` | 5 | — | 3 | **8** | ✅ sim (5) |
| 3 | `silver.giftback.vendas_totais` | 4 | — | 2 | **6** | ✅ sim (4) |
| 4 | `silver.valebonus.usuarios` | 1 | 1 | 3 | **5** | ✅ sim (1) |
| 5 | `silver.presenteia.tb_conversas` | — | 1 | — | **1** | ❌ (Orion) |
| 6 | `silver.presenteia.tb_entradas` | — | 1 | — | **1** | ❌ (Orion) |
| 7 | `silver.presenteia.tb_eventos` | — | 1 | — | **1** | ❌ (Orion) |
| 8 | `silver.presenteia.tb_pedidos` | — | 1 | — | **1** | ❌ (Orion) |
| 9 | `silver.presenteia.tb_usuarios` | — | 1 | — | **1** | ❌ (Orion) |
| 10 | `silver.valebonus.p5` | — | — | 2 | **2** | ❌ |
| 11 | `silver.valebonus.transactions` | — | — | 2 | **2** | ❌ |
| 12 | `silver.ads.restricao_ofertas_logs` | — | — | 1 | **1** | ❌ |
| 13 | `silver.bsat.user` | — | — | 1 | **1** | ❌ |
| 14 | `silver.ext_bases.vivo_vale_bonus_usuarios_extras` | — | — | 1 | **1** | ❌ (base externa) |
| 15 | `silver.giftback.usuarios` | — | — | — | — | (citada, sem consumidor mapeado) |
| 16 | `silver.giftback.usuarios_qualidade` | — | — | 1 | **1** | ❌ |
| 17 | `silver.zendesk.tickets` | 1 | — | — | **1** | ✅ sim (1) |
| 18 | `silver.zendesk.users` | 1 | — | — | **1** | ✅ sim (1) |

> ✅ As tabelas que **alimentam Metric Views** (`giftback.bonus`, `giftback.masters`, `giftback.vendas_totais`, `valebonus.usuarios`, `zendesk.users/tickets`) são as **candidatas prioritárias a promoção para gold** — são elas que mantêm 🟡 a governança dos MVs.

---

## 📂 Detalhamento por schema

### `silver.giftback` — núcleo Giftback (mais crítico)
| Tabela | MVs que consomem | Dashboards | Agente |
|---|---|---|---|
| `bonus` | `mv_ads_receita_ads`, `mv_ads_resgate`, `mv_bonus_gerado_marca`, `mv_bonus_gerado_master`, `mv_bonus_perdido_marca`, `mv_bonus_perdido_master`, `mv_bonus_resgate_marca`, `mv_bonus_resgate_master`, `mv_engajamento_marca`, `mv_engajamento_master`, `mv_resgate_ifood_embedded` (11) | ADS Monitoramento/Sem Parar/ARM/quartou + GB natura/vivara/cs_acoes/cs_giftback + VB Ifood/parceiros_interno/vivo (11) | Clara |
| `masters` | `mv_bonus_gerado_master`, `mv_bonus_perdido_master`, `mv_bonus_resgate_master`, `mv_bonus_vendatotal_master`, `mv_engajamento_master` (5) | GB natura/vivara/cs_acoes (3) | — |
| `vendas_totais` | `mv_bonus_vendatotal_marca`, `mv_bonus_vendatotal_master`, `mv_engajamento_marca`, `mv_engajamento_master` (4) | GB cs_giftback + VB Ifood (2) | — |
| `usuarios` | — | — | — |
| `usuarios_qualidade` | — | GB cliente_vivara (1) | — |

### `silver.presenteia` — base do Orion (substituem o bronze do Presenteia)
| Tabela | Consumo |
|---|---|
| `tb_conversas` | Agente Orion |
| `tb_entradas` | Agente Orion |
| `tb_eventos` | Agente Orion |
| `tb_pedidos` | Agente Orion |
| `tb_usuarios` | Agente Orion |

> 💡 Estas 5 silver são a **alternativa governada** ao bronze do Presenteia. O plano (A2.4–A2.6) prevê trocar `bronze.presenteia.{orders,users,user_events}` por `tb_pedidos`/`tb_usuarios`/`tb_eventos`.

### `silver.valebonus` — usuários e transações VB
| Tabela | MVs | Dashboards | Agente |
|---|---|---|---|
| `usuarios` | `mv_valebonus_cadastro` (1) | VB vale_bonus_parceiros/vivo/vivo_acompanhamento (3) | VB Insights |
| `transactions` | — | VB vivo/vivo_acompanhamento (2) | — |
| `p5` | — | VB vivo/vivo_acompanhamento (2) | — |

### `silver.zendesk` — atendimento (Presenteia)
| Tabela | Consumo |
|---|---|
| `users` | `mv_atendimentos` |
| `tickets` | `mv_atendimentos` |

> ⚠️ `mv_atendimentos` usa `silver.zendesk.users/tickets` + `bronze.zendesk.organizations`. Falta `silver.zendesk.organizations` para o domínio ficar completo (plano A3.6).

### Outras silver (uso pontual em dashboards)
| Tabela | Consumo |
|---|---|
| `silver.ads.restricao_ofertas_logs` | Dash ADS Monitoramento |
| `silver.bsat.user` | Dash VB Ifood Embedded |
| `silver.ext_bases.vivo_vale_bonus_usuarios_extras` | Dash VB vivo_acompanhamento (base externa Vivo) |

---

## 🎯 Leitura para governança

1. **`silver.giftback.bonus` é o ponto único de maior risco** — 23 consumidores. Promovê-la a gold (ou materializar uma gold equivalente) sobe a governança de quase toda a avenida Giftback de uma vez.
2. **6 silver alimentam Metric Views** (`giftback.bonus/masters/vendas_totais`, `valebonus.usuarios`, `zendesk.users/tickets`) — são as que mantêm MVs em 🟡 e devem ser promovidas a gold primeiro.
3. **As `silver.presenteia.tb_*` são "boas silver"**: já existem e são governadas — o trabalho ali é trocar o bronze por elas, não criá-las.
4. Silver de uso pontual em dashboards (`ads.restricao_ofertas_logs`, `bsat.user`, `valebonus.p5/transactions`) podem seguir em silver no curto prazo — baixo raio de impacto.

_Extraído de [[Governança de Dados - Camada MV, DASHs e Agents]] em 2026-06-08. Cruza com o inventário bronze do [[Plano de Engenharia de Dados — Governança MV]] (Eixo A)._
