# PRD-P1 — Backend Spring Boot "Hello World" (S2I)

**Versão:** 1.1
**Projeto:** P1 — `api-openshift-v1`
**Data:** 10/11/2026
**Status:** Rascunho para validação

---

## 1. Contexto e Objetivo

### 1.1 Contexto

Primeiro projeto da trilha da palestra sobre o **Red Hat OpenShift Developer Sandbox** (gratuito; cota aproximada: 3 cores, 14 GB RAM, 40 GB storage; validade de 30 dias). O P1 é o "aquecimento" da palestra: serve para provar que o OpenShift compila e roda uma aplicação **Java 17 + Spring Boot** com uma única tela, **sem Dockerfile**.

A aplicação parte do `pom.xml` atual do repositório:

- **Java 17** (`<java.version>17</java.version>`)
- **Spring Boot 3.5.3** (via `spring-boot-starter-parent`)
- **Maven Wrapper** (`mvnw` / `mvnw.cmd`)
- `groupId`: `com.api-openshift-v1`
- `artifactId`: `api-openshift-v1`
- Pacote base: `com.api_openshift_v1`
- Classe principal: `ApiOpenshiftV1Application`

### 1.2 Objetivo Geral

Demonstrar o deploy mais simples possível de uma aplicação Spring Boot no OpenShift, usando o builder **Source-to-Image (S2I)** com detecção automática de projetos Java/Maven.

### 1.3 Objetivos Específicos

- Demonstrar o fluxo **+Add → Import from Git** no console Developer.
- Demonstrar detecção automática do `pom.xml` e seleção do builder Java.
- Demonstrar criação automática de **Build, Deployment, Service e Route**.
- Provar que o OpenShift executa Java **sem Dockerfile**.

### 1.4 Fora de Escopo

- Domínio de negócio (o P1 é apenas "Hello World").
- Persistência de dados.
- Autenticação/autorização.
- CORS (o P1 não é consumido pelo frontend).
- Pipelines CI/CD avançados (Tekton, ArgoCD).
- Probes customizadas, ConfigMap, múltiplas réplicas.

---

## 2. Público-Alvo

- Desenvolvedores Java/Spring iniciantes em containers e Kubernetes.
- Estudantes e profissionais em transição para cloud-native.
- Tech leads avaliando OpenShift como plataforma para stacks Java.

**Premissas de conhecimento:** Git básico, REST APIs, noções de Maven (desejável).

---

## 3. Restrições e Premissas Técnicas

| Item | Restrição |
| --- | --- |
| Plataforma | Red Hat OpenShift Developer Sandbox (free tier) |
| Cota de recursos | ~3 cores, 14 GB RAM, 40 GB storage |
| Validade | 30 dias (renovável) |
| Tempo de build | < 6 min |
| Imagens | Builder S2I oficial (Java/Maven, Java 17) |
| Conectividade | Repositório público no GitHub |
| Idioma | Conteúdo em pt-BR |

**Premissas:**

- O palestrante fará deploy prévio do P1 antes da palestra.
- O P1 é independente dos demais projetos.

---

## 4. Arquitetura

```text
┌─────────────────────────────────────────┐
│         Developer Sandbox                │
│                                          │
│  ┌────────────────┐                      │
│  │ P1: API        │                      │
│  │ Spring Boot    │ ──► Route HTTPS      │
│  │ Hello World    │                      │
│  │ Java 17 (S2I)  │                      │
│  └────────────────┘                      │
└─────────────────────────────────────────┘
```

---

## 5. Descrição Funcional

API REST minimalista em Spring Boot 3.5.3 (Java 17) com os endpoints:

- `GET /api/hello` → `{ "message": "Hello from Spring Boot on OpenShift Sandbox" }`
- `GET /health` → `{ "status": "ok" }`

