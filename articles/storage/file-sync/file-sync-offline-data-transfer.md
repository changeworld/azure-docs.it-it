---
title: Eseguire la migrazione dei dati Sincronizzazione file di Azure con Azure Data Box
description: Eseguire la migrazione offline dei dati in blocco compatibili con Sincronizzazione file di Azure. Evitare conflitti di file e mantenere gli ACL e i timestamp di file e cartelle dopo aver abilitato la sincronizzazione.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 453a6f9c83b992df62681f366fcf95a77cda9f25
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796844"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Eseguire la migrazione di dati in blocco a Sincronizzazione file di Azure con Azure DataBox
È possibile eseguire la migrazione dei dati in blocco Sincronizzazione file di Azure in due modi:

* **Caricare i file usando Sincronizzazione file di Azure.** Questo è il metodo più semplice. Spostare i file in locale in Windows Server 2012 R2 o versione successiva e installare l Sincronizzazione file di Azure agente. Dopo aver configurato la sincronizzazione, i file verranno caricati dal server. I clienti attualmente hanno una velocità di caricamento media di 1 TiB circa ogni due giorni. Per assicurarsi che il server non usi una quantità troppo elevata di larghezza di banda per il data center, è possibile configurare una pianificazione della limitazione [della larghezza di banda.](file-sync-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)
* **Trasferire i file offline.** Se la larghezza di banda disponibile non è sufficiente, potrebbe non essere possibile caricare i file in Azure in un intervallo di tempo ragionevole. La sfida è la sincronizzazione iniziale dell'intero set di file. Per risolvere questo problema, usare gli strumenti di migrazione in blocco offline, ad esempio [Azure Data Box famiglia](https://azure.microsoft.com/services/storage/databox). 

Questo articolo illustra come eseguire la migrazione offline dei file in modo compatibile con Sincronizzazione file di Azure. Seguire queste istruzioni per evitare conflitti di file e mantenere gli elenchi di controllo di accesso (ACL) e i timestamp di file e cartelle dopo aver abilitato la sincronizzazione.

## <a name="migration-tools"></a>Strumenti di migrazione
Il processo descritto in questo articolo funziona non solo per Data Box ma anche per altri strumenti di migrazione offline. Funziona anche per strumenti come AzCopy, Robocopy o strumenti e servizi partner che funzionano direttamente su Internet. Tuttavia, per superare la sfida di caricamento iniziale, seguire la procedura descritta in questo articolo per usare questi strumenti in modo compatibile con Sincronizzazione file di Azure.

In alcuni casi, è necessario passare da un'istanza di Windows Server a un'altra prima di adottare Sincronizzazione file di Azure. [Il Servizio Migrazione del servizio](/windows-server/storage/storage-migration-service/overview) di archiviazione (SMS) può essere utile. Sia che sia necessario eseguire la migrazione a una versione del sistema operativo server supportata da Sincronizzazione file di Azure (Windows Server 2012R2 e versioni successiva) o semplicemente perché si acquista un nuovo sistema per Sincronizzazione file di Azure, SMS offre numerose funzionalità e vantaggi che consentono di eseguire la migrazione senza problemi.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Vantaggi dell'uso di uno strumento per trasferire i dati offline
Ecco i principali vantaggi dell'uso di uno strumento di trasferimento come Data Box per la migrazione offline:

- Non è necessario caricare tutti i file in rete. Per spazi dei nomi di grandi dimensioni, questo strumento potrebbe risparmiare tempo e larghezza di banda di rete significativi.
- Quando si usa Sincronizzazione file di Azure, indipendentemente da quale strumento di trasferimento si usa (Data Box, servizio Importazione/Esportazione di Azure e così via), il server live carica solo i file che cambiano dopo lo spostamento dei dati in Azure.
- Sincronizzazione file di Azure sincronizza gli ACL di file e cartelle anche se lo strumento di migrazione in blocco offline non trasporta gli ACL.
- Data Box e Sincronizzazione file di Azure non richiedono tempi di inattività. Quando si usa Data Box per trasferire i dati in Azure, si usa la larghezza di banda di rete in modo efficiente e si mantiene la fedeltà dei file. È anche possibile mantenere aggiornato lo spazio dei nomi caricando solo i file che cambiano dopo lo spostamento dei dati in Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Prerequisiti per il trasferimento dei dati offline
Non abilitare la sincronizzazione nel server di cui si esegue la migrazione prima di completare il trasferimento dei dati offline. Altri aspetti da considerare prima di iniziare sono i seguenti:

