---
title: Configurare Azure Cosmos DB account con backup periodico
description: Questo articolo descrive come configurare gli account Azure Cosmos DB con backup periodico con intervallo di backup. e conservazione. Viene inoltre illustrato come contattare il supporto per ripristinare i dati.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: d0470759a589927b65462f258b20446af608175c
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284039"
---
# <a name="configure-azure-cosmos-db-account-with-periodic-backup"></a>Configurare Azure Cosmos DB account con backup periodico
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB esegue automaticamente il backup dei dati a intervalli regolari. I backup automatici vengono eseguiti senza impatto sulle prestazioni o sulla disponibilità delle operazioni del database. Tutti i backup sono archiviati separatamente in un servizio di archiviazione e replicati a livello globale per garantire la resilienza in caso di emergenze a livello di area. Con Azure Cosmos DB, non solo i dati ma anche i relativi backup sono altamente ridondanti e resilienti in caso di emergenze a livello di area. Nei passaggi seguenti viene illustrato il modo in cui Azure Cosmos DB esegue il backup dei dati:

* Azure Cosmos DB esegue automaticamente un backup completo del database ogni 4 ore e in qualsiasi momento, per impostazione predefinita vengono archiviati solo i due backup più recenti. Se gli intervalli predefiniti non sono sufficienti per i carichi di lavoro, è possibile modificare l'intervallo di backup e il periodo di memorizzazione dal portale di Azure. È possibile modificare la configurazione di backup durante o dopo la creazione dell'account Azure Cosmos. Se il contenitore o il database viene eliminato, Azure Cosmos DB conserva gli snapshot esistenti di un determinato contenitore o database per 30 giorni.

* Azure Cosmos DB archivia questi backup nell'archiviazione BLOB di Azure, mentre i dati effettivi si trovano localmente all'interno Azure Cosmos DB.

* Per garantire una bassa latenza, lo snapshot del backup viene archiviato nell'archivio BLOB di Azure nella stessa area dell'area di scrittura corrente o in **una** delle aree di scrittura, nel caso in cui si disponga di una configurazione di scrittura in più aree. Per garantire la resilienza in caso di emergenze a livello di area, ogni snapshot dei dati di backup nell'archivio BLOB di Azure viene a sua volta replicato in un'altra area tramite l'archiviazione con ridondanza geografica. L'area in cui viene replicato il backup dipende dall'area di origine e dalla coppia di aree associata all'area di origine. Per altre informazioni, vedere l'[elenco di coppie di aree di Azure con ridondanza geografica](../best-practices-availability-paired-regions.md). Non è possibile accedere direttamente a questo backup. Il team di Azure Cosmos DB ripristinerà il backup quando viene richiesto attraverso una richiesta di supporto.

  L'immagine seguente mostra come viene eseguito il backup di un contenitore di Azure Cosmos con tutte le tre partizioni fisiche primarie nell'area Stati Uniti occidentali in un account di Archiviazione BLOB di Azure remoto negli Stati Uniti occidentali e quindi replicato negli Stati Uniti orientali:

  :::image type="content" source="./media/configure-periodic-backup-restore/automatic-backup.png" alt-text="Backup completi periodici di tutte le entità Cosmos DB nell'archiviazione GRS di Azure." lightbox="./media/configure-periodic-backup-restore/automatic-backup.png" border="false":::

* I backup vengono eseguiti senza effetti sulle prestazioni o la disponibilità dell'applicazione. Azure Cosmos DB esegue il backup dei dati in background senza richiedere la velocità effettiva (UR) con provisioning supplementare o influire sulle prestazioni e sulla disponibilità del database.

## <a name="backup-storage-redundancy"></a><a id="backup-storage-redundancy"></a>Ridondanza dell'archiviazione di backup

Per impostazione predefinita, Azure Cosmos DB archivia i dati di backup in modalità periodica nell' [Archivio BLOB](../storage/common/storage-redundancy.md) con ridondanza geografica che viene replicato in un' [area associata](../best-practices-availability-paired-regions.md).  

