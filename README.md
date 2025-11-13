# Day 11/30 — Logging Service in Spring Boot

> **Logback + SLF4J, correlação distribuída, mascaramento de dados sensíveis e centralização ELK.**

## 📚 Overview

Este projeto implementa um **Logging Service** que empacota correlação distribuída, mascaramento de PII e ingestão centralizada em um starter pronto para qualquer microserviço Spring Boot.

## 🧰 Features

- **Starter de logging** (`logging-service-starter`) com SLF4J + Logback, layouts JSON e appenders STDOUT/FILE/GELF
- **Correlation ID + Session ID** propagados via filtros Servlet, `RestTemplate` e `WebClient`, inclusive em chamadas assíncronas
- **Log context** com MDC (`requestId`, `tenant`, `userId`, `featureFlag`, `endpoint`) aplicado automaticamente em cada request
- **Mascaramento e redaction** (`@SensitiveLog`) para PII (email, taxId) antes dos logs saírem do pod
- **Shipping centralizado**: Filebeat → Logstash → Elasticsearch → Kibana, com dashboards "Error Heatmap" e "Slow Requests"

## 💡 Why it matters

- Auditoria consegue seguir um request em segundos — do mobile ao banco
- Times de incident response ganham um atalho: `requestId` → stacktrace → fix
- Logs estruturados viram insumo para métricas, alertas e análise de produto

## 🚀 Quick Start

### Prerequisites

- Java 21+
- Maven 3.8+
- Docker & Docker Compose (para ELK stack)

### Installation

```bash
# Clone o repositório
git clone https://github.com/adelmonsouza/30DiasJava-Day11-Logging.git
cd 30DiasJava-Day11-Logging

# Build
mvn clean install

# Incluir no seu projeto Spring Boot
<dependency>
    <groupId>com.adelmonsouza</groupId>
    <artifactId>logging-service-starter</artifactId>
    <version>1.0.0</version>
</dependency>
```

### Configuration

```yaml
# application.yml
logging:
  config: classpath:logging/logback-spring.xml
  level:
    com.adelmonsouza: DEBUG
```

## 🧩 Implementation Notes

- **Logback JSON encoder** (`net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder`) com providers (`mdc`, `arguments`, `stackTrace`)
- **Config as code**: `logging/enouveau-logback.xml` incluído em todos os serviços via Spring Boot auto-config
- **Async logging** com `AsyncAppender` para não bloquear threads HTTP
- **Dynamic log level**: endpoint `/actuator/loggers` protegido via RBAC para ajustar níveis em runtime
- **Metrics**: `logging.events.total{level="ERROR"}`, `logging.events.dropped` expostos via Micrometer + Prometheus

## ✅ Daily checklist

- Filebeat/Logstash/Elasticsearch verdes (health check) e índices rotacionando (`log-enouveau-YYYY.MM.DD`)
- Alertas (`logging.error.count > 50 em 5m`) sendo enviados ao Slack/Teams
- Dashboards Kibana revisados com o time de suporte e SRE

## 📚 Documentation

- **Blog post**: https://enouveau.io/blog/2025/11/11/logging-service.html
- **Series**: [#30DiasJava](https://github.com/adelmonsouza/30DiasJava)

## 📄 License

MIT License

## 👤 Author

**Adelmon Souza**

- GitHub: [@adelmonsouza](https://github.com/adelmonsouza)
- LinkedIn: [in/adelmonsouza](https://linkedin.com/in/adelmonsouza)

---

**Next episode** → [Day 12/30 — Monitoring Service with Prometheus & Grafana](https://github.com/adelmonsouza/30DiasJava-Day12-Monitoring)
