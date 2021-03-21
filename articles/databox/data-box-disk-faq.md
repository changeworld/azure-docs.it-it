---
title: Domande frequenti su Microsoft Azure Data Box Disk | Microsoft Docs
description: Contiene domande frequenti e risposte per Azure Data Box Disk, una soluzione cloud che consente di trasferire grandi quantità di dati in Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: alkohli
ms.custom: references_regions
ms.openlocfilehash: 88aedb7daa375ae6b4a9107dceed1d25ed72ed92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039105"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure Data Box Disk: Domande frequenti

La soluzione cloud Microsoft Azure Data Box Disk consente di inviare terabyte di dati ad Azure in modo veloce, conveniente e affidabile. Queste domande frequenti includono domande che possono sorgere durante l'uso dei dischi di Data Box nel portale di Azure e relative risposte. 

Le domande e le risposte sono suddivise nelle categorie seguenti:

- Informazioni sul servizio
- Configurazione e connessione 
- Monitoraggio dello stato
- Eseguire la migrazione dei dati 
- Verifica e caricamento dei dati 


## <a name="about-the-service"></a>Informazioni sul servizio

### <a name="q-what-is-azure-data-box-service"></a>Q. Che cos'è il servizio Azure Data Box? 
R.  Il servizio Azure Data Box è progettato per l'inserimento di dati offline. Il servizio gestisce una gamma di prodotti tutti progettati su misura per il trasporto di dati per capacità di archiviazione diverse. 

### <a name="q-what-are-azure-data-box-disks"></a>Q. Che cosa sono i dischi di Azure Data Box?
R. I dischi di Azure Data Box consentono il trasferimento rapido, conveniente e sicuro di terabyte di dati in e verso Azure. Microsoft fornisce da 1 a 5 dischi, con una capacità di archiviazione massima di 35 TB. È possibile configurare, connettere e sbloccare facilmente questi dischi tramite il servizio Data Box nel portale di Azure.  

I dischi vengono crittografati tramite Crittografia unità BitLocker Microsoft e le chiavi di crittografia vengono gestite nel portale di Azure. È quindi possibile copiare i dati dai server del cliente. Nel data center, Microsoft esegue la migrazione dei dati dall'unità al cloud usando un collegamento di caricamento di rete privato e veloce e li carica in Azure.

### <a name="q-when-should-i-use-data-box-disks"></a>Q. Quando è consigliabile usare i dischi di Data Box?
R. Quando si ha l'esigenza di trasferire 40 TB di dati (o meno) in Azure, l'uso dei dischi di Data Box può risultare vantaggioso.

### <a name="q-what-is-the-price-of-data-box-disks"></a>Q. Qual è il prezzo dei dischi di Data Box?
R. Per informazioni sul prezzo dei dischi di Data Box Disk, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/databox/disk/).