> **Observação de dependência:** o `pom.xml` atual possui apenas `spring-boot-starter`, `spring-boot-devtools` e `spring-boot-starter-test`. Para expor endpoints HTTP, é necessário adicionar a dependência `spring-boot-starter-web` (inclui Tomcat embutido e Spring MVC); caso contrário a aplicação sobe sem servidor web e os endpoints não ficam acessíveis.

---

## 6. Requisitos Técnicos

| Item | Especificação |
| --- | --- |
| Linguagem | **Java 17** |
| Framework | **Spring Boot 3.5.3** (`spring-boot-starter-parent`) |
| Dependências | `spring-boot-starter`, `spring-boot-starter-web` (necessária para os endpoints), `spring-boot-devtools`, `spring-boot-starter-test` |
| Build | Maven via **Maven Wrapper** (`./mvnw`) |
| Porta | `8080` (padrão do Spring Boot / Tomcat embutido) |
| Repositório | GitHub público (`api-openshift-v1`) |
| Deploy | Console Developer → **+Add → Import from Git** |
| Builder | Detecção automática (Java/Maven) — **S2I** |
| Recursos | Requests: 100m CPU / 128Mi RAM · Limits: 250m CPU / 256Mi RAM |
| JVM | `JAVA_OPTS="-XX:MaxRAMPercentage=75.0"` |

---

## 7. Critérios de Aceite

- [ ] App acessível via Route HTTPS pública.
- [ ] `GET /api/hello` retorna `200` com o JSON esperado.
- [ ] `GET /health` retorna `200`.
- [ ] Build concluído em `< 6 min`.
- [ ] Pod em estado `Running` sem reinícios.
- [ ] Logs mostram inicialização do Spring Boot sem erros.
- [ ] Topology mostra Build, Deployment, Service e Route criados automaticamente.

---

## 8. Roteiro de Demonstração

1. Console Developer → **+Add → Import from Git**.
2. Colar a URL do repositório (`api-openshift-v1`).
3. OpenShift detecta o `pom.xml` e seleciona o builder **Java (Java 17)**.
4. Definir porta `8080` e marcar **"Create Route"**.
5. Aguardar o build (S2I baixa dependências Maven, compila e empacota).
6. Abrir **Topology → Route** no navegador → ver o JSON de `/api/hello`.
7. Mostrar os logs do Pod exibindo o banner do Spring Boot.

---

## 9. Mensagem-chave

"Com Spring Boot e OpenShift S2I, você não precisa escrever Dockerfile nem configurar registry. O cluster compila, empacota e executa sua aplicação Java com uma única tela. Isso é o básico — agora vamos ver o que acontece quando o negócio entra em cena."

---

## 10. Riscos e Mitigações

| Risco | Impacto | Mitigação |
| --- | --- | --- |
| Build Maven lento durante a palestra | Médio | P1 pré-deployado; ter fallback gravado |
| Expiração do Sandbox | Alto | Verificar validade em D-1; plano B (gravação) |
| Rede/Internet instável | Alto | Vídeos/gifs de fallback |

---

## 11. Anexos (a produzir)

- **A1-P1.** Exemplo de `pom.xml` (ver abaixo).
- **A2-P1.** Classe `HelloController` de referência.
- **A3-P1.** Checklist de validação pré-palestra (P1).

### A1-P1 — `pom.xml` de referência

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.5.3</version>
        <relativePath/>
    </parent>

    <groupId>com.api-openshift-v1</groupId>
    <artifactId>api-openshift-v1</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>api-openshift-v1</name>

    <properties>
        <java.version>17</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

### A2-P1 — `HelloController` de referência

```java
package com.api_openshift_v1.controller;

import java.util.Map;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api")
public class HelloController {

    @GetMapping("/hello")
    public Map<String, String> hello() {
        return Map.of("message", "Hello from Spring Boot on OpenShift Sandbox");
    }
}
```

### A3-P1 — Checklist de validação pré-palestra (P1)

