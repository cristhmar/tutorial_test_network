# Tutorial de Hyperledger Fabric

![testNetwork](/imgs/tutorialBlockchain.png)

## CONTENIDO DEL TUTORIAL
1. Instalar Prerequisitos.
2. Instalar Fabric, descargar binarios y ejemplos.
3. Desplegar una red de prueba (test_network)
4. Crear Canales (mychannel).
5. Iniciar Chaincode (basic).
6. Realizar consultas a la red (Invocar Chaincode).


Referencias:
1. https://hyperledger-fabric.readthedocs.io/en/latest/getting_started.html

2. https://dl.acm.org/doi/pdf/10.1145/3190508.3190538

Versiones utilizadas en el tutorial:

- Hyperledger Fabric: 2.4.2
- Hyperledger Fabric CA: 1.5.2
- Git: 2.25.1
- cURL: 7.68.0
- Docker: 20.10.12
- Go: 1.18



# Paso 1. Instalar prerequisitos.
Fuente: https://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html

- Git:
```console
sudo apt-get install git
```
- cURL:
```console
sudo apt-get install curl
```

- Docker:
```console
sudo apt-get -y install docker-compose
```

- Go: 
[1] https://go.dev/doc/install

Eliminar versiones previas de Go:
```console
rm -rf /usr/local/go 
```
Descargar fichero (go1.18.linux-amd64.tar.gz) de [1] descomprimir y copiar a /usr/local/go/:

```console
tar -C /usr/local -xzf go1.18.linux-amd64.tar.gz
```


# Paso 2. Instalar Fabric, descargar binarios y ejemplos.

Crear directorio de trabajo:
```console
mkdir -p $HOME/go/src/github.com/<your_github_userid>

Ejemplo:
$ mkdir -p $HOME/go/src/github.com/cristhmar
```

Entrar al directorio de trabajo:
```console
cd $HOME/go/src/github.com/<your_github_userid>

Ejemplo:
$ cd $HOME/go/src/github.com/cristhmar
```

Comando general para descargar version especifica de Fabric.
> curl -sSL https://bit.ly/2ysbOFE | bash -s -- <fabric_version> <fabric-ca_version>

Ejemplo de descarga de una version especifica:

```console
$ curl -sSL https://bit.ly/2ysbOFE | bash -s -- 2.4.2 1.5.2
```

Imagenes de Docker descargadas:
![imagesDocker](/imgs/tutorial1.png)

Ubicacion de los binarios de Fabric:
![binariosFabric](/imgs/tutorial2.png)

These binaries will help you interact with the test network:

- **configtxgen**: permite a los usuarios **crear e inspeccionar** artefactos relacionados con la configuración del canal. El contenido de los artefactos generados está definido por el contenido de configtx.yaml

- **configtxlator**: permite a los usuarios traducir entre las versiones protobuf y JSON de las estructuras de datos de Fabric y crear actualizaciones de la configuración. El comando puede iniciar un servidor REST para exponer sus funciones a través de HTTP o puede ser utilizado directamente como una herramienta de línea de comandos.

- **cryptogen**: es una utilidad para generar material de claves de Hyperledger Fabric. Se proporciona como medio para preconfigurar una red con fines de prueba. Normalmente no se utilizaría en el funcionamiento de una red de producción.

- **discover**: El servicio discovery tiene su propia interfaz de línea de comandos (CLI) que utiliza un archivo de configuración YAML para mantener propiedades como las rutas de los certificados y las claves privadas, así como el ID de MSP.

- **fabric-ca-client**: permite gestionar las identidades (incluyendo la gestión de atributos) y los certificados (incluyendo su renovación y revocación).

- **fabric-ca-server**: permite inicializar e iniciar un proceso de servidor que puede alojar una o varias autoridades de certificación

- **ledgerutil**: Tiene el subcomando compare que permite a los administradores comparar las instantáneas del canal de dos peers diferentes. Si los archivos de las instantáneas indican diferentes hashes de estado, entonces indica que al menos una de las bases de datos de estado de los peers no es correcta en relación con la blockchain y puede haberse corrompido.

- **osnadmin**: El comando osnadmin channel permite a los administradores realizar operaciones relacionadas con los canales en un ordenante, como unirse a un canal, listar los canales a los que se ha unido un ordenante y eliminar un canal. La API de participación en el canal debe estar habilitada y el punto final Admin debe estar configurado en el orderer.yaml para cada ordenante.

- **peer**: El comando peer tiene cinco subcomandos diferentes (peer channel, peer chaincode, peer node, peer version), cada uno de los cuales permite a los administradores realizar un conjunto específico de tareas relacionadas con un peer. Por ejemplo, puedes utilizar el subcomando peer channel para unir a un peer a un canal, o el comando peer chaincode para desplegar un contrato inteligente chaincode a un peer.

- **orderer**.

# Paso 3. Desplegar una red de prueba (test_network)

