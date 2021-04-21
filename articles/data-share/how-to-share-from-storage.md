---
title: Condividere e ricevere dati da Archiviazione BLOB di Azure e Azure Data Lake Storage
description: Informazioni su come condividere e ricevere dati da Archiviazione BLOB di Azure e Azure Data Lake Storage.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 59c1ca67c9e93b62890512cda647ffcdf7712f9a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819269"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Condividere e ricevere dati da Archiviazione BLOB di Azure e Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Condivisione dati di Azure supporta la condivisione basata su snapshot da un account di archiviazione. Questo articolo illustra come condividere e ricevere dati da Archiviazione BLOB di Azure, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2.

Condivisione dati di Azure supporta la condivisione di file, cartelle e file system da Azure Data Lake Gen1 e Azure Data Lake Gen2. Supporta anche la condivisione di BLOB, cartelle e contenitori da Archiviazione BLOB di Azure. È possibile condividere BLOB in blocchi, di accodamento o di pagine che vengono ricevuti come BLOB in blocchi. I dati condivisi da queste origini possono essere ricevuti da Azure Data Lake Gen2 o Archiviazione BLOB di Azure.

Quando file system, contenitori o cartelle vengono condivisi nella condivisione basata su snapshot, i consumer di dati possono scegliere di creare una copia completa dei dati di condivisione. Oppure possono usare la funzionalità di snapshot incrementale per copiare solo file nuovi o aggiornati. La funzionalità di snapshot incrementale si basa sull'ora dell'ultima modifica dei file. 

I file esistenti con lo stesso nome vengono sovrascritti durante uno snapshot. Un file eliminato dall'origine non viene eliminato nella destinazione. Le sottocartelle vuote nell'origine non vengono copiate nella destinazione. 

## <a name="share-data"></a>Condividere dati

Usare le informazioni nelle sezioni seguenti per condividere i dati usando Condivisione dati di Azure. 
### <a name="prerequisites-to-share-data"></a>Prerequisiti per la condivisione dei dati

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Trovare l'indirizzo di posta elettronica di accesso di Azure del destinatario. L'alias di posta elettronica del destinatario non funzionerà per gli scopi dell'utente.
* Se l'archivio dati di Azure di origine si trova in una sottoscrizione di Azure diversa da quella in cui si creerà la risorsa Condivisione dati, registrare il provider di risorse [Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) nella sottoscrizione in cui si trova l'archivio dati di Azure. 

### <a name="prerequisites-for-the-source-storage-account"></a>Prerequisiti per l'account di archiviazione di origine

* Un account dell'Archiviazione di Azure. Se non si ha già un account, [crearne uno.](../storage/common/storage-account-create.md)
* Autorizzazione per scrivere nell'account di archiviazione. L'autorizzazione di scrittura si trova in *Microsoft.Storage/storageAccounts/write*. Fa parte del ruolo Collaboratore.
* Autorizzazione per aggiungere l'assegnazione di ruolo all'account di archiviazione. Questa autorizzazione si trova in *Microsoft.Authorization/role assignments/write*. Fa parte del ruolo Proprietario. 

### <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Creare un account di condivisione dati

Creare una risorsa di condivisione dati di Azure in un gruppo di risorse di Azure.

1. Nell'angolo superiore sinistro del portale aprire il menu e quindi selezionare **Crea una risorsa** (+).

1. Cercare *Condivisione dati*.

1. Selezionare **Condivisione dati e** **Crea**.

1. Specificare i dettagli di base della risorsa Condivisione dati di Azure seguente: 

     **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Subscription | Sottoscrizione in uso | Selezionare una sottoscrizione di Azure per l'account di condivisione dati.|
    | Gruppo di risorse | *test-resource-group* | Usare un gruppo di risorse esistente o crearne uno. |
    | Posizione | *Stati Uniti orientali 2* | Selezionare un'area per l'account di condivisione dati.
    | Nome | *datashareaccount* | Assegnare un nome all'account di condivisione dati. |
    | | |

1. Selezionare **Rivedi e crea per**  >  **effettuare** il provisioning dell'account di condivisione dati. Il provisioning di un nuovo account di condivisione dati richiede in genere circa 2 minuti. 

1. Al termine della distribuzione, selezionare **Vai alla risorsa**.

### <a name="create-a-share"></a>Creare una condivisione

1. Passare alla pagina Panoramica **della** condivisione dati.

   :::image type="content" source="./media/share-receive-data.png" alt-text="Screenshot che mostra la panoramica della condivisione dati.":::

1. Selezionare **Start sharing your data** (Inizia a condividere i dati).

1. Selezionare **Crea**.   

1. Specificare i dettagli per la condivisione. Specificare un nome, un tipo di condivisione, una descrizione del contenuto della condivisione e le condizioni per l'utilizzo (facoltativo). 

    ![Screenshot che mostra i dettagli della condivisione dati.](./media/enter-share-details.png "Immettere i dettagli della condivisione dati.") 