Per assicurarsi che i dati di backup rimangano nella stessa area in cui viene effettuato il provisioning dell'account di Azure Cosmos DB, è possibile modificare l'archiviazione di backup con ridondanza geografica predefinita e configurare l'archiviazione con ridondanza locale o con ridondanza della zona. I meccanismi di ridondanza dell'archiviazione archiviano più copie dei backup, in modo che siano protette da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni di rete o interruzioni dell'alimentazione o gravi calamità naturali.

I dati di backup in Azure Cosmos DB vengono replicati tre volte nell'area primaria. È possibile configurare la ridondanza di archiviazione per la modalità di backup periodico al momento della creazione dell'account o aggiornarlo per un account esistente. È possibile utilizzare le seguenti tre opzioni di ridondanza dei dati in modalità di backup periodico:

* **Archiviazione di backup con ridondanza geografica:** Questa opzione consente di copiare i dati in modo asincrono nell'area abbinata.

* **Archiviazione di backup con ridondanza della zona:** Questa opzione consente di copiare i dati in modo asincrono in tre zone di disponibilità di Azure nell'area primaria.

* **Archiviazione di backup con ridondanza locale:** Questa opzione consente di copiare i dati in modo asincrono tre volte all'interno di una singola posizione fisica nell'area primaria.

> [!NOTE]
> L'archiviazione con ridondanza della zona è attualmente disponibile solo in [aree specifiche](high-availability.md#availability-zone-support). In base all'area selezionata; Questa opzione non sarà disponibile per gli account nuovi o esistenti.
>
> L'aggiornamento della ridondanza di archiviazione dei backup non avrà alcun effetto sui prezzi di archiviazione di backup.

## <a name="modify-the-backup-interval-and-retention-period"></a><a id="configure-backup-interval-retention"></a>Modificare l'intervallo di backup e il periodo di memorizzazione

Azure Cosmos DB esegue automaticamente un backup completo dei dati ogni 4 ore e in qualsiasi momento, vengono archiviati i due backup più recenti. Questa configurazione è l'opzione predefinita e viene offerta senza costi aggiuntivi. L'intervallo di backup e il periodo di conservazione predefiniti possono essere modificati durante o dopo la creazione dell'account Azure Cosmos. La configurazione del backup è impostata a livello di account Azure Cosmos e deve essere eseguita in ogni account. Dopo aver configurato le opzioni di backup per un account, questo viene applicato a tutti i contenitori all'interno dell'account. Al momento è possibile modificare le opzioni di backup solo dal portale di Azure.

Se i dati sono stati eliminati o danneggiati in modo accidentale, **prima di creare una richiesta di supporto per il ripristino dei dati, assicurarsi di aumentare la conservazione del backup per l'account per almeno sette giorni. È consigliabile aumentare la conservazione entro 8 ore da questo evento.** In questo modo, il team di Azure Cosmos DB avrà tempo a sufficienza per ripristinare l'account.

### <a name="modify-backup-options-for-an-existing-account"></a>Modificare le opzioni di backup per un account esistente

Per modificare le opzioni di backup predefinite per un account Azure Cosmos esistente, attenersi alla procedura seguente:

