---
title: Condividere e ricevere dati da Archiviazione BLOB di Azure e Azure Data Lake Storage
description: Informazioni su come condividere e ricevere dati dall'archiviazione BLOB di Azure e Azure Data Lake Storage.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/23/2021
ms.openlocfilehash: dc309e85373193e4f5d431f543ff3e59ea5bebc7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739263"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Condividere e ricevere dati da Archiviazione BLOB di Azure e Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

La condivisione di dati di Azure supporta la condivisione basata su snapshot da un account di archiviazione. Questo articolo illustra come condividere e ricevere dati da archiviazione BLOB, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2 di Azure.

La condivisione di dati di Azure supporta la condivisione di file, cartelle e file System da Azure Data Lake Gen1 e Azure Data Lake Gen2. Supporta anche la condivisione di BLOB, cartelle e contenitori dall'archiviazione BLOB di Azure. Attualmente sono supportati solo i BLOB in blocchi. I dati condivisi da queste origini possono essere ricevuti da Azure Data Lake Gen2 o dall'archiviazione BLOB di Azure.

Quando i file System, i contenitori o le cartelle sono condivisi nella condivisione basata su snapshot, i consumer di dati possono scegliere di eseguire una copia completa dei dati della condivisione. In alternativa, è possibile usare la funzionalità di snapshot incrementale per copiare solo i file nuovi o aggiornati. La funzionalità di snapshot incrementale è basata sull'ora dell'Ultima modifica dei file. 

I file esistenti con lo stesso nome vengono sovrascritti durante uno snapshot. Un file eliminato dall'origine non viene eliminato nella destinazione. Le sottocartelle vuote nell'origine non vengono copiate nella destinazione. 

## <a name="share-data"></a>Condividere dati

Usare le informazioni nelle sezioni riportate di seguito per condividere i dati usando la condivisione di dati di Azure. 
### <a name="prerequisites-to-share-data"></a>Prerequisiti per la condivisione di dati

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Trovare l'indirizzo di posta elettronica di accesso di Azure del destinatario. L'alias di posta elettronica del destinatario non funzionerà per le proprie esigenze.
* Se l'archivio dati di Azure di origine si trova in una sottoscrizione di Azure diversa da quella in cui verrà creata la risorsa di condivisione dati, registrare il [provider di risorse Microsoft. DataShare](concepts-roles-permissions.md#resource-provider-registration) nella sottoscrizione in cui si trova l'archivio dati di Azure. 

### <a name="prerequisites-for-the-source-storage-account"></a>Prerequisiti per l'account di archiviazione di origine

* Un account dell'Archiviazione di Azure. Se non si dispone già di un account, [crearne uno](../storage/common/storage-account-create.md).
* Autorizzazione per la scrittura nell'account di archiviazione. L'autorizzazione di scrittura è in *Microsoft. storage/storageAccounts/Write*. Fa parte del ruolo Collaboratore.
* Autorizzazione per aggiungere l'assegnazione di ruolo all'account di archiviazione. Questa autorizzazione è in *Microsoft. Authorization/Role assegnations/Write*. Fa parte del ruolo di proprietario. 

### <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Creazione di un account di condivisione dati

Creare una risorsa di condivisione dati di Azure in un gruppo di risorse di Azure.

1. Nell'angolo in alto a sinistra del portale aprire il menu e quindi selezionare **Crea una risorsa** (+).

1. Cercare *Condivisione dati*.

1. Selezionare **condivisione dati** e **creare**.

1. Fornire i dettagli di base della risorsa di condivisione dati di Azure: 

     **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Subscription | Sottoscrizione in uso | Selezionare una sottoscrizione di Azure per l'account di condivisione dati.|
    | Gruppo di risorse | *test-resource-group* | Usare un gruppo di risorse esistente o creare un gruppo di risorse. |
    | Posizione | *Stati Uniti orientali 2* | Selezionare un'area per l'account di condivisione dati.
    | Nome | *datashareaccount* | Assegnare un nome all'account di condivisione dati. |
    | | |

1. Selezionare **Verifica + crea**  >  **Crea** per eseguire il provisioning dell'account di condivisione dati. Il provisioning di un nuovo account di condivisione dati richiede in genere circa 2 minuti. 

1. Al termine della distribuzione, selezionare **Vai alla risorsa**.

### <a name="create-a-share"></a>Creare una condivisione

1. Passare alla pagina **Panoramica** della condivisione dati.

   :::image type="content" source="./media/share-receive-data.png" alt-text="Screenshot che mostra la panoramica della condivisione dati.":::

1. Selezionare **Start sharing your data** (Inizia a condividere i dati).

1. Selezionare **Crea**.   