![testNetwork](/imgs/tutorialBlockchain.png)

1. Entrar al directorio de la red de prueba test-network:
```console
$ cd fabric-samples/test-network
```

2. Limpiar anteriores ejecuciones:
```console
$ ./network.sh down
```
El comando anterior elimina los contenedores docker que conforman la red blockchain.

3. Desplegar la red:
```console
$ ./network.sh up
```

Se levantan 4 nodos (contenedores):
- orderer.example.com
- peer0.org1.example.com 
- peer0.org2.example.com
- cli


> **_NOTE_ :** En caso de error durante el levantamiento de la red, por numero de puerto usado, buscar el proceso que ocupa el puerto y detenerlo (si es posible). Listar puertos: 
```console
$ sudo lsof -i -P -n | grep LISTEN
...
$ kill <pid process>
```

Por defecto, la red utiliza la herramienta cryptogen para poner en marcha la red. Sin embargo, también se puede hacer aparecer la red con Autoridades de Certificación.


# Paso 4. Crear Canales (mychannel).

```console
$ ./network.sh createChannel -c <name_channel>
```

Ejemplo:
```console
$ ./network.sh createChannel -c mychannel
```
> **_NOTE_ :** Restricciones en los nombres de los canales: 1) Solo puede contener caracteres alfanuméricos ASCII en minúsculas, puntos '.' y guiones '-' ; 2) Inferior a 250 caracteres
y 3) Empezar por una letra.


Todos los canales creados (.block) se guardaran en:
fabric-samples/test-network/channel-artifacts/*.block

# Paso 5. Iniciar Chaincode/Smart Contract (basic) en el Canal.

![binariosFabric](/imgs/chaincode.png)

- Desplegar el Chaincode:
```console
./network.sh deployCC -ccn basic -ccp ../asset-transfer-basic/chaincode-go -ccl go
```

Cuando desplegamos un chaincode en la red, se crea un nuevo contenedor por cada organizacion:
```console
$ docker ps
...
dev-peer0.org1.example.com-basic_1.0..

dev-peer0.org2.example.com-basic_1.0
```

# Paso 6. Realizar consultas a la red (Invocar Chaincode).

1. Agregar los binarios de Fabric (../fabric-samples/bin) en la variable PATH:

export PATH=${PWD}/../bin:$PATH

Ejemplo:
```console
$ export PATH=/home/cristhma/go/src/github.com/cristhmar/fabric-samples/bin:$PATH
```

2. Agregar variable de entorno con la ruta de ficheros de configuracion (canales, ledger,etc.)

export FABRIC_CFG_PATH=$PWD/../config/

Ejemplo:
```console
$ export FABRIC_CFG_PATH=/home/cristhma/go/src/github.com/cristhmar/fabric-samples/config/
```

3. Operar el peer CLI como la Org1:
```console
# Environment variables for Org1

export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_LOCALMSPID="Org1MSP"
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
export CORE_PEER_ADDRESS=localhost:7051
```

4. Inicializar el Ledger con Activos - InitLedger:

```console
peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile "${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem" -C mychannel -n basic --peerAddresses localhost:7051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt" --peerAddresses localhost:9051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt" -c '{"function":"InitLedger","Args":[]}'
```

Salida:

```console
-> INFO 001 Chaincode invoke successful. result: status:200
```

5. Consultar activos:

```console
peer chaincode query -C mychannel -n basic -c '{"Args":["GetAllAssets"]}'
```

Salida:
```json
[
  {"ID": "asset1", "color": "blue", "size": 5, "owner": "Tomoko", "appraisedValue": 300},
  {"ID": "asset2", "color": "red", "size": 5, "owner": "Brad", "appraisedValue": 400},
  {"ID": "asset3", "color": "green", "size": 10, "owner": "Jin Soo", "appraisedValue": 500},
  {"ID": "asset4", "color": "yellow", "size": 10, "owner": "Max", "appraisedValue": 600},
  {"ID": "asset5", "color": "black", "size": 15, "owner": "Adriana", "appraisedValue": 700},
  {"ID": "asset6", "color": "white", "size": 15, "owner": "Michel", "appraisedValue": 800}
]
```

![queryFabric](/imgs/tutorial3.png)



6. Transferir activo:

```console
peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile "${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem" -C mychannel -n basic --peerAddresses localhost:7051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt" --peerAddresses localhost:9051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt" -c '{"function":"TransferAsset","Args":["asset6","Christopher"]}'
```

7. Consultar el chaincode con la Org2:

```console
# Environment variables for Org2

export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_LOCALMSPID="Org2MSP"
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
export CORE_PEER_ADDRESS=localhost:9051
```

8. Leer un activo:
```console
peer chaincode query -C mychannel -n basic -c '{"Args":["ReadAsset","asset6"]}'
```
Salida:

```json
{"AppraisedValue":800,"Color":"white","ID":"asset6","Owner":"Christopher","Size":15}
```

