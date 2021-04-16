---
title: Distribuire Hyperledger Fabric consorzio in servizio Azure Kubernetes
description: Come distribuire e configurare una rete Hyperledger Fabric consortium in servizio Azure Kubernetes
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: 03f19d1922c011c1b5304b66488e9fa8de703bf9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478311"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>Distribuire Hyperledger Fabric consorzio in servizio Azure Kubernetes

È possibile usare il modello Hyperledger Fabric on servizio Azure Kubernetes (AKS) per distribuire e configurare una rete Hyperledger Fabric consorzio in Azure.

Dopo avere letto l'articolo, si sarà in grado di:

- Avere una conoscenza approfondita dei Hyperledger Fabric e dei componenti che formano i blocchi predefiniti di una Hyperledger Fabric blockchain.
- Informazioni su come distribuire e configurare una rete Hyperledger Fabric consorzio in servizio Azure Kubernetes per gli scenari di produzione.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Scegliere una soluzione Azure Blockchain personalizzata

Prima di scegliere di usare un modello di soluzione, confrontare lo scenario con i casi d'uso comuni delle opzioni Azure Blockchain disponibili:

Opzione | Modello di servizio | Caso d'uso comune
-------|---------------|-----------------
Modelli di soluzioni | IaaS | I modelli di soluzione Azure Resource Manager che è possibile usare per effettuare il provisioning di una topologia di rete blockchain completamente configurata. I modelli distribuiscono e configurano Microsoft Azure servizi di calcolo, rete e archiviazione per un tipo di rete blockchain. I modelli di soluzione vengono forniti senza un contratto di servizio. Usare la [pagina Microsoft Q&A per](/answers/topics/azure-blockchain-workbench.html) il supporto.
[Servizio Azure Blockchain](../service/overview.md) | PaaS | servizio Azure Blockchain anteprima semplifica la formazione, la gestione e la governance delle reti blockchain del consorzio. Usare servizio Azure Blockchain soluzioni che richiedono PaaS, la gestione del consorzio o la privacy di contratti e transazioni.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS e PaaS | Azure Blockchain Workbench Anteprima è una raccolta di servizi e funzionalità di Azure che consentono di creare e distribuire applicazioni blockchain per condividere dati e processi aziendali con altre organizzazioni. Usare Azure Blockchain Workbench per la prototipazione di una soluzione blockchain o un modello di verifica per un'applicazione blockchain. Azure Blockchain Workbench viene fornito senza un contratto di servizio. Usare la [pagina Microsoft Q&A per](/answers/topics/azure-blockchain-workbench.html) il supporto.

## <a name="deploy-the-orderer-and-peer-organization"></a>Distribuire l'ordinamento e l'organizzazione peer