1. Selezionare **Continua**.

1. Per aggiungere set di dati alla condivisione, selezionare **Aggiungi set di dati**. 

    ![Screenshot che mostra come aggiungere set di dati alla condivisione.](./media/datasets.png "Set di dati.")

1. Selezionare un tipo di set di dati da aggiungere. L'elenco dei tipi di set di dati varia a seconda che nel passaggio precedente sia stata selezionata la condivisione basata su snapshot o la condivisione sul posto. 

    ![Screenshot che mostra dove selezionare un tipo di set di dati.](./media/add-datasets.png "Aggiungere set di dati.")    

1. Passare all'oggetto che si vuole condividere. Selezionare quindi **Aggiungi set di dati.** 

    ![Screenshot che mostra come selezionare un oggetto da condividere.](./media/select-datasets.png "Selezionare set di dati.")    

1. Nella scheda **Destinatari** aggiungere l'indirizzo di posta elettronica del consumer di dati selezionando **Aggiungi destinatario.** 

    ![Screenshot che mostra come aggiungere indirizzi di posta elettronica dei destinatari.](./media/add-recipient.png "Aggiungere i destinatari.") 

1. Selezionare **Continua**.

1. Se è stato selezionato un tipo di condivisione snapshot, è possibile configurare la pianificazione dello snapshot per aggiornare i dati per il consumer di dati. 

    ![Screenshot che mostra le impostazioni di pianificazione degli snapshot.](./media/enable-snapshots.png "Abilitare gli snapshot.") 

1. Selezionare un'ora di inizio e un intervallo di ricorrenza. 

1. Selezionare **Continua**.

1. Nella scheda **Rivedi e crea** esaminare il contenuto del pacchetto, le impostazioni, i destinatari e le impostazioni di sincronizzazione. Quindi selezionare **Crea**

A questo punto è stata creata la condivisione dati di Azure. Il destinatario della condivisione dati può accettare l'invito. 

## <a name="receive-data"></a>Ricevere dati

