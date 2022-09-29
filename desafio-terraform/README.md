# Desafio Terraform

Em um fork desse repositório, responda as questões abaixo e envie o endereço para desafio@getupcloud.com.
O formato é livre. Quanto mais sucinto e direto, melhor.

Para cada questão prática 1, 2 e 3, crie os arquivos em um diretório com o número do desafio 1/, 2/ e 3/.

Utilize a versão que desejar do terraform.

## 1. Módulos

Escreva um módulo terraform para criar um cluster kind usando o provider `kyma-incubator/kind` com as seguintes características:

- Node `infra` com label `role=infra` e taint `dedicated=infra:NoSchedule`;
- Node `app` com label `role=app`;
- Metrics Server instalado e reportando métricas de nodes e pods.

O módulo deve permitir, no mínimo, as seguintes variáveis:

- cluster_name
- kubernetes_version

O módulo deve retornar, no mínimo, os seguintes atributos:

- api_endpoint
- kubeconfig
- client_certificate
- client_key
- cluster_ca_certificate

## 2. Gerenciando recursos customizados

Utilizando o provider `scottwinkler/shell`, crie um módulo que gerencie a instalação de pacotes em um ambiente linux (local).
Você pode utilizar qualquer gerenciador de pacotes (rpm, deb, tgz, ...).

O módulo deve ser capaz de:

- Instalar e desinstalar um pacote no host;
- Atualizar um pacote já instalado com a nova versão especificada;
- Reinstalar um pacote que foi removido manualmente (fora do terraform).

O módulo deve permitir, no mínimo, as seguintes variáveis:

- install_pkgs: lista de pacotes a serem instalados, com versão;
- uninstall_pkgs: lista de pacotes a serem desinstalados.

## 3. Templates

Escreva um código para criar automaticamente o arquivo `alo_mundo.txt` a partir do template `alo_mundo.txt.tpl` abaixo:

```
Alo Mundo!

Meu nome é ${nome} e estou participando do Desafio Terraform da Getup/LinuxTips.

Hoje é dia ${data} e os números de 1 a 100 divisíveis por ${div} são: ${...}

```

Nota:

> A subtituição `${...}` deve ser gerada dinamicamente no formato `a, b, c, ...`
> onde `a`, `b`, `c`, ... são os números cuja divisão por `div` é exata (resto 0).
>
> Ex: Se `div = 15`, então o resultado em `${...}` será `15, 30, 45, 60, 75, 90`

## 4. Assumindo recursos

Descreva abaixo como você construiria um `resource` terraform a partir de um recurso já existente, como uma instância `ec2`.
