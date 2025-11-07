# Teste DevOps - Ivory (Site Estático + CI/CD)

[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=viniciusalmeidadeavila_test-ivory-devops&metric=alert_status)](https://sonarcloud.io/summary/new_code?id=viniciusalmeidadeavila_test-ivory-devops)
[![Coverage](https://sonarcloud.io/api/project_badges/measure?project=viniciusalmeidadeavila_test-ivory-devops&metric=coverage)](https://sonarcloud.io/summary/new_code?id=viniciusalmeidadeavila_test-ivory-devops)

Este repositório contém a solução para o Teste Técnico de Estágio DevOps da Ivory. O objetivo foi provisionar uma infraestrutura em nuvem (AWS) para um site estático e construir uma esteira de CI/CD completa, integrando Git, GitHub Actions, SonarCloud e deploy automático na AWS.

## 🚀 Acesso ao Site (Deploy)

O site estático está hospedado em um bucket S3 e distribuído globalmente via Amazon CloudFront (CDN), com HTTPS habilitado.

**URL:** [https://dk7i4hyedctjk.cloudfront.net/](https://dk7i4hyedctjk.cloudfront.net/)

## 🛠️ Tecnologias Utilizadas

* **Cloud:** AWS S3 (hospedagem estática) e AWS CloudFront (CDN e HTTPS).
* **CI/CD:** GitHub Actions.
* **Qualidade de Código:** SonarCloud.
* **Site:** HTML, CSS e JavaScript.

## ⚙️ Pipeline de CI/CD (GitHub Actions)

O coração do projeto é a pipeline de CI/CD definida em `.github/workflows/pipeline.yml`.

### Gatilhos (Triggers)

A pipeline é acionada automaticamente em duas condições:

1.  **Pull Request (Teste):** Ao abrir um Pull Request para as branches `main`, `homolog` ou `develop`, a pipeline executa apenas o job `sonarcloud`. Isso garante que o código novo seja analisado antes de ser mesclado.
2.  **Push na `main` (Deploy):** Ao realizar um merge ou push direto na branch `main`, a pipeline executa o job `sonarcloud` e, em caso de sucesso, o job `deploy`.

### Jobs da Pipeline

A pipeline é composta por dois jobs principais:

#### 1. `sonarcloud`
* **O que faz:** Realiza o checkout do código e executa o SonarCloud Scanner.
* **Objetivo:** Analisar o código-fonte, reportar bugs e vulnerabilidades e validar o **Quality Gate**. Se o Quality Gate falhar, a pipeline é interrompida.

#### 2. `deploy`
Este job só é executado se o `sonarcloud` passar e se o evento for um push na branch `main`.
* **O que faz:**
    1.  **Depende do `sonarcloud`:** Garante que o deploy só ocorra se a qualidade do código for aprovada.
    2.  **Configura Credenciais AWS:** Autentica-se na AWS usando `AWS_ACCESS_KEY_ID` e `AWS_SECRET_ACCESS_KEY` armazenados nos GitHub Secrets.
    3.  **Sincroniza com S3:** Usa o comando `aws s3 sync . s3://test-ivory-devops --delete` para enviar os arquivos do site para o bucket, apagando os arquivos antigos (`--delete`) para garantir uma cópia limpa.
    4.  **Invalida o Cache do CloudFront:** Executa o comando `aws cloudfront create-invalidation` para forçar o CloudFront a buscar os arquivos novos no S3, garantindo que os usuários vejam a versão mais recente do site imediatamente.

## 📊 Qualidade de Código (SonarCloud)

A análise de qualidade da branch `main` pode ser acompanhada publicamente no dashboard do SonarCloud:

* **Dashboard do Projeto:** [https://sonarcloud.io/summary/new_code?id=viniciusalmeidadeavila_test-ivory-devops](https://sonarcloud.io/summary/new_code?id=viniciusalmeidadeavila_test-ivory-devops)

## 👨‍💻 Autor

* **Vinícius Almeida de Avila**
* **GitHub:** [https://github.com/viniciusalmeidadeavila](https://github.com/viniciusalmeidadeavila)
* **LinkedIn:** [https://www.linkedin.com/in/vinicius-almeida-dev/](https://www.linkedin.com/in/vinicius-almeida-dev/)