1. Specificare i dettagli per la condivisione. Specificare un nome, un tipo di condivisione, una descrizione del contenuto della condivisione e le condizioni per l'utilizzo (facoltativo). 

    ![Screenshot che mostra i dettagli della condivisione di dati.](./media/enter-share-details.png "Immettere i dettagli della condivisione di dati.") 

1. Selezionare **Continua**.

1. Per aggiungere set di impostazioni alla condivisione, selezionare **Aggiungi set di impostazioni**. 

    ![Screenshot che illustra come aggiungere set di impostazioni alla condivisione.](./media/datasets.png "Set di dati.")

1. Selezionare un tipo di set di dati da aggiungere. L'elenco dei tipi di set di dati dipende dal fatto che sia stata selezionata la condivisione basata su snapshot o la condivisione sul posto nel passaggio precedente. 

    ![Screenshot che mostra dove selezionare un tipo di set di dati.](./media/add-datasets.png "Aggiungere set di impostazioni.")    

1. Passare all'oggetto che si desidera condividere. Quindi selezionare **Aggiungi set di impostazioni**. 

    ![Screenshot che illustra come selezionare un oggetto da condividere.](./media/select-datasets.png "Selezionare set di impostazioni.")    

1. Nella scheda **destinatari** aggiungere l'indirizzo di posta elettronica del consumer di dati selezionando **Aggiungi destinatario**. 

    ![Screenshot che illustra come aggiungere indirizzi di posta elettronica del destinatario.](./media/add-recipient.png "Aggiungere i destinatari.") 

1. Selezionare **Continua**.

1. Se è stato selezionato un tipo di condivisione snapshot, è possibile configurare la pianificazione dello snapshot per aggiornare i dati per il consumer di dati. 

    ![Screenshot che mostra le impostazioni della pianificazione dello snapshot.](./media/enable-snapshots.png "Abilitare gli snapshot.") 

1. Selezionare un'ora di inizio e un intervallo di ricorrenza. 

1. Selezionare **Continua**.

1. Nella scheda **revisione e creazione** esaminare il contenuto del pacchetto, le impostazioni, i destinatari e le impostazioni di sincronizzazione. Quindi selezionare **Crea**

A questo punto è stata creata la condivisione di dati di Azure. Il destinatario della condivisione dati può accettare l'invito. 

## <a name="receive-data"></a>Ricevere dati

