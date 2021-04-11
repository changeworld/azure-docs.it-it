---
title: "Esercitazione: Eseguire la migrazione offline di MongoDB all'API di Azure Cosmos DB per MongoDB"
titleSuffix: Azure Database Migration Service
description: Eseguire la migrazione da MongoDB locale a Azure Cosmos DB API per MongoDB offline usando il servizio migrazione del database di Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 02/03/2021
ms.openlocfilehash: 8977bb90087d9d3d4962d7eda50903d97da93539
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443868"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-api-for-mongodb-offline"></a>Esercitazione: eseguire la migrazione di MongoDB all'API Azure Cosmos DB per MongoDB offline

Usare il servizio migrazione del database di Azure per eseguire una migrazione unica e offline dei database da un'istanza locale o cloud di MongoDB all'API Azure Cosmos DB per MongoDB.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
>
> * Creare un'istanza del servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.

In questa esercitazione si esegue la migrazione di un set di dati in MongoDB ospitato in una macchina virtuale di Azure. Con il servizio migrazione del database di Azure è possibile migrare il set di dati all'API Azure Cosmos DB per MongoDB. Se non è già stata configurata un'origine MongoDB, vedere [installare e configurare MongoDB in una VM Windows in Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* [Completare i passaggi di pre-migrazione](../cosmos-db/mongodb-pre-migration.md) , ad esempio la stima della velocità effettiva e la scelta di una chiave di partizione.
* [Creare un account per l'API Azure Cosmos DB per MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Creare una Rete virtuale di Microsoft Azure per il servizio migrazione del database di Azure usando Azure Resource Manager. Questo modello di distribuzione fornisce la connettività da sito a sito ai server di origine locali usando [Azure ExpressRoute](../expressroute/expressroute-introduction.md) o [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Per ulteriori informazioni sulla creazione di una rete virtuale, vedere la [documentazione relativa alla rete virtuale di Azure](../virtual-network/index.yml), in particolare gli articoli "Guida introduttiva" con informazioni dettagliate.

    > [!NOTE]
    > Durante la configurazione della rete virtuale, se si usa ExpressRoute con il peering di rete per Microsoft, aggiungere gli [endpoint](../virtual-network/virtual-network-service-endpoints-overview.md) servizio seguenti alla subnet in cui verrà effettuato il provisioning del servizio:
    >
    > * Endpoint database di destinazione (ad esempio, endpoint SQL o endpoint Azure Cosmos DB)
    > * Endpoint di archiviazione
    > * Endpoint bus di servizio
    >
    > Questa configurazione è necessaria perché il Servizio Migrazione del database di Azure non ha connettività Internet.

* Verificare che le regole del gruppo di sicurezza di rete (NSG) per la rete virtuale non blocchino le porte di comunicazione seguenti: 53, 443, 445, 9354 e 10000-20000. Per altre informazioni, vedere [Filtrare il traffico di rete con gruppi di sicurezza di rete](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Aprire Windows Firewall per consentire a Servizio Migrazione del database di Azure di accedere al server MongoDB di origine, per impostazione predefinita attraverso la porta TCP 27017.
* Quando si usa un'appliance firewall davanti al database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al servizio migrazione del database di Azure di accedere al database di origine per la migrazione.

## <a name="configure-the-server-side-retry-feature"></a>Configurare la funzionalità di ripetizione dei tentativi sul lato server

È possibile trarre vantaggio dalle funzionalità di governance delle risorse se si esegue la migrazione da MongoDB a Azure Cosmos DB. Con queste funzionalità è possibile usare in modo completo le unità richiesta di cui è stato effettuato il provisioning (UR/sec) di velocità effettiva. Azure Cosmos DB potrebbe limitare una determinata richiesta del servizio migrazione del database durante la migrazione, se tale richiesta supera le UR/sec con provisioning del contenitore. La richiesta deve quindi essere ritentata.

Il servizio migrazione del database è in grado di eseguire nuovi tentativi. È importante comprendere che il tempo di round trip interessato dall'hop di rete tra il servizio migrazione del database e Azure Cosmos DB influiscono sul tempo di risposta complessivo della richiesta. Il miglioramento del tempo di risposta per le richieste limitate può ridurre il tempo totale necessario per la migrazione.

La funzionalità di ripetizione dei tentativi lato server di Azure Cosmos DB consente al servizio di intercettare i codici di errore e riprovare con un tempo di round trip molto inferiore, migliorando in modo significativo i tempi di risposta della richiesta.

Per usare il tentativo lato server, nel portale di Azure Cosmos DB selezionare **funzionalità**  >  **Riprova sul lato server**.

![Screenshot che mostra dove trovare la funzionalità di ripetizione dei tentativi sul lato server.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Se la funzionalità è disabilitata, selezionare **Abilita**.

![Screenshot che illustra come abilitare il tentativo del lato server.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="register-the-resource-provider"></a>Registrare il provider di risorse

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

   ![Screenshot che mostra le sottoscrizioni del portale.](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Screenshot che mostra i provider di risorse.](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.

    ![Screenshot che illustra come registrare il provider di risorse.](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Creare un'istanza

1. Nel portale di Azure selezionare **+ Crea una risorsa,** cercare Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Screenshot che mostra Azure Marketplace.](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Screenshot che illustra come creare un'istanza del servizio migrazione del database di Azure.](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. In **Crea servizio migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare la località in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure. 

5. Selezionare una rete virtuale esistente o crearne una nuova.

    La rete virtuale consente al Servizio Migrazione del database di Azure di accedere all'istanza di MongoDB di origine e all'account Azure Cosmos DB di destinazione.

    Per altre informazioni su come creare una rete virtuale nella portale di Azure, vedere [creare una rete virtuale usando il portale di Azure](../virtual-network/quick-create-portal.md).

6. Selezione di un piano tariffario.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    ![Screenshot che mostra le impostazioni di configurazione per l'istanza del servizio migrazione del database di Azure.](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato il servizio, individuarlo all'interno del portale di Azure e aprirlo. Quindi, creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

      ![Screenshot che illustra come trovare tutte le istanze del servizio migrazione del database di Azure.](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Nella schermata **Servizi Migrazione del database di Azure** cercare il nome dell'istanza di Servizio Migrazione del database di Azure appena creata e quindi selezionare l'istanza.

3. Selezionare **+ nuovo progetto di migrazione**.

4. In **nuovo progetto di migrazione** specificare un nome per il progetto e nella casella di testo **tipo server di origine** Selezionare **MongoDB**. Nella casella di testo **tipo server di destinazione** selezionare **CosmosDB (API MongoDB)**, quindi selezionare **migrazione dati offline** per **scegliere il tipo di attività**. 

    ![Screenshot che mostra le opzioni del progetto.](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Selezionare **Crea ed esegui attività** per creare il progetto ed eseguire l'attività di migrazione.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Dettagli origine** specificare i dettagli di connessione per il server MongoDB di origine.

   > [!IMPORTANT]
   > Il servizio migrazione del database di Azure non supporta Azure Cosmos DB come origine.

    Per la connessione a un'origine sono disponibili tre modalità:
   * **Modalità standard**, che accetta un nome di dominio completo o un indirizzo IP, un numero di porta e le credenziali di connessione.
   * **Modalità stringa di connessione**, che accetta una stringa di connessione MongoDB come descritto in [formato URI stringa di connessione](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Dati di Archiviazione di Azure**, che accetta l'URL di firma di accesso condiviso di un contenitore BLOB. Select **BLOB contiene dump BSON** se nel contenitore BLOB sono presenti dump BSON prodotti dallo [strumento bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/)di MongoDB. Non selezionare questa opzione se il contenitore contiene file JSON.

     Se si seleziona questa opzione, assicurarsi che la stringa di connessione dell'account di archiviazione sia presente nel formato seguente:

     ```
     https://blobnameurl/container?SASKEY
     ```

     È possibile trovare la stringa di connessione della firma di accesso condiviso del contenitore BLOB in Azure Storage Explorer. La creazione della firma di accesso condiviso per il contenitore interessato fornisce l'URL nel formato richiesto.
     
     Inoltre, in base alle informazioni sul dump del tipo in archiviazione di Azure, tenere presente quanto segue:

     * Per i dump di BSON, i dati all'interno del contenitore BLOB devono essere nel formato bsondump. Inserire i file di dati in cartelle denominate dopo i database contenitore nel formato *Collection. BSON*. Assegnare un nome ai file di metadati utilizzando il formato *collection.metadata.json*.

     * Per i dump JSON, i file nel contenitore di BLOB devono essere inseriti in cartelle con gli stessi nomi dei database che li contengono. All'interno di ogni cartella del database, i file di dati devono essere inseriti in una sottocartella denominata *dati* e denominati usando il formato *collection.json*. Inserire i file di metadati in una sottocartella denominata *Metadata* e denominarli usando lo stesso formato *collection.json*. I file di metadati devono avere lo stesso formato di quelli prodotti con lo strumento bsondump di MongoDB.

    > [!IMPORTANT]
    > Non è consigliabile usare un certificato autofirmato nel server MongoDB. Se è necessario utilizzarne uno, connettersi al server utilizzando la modalità stringa di connessione e assicurarsi che la stringa di connessione includa le virgolette ("").
    >
    >```
    >&sslVerifyCertificate=false
    >```

   È anche possibile usare l'indirizzo IP per le situazioni in cui la risoluzione dei nomi DNS non è possibile.

   ![Screenshot che Mostra come specificare i dettagli di origine.](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Selezionare **Salva**.

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Nella schermata **Dettagli destinazione migrazione** specificare i dettagli di connessione per l'account Azure Cosmos DB di destinazione. Questo è l'account di Azure Cosmos DB API per MongoDB di cui è stato effettuato il provisioning per il quale si sta eseguendo la migrazione dei dati di MongoDB.

    ![Screenshot che Mostra come specificare i dettagli di destinazione.](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Selezionare **Salva**.

## <a name="map-to-target-databases"></a>Eseguire il mapping nei database di destinazione

1. Nella schermata **Map to target databases** (Esegui il mapping nel database di destinazione) eseguire il mapping del database di origine e del database di destinazione per la migrazione.

    Se il database di destinazione contiene lo stesso nome del database di origine, il Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.

    Se **create** viene visualizzato accanto al nome del database, significa che il servizio migrazione del database di Azure non ha trovato il database di destinazione e il servizio creerà il database per l'utente.

    A questo punto della migrazione è possibile [eseguire il provisioning della velocità effettiva](../cosmos-db/set-throughput.md). In Azure Cosmos DB è possibile effettuare il provisioning della velocità effettiva a livello di database o singolarmente per ogni raccolta. La velocità effettiva viene misurata in [unità richiesta](../cosmos-db/request-units.md). Vedere altre informazioni sui [prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Screenshot che mostra il mapping ai database di destinazione.](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Selezionare **Salva**.
3. Nella schermata **Impostazioni raccolta** espandere l'elenco delle raccolte, quindi esaminare le raccolte di cui verrà eseguita la migrazione.

    Il servizio migrazione del database di Azure seleziona automaticamente tutte le raccolte presenti nell'istanza di MongoDB di origine che non esistono nell'account Azure Cosmos DB di destinazione. Se si desidera rieseguire la migrazione di raccolte che includono già dati, è necessario selezionare in modo esplicito le raccolte in questo riquadro.

    È possibile specificare il numero di UR da usare per le raccolte. Servizio Migrazione del database di Azure suggerisce le impostazioni predefinite intelligenti in base alle dimensioni della raccolta.

    > [!NOTE]
    > Eseguire la migrazione e la raccolta del database in parallelo. Se necessario, è possibile usare più istanze del servizio migrazione del database di Azure per velocizzare l'esecuzione.

    È anche possibile specificare una chiave di partizione per sfruttare il [partizionamento in Azure Cosmos DB](../cosmos-db/partitioning-overview.md) per una scalabilità ottimale. Esaminare le [procedure consigliate per la selezione di una partizione o di una chiave di partizione](../cosmos-db/partitioning-overview.md#choose-partitionkey).

    ![Screenshot che mostra la selezione delle tabelle delle raccolte.](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Selezionare **Salva**.

5. Nella schermata **Riepilogo migrazione** specificare un nome per l'attività di migrazione nella casella di testo **Nome attività**.

    ![Screenshot che mostra il riepilogo di nigration.](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Eseguire la migrazione

Selezionare **Esegui migrazione**. Viene visualizzata la finestra attività di migrazione e lo stato dell'attività non è stato **avviato**.

![Screenshot che mostra lo stato dell'attività.](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorare la migrazione

Nella schermata attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione fino a quando lo stato della migrazione non viene visualizzato come **completato**.

> [!NOTE]
> È possibile selezionare l'attività per ottenere i dettagli delle metriche di migrazione a livello di database e di raccolta.

![Screenshot che mostra lo stato dell'attività completato.](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-azure-cosmos-db"></a>Verificare i dati in Azure Cosmos DB

Al termine della migrazione, è possibile controllare l'account Azure Cosmos DB per verificare che tutte le raccolte siano state migrate correttamente.

![Screenshot che mostra dove controllare l'account Azure Cosmos DB per verificare che la migrazione di tutte le raccolte sia stata completata correttamente.](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Ottimizzazione della post-migrazione

Dopo aver eseguito la migrazione dei dati archiviati nel database MongoDB all'API Azure Cosmos DB per MongoDB, è possibile connettersi a Azure Cosmos DB e gestire i dati. È anche possibile eseguire altri passaggi di ottimizzazione post-migrazione. Questi possono includere l'ottimizzazione dei criteri di indicizzazione, l'aggiornamento del livello di coerenza predefinito o la configurazione della distribuzione globale per l'account Azure Cosmos DB. Per altre informazioni, vedere [ottimizzazione post-migrazione](../cosmos-db/mongodb-post-migration.md).

## <a name="next-steps"></a>Passaggi successivi

Esaminare le linee guida per la migrazione per altri scenari nella [Guida alla migrazione del database di Azure](https://datamigration.microsoft.com/).



