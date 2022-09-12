# Desafio AWS

As questões abaixo devem ser respondidas no arquivo [RESPOSTAS-AWS.md](RESPOSTAS-AWS.md) em um fork desse repositório.
O formato é livre. Quanto mais sucinto e direto, melhor. Envie o endereço do seu repositório para desafio@getupcloud.com.

# Configuração Inicial

1 - Você precisa instalar e configurar o aws-cli em seu equipamento: [AWS CLI Install](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

2 - Obtenha o AccessKey e SecretKey do seu usuário [AWS CLI Install](https://docs.aws.amazon.com/pt_br/IAM/latest/UserGuide/id_credentials_access-keys.html) e configure o aws cli:

```
aws configure
```

# Criação do Ambiente Base

Baixe esse repositório e execute:
```
cd desafio-aws
export STACK_NAME="desafio-aws"
export STACK_FILE="file://formandodevops-desafioaws.json"
aws cloudformation create-stack --template-body "$STACK_FILE" \
    --stack-name "$STACK_NAME"
```