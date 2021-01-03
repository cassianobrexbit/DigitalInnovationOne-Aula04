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

```export CHANNEL_NAME=mychannel  && ../bin/configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel.tx -channelID $CHANNEL_NAME```   

```../bin/configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors.tx -channelID $CHANNEL_NAME -asOrg Org1MSP```   

```../bin/configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors.tx -channelID $CHANNEL_NAME -asOrg Org2MSP``` 

As duas últimas linhas são importantes. Os pares âncora ```(anchor peers)``` são criados para que novos participantes que ingressam na rede possam conversar com ela e descobrir quem são os outros participantes do canal.

## Iniciando a rede

Utilize o comando do Docker para iniciar o container da rede

```docker-compose -f docker-compose-cli.yaml up -d```   

Esta é a resposta esperada

 ![img3](https://miro.medium.com/max/700/1*mSRCEpE6TuZatz74XUTiYQ.png)
 
 Configure a interface de linha de comando do Docker para executar os comandos de transação.
 
 ```docker start cli```
 
 Acessar o container docker
 
 ```docker exec -it cli bash```
 
 O passo seguinte é passar na configuração do canal criado anteriormente para que o container docker possa iniciar o canal.

```export CHANNEL_NAME=mychannel```   

```peer channel create -o orderer.example.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/channel.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem```

Adicionando peers ao canal

```peer channel join -b mychannel.block```

```CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp CORE_PEER_ADDRESS=peer0.org2.example.com:7051 CORE_PEER_LOCALMSPID="Org2MSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt peer channel join -b mychannel.block```

```peer channel update -o orderer.example.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/Org1MSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem```

```CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp CORE_PEER_ADDRESS=peer0.org2.example.com:7051 CORE_PEER_LOCALMSPID="Org2MSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt peer channel update -o orderer.example.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/Org2MSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem```

Conectar o primeiro par de nossa primeira organização
Conectar ao primeiro par de nossa segunda organização e atualizamos as variáveis de ambiente de acordo para reconhecê-lo
Definidos esses dois pares como os pares âncora de cada organização para que novos pares possam conversar com eles e aprender sobre outros pares

## Instalando o chaincode - Smart Contract

Baixar do repositório um chaincode pré-empacotado que o Fabric fornece e instalá-lo na rede

```peer chaincode install -n mycc -v 1.0 -p github.com/chaincode/chaincode_example02/go/```

```peer chaincode instantiate -o orderer.example.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C $CHANNEL_NAME -n mycc -v 1.0 -c '{"Args":["init","a", "100", "b","200"]}' -P "OR ('Org1MSP.peer','Org2MSP.peer')"```

A resposta esperada é a seguinte

 ![img3](https://miro.medium.com/max/700/1*NFkhzjyGDqYumV2lqARpog.png)
 
 Ações executadas
 
 * Download do chaincode do Github
 
 * O chaincode é instanciado e, em seguida, são definidos saldos dos ativos. Foi definido como “a”, ou o primeiro par criado com saldo de 100 tokens, e “b”, o segundo par com saldo de 200 tokens.