- [ ] Sandbox ativo e válido (verificar validade em D-1).
- [ ] Repositório público acessível.
- [ ] `./mvnw -q clean package` executando com sucesso em Java 17.
- [ ] `spring-boot-starter-web` presente no `pom.xml`.
- [ ] Build S2I concluído em `< 6 min`.
- [ ] Route HTTPS respondendo `/api/hello` e `/health`.
- [ ] Pod `Running` sem reinícios.
- [ ] Fallback gravado (vídeo/gif) disponível.

---

## 12. Atividades

Sequência de atividades necessárias para sair do PRD e chegar ao P1 deployado e validado no OpenShift Developer Sandbox. As atividades estão agrupadas por fase e devem ser executadas na ordem apresentada.

### 12.1 Fase 1 — Ajustes de código

| ID | Atividade | Resultado esperado | Depende de |
| --- | --- | --- | --- |
| AT-01 | Adicionar `spring-boot-starter-web` ao `pom.xml` | Aplicação com Tomcat embutido e Spring MVC habilitados | — |
| AT-02 | Criar pacote `com.api_openshift_v1.controller` | Estrutura base para os controllers | AT-01 |
| AT-03 | Implementar `HelloController` com `GET /api/hello` | Endpoint retornando `{ "message": "Hello from Spring Boot on OpenShift Sandbox" }` | AT-02 |
| AT-04 | Implementar `HealthController` com `GET /health` | Endpoint retornando `{ "status": "ok" }` | AT-02 |
| AT-05 | Ajustar `application.properties` (`spring.application.name`, `server.port=8080`) | Configuração explícita de nome e porta | AT-01 |

### 12.2 Fase 2 — Validação local

| ID | Atividade | Resultado esperado | Depende de |
| --- | --- | --- | --- |
| AT-06 | Criar testes de endpoints com `MockMvc` | Testes cobrindo `200` e o corpo JSON de `/api/hello` e `/health` | AT-03, AT-04 |
| AT-07 | Executar `./mvnw clean package` em Java 17 | Build local concluído sem erros e testes verdes | AT-06 |
| AT-08 | Subir a aplicação local e validar com `curl` | `http://localhost:8080/api/hello` e `/health` respondendo corretamente | AT-07 |

### 12.3 Fase 3 — Publicação e deploy no OpenShift

| ID | Atividade | Resultado esperado | Depende de |
| --- | --- | --- | --- |
| AT-09 | Publicar o repositório público no GitHub | Repositório `api-openshift-v1` acessível publicamente | AT-08 |
| AT-10 | Fazer deploy via **+Add → Import from Git** | Build S2I detectando `pom.xml` e builder Java 17 | AT-09 |
| AT-11 | Configurar recursos (requests/limits) e `JAVA_OPTS` | Requests `100m`/`128Mi`, limits `250m`/`256Mi`, `-XX:MaxRAMPercentage=75.0` | AT-10 |
| AT-12 | Criar/validar a Route HTTPS | Aplicação acessível por URL pública | AT-11 |

### 12.4 Fase 4 — Validação final e preparação da demo

| ID | Atividade | Resultado esperado | Depende de |
| --- | --- | --- | --- |
| AT-13 | Validar os Critérios de Aceite (seção 7) e registrar evidências | Checklist aprovado com logs e capturas de tela | AT-12 |
| AT-14 | Ensaiar o Roteiro de Demonstração (seção 8) e gravar fallback | Demo fluida e vídeo/gif de contingência disponível | AT-13 |
| AT-15 | Finalizar os anexos A1, A2 e A3 | `pom.xml`, `HelloController` e checklist prontos para publicação | AT-03, AT-07, AT-13 |

### 12.5 Ordem sugerida (resumo)

1. AT-01 → AT-05: preparar o código.
2. AT-06 → AT-08: validar localmente.
3. AT-09 → AT-12: publicar e deployar no OpenShift.
4. AT-13 → AT-15: validar, ensaiar e documentar.

> **Observação:** AT-01 é bloqueante — sem `spring-boot-starter-web` os endpoints não existem, e as atividades seguintes da Fase 1 em diante não podem ser concluídas.
