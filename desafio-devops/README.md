# Desafio DevOps

Utilizando o repositorio do app [podinfo](https://github.com/stefanprodan/podinfo) como base, crie um novo repositório
com as seguintes features:

O repositório deve conter o código para implementar as fetaures acima e screenshots com evidencias de
todos os itens entregues.

Utilize a versão que desejar para cada ferramenta.
Envie a URL do seu repositório para desafio@getupcloud.com.

## 1. Gitlab

```
  -> pipeline com build da imagem do app
     -> [plus] linter do Dockerfile (tip: use o [hadolint](https://github.com/hadolint/hadolint))
        -> falhar se houver severidade > Warning
  -> [plus] scan da imagem usando [trivy](https://github.com/aquasecurity/trivy) standalone (binário)
     -> falhar se houver bug crítico
```

Tips:

- Instale o [GitLab CI/CD workflow agent](https://docs.gitlab.com/ee/user/clusters/agent/#gitlab-cicd-workflow) para fazer o build e aplicar os manifestos no cluster (caso não use fluxcd): https://docs.gitlab.com/ee/user/clusters/agent/install/
- Para fazer o build da imagem, utilize como base o pipeline em https://gitlab.com/gitlab-org/gitlab-foss/-/blob/master/lib/gitlab/ci/templates/Docker.gitlab-ci.yml
- Utilize o seguinte nome para a imagem: `$CI_REGISTRY/$SEU_USER_GITLAB/podinfo:$CI_COMMIT_SHORT_SHA`

## 2. Terraform

```
  -> criar cluster kind
  -> [plus] criar repo no gitlab
```

## 3. Kubernetes

```
  -> implementar no app
     -> probes liveness e readiness
     -> definir resource de cpu e memória
  -> [plus] escalar app com base na métrica `requests_total`
     -> escalar quando observar > 2 req/seg.
  -> [plus] instalar app com fluxcd
```

## 4. Observabilidade

```
  -> prometheus stack (prometheus, grafana, alertmanager)
  -> retenção de métricas 3 dias
     -> storage local (disco), persistente
  -> enviar alertas para um canal no telegram
  -> logs centralizados (loki, no mesmo grafana do monitoramento)
  -> [plus] monitorar métricas do app `request_duration_seconds`
     -> alertar quando observar > 3 seg.
  -> [plus] tracing (Open Telemetry)
```