- Se si prevede di usare Data Box per la migrazione in blocco: esaminare i prerequisiti di distribuzione [per Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Pianificare la topologia di Sincronizzazione file di Azure finale: [pianificare una Sincronizzazione file di Azure distribuzione](file-sync-planning.md)
- Selezionare gli account di archiviazione di Azure che conterranno le condivisioni file con cui si vuole eseguire la sincronizzazione. Assicurarsi che la migrazione in blocco venga eseguita in condivisioni di staging temporanee negli stessi account di archiviazione. La migrazione in blocco può essere abilitata solo usando una condivisione finale e una condivisione di staging che si trovano nello stesso account di archiviazione.
- Una migrazione in blocco può essere usata solo quando si crea una nuova relazione di sincronizzazione con un percorso server. Non è possibile abilitare una migrazione in blocco con una relazione di sincronizzazione esistente.


## <a name="process-for-offline-data-transfer"></a>Processo per il trasferimento dei dati offline
Ecco come configurare Sincronizzazione file di Azure in modo compatibile con gli strumenti di migrazione in blocco, ad esempio Azure Data Box:

![Diagramma che illustra come configurare Sincronizzazione file di Azure](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Passaggio | Dettaglio |
|---|---------------------------------------------------------------------------------------|
| ![Passaggio 1](media/storage-sync-files-offline-data-transfer/bullet-1.png) | [Ordinare il Data Box](../../databox/data-box-deploy-ordered.md). La Data Box offre [diversi prodotti per](https://azure.microsoft.com/services/storage/databox/data) soddisfare le proprie esigenze. Quando si riceve il Data Box, [](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) seguire la relativa documentazione per copiare i dati in questo percorso UNC Data Box: *\\<\> \<StorageAccountName_AzFile\> \<ShareName\> DeviceIPAddres*. In questo *caso, ShareName* è il nome della condivisione di staging. Inviare il Data Box ad Azure. |
| ![Passaggio 2](media/storage-sync-files-offline-data-transfer/bullet-2.png) | Attendere fino a quando i file non vengono visualizzati nelle condivisioni file di Azure scelte come condivisioni di staging temporanee. *Non abilitare la sincronizzazione con queste condivisioni.* |
| ![Passaggio 3](media/storage-sync-files-offline-data-transfer/bullet-3.png) | <ul><li>Creare una nuova condivisione vuota per ogni condivisione file Data Box creata automaticamente. Questa nuova condivisione deve essere nello stesso account di archiviazione della Data Box condivisa. [Come creare una nuova condivisione file di Azure](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).</li><li>[Creare un gruppo di sincronizzazione](file-sync-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) in un servizio di sincronizzazione archiviazione. Fare riferimento alla condivisione vuota come endpoint cloud. Ripetere questo passaggio per ogni condivisione file del Data Box. [Configurare Sincronizzazione file di Azure](file-sync-deployment-guide.md).</li></ul> |
| ![Passaggio 4](media/storage-sync-files-offline-data-transfer/bullet-4.png) | [Aggiungere la directory del server live come endpoint server](file-sync-deployment-guide.md#create-a-server-endpoint). Nel processo specificare che i file sono stati spostati in Azure e fare riferimento alle condivisioni di staging. È possibile abilitare o disabilitare il cloud a livelli in base alle esigenze. Durante la creazione di un endpoint server nel server live, fare riferimento alla condivisione di staging. Nel pannello **Aggiungi endpoint server,** in Trasferimento dati **offline,** selezionare Abilitato **e** quindi selezionare la condivisione di gestione temporanea che deve essere nello stesso account di archiviazione dell'endpoint cloud. In questo caso, l'elenco delle condivisioni disponibili viene filtrato in base all'account di archiviazione e alle condivisioni che non sono già sincronizzate. Lo screenshot che segue questa tabella mostra come fare riferimento alla condivisione DataBox durante la creazione dell'endpoint server nel portale di Azure. |
| ![Passaggio 5](media/storage-sync-files-offline-data-transfer/bullet-5.png) | Dopo aver aggiunto l'endpoint server nel passaggio precedente, i dati iniziano automaticamente a fluire dall'origine destra. La [sezione Sincronizzazione della condivisione](#syncing-the-share) illustra quando i dati passano dalla condivisione DataBox o da Windows Server |
| |

![Screenshot dell'interfaccia portale di Azure utente, che mostra come abilitare il trasferimento dei dati offline durante la creazione di un nuovo endpoint server](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Sincronizzazione della condivisione
Dopo aver creato l'endpoint server, verrà avviata la sincronizzazione. Il processo di sincronizzazione determina se ogni file nel server esiste anche nella condivisione di gestione temporanea in cui Data Box i file. Se il file esiste, il processo di sincronizzazione copia il file dalla condivisione di staging anziché caricarlo dal server. Se il file non esiste nella condivisione di staging o se è disponibile una versione più recente nel server locale, il processo di sincronizzazione carica il file dal server locale.

Quando si sincronizza la condivisione, la sincronizzazione unirà tutti gli attributi di file mancanti, le autorizzazioni o i timestamp dalle varianti di file nel server locale, combinandole con le relative controparti di file dalla condivisione DataBox. Ciò garantisce che ogni file e cartella arrivi con tutte le possibili fedeltà dei file nella condivisione file di Azure.

> [!IMPORTANT]
> È possibile abilitare la modalità di migrazione in blocco solo durante la creazione di un endpoint server. Dopo aver stabilito un endpoint server, non è possibile integrare i dati migrati in blocco da un server già sincronizzato nello spazio dei nomi .

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL e timestamp di file e cartelle
Sincronizzazione file di Azure che gli ACL di file e cartelle siano sincronizzati dal server live anche se lo strumento di migrazione in blocco usato inizialmente non ha trasportato gli ACL. Per questo scopo, la condivisione di staging non deve contenere elenchi di controllo di accesso per file e cartelle. Quando si abilita la funzionalità di migrazione dei dati offline durante la creazione di un nuovo endpoint server, tutti gli ACL dei file vengono sincronizzati nel server. Vengono sincronizzati anche i timestamp appena creati e modificati.

## <a name="shape-of-the-namespace"></a>Forma dello spazio dei nomi
Quando si abilita la sincronizzazione, il contenuto del server determina la forma dello spazio dei nomi. Se i file vengono eliminati dal server locale al termine dello snapshot Data Box e della migrazione, questi file non vengono spostati nello spazio dei nomi live e sincronizzato. Rimangono nella condivisione di staging, ma non vengono copiati. Questa operazione è necessaria perché la sincronizzazione mantiene lo spazio dei nomi in base al server live. Lo Data Box *snapshot è* solo una base di staging per una copia efficiente dei file. Non è l'autorità per la forma dello spazio dei nomi live.

## <a name="cleaning-up-after-bulk-migration"></a>Pulizia dopo la migrazione in blocco 
Quando il server completa la sincronizzazione iniziale dello spazio dei nomi, Data Box file migrati in blocco usano la condivisione file di staging. Nel pannello **Proprietà endpoint server** in portale di Azure, nella sezione Trasferimento dati **offline** lo stato cambia da **In corso** a **Completato.** 

![Screenshot del pannello Proprietà endpoint server, in cui si trovano i controlli di stato e disabilitazione per il trasferimento dei dati offline](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

A questo punto è possibile pulire la condivisione di staging per risparmiare sui costi:

1. Nel pannello **Proprietà endpoint server,** quando lo stato è **Completato,** selezionare **Disabilita trasferimento dati offline.**
2. È consigliabile eliminare la condivisione di staging per risparmiare sui costi. La condivisione di gestione temporanea probabilmente non contiene ACL di file e cartelle, quindi è improbabile che sia utile. Ai fini del backup tempormente, creare uno snapshot reale della condivisione file di [Azure sincronizzata.](../files/storage-snapshots-files.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) È possibile [configurare Backup di Azure creare snapshot in]( ../../backup/backup-afs.md) base a una pianificazione.

Disabilitare la modalità di trasferimento dei dati offline solo quando lo stato è **Completato** o quando si vuole annullare a causa di un errore di configurazione. Se si disabilita la modalità durante una distribuzione, i file inizieranno a essere caricati dal server anche se la condivisione di staging è ancora disponibile.

> [!IMPORTANT]
> Dopo aver disabilitato la modalità di trasferimento dei dati offline, non è possibile abilitarla nuovamente, anche se la condivisione di gestione temporanea dalla migrazione in blocco è ancora disponibile.

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>Sincronizzazione file di Azure e pre-seeded dei file nel cloud

Se è stato eseguito il seeded dei file in una condivisione file di Azure con altri mezzi rispetto a DataBox, ad esempio tramite AzCopy, RoboCopy da un backup cloud o qualsiasi altro metodo, è comunque necessario seguire il processo di trasferimento dei dati [offline](#process-for-offline-data-transfer) descritto in questo articolo. È necessario ignorare solo DataBox come metodo di spostamento dei file nel cloud. Tuttavia, è fondamentale assicurarsi di seguire ancora il processo di seeding dei file in una condivisione di *staging* e non nella condivisione finale Sincronizzazione file di Azure connessa.

> [!WARNING]
> **Seguire il processo di seeding dei file in una condivisione di staging** e non nell'ultimo Sincronizzazione file di Azure connessa. In caso contrario, possono verificarsi conflitti di file (verranno archiviate entrambe le versioni dei file) e i file eliminati nel server live possono tornare indietro, se sono ancora presenti nel set di file con seeded precedente. Inoltre, le modifiche alle cartelle verranno unite tra loro, rendendo molto difficile separare lo spazio dei nomi dopo tale errore.

## <a name="next-steps"></a>Passaggi successivi
- [Pianificare una distribuzione di Sincronizzazione file di Azure](file-sync-planning.md)
- [Distribuire Sincronizzazione file di Azure](file-sync-deployment-guide.md)