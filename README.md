# Monitoramento Inteligente com o Azure

Este repositório reúne um resumo dos conceitos estudados no desafio **Monitoramento Inteligente com o Azure**, da **DIO (Digital Innovation One)**.

O objetivo é compreender como acompanhar a saúde de recursos e aplicações na nuvem, identificar anomalias, investigar falhas e definir alertas usando ferramentas de **observabilidade do Microsoft Azure**.

> Este é um material de estudo. Os exemplos apresentados são ilustrativos; não representam necessariamente serviços implantados ou testes realizados durante o laboratório.

## Objetivos do desafio

- Entender a importância do monitoramento em ambientes de nuvem.
- Diferenciar métricas, logs, traces e alertas.
- Conhecer Azure Monitor, Log Analytics e Application Insights.
- Compreender coleta, consulta e visualização de telemetria.
- Aprender conceitos de monitoramento proativo e resposta a incidentes.
- Avaliar retenção, custos e segurança dos dados de observabilidade.

## 1. Por que monitorar a infraestrutura?

O monitoramento permite detectar degradação de desempenho, falhas e mudanças de comportamento antes que afetem muitos usuários.

É importante observar tanto a **disponibilidade** dos serviços quanto o **desempenho percebido pelo usuário**. Uma VM ligada, por exemplo, não garante que uma aplicação esteja saudável.

## 2. Azure Monitor

O **Azure Monitor** é a plataforma de monitoramento e observabilidade da Azure, utilizada para coletar, analisar e atuar sobre dados de recursos e aplicações.

Ele se integra a ferramentas de consulta, dashboards, alertas e análise de desempenho.

```text
Recursos Azure / Aplicações
          ↓
    Dados de telemetria
      ├── Métricas
      ├── Logs
      └── Traces
          ↓
       Azure Monitor
      ├── Consultas
      ├── Visualizações
      └── Alertas
          ↓
      Investigação / Ação
```

## 3. Métricas, logs e traces

| Tipo | Finalidade | Exemplo |
| --- | --- | --- |
| **Métricas** | Acompanhar valores numéricos ao longo do tempo | Percentual de CPU |
| **Logs** | Registrar eventos e informações detalhadas | Erro de aplicação |
| **Traces** | Acompanhar operações entre componentes | Uma requisição percorrendo diferentes serviços |

A combinação desses dados é essencial para entender o que aconteceu e onde ocorreu o problema.

## 4. Azure Metrics Explorer

O **Metrics Explorer** permite visualizar métricas disponibilizadas por recursos monitorados.

Alguns indicadores são:

- utilização de CPU;
- tráfego de rede;
- operações de armazenamento;
- erros e latência, conforme o serviço;
- uso de recursos ao longo do tempo.

Métricas disponíveis variam por tipo de recurso. Métricas de memória dentro de VMs, por exemplo, normalmente exigem coleta de dados do sistema convidado.

## 5. Log Analytics Workspace

Um **Log Analytics Workspace** é um ambiente que armazena e permite consultar determinados dados de log coletados e enviados para o Azure Monitor.

Consultas podem ser feitas com **Kusto Query Language (KQL)**.

Exemplo ilustrativo para dados da tabela Heartbeat, se essa coleta estiver configurada:

```kusto
Heartbeat
| summarize UltimoSinal = max(TimeGenerated) by Computer
| order by UltimoSinal desc
```

Essa consulta ajuda a identificar o horário do último sinal registrado por computador. A ausência de dados pode indicar problemas de conectividade, agente, configuração ou coleta e não prova, isoladamente, uma indisponibilidade.

## 6. Application Insights

O **Application Insights** é uma funcionalidade de monitoramento de aplicações integrada ao Azure Monitor.

Pode registrar, quando devidamente instrumentado:

- requisições e tempo de resposta;
- falhas e exceções;
- dependências externas;
- rastreamento distribuído;
- comportamento de desempenho.

Ele é especialmente útil para investigar gargalos em APIs, aplicações web e sistemas distribuídos.

## 7. Alertas no Azure Monitor

**Alert Rules** permitem avaliar condições em métricas, logs ou outros sinais suportados e disparar notificações ou ações.

Exemplo conceitual:

```text
Métrica: CPU de uma VM
        ↓
Condição: CPU acima de 85%
        ↓
Regra de alerta
        ↓
Action Group
   ├── E-mail
   ├── Webhook
   └── Automação compatível
```

Esse limite é somente um exemplo: valores adequados dependem do comportamento normal da carga e dos impactos reais.

## 8. Action Groups

Os **Action Groups** agrupam destinos de notificação e ações reutilizáveis para regras de alerta.

Podem encaminhar informações a equipes ou integrar processos automatizados. É importante testar as notificações e evitar ações automáticas arriscadas sem controles apropriados.

## 9. Azure Service Health e Resource Health

Esses recursos atendem a perguntas diferentes:

- **Azure Service Health:** informa sobre eventos e problemas em serviços Azure que podem afetar assinaturas e regiões.
- **Azure Resource Health:** apresenta o estado de saúde de recursos individuais suportados.

Ambos complementam o monitoramento próprio da aplicação. Uma indicação de serviço saudável não garante que todos os fluxos da aplicação funcionem corretamente.

## 10. Azure Advisor

O **Azure Advisor** fornece recomendações sobre aspectos do ambiente, incluindo confiabilidade, segurança, desempenho e custos, conforme os recursos analisados.

Suas recomendações ajudam a identificar melhorias, mas devem ser revisadas antes de qualquer mudança operacional.

## 11. Dashboards e Workbooks

É possível criar visualizações para acompanhar indicadores importantes.

**Azure Workbooks** permite combinar consultas, métricas, textos e componentes visuais em relatórios interativos.

Um painel de operações pode reunir:

```text
Visão operacional
├── Disponibilidade
├── Latência das APIs
├── Taxa de erros
├── Consumo de recursos
└── Alertas ativos
```

O ideal é exibir indicadores que ajudem a identificar riscos e tomar decisões, evitando painéis com métricas irrelevantes.

## 12. Monitoramento inteligente e detecção de anomalias

Além dos limites fixos, alguns recursos de monitoramento oferecem mecanismos de detecção de padrões e anomalias.

Uma estratégia de monitoramento proativo considera:

1. Conhecer a linha de base de comportamento normal.
2. Identificar desvios e tendências.
3. Correlacionar métricas, logs e traces.
4. Classificar gravidade e impacto.
5. Notificar a equipe responsável.
6. Investigar a causa e validar a recuperação.

Alertas precisam ser ajustados para reduzir ruído e evitar **fadiga de alertas**.

## Exemplo prático: API com lentidão

Imagine uma API hospedada no Azure que começa a responder lentamente.

```text
Usuário percebe lentidão
          ↓
Application Insights
   ├── Tempo de resposta elevado
   ├── Requisições com erro
   └── Dependência lenta
          ↓
Azure Monitor / Logs
   ├── Uso de CPU
   ├── Eventos relevantes
   └── Histórico de alterações
          ↓
Hipótese de causa
          ↓
Correção + validação
```

Antes de aumentar recursos da infraestrutura, é importante verificar se a origem está na aplicação, no banco de dados, em dependências ou em limites da plataforma.

## 13. Custos e retenção da observabilidade

Monitoramento também tem custos. Dependendo do serviço e da configuração, podem existir cobranças por:

- ingestão e retenção de logs;
- consultas e exportações;
- alertas;
- dados adicionais de telemetria.

Boas práticas incluem filtrar telemetria desnecessária, configurar retenção apropriada e analisar preços antes de ampliar a coleta.

Dados coletados podem conter informações sensíveis. Evite registrar senhas, tokens e dados pessoais sem necessidade e utilize controles de acesso.

## Boas práticas

1. Monitorar a experiência do usuário, não apenas a infraestrutura.
2. Definir indicadores claros de disponibilidade, latência e erros.
3. Correlacionar métricas com eventos e logs.
4. Criar alertas acionáveis com responsáveis definidos.
5. Testar notificações e procedimentos de resposta.
6. Revisar limites para evitar excesso de alertas.
7. Restringir acesso aos logs e dados operacionais.
8. Controlar ingestão e retenção para evitar custos inesperados.
9. Documentar incidentes e ações corretivas.
10. Validar a recuperação pelos indicadores afetados.

## Conclusão

O monitoramento inteligente na Azure combina coleta de informações, análise e capacidade de resposta. Ferramentas como **Azure Monitor, Log Analytics, Application Insights, Service Health e alertas** ajudam a transformar telemetria em decisões operacionais.

O principal aprendizado é que **observabilidade não é apenas identificar problemas após uma falha, mas compreender o comportamento do sistema para prevenir impactos e melhorar continuamente a confiabilidade**.

## Referências

- [Azure Monitor](https://learn.microsoft.com/pt-br/azure/azure-monitor/overview)
- [Métricas do Azure Monitor](https://learn.microsoft.com/pt-br/azure/azure-monitor/metrics/data-platform-metrics)
- [Log Analytics](https://learn.microsoft.com/pt-br/azure/azure-monitor/logs/log-analytics-overview)
- [Application Insights](https://learn.microsoft.com/pt-br/azure/azure-monitor/app/app-insights-overview)
- [Alertas do Azure Monitor](https://learn.microsoft.com/pt-br/azure/azure-monitor/alerts/alerts-overview)
- [Azure Service Health](https://learn.microsoft.com/pt-br/azure/service-health/overview)
- [Azure Advisor](https://learn.microsoft.com/pt-br/azure/advisor/advisor-overview)
- [Kusto Query Language](https://learn.microsoft.com/pt-br/kusto/query/)

---

**Desafio de Projeto — DIO**
