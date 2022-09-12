# Desafio AWS

As questões abaixo devem ser respondidas no arquivo [RESPOSTAS-AWS.md](RESPOSTAS-AWS.md) em um fork desse repositório.
O formato é livre. Quanto mais sucinto e direto, melhor. Envie o endereço do seu repositório para desafio@getupcloud.com.

# Preparação Inicial

1 - Você precisa instalar e configurar o aws-cli em seu equipamento: [AWS CLI Install](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

2 - Obtenha o AccessKey e SecretKey do seu usuário e configure o aws cli:

```
aws configure
AWS Access Key ID [****************]: [SEU ACCESS KEY AQUI]
AWS Secret Access Key [****************.]: [SEU SECRET KEY AQUI]
Default region name []: us-east-1
Default output format [None]:
```
Dúvidas: [AWS CLI Credentials](https://docs.aws.amazon.com/pt_br/IAM/latest/UserGuide/id_credentials_access-keys.html)


# Criação do Ambiente de Controle

Baixe esse repositório e execute:
```
cd desafio-aws
export STACK_NAME="stack-controle"
export STACK_FILE="file://aws-controle.json"
aws cloudformation create-stack --region us-east-1 --template-body "$STACK_FILE" --stack-name "$STACK_NAME" --no-cli-pager
aws cloudformation wait stack-create-complete --stack-name "$STACK_NAME" (esse comando não possui output. Apenas liberará seu terminal quando a criação da stack finalizar)
```

Os comandos acima criarão na região US-EAST-1 da sua conta AWS os seguintes recursos:
- 1 VPC
- 4 Subnets
- 1 Internet Gateway
- 1 EC2

Execute o comando abaixo, copie o IP e abra em seu navegador:
```
aws cloudformation describe-stacks --region us-east-1 --query "Stacks[?StackName=='"$STACK_NAME"'][].Outputs[?OutputKey=='PublicIp'].OutputValue" --output text --no-cli-pager
```

Se tudo ocorreu bem até aqui, você verá um texto que remete ao desafio e o recurso que acabou de ser criado.

Agora, tome um tempo para analisar os recursos que foram criados e suas configurações. Após isso, destrua esse ambiente e vamos ao desafio!

```
aws cloudformation delete-stack --region us-east-1 --stack-name "$STACK_NAME"
```

# Desafio AWS

1 - Setup de ambiente

Execute os mesmos passos de criação de ambiente descritos anteriormente, *porém* atenção: dessa vez utilize o arquivo "formandodevops-desafio-aws.json"



2 - Networking

A página web dessa vez não está sendo exibida corretamente. Verifique as *configurações de rede* que estão impedindo seu funcionamento.

3 - EC2 Access

Para acessar a EC2 por SSH, você precisa de uma key pair, que não está disponível. Pesquise como alterar a key pair de uma EC2. Após trocar a key pair e acessar a EC2, altere o texto da página web exibida, colocando seu nome no início do texto do arquivo "/var/www/html/index.html".

4 - EC2 troubleshooting

No último procedimento, A EC2 precisou ser desligada e após isso o serviço responsável pela página web não iniciou. Encontre o problema e realize as devidas alterações para que esse serviço inicie automaticamente durante o boot da EC2.

5 - Balanceamento

Crie uma cópia idêntica de de sua EC2 e inicie essa segunda EC2. Após isso, crie um balanceador, configure ambas EC2 nesse balancedor e garanta que, mesmo que uma das EC2 esteja desligada, o usuário final conseguirá acessar a página web.

6 - Segurança

Garanta que o acesso para suas EC2 ocorra somente através do balanceador, ou seja, chamadas HTTP diretamente realizadas da sua máquina para o EC2 deverão ser barradas. Elas só aceitarão chamadas do balanceador e esse, por sua vez, aceitará conexões externas normalmente.