Le sezioni seguenti descrivono come ricevere dati condivisi.
### <a name="prerequisites-to-receive-data"></a>Prerequisiti per la ricezione dei dati
Prima di accettare un invito alla condivisione dati, verificare che siano soddisfatti i prerequisiti seguenti: 

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, creare un [account gratuito](https://azure.microsoft.com/free/).
* Invito da Azure. L'oggetto del messaggio di posta elettronica deve essere "invito di condivisione dati di Azure da *\<yourdataprovider\@domain.com>* ".
* Un [provider di risorse Microsoft. DataShare](concepts-roles-permissions.md#resource-provider-registration) registrato in:
    * Sottoscrizione di Azure in cui verrà creata una risorsa di condivisione dati.
    * Sottoscrizione di Azure in cui si trovano gli archivi dati di Azure di destinazione.

### <a name="prerequisites-for-a-target-storage-account"></a>Prerequisiti per un account di archiviazione di destinazione

* Un account dell'Archiviazione di Azure. Se non si dispone già di un [account, crearne](../storage/common/storage-account-create.md)uno. 
* Autorizzazione per la scrittura nell'account di archiviazione. Questa autorizzazione si trova in *Microsoft. storage/storageAccounts/Write*. Fa parte del ruolo Collaboratore. 
* Autorizzazione per aggiungere l'assegnazione di ruolo all'account di archiviazione. Questa assegnazione si trova in *Microsoft. Authorization/Role assegnations/Write*. Fa parte del ruolo di proprietario.  

### <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

### <a name="open-an-invitation"></a>Apri un invito

È possibile aprire un invito da un messaggio di posta elettronica o direttamente dalla portale di Azure.

1. Per aprire un invito da un messaggio di posta elettronica, controllare la posta in arrivo per un invito da un provider di dati. L'invito da Microsoft Azure è denominato "invito di condivisione dati di Azure da *\<yourdataprovider\@domain.com>* ". Selezionare **Visualizza invito** per visualizzare l'invito in Azure. 

   Per aprire un invito dal portale di Azure, cercare gli *inviti* per la condivisione di dati. Viene visualizzato un elenco di inviti per la condivisione di dati.

   ![Screenshot che mostra l'elenco degli inviti nel portale di Azure.](./media/invitations.png "Elenco di inviti.") 

1. Selezionare la condivisione che si desidera visualizzare. 

### <a name="accept-an-invitation"></a>Accetta un invito
1. Esaminare tutti i campi, inclusa la **condizioni per l'utilizzo**. Se si accettano le condizioni, selezionare la casella di controllo. 

   ![Screenshot che mostra l'area Condizioni per l'utilizzo.](./media/terms-of-use.png "Condizioni per l'utilizzo.") 

1. In **account di condivisione dati di destinazione** selezionare la sottoscrizione e il gruppo di risorse in cui verrà distribuita la condivisione di dati. Compilare i campi seguenti:

   * Nel campo **account condivisione dati** selezionare **Crea nuovo** se non si ha un account di condivisione dati. Altrimenti, selezionare un account di condivisione dati esistente che accetterà la condivisione di dati. 

   * Nel campo **nome condivisione ricevuta** lasciare l'impostazione predefinita specificata dal provider di dati o specificare un nuovo nome per la condivisione ricevuta. 

1. Selezionare **Accept e Configure**. Viene creata una sottoscrizione di condivisione. 

   ![Screenshot che mostra dove accettare le opzioni di configurazione.](./media/accept-options.png "Opzioni di accettazione") 

    La condivisione ricevuta viene visualizzata nell'account di condivisione dati. 

    Se non si desidera accettare l'invito, selezionare **rifiuta**. 

### <a name="configure-a-received-share"></a>Configurare una condivisione ricevuta
Eseguire la procedura descritta in questa sezione per configurare un percorso per la ricezione dei dati.

1. Nella scheda **set** di dati selezionare la casella di controllo accanto al set di dati a cui si desidera assegnare una destinazione. Selezionare **mapping alla destinazione** per scegliere un archivio dati di destinazione. 

   ![Screenshot che illustra come eseguire il mapping a una destinazione.](./media/dataset-map-target.png "Mappa per la destinazione.") 

1. Selezionare un archivio dati di destinazione per i dati. I file nell'archivio dati di destinazione con lo stesso percorso e nome dei file nei dati ricevuti verranno sovrascritti. 

   ![Screenshot che mostra dove selezionare un account di archiviazione di destinazione.](./media/map-target.png "Archiviazione di destinazione.") 

1. Per la condivisione basata su snapshot, se il provider di dati utilizza una pianificazione snapshot per aggiornare regolarmente i dati, è possibile abilitare la pianificazione dalla scheda **pianificazione snapshot** . Selezionare la casella accanto alla pianificazione dello snapshot. Selezionare quindi **Abilita**.

   ![Screenshot che illustra come abilitare una pianificazione dello snapshot.](./media/enable-snapshot-schedule.png "Abilitare la pianificazione dello snapshot.")

### <a name="trigger-a-snapshot"></a>Attivazione di uno snapshot
I passaggi descritti in questa sezione si applicano solo alla condivisione basata su snapshot.

1. È possibile attivare uno snapshot dalla scheda **Dettagli** . Nella scheda selezionare **trigger snapshot**. È possibile scegliere di attivare uno snapshot completo o uno snapshot incrementale dei dati. Se si ricevono dati dal provider di dati per la prima volta, selezionare **copia completa**. Quando uno snapshot è in esecuzione, gli snapshot successivi non vengono avviati fino al completamento di quello precedente.

   ![Screenshot che mostra la selezione dello snapshot del trigger.](./media/trigger-snapshot.png "Snapshot del trigger.") 

1. Quando lo stato dell'ultima esecuzione ha *esito positivo*, passare all'archivio dati di destinazione per visualizzare i dati ricevuti. Selezionare **set di impostazioni** e quindi selezionare il collegamento percorso di destinazione. 

   ![Screenshot che mostra un mapping del set di dati consumer.](./media/consumer-datasets.png "Mapping del set di dati del consumer.") 

### <a name="view-history"></a>Visualizzare la cronologia
È possibile visualizzare la cronologia degli snapshot solo nella condivisione basata su snapshot. Per visualizzare la cronologia, aprire la scheda **cronologia** . Qui viene visualizzata la cronologia di tutti gli snapshot generati negli ultimi 30 giorni. 

## <a name="storage-snapshot-performance"></a>Prestazioni degli snapshot di archiviazione
Le prestazioni degli snapshot di archiviazione sono interessate da diversi fattori, oltre al numero di file e alle dimensioni dei dati condivisi. È sempre consigliabile eseguire il test delle prestazioni. Di seguito sono riportati alcuni fattori di esempio che influiscono sulle prestazioni.

* Accesso simultaneo agli archivi dati di origine e di destinazione.  
* Percorso degli archivi dati di origine e di destinazione. 
* Per gli snapshot incrementali, il numero di file nel set di dati condiviso può influito sul tempo necessario per trovare l'elenco dei file con l'ora dell'Ultima modifica dopo l'ultimo snapshot riuscito. 


## <a name="next-steps"></a>Passaggi successivi
Si è appreso come condividere e ricevere dati da un account di archiviazione usando il servizio di condivisione dati di Azure. Per informazioni sulla condivisione da altre origini dati, vedere [archivi dati supportati](supported-data-stores.md).