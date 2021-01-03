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

É necessário agora criar os participantes em nossa rede. Como eles são participantes autorizados, precisamos fornecer a todos eles identificações exclusivas e seguras. O comando abaixo
executa a função.

```../bin/cryptogen generate --config=./crypto-config.yaml```

A seguinte resposta é esperada:


 ![img1](https://miro.medium.com/max/223/1*-fWIivkBt1PNJmf6LWvPZg.png)

Execute os seguintes comandos

```export FABRIC_CFG_PATH=$PWD```   
```../bin/configtxgen -profile TwoOrgsOrdererGenesis -outputBlock ./channel-artifacts/genesis.block```

A resposta é a seguinte

 ![img2](https://miro.medium.com/max/700/1*tjrs6d06rszMWm0kybM-EQ.png)

Foram criados:   
 - 2 organizações   
 - 2 pares por organização   
 - Certificados para cada um dos itens acima, de modo que cada transação pode ser assinada por eles e sabemos quem criou e assinou as transações   
 - Bloco de gênese   

Em seguida é necessário criar canais onde nossos pares possam interagir e criar transações. Chamaremos isso de ```mychannel``` , mas sinta-se à vontade para alterá-lo para o que quiser.



