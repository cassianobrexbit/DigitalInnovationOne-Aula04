# DigitalInnovationOne-Aula04 - Introdução ao Hyperledger Fabric

Tutorial para desenvolvimento da parte prática da Aula 04.

* [Docker](https://docs.docker.com/get-docker/).
* Linguagem [Go](https://golang.org/)

## Instalando dependências

* Execute o seguinte script. Este script clonará os exemplos pré-empacotados do Hyperledger Fabric e baixará os binários de que você precisa. 
Em seguida, navegue até o diretório ```first-network``` a partir do qual trabalharemos com o comando ```cd``` abaixo.

```curl -sSL https://goo.gl/6wtTN5 | bash -s 1.1.0```  
```cd fabric-samples/first-network```


* Defina uma variável de ambiente com um caminho para seus binários para que o Fabric saiba onde encontrá-los. 
Substitua a parte ```<>``` pelo caminho completo do diretório onde a pasta ```bin``` se encontra. 
Você pode descobrir isso digitando ```pwd``` em seu terminal

```export PATH=<replace this with your path>/bin:$PATH```

## Criando entidades da rede

* É necessário agora criar os participantes em nossa rede. Como eles são participantes autorizados, precisamos fornecer a todos eles identificações exclusivas e seguras. O comando abaixo
executa a função.

```../bin/cryptogen generate --config=./crypto-config.yaml```

* A seguinte resposta é esperada:

```org1.example.com```  
```org2.example.com```

* Execute os seguintes comandos

```export FABRIC_CFG_PATH=$PWD```   
```../bin/configtxgen -profile TwoOrgsOrdererGenesis -outputBlock ./channel-artifacts/genesis.block```

* A resposta é a seguinte