1. Accedere al [portale di Azure.](https://portal.azure.com/)
1. Passare all'account Azure Cosmos e aprire il riquadro **Backup & Restore** . Aggiornare l'intervallo di backup e il periodo di conservazione dei backup in modo obbligatorio.

   * **Intervallo di backup** : si tratta dell'intervallo in cui Azure Cosmos DB tenta di eseguire un backup dei dati. Il backup richiede un periodo di tempo diverso da zero e in alcuni casi potrebbe avere esito negativo a causa di dipendenze downstream. Azure Cosmos DB tenta di eseguire un backup nell'intervallo configurato, tuttavia, non garantisce che il backup venga completato entro tale intervallo di tempo. È possibile configurare questo valore in ore o minuti. L'intervallo di backup non può essere minore di 1 ora e maggiore di 24 ore. Quando si modifica questo intervallo, il nuovo intervallo diventa effettivo a partire dall'ora in cui è stato effettuato l'ultimo backup.

   * **Conservazione dei backup** : rappresenta il periodo di conservazione di ogni backup. È possibile configurarlo in ore o giorni. Il periodo di memorizzazione minimo non può essere minore di due volte l'intervallo di backup (in ore) e non può essere maggiore di 720 ore.

   * **Copie dei dati conservati** . per impostazione predefinita, vengono offerte gratuitamente due copie di backup dei dati. Se sono necessarie più di due copie, è previsto un addebito aggiuntivo. Per conoscere il prezzo esatto per le copie aggiuntive, vedere la sezione archiviazione utilizzata nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) .

   * **Ridondanza dell'archiviazione di backup** : selezionare l'opzione di ridondanza di archiviazione necessaria. per le opzioni disponibili, vedere la sezione [ridondanza](#backup-storage-redundancy) di archiviazione di backup. Per impostazione predefinita, gli account di modalità di backup periodici esistenti hanno archiviazione con ridondanza geografica. È possibile scegliere altre opzioni di archiviazione, ad esempio con ridondanza locale, per assicurarsi che il backup non venga replicato in un'altra area. Le modifiche apportate a un account esistente vengono applicate solo ai backup futuri. Dopo l'aggiornamento della ridondanza di archiviazione di backup di un account esistente, potrebbe essere necessario attendere fino al doppio del tempo necessario per rendere effettive le modifiche e **si perderà l'accesso per ripristinare immediatamente i backup precedenti.**

   > [!NOTE]
   > Per configurare la ridondanza dell'archiviazione di backup, è necessario che il ruolo [ruolo lettore account Azure Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role) sia assegnato a livello di sottoscrizione.

   :::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-options-existing-accounts.png" alt-text="Configurare l'intervallo di backup, la conservazione e la ridondanza di archiviazione per un account Azure Cosmos esistente." border="true":::

### <a name="modify-backup-options-for-a-new-account"></a>Modificare le opzioni di backup per un nuovo account

Quando si esegue il provisioning di un nuovo account, nella scheda **criteri di backup** selezionare **periodico** _ criteri di backup. Il criterio periodico consente di configurare l'intervallo di backup, la conservazione dei backup e la ridondanza dell'archiviazione di backup. È ad esempio possibile scegliere le opzioni di archiviazione di *backup con ridondanza locale** o di **archiviazione di backup con ridondanza della zona** per impedire la replica dei dati di backup all'esterno dell'area

:::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-options-new-accounts.png" alt-text="Configurare criteri di backup periodici o continui per i nuovi account Azure Cosmos." border="true":::

## <a name="request-data-restore-from-a-backup"></a><a id="request-restore"></a>Richiedere il ripristino dei dati da un backup

Se si elimina accidentalmente il database o un contenitore, è possibile [archiviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) o [chiamare il supporto di Azure](https://azure.microsoft.com/support/options/) per ripristinare i dati dai backup online automatici. Il supporto tecnico di Azure è disponibile solo per i piani selezionati, ad esempio **standard**, **Developer** e piani superiori a quelli previsti. Il supporto di Azure non è disponibile con il piano **Basic**. Per altre informazioni sui diversi piani di supporto, vedere la pagina [Piani di supporto per Azure](https://azure.microsoft.com/support/plans/).

Per ripristinare uno snapshot specifico del backup, Azure Cosmos DB richiede che i dati siano disponibili durante il ciclo di backup per tale snapshot.
Prima di richiedere un ripristino, è necessario avere a disposizione i dettagli seguenti:

* ID della sottoscrizione.

* A seconda del modo in cui i dati sono stati accidentalmente eliminati o modificati, prepararsi per avere a disposizione ulteriori informazioni. Si consiglia di procurarsi le informazioni richieste in anticipo, per ridurre al minimo lo scambio di comunicazioni che potrebbe essere deleterio nei casi in cui la tempestività è cruciale.

* Se viene eliminato l'intero account Azure Cosmos DB, è necessario specificare il nome dell'account eliminato. Se si crea un altro account con lo stesso nome dell'account eliminato, condividerlo con il team di supporto perché ciò permette di determinare l'account corretto da scegliere. È consigliabile archiviare ticket di supporto diversi per ogni account eliminato, perché riduce al minimo la confusione per lo stato di ripristino.

* Se uno o più database vengono eliminati, è necessario fornire l'account Azure Cosmos e i nomi dei database di Azure Cosmos e specificare se esiste un nuovo database con lo stesso nome.

* Se vengono eliminati uno o più contenitori, è necessario comunicare il nome dell'account Azure Cosmos, i nomi dei database e i nomi dei contenitori. Specificare inoltre se esiste un contenitore con lo stesso nome.

* Se i dati sono stati eliminati o danneggiati per errore, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/options/) entro 8 ore, in modo che il team di Azure Cosmos DB possa aiutare a ripristinare i dati dai backup. **Prima di creare una richiesta di supporto per il ripristino dei dati, assicurarsi di [aumentare la conservazione del backup](#configure-backup-interval-retention) per l'account per almeno sette giorni. È consigliabile aumentare la conservazione entro 8 ore da questo evento.** In questo modo il team di supporto di Azure Cosmos DB avrà tempo sufficiente per ripristinare l'account.

Oltre al nome dell'account Azure Cosmos, ai nomi dei database e ai nomi dei contenitori, è necessario specificare il punto nel tempo in cui è possibile ripristinare i dati. È importante essere il più precisi possibile per consentire a Microsoft di determinare i backup disponibili migliori nell'intervallo di tempo specificato. **È anche importante specificare l'ora in formato UTC.**

Lo screenshot seguente illustra come creare una richiesta di supporto per un contenitore (raccolta/grafo/tabella) per ripristinare i dati tramite il portale di Azure. Fornire altri dettagli, ad esempio il tipo di dati, lo scopo del ripristino, il momento in cui i dati sono stati eliminati per consentire la priorità della richiesta.

:::image type="content" source="./media/configure-periodic-backup-restore/backup-support-request-portal.png" alt-text="Creare una richiesta di supporto per il backup usando portale di Azure." border="true":::

## <a name="considerations-for-restoring-the-data-from-a-backup"></a>Considerazioni per il ripristino dei dati da un backup

I dati possono essere eliminati o modificati accidentalmente in uno dei seguenti scenari:  

* Eliminare l'intero account Azure Cosmos.

* Eliminare uno o più database di Azure Cosmos.

* Eliminare uno o più contenitori di Azure Cosmos.

* Eliminare o modificare gli elementi di Azure Cosmos, ad esempio i documenti, all'interno di un contenitore. Questo caso specifico viene in genere definito danneggiamento dei dati.

* Un database di offerta condivisa o contenitori all'interno di un database di offerta condivisa vengono eliminati o danneggiati.

Azure Cosmos DB può ripristinare i dati in tutti gli scenari precedenti. Quando si esegue il ripristino, viene creato un nuovo account Azure Cosmos per conservare i dati ripristinati. Il nome del nuovo account, se non è specificato, avrà il formato `<Azure_Cosmos_account_original_name>-restored1` . L'ultima cifra viene incrementata quando si tenta di eseguire più ripristini. Non è possibile ripristinare i dati in un account Azure Cosmos creato in precedenza.

Quando si elimina accidentalmente un account Azure Cosmos, è possibile ripristinare i dati in un nuovo account con lo stesso nome, se il nome dell'account non è in uso. Quindi, è consigliabile non ricreare l'account dopo averlo eliminato. Poiché non solo impedisce ai dati ripristinati di utilizzare lo stesso nome, ma rende anche più difficile l'individuazione dell'account corretto.

Quando si elimina accidentalmente un database di Azure Cosmos, è possibile ripristinare l'intero database o un subset dei contenitori all'interno del database. È anche possibile selezionare contenitori specifici tra database e ripristinarli in un nuovo account Azure Cosmos.

Quando si elimina o si modifica accidentalmente uno o più elementi all'interno di un contenitore (caso di danneggiamento dei dati), è necessario specificare l'ora in cui eseguire il ripristino. Il tempo è importante in caso di danneggiamento dei dati. Poiché il contenitore è attivo, il backup è ancora in esecuzione, pertanto se si attende il superamento del periodo di memorizzazione (il valore predefinito è otto ore), i backup verranno sovrascritti. Per evitare la sovrascrittura del backup, aumentare la conservazione del backup per l'account per almeno sette giorni. È consigliabile aumentare la conservazione entro 8 ore dal danneggiamento dei dati.

Se i dati sono stati eliminati o danneggiati per errore, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/options/) entro 8 ore, in modo che il team di Azure Cosmos DB possa aiutare a ripristinare i dati dai backup. In questo modo il team di supporto di Azure Cosmos DB avrà tempo sufficiente per ripristinare l'account.

> [!NOTE]
> Dopo aver ripristinato i dati, non tutte le funzionalità o le impostazioni di origine vengono trasferite nell'account ripristinato. Le impostazioni seguenti non vengono trasferite al nuovo account:
> * Elenchi di controllo di accesso di VNET
> * Stored procedure, trigger e funzioni definite dall'utente
> * Impostazioni per più aree  

Se si effettua il provisioning della velocità effettiva a livello di database, in questo caso il processo di backup e ripristino viene eseguito a livello dell'intero database e non a livello dei singoli contenitori. In questi casi, non è possibile selezionare un subset di contenitori da ripristinare.

## <a name="required-permissions-to-change-retention-or-restore-from-the-portal"></a>Autorizzazioni necessarie per modificare la conservazione o il ripristino dal portale
Le entità che fanno parte del ruolo [CosmosdbBackupOperator](../role-based-access-control/built-in-roles.md#cosmosbackupoperator), proprietario o collaboratore possono richiedere un ripristino o modificare il periodo di memorizzazione.

## <a name="understanding-costs-of-extra-backups"></a>Informazioni sui costi dei backup aggiuntivi
Sono disponibili due backup gratuiti e i backup aggiuntivi vengono addebitati in base ai prezzi basati sull'area per l'archiviazione di backup descritti in [prezzi di archiviazione di backup](https://azure.microsoft.com/pricing/details/cosmos-db/). Ad esempio, se la conservazione dei backup è configurata su 240 ore, ovvero 10 giorni e intervallo di backup a 24 ore. Questo implica 10 copie dei dati di backup. Supponendo 1 TB di dati negli Stati Uniti occidentali 2, il costo sarà di 0,12 * 1000 * 8 per l'archiviazione di backup in un determinato mese.

## <a name="options-to-manage-your-own-backups"></a>Opzioni per la gestione dei backup

Con gli account API SQL di Azure Cosmos DB è anche possibile gestire backup propri adottando uno degli approcci seguenti:

* Usare [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) per spostare periodicamente i dati in una risorsa di archiviazione di propria scelta.

* Usare Azure Cosmos DB [feed di modifiche](change-feed.md) per leggere i dati periodicamente per i backup completi o per le modifiche incrementali e archiviarli nella propria risorsa di archiviazione.

## <a name="post-restore-actions"></a>Azioni successive al ripristino

L'obiettivo principale del ripristino dei dati è ripristinare i dati eliminati o modificati accidentalmente. Pertanto, è consigliabile esaminare prima di tutto il contenuto dei dati ripristinati per assicurarsi che siano quelli previsti. Se tutto sembra corretto, è possibile eseguire la migrazione dei dati all'account primario. Sebbene sia possibile usare l'account ripristinato come nuovo account attivo, non è un'opzione consigliata se si dispone di carichi di lavoro di produzione. 

Dopo aver ripristinato i dati, si riceverà una notifica riguardante il nome del nuovo account (in genere nel formato `<original-name>-restored1`) e l'ora del ripristino dell'account. L'account ripristinato avrà la stessa velocità effettiva di cui è stato effettuato il provisioning, gli stessi criteri di indicizzazione e sarà incluso nella stessa area dell'account originale. Un utente coamministratore o amministratore della sottoscrizione può visualizzare l'account ripristinato.

### <a name="migrate-data-to-the-original-account"></a>Migrare i dati all'account originale

Di seguito sono riportati diversi modi per eseguire la migrazione dei dati all'account originale:

* Usare l' [utilità di migrazione dati Azure Cosmos DB](import-data.md).
* Usare il [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).
* Usare il [feed delle modifiche](change-feed.md) in Azure Cosmos DB.
* È possibile scrivere codice personalizzato.

Si consiglia di eliminare il contenitore o il database immediatamente dopo la migrazione dei dati. Se non si eliminano i database o i contenitori ripristinati, questi elementi genereranno costi per unità richieste, archiviazione e dati in uscita.

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire una richiesta di ripristino, contattare il supporto tecnico di Azure per [archiviare un ticket dal portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Configurare e gestire il backup continuo con [portale di Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)o [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gestire le autorizzazioni](continuous-backup-restore-permissions.md) necessarie per ripristinare i dati con la modalità di backup continuo.