### <a name="q-how-do-i-get-data-box-disks"></a>Q. Come è possibile ottenere i dischi di Data Box? 
R.  Per ottenere gli Azure Data Box Disk, accedere al portale di Azure e creare un ordine di Data Box per i dischi. Fornire le informazioni di contatto e i dettagli per le notifiche. Quando si effettua un ordine, in base alla disponibilità, i dischi vengono spediti all'utente entro 10 giorni.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>Q. Qual è la quantità massima di dati che è possibile trasferire con i dischi di Data Box in una sola volta?
R. Per cinque dischi, ognuno con capacità di 8 TB (7 TB di capacità utilizzabile), la capacità massima utilizzabile è 35 TB. È quindi possibile trasferire 35 TB di dati in un'istanza. Per trasferire più dati, è necessario ordinare altri dischi.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>Q. Come è possibile verificare se i dischi di Data Box sono disponibili nella propria area? 
R.  Per visualizzare dove sono attualmente disponibili i dischi di Data Box, vedere le [Aree di disponibilità](data-box-disk-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>Q. In quali aree è possibile archiviare dati con i dischi di Data Box?
R. Data Box Disk è supportato per tutte le aree all'interno di Stati Uniti, Canada, Europa, Regno Unito, Australia, Singapore, India, Cina, Hong Kong, Giappone, Corea e Sudafrica. Sono supportate solo le aree del cloud pubblico di Azure. Non sono supportati i cloud Azure per enti pubblici o altri cloud sovrani.

### <a name="q-how-can-i-import-source-data-present-at-my-location-in-one-countryregion-to-an-azure-region-in-a-different-country"></a>Q. Come è possibile importare i dati di origine presenti nel percorso in un paese/area geografica in un'area di Azure in un paese diverso?
R. Data Box Disk supporta l'inserimento di dati solo all'interno dello stesso paese/area geografica della destinazione e non attraverserà i confini internazionali. L'unica eccezione riguarda gli ordini nell'Unione europea (UE), in cui i dischi Data Box possono essere distribuiti da e verso qualsiasi paese dell'Unione europea.

Se, ad esempio, si desidera spostare i dati nel proprio percorso in Canada in un account di archiviazione di Azure West US, è possibile ottenere il risultato seguente:

#### <a name="option-1"></a>Opzione 1: 

Spedire un [disco supportato](../import-export/storage-import-export-requirements.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#supported-disks) contenente i dati usando il [servizio di importazione/esportazione di Azure](../import-export/storage-import-export-service.md) dalla posizione di origine in Canada al Data Center degli Stati Uniti occidentali di Azure.

#### <a name="option-2"></a>Opzione 2:

1. Ordinare Data Box Disk in Canada scegliendo un account di archiviazione in Canada centrale. I dischi SSD vengono spediti dal Data Center di Azure in Canada centrale all'indirizzo di spedizione (in Canada) fornito durante la creazione dell'ordine.

2. Dopo aver copiato i dati dal server locale nei dischi, restituirli al Data Center di Azure in Canada usando le etichette restituite fornite da Microsoft. I dati presenti nel Data Box Disk vengono quindi caricati nell'account di archiviazione di destinazione nell'area di Azure del Canada selezionata durante la creazione dell'ordine.

3. È quindi possibile usare uno strumento come AzCopy per copiare i dati in un account di archiviazione negli Stati Uniti occidentali. Questo passaggio comporta costi di archiviazione e [larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/) [standard](https://azure.microsoft.com/pricing/details/storage/) che non sono inclusi nel data box disk la fatturazione.

#### <a name="q-does-data-box-disk-store-any-customer-data-outside-of-the-service-region"></a>Q. Data Box Disk archivia i dati dei clienti al di fuori dell'area del servizio?

R. No. Data Box Disk non archivia i dati del cliente all'esterno dell'area del servizio. Il cliente ha la proprietà completa dei propri dati e può salvare i dati in una posizione specificata in base all'account di archiviazione selezionato durante la creazione dell'ordine.  

Oltre ai dati dei clienti, sono disponibili Data Box Disk dati che includono i metadati e i log di monitoraggio. In tutte le aree, ad eccezione del Brasile meridionale e sud-orientale, Data Box Disk dati vengono archiviati e replicati in un' [area abbinata](../best-practices-availability-paired-regions.md) tramite un account di archiviazione con ridondanza geografica per proteggersi dalla perdita di dati.  

A causa [dei requisiti di residenza dei dati](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information) in Brasile meridionale e Asia sudorientale, i dati Data Box disk vengono archiviati in un account di archiviazione con ridondanza della zona (ZRS) in modo che sia contenuto in una singola area. Per l'Asia sudorientale, tutti i dati Data Box Disk vengono archiviati a Singapore e per il Brasile meridionale, i dati vengono archiviati in Brasile. 

Se si verifica un'interruzione del servizio in Brasile meridionale e Asia sudorientale, i clienti possono creare nuovi ordini da un'altra area. I nuovi ordini verranno serviti dall'area in cui vengono creati e i clienti saranno responsabili delle spedizioni da e verso il Data Box Disk.



### <a name="q-how-can-i-recover-my-data-if-an-entire-region-fails"></a>Q. Come è possibile ripristinare i dati in caso di errore di un'intera area?

R. In casi estremi in cui un'area viene persa a causa di un'emergenza significativa, Microsoft può avviare un failover a livello di area. In questo caso non è necessaria alcuna azione da parte dell'utente. L'ordine verrà evaso attraverso l'area di failover se si trova nello stesso paese o limite commerciale. Tuttavia, alcune aree di Azure non hanno un'area abbinata nello stesso confine geografico o commerciale. Se si verifica un'emergenza in una di queste aree, è necessario creare nuovamente l'ordine di Data Box da un'area diversa disponibile e copiare i dati in Azure nella nuova area. Per altre informazioni, vedere [Continuità aziendale e ripristino di emergenza (BCDR): aree geografiche abbinate di Azure](../best-practices-availability-paired-regions.md).

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>Q. A chi ci si può rivolgere in caso di problemi con i dischi di Data Box?
R. Se si verificano problemi con i dischi Data Box, [contattare supporto tecnico Microsoft](./data-box-disk-contact-microsoft-support.md).

## <a name="order-device"></a>Ordinare il dispositivo

### <a name="q-how-do-i-get-data-box-disk"></a>Q. Ricerca per categorie ottenere Data Box Disk? 
R.  Per ottenere Azure Data Box Disk, accedere al portale di Azure e creare un ordine di Data Box Disk. Fornire le informazioni di contatto e i dettagli per le notifiche. Una volta effettuato un ordine, in base alla disponibilità, Data Box Disk viene spedito all'utente entro 10 giorni. Per altre informazioni, vedere [Order a Data Box](data-box-disk-deploy-ordered.md) (Ordinare un Data Box).

### <a name="q-i-couldnt-create-a-data-box-disk-order-in-the-azure-portal-why"></a>Q. Non è stato possibile creare un ordine di Data Box Disk nel portale di Azure. Perché?
R. Se non è possibile creare un ordine di Data Box Disk, si verifica un problema con il tipo di sottoscrizione o l'accesso.

Controllare la sottoscrizione. Data Box Disk è disponibile solo per le offerte di sottoscrizione di Contratto Enterprise (EA) e Cloud Solution Provider (CSP). Se non si dispone di uno di questi tipi di sottoscrizione, contattare supporto tecnico Microsoft per aggiornare la sottoscrizione.

Se si dispone di un tipo di offerta supportato per la sottoscrizione, controllare il livello di accesso della sottoscrizione stessa. Per creare un ordine, è necessario essere un collaboratore o un proprietario della sottoscrizione.

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>Q. Quanto tempo è necessario per la creazione dell'ordine ai dati caricati in Azure?

R. I tempi di esecuzione stimati seguenti per ogni fase di elaborazione degli ordini forniranno un'idea di cosa aspettarsi.  

Questi lead time sono *stime*. Il tempo per ogni fase di elaborazione degli ordini è influenzato dal carico nel datacenter, dagli ordini simultanei e da altre condizioni ambientali.

**Tempo di esecuzione stimato per un ordine di Data Box Disk:**

1. Ordine Data Box Disk: pochi minuti dal portale
2. Allocazione e preparazione del disco: fino a 5 giorni lavorativi, a seconda della disponibilità dell'inventario e del numero di ordini in sospeso da elaborare
3. Spedizione: 2-3 giorni lavorativi
4. Copia dei dati nel sito del cliente: dipende dalla natura dei dati, dalle dimensioni e dal numero di file.
5. Effettuare il reso della spedizione: 2-3 giorni lavorativi
6. Elaborazione nel datacenter e caricamento in Azure: il caricamento dei dati inizia dal Data Center non appena viene completata l'elaborazione operativa e il disco è connesso. Il tempo di caricamento dipende dalla natura dei dati, dalle dimensioni e dal numero di file.

## <a name="configure-and-connect"></a>Configurazione e connessione
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>Q. È possibile specificare il numero di dischi di Data Box nell'ordine?
R.  No. Si ottengono dischi da 8 TB (un massimo di cinque dischi), a seconda delle dimensioni e della disponibilità dei dati dei dischi.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>Q. Come si sbloccano i dischi di Data Box? 
R.  Nel portale di Azure accedere all'ordine di dischi di Data Box e passare a **Dettagli dispositivo**. Copiare la passkey. Scaricare ed estrarre lo strumento di sblocco di Data Box Disk dal portale di Azure per il sistema operativo in uso. Eseguire lo strumento nel computer che contiene i dati che si vuole copiare nei dischi. Fornire la passkey per sbloccare i dischi. La stessa passkey sblocca tutti i dischi. 

Per istruzioni dettagliate, vedere [Sbloccare i dischi in un client Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) oppure [Sbloccare i dischi in un client Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>Q. È possibile usare un computer host Linux per connettersi e copiare i dati nei dischi di Data Box?
R.  Sì. È possibile usare sia il client Linux che Windows per connettersi e copiare i dati nei dischi di Data Box Disk. Per altre informazioni, passare all'elenco dei [sistemi operativi supportati](data-box-disk-system-requirements.md) per il computer host.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Q. I dischi sono stati spediti, ma si vuole annullare l'ordine. Per quale motivo il pulsante di annullamento non è disponibile?
R.  È possibile annullare l'ordine solo dopo che i dischi sono stati ordinati e prima della spedizione. Dopo la spedizione dei dischi, non è più possibile annullare l'ordine. Tuttavia, è possibile restituire i dischi a un costo. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>Q. È possibile connettere più dischi di Data Box allo stesso computer host per il trasferimento dei dati?
R. Sì. È possibile connettere più dischi di Data Box allo stesso computer host per trasferire i dati ed è supportata l'esecuzione in parallelo di più processi di copia.

## <a name="track-status"></a>Monitoraggio dello stato

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>Q. Come è possibile monitorare lo stato dei dischi da quando viene effettuato l'ordine alla loro restituzione? 
R.  È possibile monitorare lo stato dell'ordine dei dischi di Data Box nel portale di Azure. Quando si crea l'ordine, viene richiesto anche di fornire un indirizzo di posta elettronica per le notifiche. Se ne è stato fornito uno, si riceverà una notifica tramite posta elettronica per tutte le modifiche dello stato dell'ordine. Altre informazioni su come [configurare i messaggi di posta elettronica di notifica](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>Q. Qual è la procedura per restituire i dischi? 
R.  Microsoft fornisce un'etichetta di spedizione con i dischi di Data Box nel pacchetto di spedizione. Apporre l'etichetta sulla scatola per la spedizione e consegnare il pacchetto sigillato presso il punto di ritiro del vettore di spedizione. In caso di danneggiamento o smarrimento dell'etichetta, passare a **Panoramica > Scarica etichetta di spedizione** e scaricare una nuova etichetta per la spedizione di ritorno.

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>È possibile ritirare personalmente l'ordine di Data Box Disk? È possibile restituire i dischi tramite un vettore di propria scelta?
R. Sì. Nella sola area US Gov, Microsoft offre anche la spedizione autogestita. Quando si effettua l'ordine di Data Box Disk, è possibile scegliere tale opzione. Per ritirare l'ordine di Data Box Disk, seguire questa procedura:
    
1. Dopo aver inserito l'ordine, l'ordine viene elaborato e i dischi vengono preparati. Si riceverà una notifica tramite posta elettronica che informa che l'ordine è pronto per il ritiro. 
2. Quando l'ordine è pronto per il ritiro, andare all'ordine nel portale di Azure e passare al pannello **Panoramica**. 
3. Nel portale di Azure verrà visualizzata una notifica con un codice. Inviare un messaggio di posta elettronica al [team Azure Data Box Operations](mailto:adbops@microsoft.com) specificando il codice. Il team indicherà l'ubicazione e pianificherà una data e un'ora di ritiro. È necessario chiamare il team entro 5 giorni lavorativi dalla ricezione della notifica tramite posta elettronica.

Al termine della copia e della convalida dei dati, per restituire il disco seguire questa procedura:

1. Dopo aver completato la convalida dei dati, scollegare i dischi. Rimuovere i cavi di collegamento.
2. Eseguire il wrapping di tutti i dischi e i cavi di connessione con una bolla a capo e posizionarli nella casella shipping. In caso di accessori mancanti, potrebbero essere addebitate spese aggiuntive.

    - Riutilizzare l'imballaggio della spedizione iniziale. È consigliabile imballare i dischi avvolgendoli con cura in una pellicola a bolle d'aria.
    - Assicurarsi di riempire i vuoti per ridurre i movimenti all'interno della scatola.
3. Passare al pannello **Panoramica** dell'ordine nel portale di Azure. Verrà visualizzata una notifica con un codice.
4. Usare tale codice e inviare un messaggio di posta elettronica al [team Azure Data Box Operations](mailto:adbops@microsoft.com) specificandolo. Il team fornirà le informazioni su dove e quando consegnare i dischi.


## <a name="migrate-data"></a>Eseguire la migrazione dei dati

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>Q. Quali sono le dimensioni massime dei dati che possono essere usate con i dischi di Data Box?  
R.  La soluzione Data Box Disk può supportare al massimo 5 dischi con una capacità massima utilizzabile di 35 TB. I dischi stessi sono da 8 TB (di cui 7 TB utilizzabili).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>Q. Quali sono le dimensioni massime di BLOB in blocchi e BLOB di pagine supportate dai dischi di Data Box? 
R.  Le dimensioni massime dipendono dai limiti di Archiviazione di Azure. Le dimensioni massime dei BLOB in blocchi sono di circa 4,768 TiB e le dimensioni massime dei BLOB di pagine sono di 8 TiB. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione BLOB](../storage/blobs/scalability-targets.md).

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>Q. Qual è la velocità di trasferimento dei dati per i dischi di Data Box?
R. Nei test con dischi connessi tramite USB 3.0 sono risultate prestazioni dei dischi fino a 430 MB/s. I valori effettivi variano a seconda delle dimensioni dei file usate. Per i file più piccoli, le prestazioni potrebbero essere inferiori.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Q. In quale modo si può essere certi della sicurezza dei dati in transito? 
R.  I dischi di Data Box vengono crittografati tramite crittografia AES-128 bit BitLocker e la passkey è disponibile solo nel portale di Azure. Accedere al portale di Azure usando le credenziali dell'account per ottenere la passkey. Specificare questa passkey quando si esegue lo strumento di sblocco dei dischi di Data Box.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>Q. Qual è la procedura per copiare i dati nei dischi di Data Box? 
R.  Usare uno strumento di copia SMB, ad esempio `Robocopy` , `Diskboss` o anche il trascinamento della selezione di Esplora file di Windows per copiare i dati su dischi.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Q. Ci sono suggerimenti per velocizzare la copia dei dati?
R.  Per velocizzare il processo di copia:

- Usare più flussi di copia dei dati. Ad esempio, con `Robocopy` usare l'opzione multithreading. Per altre informazioni sul comando esatto da usare, vedere [Esercitazione: copiare i dati in Azure Data Box Disk ed eseguire la verifica](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Usare più sessioni.
- Invece di eseguire la copia tramite una condivisione di rete (operazione che potrebbe essere limitata dalle velocità di rete) assicurarsi di avere a disposizione i dati in locale nel computer a cui sono collegati i dischi.
- Verificare che sia in uso USB 3,0 o versione successiva durante il processo di copia. Scaricare e usare lo [ `USBView` strumento](/windows-hardware/drivers/debugger/usbview) per identificare i controller USB e i dispositivi USB collegati al computer.
- Valutare le prestazioni del computer usato per copiare i dati. Scaricare e usare lo [ `Bluestop` `FIO` strumento](https://ci.appveyor.com/project/axboe/fio) per eseguire il benchmark delle prestazioni dell'hardware del server. Selezionare la build x86 o x64 più recente, selezionare la scheda **Artefatti** e scaricare il file MSI.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>Q. In quale modo si può velocizzare la copia dei dati se i dati di origine sono in file di piccole dimensioni (KB o pochi MB)?
R.  Per velocizzare il processo di copia:

- Creare un file VHDx locale in un supporto di archiviazione veloce o creare un disco rigido virtuale vuoto nell'unità HDD o SSD (più lenta).
- Montarlo in una macchina virtuale.
- Copiare i file nel disco della macchina virtuale.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>Q. È possibile usare più account di archiviazione con i dischi di Data Box?
R.  No. Con i dischi di Data Box è attualmente supportato un solo account di archiviazione, generale o classico. Sono supportati BLOB sia ad accesso frequente che sporadico.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>Q. Qual è il set di strumenti disponibile per i dati con i Data Box Disk?
R. Il set di strumenti disponibile con il Data Box Disk contiene tre strumenti:
 - **Strumento di sblocco di Data Box Disk**: usare questo strumento per sbloccare i dischi crittografati forniti da Microsoft. Quando si sbloccano i dischi che usano lo strumento, è necessario fornire una passkey disponibile nell'ordine Data Box Disk nel portale di Azure. 
 - **Strumento di convalida di Data Box Disk**: usare questo strumento per convalidare le dimensioni, il formato e i nomi dei BLOB in base alle convenzioni di denominazione di Azure. Genera inoltre i checksum per i dati copiati, che vengono quindi usati per verificare i dati caricati in Azure.
 - **Strumento di divisione della copia di Data Box Disk**: Usare questo strumento quando si usano più dischi e si ha un set di dati di grandi dimensioni che deve essere suddiviso e copiato tra tutti i dischi. Questo strumento è attualmente disponibile per Windows. Questo strumento non è supportato con dischi gestiti. Questo strumento convalida i dati durante la copia, quindi è possibile ignorare il passaggio di convalida quando si usa questo strumento.

Il set di strumenti è disponibile per Windows e Linux. È possibile scaricare il set di strumenti qui:
- [Scaricare il set di strumenti di Data Box Disk per Windows](https://aka.ms/databoxdisktoolswin) 
- [Scaricare il set di strumenti di Data Box Disk per Linux](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>Q. È possibile usare Data Box Disk per trasferire i dati in File di Azure e quindi usare i dati con Sincronizzazione file di Azure? 
R. I file di Azure sono supportati con Data Box Disk ma non funzionano bene con Sincronizzazione file di Azure. I metadati non vengono conservati se i dati dei file vengono usati con Sincronizzazione file di Azure.


## <a name="verify-and-upload"></a>Verifica e caricamento

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>Q. Dopo quanto tempo sarà possibile accedere ai dati in Azure dopo aver restituito i dischi? 
R.  Quando lo stato dell'ordine per la copia dei dati risulta completato, dovrebbe essere possibile accedere immediatamente ai dati.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Q. Dove vengono collocati i dati in Azure dopo il caricamento?
R.  Quando si copiano i dati nelle cartelle *BlockBlob* e *PageBlob* nel disco, viene creato un contenitore nell'account di archiviazione di Azure per ogni sottocartella nella cartella *BlockBlob* e *PageBlob*. Se i file sono stati copiati direttamente nelle cartelle *BlockBlob* e *PageBlob* , i file si trovano in un contenitore predefinito *$root* nell'account di archiviazione di Azure. Quando si copiano i dati in una cartella all'interno della cartella *AzureFile*, viene creata una condivisione file.

### <a name="q-i-just-noticed-that-i-didnt-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Q. Ho notato che non ho rispettato i requisiti di denominazione di Azure per i contenitori. i dati non verranno caricati in Azure?
R. Tutte le lettere maiuscole nei nomi dei contenitori vengono convertite automaticamente in lettere minuscole. Se i nomi non sono conformi in altri modi, ad esempio contengono caratteri speciali o altre lingue, il caricamento avrà esito negativo. Per altre informazioni, vedere le [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>Q. Come è possibile verificare i dati copiati in più dischi di Data Box?
R.  Dopo aver completato la copia dei dati, è possibile eseguire `DataBoxDiskValidation.cmd` disponibile nella cartella *DataBoxDiskImport* per generare i checksum per la convalida. Per più dischi è necessario aprire una finestra di comando per ogni disco ed eseguire questo comando. Tenere presente che questa operazione può richiedere molto tempo (anche ore) a seconda delle dimensioni dei dati.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>Q. Che cosa succede ai dati dopo la restituzione dei dischi?
R.  Una volta completata la copia dei dati in Azure, i dati vengono cancellati dai dischi in modo sicuro in base alle linee guida NIST SP 800-88 revisione 1.  

### <a name="q-how-is-my-data-protected-during-transit"></a>Q. Come vengono protetti i dati durante il transito? 
R.  I dischi Data Box vengono crittografati con AES-128 crittografia BitLocker Microsoft e è necessaria una sola passkey per sbloccare tutti i dischi e accedere ai dati.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>Q. È necessario eseguire di nuovo la convalida dei checksum se si aggiungono altri dati ai dischi di Data Box?
R. Sì. Se si decide di convalidare i dati (operazione consigliata), è necessario eseguire di nuovo la convalida se si aggiunto altri dati ai dischi.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>Q. Se ci si rende conto che i dischi non sono sufficienti per il trasferimento di tutti i dati, è possibile ordinare rapidamente altri dischi?
R. È possibile clonare l'ordine precedente. La clonazione consente di creare un ordine identico e di modificare solo i dettagli dell'ordine senza dover digitare di nuovo indirizzo, informazioni di contatto e dettagli per le notifiche.

### <a name="q-i-copied-data-to-the-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-how-can-i-locate-it"></a>Q. I dati sono stati copiati nella cartella ManagedDisk. Non è presente nessun disco gestito con il gruppo di risorse specificato per i dischi gestiti. I dati sono stati caricati in Azure? Come è possibile individuarlo?
R. Sì. I dati sono stati caricati in Azure, ma se non viene visualizzato alcun disco gestito con i gruppi di risorse specificati, è probabile che i dati non siano validi. Se i BLOB di pagine, i BLOB in blocchi, File di Azure o i dischi gestiti non sono validi, verranno inseriti nelle cartelle seguenti:
 - I BLOB di pagine verranno indirizzati a un contenitore BLOB in blocchi a partire da *databoxdisk-non valido-PB-*.
 - File di Azure andrà a un contenitore BLOB in blocchi a partire da *databoxdisk-non valido-AF-*.
 - Managed disks passerà a un contenitore BLOB in blocchi che inizia con *databoxdisk-non valido-MD-*.

## <a name="next-steps"></a>Passaggi successivi

- Vedere i [requisiti di sistema di Data Box Disk](data-box-disk-system-requirements.md).
- Comprendere i [limiti di Data Box Disk](data-box-disk-limits.md).
- Distribuire rapidamente [Azure Data Box Disk](data-box-disk-quickstart-portal.md) nel portale di Azure.