Le sezioni seguenti descrivono come ricevere dati condivisi.
### <a name="prerequisites-to-receive-data"></a>Prerequisiti per la ricezione dei dati
Prima di accettare un invito alla condivisione dati, assicurarsi di avere i prerequisiti seguenti: 

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, creare un [account gratuito.](https://azure.microsoft.com/free/)
* Un invito da Azure. L'oggetto del messaggio di posta elettronica deve essere "Condivisione dati di Azure'invito da *\<yourdataprovider\@domain.com>* ".
* Provider di [risorse Microsoft.DataShare registrato](concepts-roles-permissions.md#resource-provider-registration) in:
    * Sottoscrizione di Azure in cui si creerà una risorsa Condivisione dati.
    * Sottoscrizione di Azure in cui si trovano gli archivi dati di Azure di destinazione.

### <a name="prerequisites-for-a-target-storage-account"></a>Prerequisiti per un account di archiviazione di destinazione

* Un account dell'Archiviazione di Azure. Se non è già presente, creare [un account](../storage/common/storage-account-create.md). 
* Autorizzazione per scrivere nell'account di archiviazione. Questa autorizzazione si trova in *Microsoft.Storage/storageAccounts/write.* Fa parte del ruolo Collaboratore. 
* Autorizzazione per aggiungere l'assegnazione di ruolo all'account di archiviazione. Questa assegnazione si trova in *Microsoft.Authorization/role assignments/write*. Fa parte del ruolo Proprietario.  

### <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

### <a name="open-an-invitation"></a>Aprire un invito

È possibile aprire un invito tramite posta elettronica o direttamente dal portale di Azure.

1. Per aprire un invito tramite posta elettronica, controllare la posta in arrivo per un invito dal provider di dati. L'invito da Microsoft Azure è intitolato "Condivisione dati di Azure invito da *\<yourdataprovider\@domain.com>* ". Selezionare **Visualizza invito** per visualizzare l'invito in Azure. 

   Per aprire un invito dal portale di Azure, cercare *Inviti alla condivisione dati*. Viene visualizzato un elenco di inviti alla condivisione dati.

   ![Screenshot che mostra l'elenco di inviti nel portale di Azure.](./media/invitations.png "Elenco di inviti.") 

1. Selezionare la condivisione da visualizzare. 

### <a name="accept-an-invitation"></a>Accettare un invito
1. Esaminare tutti i campi, incluso il **Condizioni per l'utilizzo**. Se si accettano le condizioni, selezionare la casella di controllo. 

   ![Screenshot che mostra l'area Condizioni per l'utilizzo dati.](./media/terms-of-use.png "Condizioni per l'utilizzo.") 

1. In **Account condivisione dati di destinazione** selezionare la sottoscrizione e il gruppo di risorse in cui si distribuirà la condivisione dati. Compilare i campi seguenti:

   * Nel campo **Account di condivisione** dati selezionare Crea **nuovo** se non si ha un account di condivisione dati. In caso contrario, selezionare un account di condivisione dati esistente che accetterà la condivisione dati. 

   * Nel campo **Nome condivisione ricevuta** lasciare l'impostazione predefinita specificata dal provider di dati o specificare un nuovo nome per la condivisione ricevuta. 

1. Selezionare **Accetta e configura**. Viene creata una sottoscrizione di condivisione. 

   ![Screenshot che mostra dove accettare le opzioni di configurazione.](./media/accept-options.png "Opzioni di accettazione") 

    La condivisione ricevuta viene visualizzata nell'account di Condivisione dati. 

    Se non si vuole accettare l'invito, selezionare **Rifiuta.** 

### <a name="configure-a-received-share"></a>Configurare una condivisione ricevuta
Seguire i passaggi descritti in questa sezione per configurare un percorso per la ricezione dei dati.

1. Nella scheda **Set di dati** selezionare la casella di controllo accanto al set di dati a cui si vuole assegnare una destinazione. Selezionare **Mappa alla destinazione per** scegliere un archivio dati di destinazione. 

   ![Screenshot che mostra come eseguire il mapping a una destinazione.](./media/dataset-map-target.png "Eseguire il mapping alla destinazione.") 

1. Selezionare un archivio dati di destinazione per i dati. I file nell'archivio dati di destinazione che hanno lo stesso percorso e lo stesso nome dei file nei dati ricevuti verranno sovrascritti. 

   ![Screenshot che mostra dove selezionare un account di archiviazione di destinazione.](./media/map-target.png "Archiviazione di destinazione.") 

1. Per la condivisione basata su snapshot, se il provider di dati usa una pianificazione snapshot per aggiornare regolarmente i dati, è possibile abilitare la pianificazione dalla **scheda Pianificazione** snapshot. Selezionare la casella accanto alla pianificazione dello snapshot. Selezionare quindi **Abilita**. Si noti che il primo snapshot pianificato inizierà entro un minuto dall'ora di pianificazione e gli snapshot successivi inizieranno entro pochi secondi dall'ora pianificata.

   ![Screenshot che mostra come abilitare una pianificazione dello snapshot.](./media/enable-snapshot-schedule.png "Abilitare la pianificazione degli snapshot.")

### <a name="trigger-a-snapshot"></a>Attivazione di uno snapshot
I passaggi descritti in questa sezione si applicano solo alla condivisione basata su snapshot.

1. È possibile attivare uno snapshot dalla **scheda** Dettagli. Nella scheda selezionare **Trigger snapshot**. È possibile scegliere di attivare uno snapshot completo o incrementale dei dati. Se si ricevono dati dal provider di dati per la prima volta, selezionare **Copia completa.** Quando è in esecuzione uno snapshot, gli snapshot successivi non verranno avviati fino al completamento di quello precedente.

   ![Screenshot che mostra la selezione dello snapshot del trigger.](./media/trigger-snapshot.png "Snapshot del trigger.") 

1. Quando lo stato dell'ultima esecuzione *ha esito* positivo, passare all'archivio dati di destinazione per visualizzare i dati ricevuti. Selezionare **Set di dati** e quindi selezionare il collegamento al percorso di destinazione. 

   ![Screenshot che mostra il mapping di un set di dati consumer.](./media/consumer-datasets.png "Mapping del set di dati del consumer.") 

### <a name="view-history"></a>Visualizzare la cronologia
È possibile visualizzare la cronologia degli snapshot solo nella condivisione basata su snapshot. Per visualizzare la cronologia, aprire la **scheda** Cronologia. Qui viene visualizzata la cronologia di tutti gli snapshot generati negli ultimi 30 giorni. 

## <a name="storage-snapshot-performance"></a>Prestazioni dello snapshot di archiviazione
Le prestazioni dello snapshot di archiviazione sono influenzate da diversi fattori, oltre al numero di file e alle dimensioni dei dati condivisi. È sempre consigliabile eseguire test delle prestazioni personalizzati. Di seguito sono riportati alcuni fattori di esempio che influiscono sulle prestazioni.

* Accesso simultaneo agli archivi dati di origine e di destinazione.  
* Percorso degli archivi dati di origine e di destinazione. 
* Per gli snapshot incrementali, il numero di file nel set di dati condiviso può influire sul tempo necessario per trovare l'elenco di file con ora dell'ultima modifica dopo l'ultimo snapshot riuscito. 


## <a name="next-steps"></a>Passaggi successivi
Si è appreso come condividere e ricevere dati da un account di archiviazione usando il Condivisione dati di Azure archiviazione. Per informazioni sulla condivisione da altre origini dati, vedere [Archivi dati supportati.](supported-data-stores.md)