Per iniziare, è necessaria una sottoscrizione di Azure in grado di supportare la distribuzione di più macchine virtuali e account di archiviazione standard. Se non si ha una sottoscrizione di Azure, è possibile [creare un account Azure gratuito.](https://azure.microsoft.com/free/)

Per iniziare a usare la distribuzione di Hyperledger Fabric di rete, passare al [portale di Azure](https://portal.azure.com).

1. Selezionare **Crea una risorsa**  >  **Blockchain** e quindi cercare Hyperledger Fabric in servizio Azure Kubernetes **(anteprima).**

2. Immettere i dettagli del progetto nella **scheda Informazioni di** base.

    ![Screenshot che mostra la scheda Informazioni di base.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Immettere i dettagli seguenti:
    - **Sottoscrizione:** scegliere il nome della sottoscrizione in cui si vuole distribuire i componenti Hyperledger Fabric di rete.
    - **Gruppo di risorse:** creare un nuovo gruppo di risorse o scegliere un gruppo di risorse vuoto esistente. Il gruppo di risorse conterà tutte le risorse distribuite come parte del modello.
    - **Area:** scegliere l'area di Azure in cui si vuole distribuire il cluster servizio Azure Kubernetes per i Hyperledger Fabric componenti. Il modello è disponibile in tutte le aree in cui è disponibile il modulo di controllo di accesso. Scegliere un'area in cui la sottoscrizione non supera il limite di quota della macchina virtuale (VM).
    - **Prefisso risorsa:** immettere un prefisso per la denominazione delle risorse distribuite. Deve contenere meno di sei caratteri e la combinazione di caratteri deve includere sia numeri che lettere.
4. Selezionare la **scheda Impostazioni infrastruttura** per definire i Hyperledger Fabric di rete che verranno distribuiti.

    ![Screenshot che mostra la scheda Impostazioni infrastruttura.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Immettere i dettagli seguenti:
    - **Nome organizzazione:** immettere il nome dell'organizzazione Hyperledger Fabric, necessaria per diverse operazioni del piano dati. Il nome dell'organizzazione deve essere univoco per ogni distribuzione.
    - **Componente di rete** dell'infrastruttura: scegliere **il servizio di ordinamento** o i nodi **peer** in base al componente di rete blockchain che si vuole configurare.
    - **Numero di nodi**: di seguito sono riportati i due tipi di nodi:
        - **Servizio di ordinamento:** nodi responsabili dell'ordinamento delle transazioni nel libro mastro. Selezionare il numero di nodi per fornire la tolleranza di errore alla rete. Il numero di nodi dell'ordine supportato è 3, 5 e 7.
        - **Nodi peer:** nodi che ospitano i libro mastro e i contratti intelligenti. È possibile scegliere da 1 a 10 nodi in base alle esigenze.
    - **Database di stato del mondo del nodo peer:** database di stato del mondo per i nodi peer. LevelDB è il database di stato predefinito incorporato nel nodo peer. Archivia i dati chaincode come semplici coppie chiave/valore e supporta solo query chiave, intervallo di chiavi e chiave composta. CouchDB è un database di stato alternativo facoltativo che supporta query complesse quando i valori dei dati chaincode vengono modellati come JSON. Questo campo viene visualizzato quando si sceglie Nodi **peer nell'elenco** a discesa **Componente** di rete infrastruttura.
    - **Nome utente CA infrastruttura:** l'autorità di certificazione dell'infrastruttura consente di inizializzare e avviare un processo server che ospita l'autorità di certificazione. Consente di gestire identità e certificati. Per impostazione predefinita, ogni cluster del servizio Contenitore di Servizio Contenitore di Windows distribuito come parte del modello avrà un pod della CA di infrastruttura. Immettere il nome utente usato per l'autenticazione della CA di Infrastruttura.
    - **Fabric CA password (Password CA** infrastruttura): immettere la password per l'autenticazione della CA dell'infrastruttura.
    - **Conferma password:** confermare la password della CA di infrastruttura.
    - **Certificati:** se si vogliono usare i propri certificati radice per inizializzare la CA di infrastruttura, scegliere l'opzione **Upload root certificate for Fabric CA** (Carica certificato radice per la CA di infrastruttura). In caso contrario, l'autorità di certificazione dell'infrastruttura crea certificati autofirmati per impostazione predefinita.
    - **Certificato radice:** caricare il certificato radice (chiave pubblica) con cui deve essere inizializzata la CA di infrastruttura. I certificati in formato pem sono supportati. I certificati devono essere validi e in un fuso orario UTC.
    - **Chiave privata del certificato radice:** caricare la chiave privata del certificato radice. Se si dispone di un certificato con estensione pem, con una chiave pubblica e privata combinata, caricarlo anche qui.


6. Selezionare la **scheda AKS cluster Settings (Impostazioni** cluster servizio Servizio Azure Kubernetes servizio Servizio Azure Kubernetes cluster). Il cluster del servizio AKS ha vari pod configurati per l'esecuzione Hyperledger Fabric componenti di rete. Le risorse di Azure distribuite sono:

    - **Strumenti di infrastruttura:** strumenti responsabili della configurazione dei Hyperledger Fabric componenti.
    - **Pod ordinamento/peer:** i nodi della Hyperledger Fabric rete.
    - **Proxy:** pod proxy NGNIX tramite il quale le applicazioni client possono comunicare con il cluster del servizio Web AKS.
    - **CA infrastruttura:** pod che esegue l'autorità di certificazione infrastruttura.
    - **PostgreSQL:** istanza di database che gestisce le identità della CA di infrastruttura.
    - **Insieme di credenziali** delle chiavi: istanza del Azure Key Vault distribuita per salvare le credenziali dell'autorità di certificazione dell'infrastruttura e i certificati radice forniti dal cliente. L'insieme di credenziali viene usato in caso di nuovo tentativo di distribuzione del modello, per gestire i meccanismi del modello.
    - **Disco gestito:** istanza del servizio azure Managed Disks che fornisce un archivio permanente per il libro mastro e per il database dello stato mondiale del nodo peer.
    - **IP pubblico:** endpoint del cluster del servizio Web del servizio Web di Microsoft Windows distribuito per la comunicazione con il cluster.

    Immettere i dettagli seguenti: 

    ![Screenshot che mostra la scheda A K S cluster settings (Impostazioni cluster A K S).](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

    - **Nome cluster Kubernetes:** modificare il nome del cluster del servizio Kubernetes, se necessario. Questo campo viene prepopolato in base al prefisso della risorsa fornito.
    - **Versione di Kubernetes:** scegliere la versione di Kubernetes che verrà distribuita nel cluster. In base all'area selezionata nella scheda **Informazioni di** base, le versioni supportate disponibili potrebbero cambiare.
    - **Prefisso DNS:** immettere un prefisso Domain Name System (DNS) per il cluster del servizio Web AKS. Si userà DNS per connettersi all'API Kubernetes durante la gestione dei contenitori dopo aver creato il cluster.
    - **Dimensioni del** nodo: per le dimensioni del nodo Kubernetes, è possibile scegliere dall'elenco di unità di stock-keeping (SKU) delle macchine virtuali disponibili in Azure. Per prestazioni ottimali, è consigliabile usare DS3 v2 Standard.
    - **Numero di** nodi: immettere il numero di nodi Kubernetes da distribuire nel cluster. È consigliabile mantenere il numero di nodi uguale o maggiore del numero di Hyperledger Fabric specificati nella **scheda Impostazioni infrastruttura.**
    - **ID client dell'entità** servizio: immettere l'ID client di un'entità servizio esistente o crearne una nuova. Per l'autenticazione del servizio AKS è necessaria un'entità servizio. Vedere la [procedura per creare un'entità servizio](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal).
    - **Segreto client dell'entità** servizio: immettere il segreto client dell'entità servizio specificata nell'ID client per l'entità servizio.
    - **Conferma segreto client:** confermare il segreto client per l'entità servizio.
    - **Abilita monitoraggio contenitori:** scegliere di abilitare il monitoraggio del servizio Web Disattesa, che consente ai log del servizio Web Disattesa di eseguire il push nell'area di lavoro Log Analytics specificata.
    - **Area di lavoro Log Analytics:** l'area di lavoro Log Analytics verrà popolata con l'area di lavoro predefinita creata se il monitoraggio è abilitato.

8. Selezionare la **scheda Rivedi e** crea. Questo passaggio attiva la convalida per i valori specificati.
9. Al termine della convalida, selezionare **Crea**.

    La distribuzione richiede in genere da 10 a 12 minuti. Il tempo può variare a seconda delle dimensioni e del numero di nodi del cluster di controllo di rete specificati.
10. Al termine della distribuzione, si viene informati tramite le notifiche di Azure nell'angolo superiore destro.
11. Selezionare **Vai al gruppo di risorse** per controllare tutte le risorse create come parte della distribuzione del modello. Tutti i nomi delle risorse inizieranno con il prefisso specificato nella scheda Informazioni **di** base.

## <a name="build-the-consortium"></a>Creare il consorzio

Per creare il consorzio blockchain dopo aver distribuito il servizio di ordinamento e i nodi peer, seguire questa procedura in sequenza. Lo script Hyperledger Fabric Azure (azhlf) consente di configurare il consorzio, creare il canale ed eseguire operazioni di chaincode.

> [!NOTE]
> Lo script Hyperledger Fabric Azure Hyperledger Fabric (azhlf) è stato aggiornato per offrire altre funzionalità. Se si vuole fare riferimento allo script precedente, vedere il [file leggimi in GitHub.](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md) Questo script è compatibile con Hyperledger Fabric in servizio Azure Kubernetes modello versione 2.0.0 e successive. Per controllare la versione della distribuzione, seguire la procedura descritta in [Risolvere i problemi di](#troubleshoot).

> [!NOTE]
> Lo script viene fornito solo per scenari dimostrativi, di sviluppo e di test. Il canale e il consorzio creati da questo script Hyperledger Fabric criteri di base per semplificare gli scenari di demo, sviluppo e test. Per la configurazione di produzione, è consigliabile aggiornare i criteri di Hyperledger Fabric canale/consorzio in base alle esigenze di conformità dell'organizzazione usando le API Hyperledger Fabric native.


Tutti i comandi per eseguire lo script Hyperledger Fabric Azure possono essere eseguiti tramite l'interfaccia della riga di comando di Azure Bash. È possibile accedere a Azure Cloud Shell tramite ̄ l'Hyperledger Fabric nell'opzione modello servizio Azure Kubernetes nell'angolo superiore destro del ![ ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) portale di Azure. Al prompt dei comandi digitare e selezionare il tasto INVIO per passare all'interfaccia della riga di comando di Bash oppure selezionare Bash dalla barra `bash` Cloud Shell comandi. 

Vedere [Azure Cloud Shell](../../cloud-shell/overview.md) per altre informazioni.

![Screenshot che mostra i comandi in Azure Cloud Shell.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


L'immagine seguente illustra il processo passo per passo per creare un consorzio tra un'organizzazione che ordina e un'organizzazione peer. Le sezioni seguenti illustrano i comandi dettagliati per completare questi passaggi.

![Diagramma del processo di creazione di un consorzio.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Al termine della configurazione iniziale, usare l'applicazione client per eseguire le operazioni seguenti: â€ ̄

- Gestione dei canali
- Gestione del consorzio
- Gestione chaincode

### <a name="download-client-application-files"></a>Scaricare i file dell'applicazione client

Il primo programma di installazione è scaricare i file dell'applicazione client. Eseguire i comandi seguenti per scaricare tutti i file e i pacchetti necessari:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

Questi comandi clonano azure Hyperledger Fabric codice dell'applicazione client dal repository GitHub pubblico, seguito dal caricamento di tutti i pacchetti npm dipendenti. Al termine dell'esecuzione del comando, è possibile visualizzare node_modules cartella nella directory corrente. Tutti i pacchetti necessari vengono caricati nella node_modules cartella .

### <a name="set-up-environment-variables"></a>Configurare le variabili di ambiente

Tutte le variabili di ambiente seguono la convenzione di denominazione delle risorse di Azure.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>Impostare le variabili di ambiente per il client dell'organizzazione degli ordini

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>Impostare le variabili di ambiente per il client dell'organizzazione peer

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

In base al numero di organizzazioni peer nel consorzio, potrebbe essere necessario ripetere i comandi peer e impostare la variabile di ambiente di conseguenza.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Impostare le variabili di ambiente per un account di archiviazione di Azure

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Usare i comandi seguenti per creare un account di archiviazione di Azure. Se si ha già un account di archiviazione di Azure, ignorare questo passaggio.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Usare i comandi seguenti per creare una condivisione file nell'account di archiviazione di Azure. Se si dispone già di una condivisione file, ignorare questo passaggio.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Usare i comandi seguenti per generare una stringa di connessione per una condivisione file di Azure.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>Importare un profilo di connessione dell'organizzazione, l'identità dell'utente amministratore e msp

Usare i comandi seguenti per recuperare il profilo di connessione dell'organizzazione, l'identità utente amministratore e il provider di servizi gestiti (MSP) dal cluster servizio Azure Kubernetes e archiviare queste identità nell'archivio locale dell'applicazione client. Un esempio di archivio locale è la directory *azhlfTool/stores.*

Per l'organizzazione dell'ordinamento:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

Per l'organizzazione peer:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-a-channel"></a>Creare un canale

Dal client dell'organizzazione dell'ordinamento usare il comando seguente per creare un canale che contenga solo l'organizzazione dell'ordinamento.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>Aggiungere un'organizzazione peer per la gestione del consorzio

>[!NOTE]
> Prima di iniziare con qualsiasi operazione di consorzio, assicurarsi di aver completato la configurazione iniziale dell'applicazione client.  

Eseguire i comandi seguenti nell'ordine specificato per aggiungere un'organizzazione peer in un canale e in un consorzio: 

1.  Dal client dell'organizzazione peer caricare il file MSP dell'organizzazione peer Archiviazione di Azure.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Dal client dell'organizzazione ordinazione scaricare il msp dell'organizzazione peer da Archiviazione di Azure. Eseguire quindi il comando per aggiungere l'organizzazione peer nel canale e nel consorzio.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Dal client dell'organizzazione di ordinamento caricare il profilo di connessione dell'ordinamento in Archiviazione di Azure in modo che l'organizzazione peer possa connettersi ai nodi dell'ordinamento usando questo profilo di connessione.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Dal client dell'organizzazione peer scaricare il profilo di connessione dell'ordinamento da Archiviazione di Azure. Eseguire quindi il comando per aggiungere nodi peer nel canale.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Analogamente, per aggiungere altre organizzazioni peer nel canale, aggiornare le variabili di ambiente peer in base all'organizzazione peer richiesta e ripetere i passaggi da 1 a 4.

### <a name="set-anchor-peers"></a>Impostare peer di ancoraggio

Dal client dell'organizzazione peer eseguire il comando per impostare i peer di ancoraggio per l'organizzazione peer nel canale specificato.

>[!NOTE]
> Prima di eseguire questo comando, assicurarsi che l'organizzazione peer venga aggiunta nel canale usando i comandi di gestione del consorzio.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` è un elenco separato da spazi di nodi peer da impostare come peer di ancoraggio. Ad esempio:

  - Impostare `<anchorPeersList>` come se si desidera impostare solo il nodo `"peer1"` peer1 come peer di ancoraggio.
  - Impostare come se si desidera impostare i nodi peer1 e `<anchorPeersList>` `"peer1" "peer3"` peer3 come peer di ancoraggio.

## <a name="chaincode-management-commands"></a>Comandi di gestione del codice a catena

>[!NOTE]
> Prima di iniziare con qualsiasi operazione chaincode, assicurarsi di aver eseguito la configurazione iniziale dell'applicazione client.  

### <a name="set-the-chaincode-specific-environment-variables"></a>Impostare le variabili di ambiente specifiche di chaincode

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=â€œchaincode_example02â€
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=â€œ1â€ for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=â€œ/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/goâ€
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Installare chaincode  

Eseguire il comando seguente per installare chaincode nell'organizzazione peer.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Il comando installerà chaincode in tutti i nodi peer dell'organizzazione peer impostata nella variabile `ORGNAME` di ambiente . Se nel canale sono presenti due o più organizzazioni peer e si vuole installare chaincode in tutte, eseguire questo comando separatamente per ogni organizzazione peer.  

A tale scopo, seguire questa procedura:  

1.  Impostare `ORGNAME` e in base a ed eseguire il comando `USER_IDENTITY` `peerOrg1` `./azhlf chaincode install` .  
2.  Impostare `ORGNAME` e in base a ed eseguire il comando `USER_IDENTITY` `peerOrg2` `./azhlf chaincode install` .  

### <a name="instantiate-chaincode"></a>Creare un'istanza di chaincode  

Dall'applicazione client peer eseguire il comando seguente per creare un'istanza di chaincode nel canale.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

Passare rispettivamente il nome della funzione di creazione dell'istanza e l'elenco di argomenti separati da spazi in `<instantiateFunc>` e `<instantiateFuncArgs>` . Ad esempio, per creare un chaincode_example02.go chaincode, impostare `<instantiateFunc>` su `init` e su `<instantiateFuncArgs>` `"a" "2000" "b" "1000"` .

È anche possibile passare il file JSON di configurazione della raccolta usando il `--collections-config` flag . In caso contrario, impostare gli argomenti temporanei usando il flag durante la creazione di `-t` un'istanza di chaincode usato per le transazioni private.

Ad esempio:

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

La parte è il percorso del file JSON che contiene le raccolte definite per la creazione di un'istanza `<collectionConfigJSONFilePath>` di chaincode di dati privati. È possibile trovare il file JSON di configurazione di una raccolta di esempio relativo alla directory *azhlfTool* nel percorso seguente: `./samples/chaincode/src/private_marbles/collections_config.json` .
Passare `<transientArgs>` come JSON valido in formato stringa. Eseguire l'escape di qualsiasi carattere speciale. ad esempio `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> Eseguire il comando una volta da qualsiasi organizzazione peer nel canale. Dopo che la transazione è stata inviata correttamente all'ordinamento, la distribuisce a tutte le organizzazioni peer nel canale. Viene quindi creata un'istanza di Chaincode in tutti i nodi peer in tutte le organizzazioni peer nel canale.  

### <a name="invoke-chaincode"></a>Richiamare il codice di catena  

Dal client dell'organizzazione peer eseguire il comando seguente per richiamare la funzione chaincode:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Passare il nome della funzione invoke e l'elenco di argomenti separati da spazi in ̄ `<invokeFunction>` â€ ̄ andâ€ ̄ `<invokeFuncArgs>` â€ ̄ respectively. Continuando con l'esempio chaincode_example02.go chaincode, per eseguire un'operazione invoke, impostareâ€ ̄ `<invokeFunction>` â€ ̄ toâ€ ̄ `invoke` â€ ̄ andâ€ ̄ `<invokeFuncArgs>` â€ ̄ to `"a" "b" "10"` .  

>[!NOTE]
> Eseguire il comando una sola volta da qualsiasi organizzazione peer nel canale. Dopo che la transazione è stata inviata correttamente all'ordinamento, l'ordinamento distribuisce la transazione a tutte le organizzazioni peer nel canale. Lo stato del mondo viene quindi aggiornato in tutti i nodi peer di tutte le organizzazioni peer nel canale.  


### <a name="query-chaincode"></a>Codice a catena di query  

Eseguire il comando seguente per eseguire una query chaincode:  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
I peer di supporto sono peer in cui è installato chaincode e viene chiamato per l'esecuzione di transazioni. È necessario impostare `<endorsingPeers>` per contenere i nomi dei nodi peer dell'organizzazione peer corrente. Elencare i peer di supporto per una determinata combinazione di codice a catena e canale separati da spazi. Ad esempio: `-p "peer1" "peer3"`.

Se si usa *azhlfTool* per installare chaincode, passare i nomi dei nodi peer come valore all'argomento peer di endorsing. Chaincode viene installato in ogni nodo peer per l'organizzazione. 

Passare il nome della funzione di query e l'elenco di argomenti separati da spazi in ̄ `<queryFunction>` â€ ̄ andâ€ ̄ `<queryFuncArgs>` â€ ̄ respectively. Anche in questo caso, considerando ̄chaincode_example02.goâ€ ̄ chaincode come riferimento, per eseguire una query sul valore di "a" nello stato del mondo, impostare ̄ `<queryFunction>` â€ ̄ toâ€ ̄ e `query` ̄ su `<queryArgs>` `"a"` .  

## <a name="troubleshoot"></a>Risolvere problemi

### <a name="find-deployed-version"></a>Trovare la versione distribuita

Eseguire i comandi seguenti per trovare la versione della distribuzione del modello. Impostare le variabili di ambiente in base al gruppo di risorse in cui è stato distribuito il modello.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>Applicare patch alla versione precedente

Se si verificano problemi con l'esecuzione di chaincode in tutte le distribuzioni della versione del modello precedente alla 3.0.0, seguire questa procedura per applicare patch ai nodi peer con una correzione.

Scaricare lo script di distribuzione peer.

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

Eseguire lo script usando il comando seguente sostituendo i parametri per il peer.

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

Attendere l'applicazione di patch a tutti i nodi peer. È sempre possibile controllare lo stato dei nodi peer, in un'istanza diversa della shell usando il comando seguente.

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>Supporto, commenti e suggerimenti

Per rimanere aggiornati sulle offerte di servizi blockchain e sulle informazioni del team di Azure Blockchain, visitare il [blog Azure Blockchain .](https://azure.microsoft.com/blog/topics/blockchain/)

Per inviare un feedback sul prodotto o richiedere nuove funzionalità, pubblicare o votare un'idea tramite il [forum di feedback su Azure per Blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Supporto della community

Interagire con i tecnici Microsoft e Azure Blockchain esperti della community:

- [Pagina Microsoft Q&A](/answers/topics/azure-blockchain-workbench.html) 
   
  Il supporto tecnico per i modelli di blockchain è limitato ai problemi di distribuzione.
- [Community tecnica Microsoft](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
