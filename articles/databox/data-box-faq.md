---
title: Domande frequenti su Microsoft Azure Data Box | Microsoft Docs
description: Contiene domande frequenti e risposte per Azure Data Box, una soluzione cloud che consente di trasferire grandi quantità di dati in Azure.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/17/2021
ms.author: alkohli
ms.openlocfilehash: 46a60642e177a2bfb9e8fb996427ea45f6777468
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652786"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: Domande frequenti

La soluzione ibrida Microsoft Azure Data Box consente di inviare terabyte di dati in Azure in modo rapido, conveniente e affidabile tramite un dispositivo di trasferimento. L'articolo contiene le domande frequenti che possono sorgere quando si usa Data Box nel portale di Azure e le relative risposte.

Le domande e le risposte sono suddivise nelle categorie seguenti:

- Informazioni sul servizio
- Ordinare il dispositivo
- Configurazione e connessione 
- Monitoraggio dello stato
- Copiare i dati 
- Spedire il dispositivo
- Verifica e caricamento dei dati 
- Supporto per la catena di custodia

## <a name="about-the-service"></a>Informazioni sul servizio

### <a name="q-what-is-azure-data-box-service"></a>Q. Che cos'è il servizio Azure Data Box? 
R.  Il servizio Azure Data Box è progettato per l'inserimento di dati offline. Il servizio gestisce una gamma di prodotti per capacità di archiviazione diverse, tutti realizzati su misura per il trasporto dei dati. 

### <a name="q-what-is-azure-data-box"></a>Q. Che cos'è Azure Data Box?
R. Azure Data Box consente di trasferire terabyte di dati in Azure in modo rapido, conveniente e sicuro. È possibile ordinare il dispositivo Data Box tramite il portale di Azure. Microsoft invia un dispositivo di archiviazione con capacità usabile pari a 80 TB tramite un corriere regionale. 

Dopo aver ricevuto il dispositivo, è possibile configurarlo rapidamente usando l'interfaccia utente Web locale. Copiare i dati dai server al dispositivo o dal dispositivo ai server e rispedire il dispositivo ad Azure. Per un ordine di importazione, nel Data Center di Azure i dati vengono caricati automaticamente dal dispositivo in Azure. L'intero processo viene monitorato per tutta la durata dal servizio Data Box nel portale di Azure.

### <a name="q-when-should-i-use-data-box"></a>Q. Quando è consigliabile usare Data Box?
R. Se si dispone di 40-500 TB di dati che si desidera trasferire da o verso Azure, è possibile sfruttare i vantaggi derivanti dall'utilizzo di Data Box. Per una quantità di dati minore di 40 TB, usare Data Box Disk, mentre per una quantità di dati maggiore di 500 TB, iscriversi a [Data Box Heavy](data-box-heavy-overview.md).

### <a name="q-what-is-the-price-of-data-box"></a>Q. Qual è il prezzo di Data Box?
R. Data Box è disponibile a un costo nominale per 10 giorni. Quando si seleziona il modello di prodotto durante la creazione di un ordine nel portale di Azure, vengono visualizzati i costi per il dispositivo. Si applicano anche costi di spedizione standard e costi per archiviazione di Azure. Gli ordini di esportazione seguono un modello di determinazione prezzi simile a quello degli ordini di importazione, sebbene possano essere applicati costi aggiuntivi in uscita. 

Per ulteriori informazioni, vedere [Azure Data Box i prezzi](https://azure.microsoft.com/pricing/details/storage/databox/) e i [costi in uscita](https://azure.microsoft.com/pricing/details/bandwidth/). 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>Q. Qual è la quantità massima di dati che è possibile trasferire con Data Box in una sola volta?
R. Data Box ha una capacità nominale di 100 TB e una capacità usabile di 80 TB. Con Data Box è possibile trasferire fino a 80 TB di dati. Per trasferire più dati, è necessario ordinare altri dispositivi.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>Q. Come è possibile verificare se Data Box è disponibile nell'area di appartenenza dell'utente? 
R.  Per informazioni sui paesi o sulle aree geografiche in cui Data Box è disponibile, vedere [Aree di disponibilità](data-box-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>Q. In quali aree è possibile archiviare dati con Data Box?
R. Data Box è supportato in tutte le aree negli Stati Uniti, in Europa occidentale e settentrionale, in Francia, nel Regno Unito, in Giappone, in Australia e in Canada. Per altre informazioni, vedere [Disponibilità a livello di area](data-box-overview.md#region-availability).

### <a name="q-how-can-i-import-source-data-at-my-location-in-a-particular-country-to-an-azure-region-in-a-different-countryregion-or-export-data-from-an-azure-region-in-one-country-to-a-different-countryregion"></a>Q. Come si importano i dati di origine nel percorso in un determinato paese in un'area di Azure in un paese o in un'area geografica diversa oppure si esportano dati da un'area di Azure in un paese a un paese diverso?

Data Box supporta l'inserimento o l'uscita di dati solo all'interno dello stesso paese/area geografica della destinazione e non supera i confini internazionali. L'unica eccezione riguarda gli ordini nell'Unione europea (UE), in cui le caselle di dati possono essere fornite da e verso qualsiasi paese dell'UE.

Ad esempio, nello scenario di importazione, se si dispone dei dati di origine in Canada che si desidera spostare in un account di archiviazione degli Stati Uniti occidentali di Azure, è possibile ottenerlo nel modo seguente:

1. Ordinare Data Box in Canada scegliendo un account di archiviazione in Canada. Il dispositivo viene spedito da un Data Center di Azure in Canada all'indirizzo di spedizione, in Canada, fornito durante la creazione dell'ordine.

2. Una volta eseguita la copia dei dati locale nella Data Box, ripristinare il dispositivo nel Data Center di Azure in Canada. I dati presenti nel Data Box vengono quindi caricati nell'account di archiviazione di destinazione nell'area di Azure del Canada scelto durante la creazione dell'ordine.

3. È quindi possibile usare uno strumento come AzCopy per copiare i dati in un account di archiviazione negli Stati Uniti occidentali. Questo passaggio comporta costi di archiviazione e [larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/) [standard](https://azure.microsoft.com/pricing/details/storage/) che non sono inclusi nel data box la fatturazione.

### <a name="q-how-can-i-recover-my-data-if-an-entire-region-fails"></a>Q. Come è possibile ripristinare i dati in caso di errore di un'intera area?

R. In casi estremi in cui un'area viene persa a causa di un'emergenza significativa, Microsoft può avviare un failover a livello di area. In questo caso non è necessaria alcuna azione da parte dell'utente. L'ordine verrà evaso attraverso l'area di failover se si trova nello stesso paese o limite commerciale. Tuttavia, alcune aree di Azure non hanno un'area abbinata nello stesso confine geografico o commerciale. Se si verifica un'emergenza in una di queste aree, è necessario creare nuovamente l'ordine di Data Box da un'area diversa disponibile e copiare i dati in Azure nella nuova area. Per altre informazioni, vedere [Continuità aziendale e ripristino di emergenza (BCDR): aree geografiche abbinate di Azure](../best-practices-availability-paired-regions.md).

### <a name="q-who-should-i-contact-if-i-come-across-any-issues-with-data-box"></a>Q. Chi devo contattare se si riscontrano problemi con Data Box?
R. In caso di problemi con Data Box, [contattare supporto tecnico Microsoft](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-lost-my-data-box-is-there-a-lost-device-charge"></a>Q. Ho perso la mia Data Box. È previsto un addebito per lo smarrimento dei dispositivi?
R. Sì. È previsto un addebito per un dispositivo smarrito o danneggiato. Questo addebito è indicato nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/databox/) e nelle [condizioni](https://www.microsoft.com/licensing/product-licensing/products)per il prodotto.


## <a name="order-device"></a>Ordinare il dispositivo

### <a name="q-how-do-i-get-data-box"></a>Q. Come è possibile ottenere Data Box? 
R.  Per ottenere Azure Data Box, accedere al portale di Azure e creare un ordine per Data Box. Fornire le informazioni di contatto e i dettagli per le notifiche. Dopo l'emissione dell'ordine, Data Box viene spedito all'utente entro 10 giorni in base alla disponibilità. Per altre informazioni, vedere [Order a Data Box](data-box-deploy-ordered.md) (Ordinare un Data Box).

### <a name="q-i-couldnt-create-a-data-box-order-in-the-azure-portal-why"></a>Q. Non è stato possibile creare un ordine di Data Box nel portale di Azure. Perché?
R. Se non è possibile creare un ordine di Data Box, si verifica un problema con il tipo di sottoscrizione o l'accesso.

Controllare la sottoscrizione. Data Box è disponibile solo per le offerte di sottoscrizione Contratto Enterprise (EA) e Cloud Solution Provider (CSP). Se non si dispone di uno di questi tipi di sottoscrizione, contattare supporto tecnico Microsoft per aggiornare la sottoscrizione.

Se si dispone di un tipo di offerta supportato per la sottoscrizione, controllare il livello di accesso della sottoscrizione stessa. Per creare un ordine, è necessario essere un collaboratore o un proprietario della sottoscrizione.

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>Q. Quanto tempo è necessario per la creazione dell'ordine ai dati caricati in Azure?

R. I tempi di esecuzione stimati seguenti per ogni fase di elaborazione degli ordini forniranno un'idea di cosa aspettarsi.  

Questi lead time sono *stime*. Il tempo per ogni fase di elaborazione degli ordini è influenzato dal carico nel datacenter, dagli ordini simultanei e da altre condizioni ambientali.

**Tempo di esecuzione stimato per un ordine di Data Box:**

1. Ordine Data Box: pochi minuti dal portale
2. Allocazione e preparazione dei dispositivi: 1-2 giorni lavorativi, soggetto alla disponibilità dell'inventario e ad altri ordini in attesa di evasione
3. Spedizione: 2-3 giorni lavorativi
4. Copia dei dati nel sito del cliente: dipende dalla natura dei dati, dalle dimensioni e dal numero di file
5. Effettuare il reso della spedizione: 2-3 giorni lavorativi
6. Elaborazione del dispositivo nel Data Center: 1-2 giorni lavorativi, soggetto ad altri ordini in attesa di elaborazione
7. Caricare i dati in Azure: inizia non appena è stata completata l'elaborazione e il dispositivo è connesso. Il tempo di caricamento dipende dalla natura dei dati, dalle dimensioni e dal numero di file.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-cant-create-any-additional-orders-why"></a>Q. Ho ordinato un paio di dispositivi Data Box. Non è possibile creare altri ordini. Perché?
R. Sono consentiti al massimo cinque ordini attivi per sottoscrizione per ogni limite commerciale (combinazione di paese e area selezionata). Se è necessario ordinare un dispositivo aggiuntivo, contattare il supporto tecnico Microsoft per aumentare il limite correlato alla sottoscrizione.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>Q. Quando si tenta di creare un ordine, si riceve una notifica relativa alla non disponibilità del servizio Data Box. Che cosa significa?
R. Il servizio Data Box non è disponibile per la combinazione di paese e area selezionata. La modifica di questa combinazione può consentire l'uso del servizio Data Box. Per un elenco di aree in cui il servizio è disponibile, vedere [Region availability for Data Box](data-box-overview.md#region-availability) (Disponibilità a livello di area per Data Box).

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>Q. Ho inserito l'ordine di Data Box alcuni giorni fa. Quando riceverò il dispositivo Data Box?
R. Quando l'utente inserisce un ordine, Microsoft controlla se un dispositivo è disponibile per l'ordine stesso. Se un dispositivo è disponibile, Microsoft lo spedisce entro 10 giorni. È plausibile che vi siano periodi di domanda elevata. In questo caso l'ordine viene accodato ed è possibile monitorare la modifica dello stato nel portale di Azure. Se l'ordine non viene evaso entro 90 giorni, viene annullato automaticamente.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>Q. Ho riempito i miei Data Box con i dati ed è necessario ordinarne un altro. è possibile ordinare rapidamente altri dischi?
R. È possibile clonare l'ordine precedente. La clonazione consente di creare un ordine identico e di modificare solo i dettagli dell'ordine senza dover digitare di nuovo indirizzo, informazioni di contatto e dettagli per le notifiche. La clonazione è consentita solo per gli ordini di importazione.

## <a name="configure-and-connect"></a>Configurazione e connessione

### <a name="q-how-do-i-unlock-the-data-box"></a>Q. Come si sblocca Data Box? 
R.  Nel portale di Azure accedere all'ordine di Data Box e andare a **Dettagli dispositivo**. Copiare la password di sblocco. Usare questa password per accedere all'interfaccia utente Web locale di Data Box. Per altre informazioni, vedere l'[Esercitazione: Disimballare, cablare e connettersi ad Azure Data Box](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>Q. È possibile usare un computer host Linux per connettersi e copiare i dati in Data Box?
R.  Sì. È possibile usare Data Box per connettersi a client SMB e NFS. Per altre informazioni, passare all'elenco dei [sistemi operativi supportati](data-box-system-requirements.md) per il computer host.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Q. Il dispositivo Data Box è stato inviato, ma ora desidero annullare l'ordine. Per quale motivo il pulsante di annullamento non è disponibile?
R.  È possibile annullare l'ordine solo dopo l'ordine di Data Box e prima che l'ordine venga elaborato. Dopo l'elaborazione dell'ordine di Data Box, non è più possibile annullarlo. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>Q. È possibile connettere un Data Box contemporaneamente a più computer host per trasferire i dati?
R. Sì. Più computer host possono connettersi a Data Box per trasferire i dati e più processi di copia possono essere eseguiti in parallelo. Per altre informazioni, vedere l'[Esercitazione: Copiare i dati in Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>Q. È possibile connettersi a entrambe le interfacce a 10 GbE del Data Box per trasferire i dati?
R. Sì. È possibile connettersi a entrambe le interfacce a 10 GbE del Data Box per copiare i dati contemporaneamente. Per altre informazioni su come copiare i dati, passare a [Esercitazione: Copiare i dati in Azure Data Box](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why?
A.

### Q. I could not set up Data Box using a Static IP address. Why?
A.

### Q. I could not set up Data Box on a private network. Why?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>Q. Il LED indicatore di errore di sistema sul pannello operativo anteriore è acceso. Cosa devo fare?
R. Sono presenti due spie LED sotto il pulsante di alimentazione nella parte anteriore di una Data Box. La luce più in basso è l'indicatore di errore di sistema, che indica se il sistema è integro.

Un indicatore di errore di sistema LED rosso potrebbe indicare uno dei problemi seguenti:
- Guasto alla ventola
- Temperatura CPU elevata
- Temperatura della scheda madre elevata
- Errore di connessione del codice (ECC) al modulo di memoria inline doppio (DIMM)

Seguire questa procedura:
1. Controllare se la ventola funziona.
2. Spostare il dispositivo in una posizione con maggiore flusso d'aria.

Se la luce dell'indicatore di errore di sistema è ancora attiva, [contattare supporto tecnico Microsoft](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why"></a>Q. Non riesco ad accedere alla password di sblocco di Data Box nel portale di Azure. Perché?
R. Se non si riesce ad accedere alla password di sblocco nella portale di Azure, controllare le autorizzazioni per la sottoscrizione e l'account di archiviazione. Verificare di disporre dell'autorizzazione di collaboratore o proprietario a livello di gruppo di risorse. Per visualizzare le credenziali di accesso, è necessario avere almeno l'autorizzazione Data Box ruolo operatore.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>Q. La configurazione del canale delle porte è supportata in Data Box? E MPIO?
R. Non è supportata la configurazione del canale porta, la configurazione di i/o a percorsi multipli (MPIO) o la configurazione vLAN su Data Box.

## <a name="track-status"></a>Monitoraggio dello stato

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>Q. Come è possibile monitorare Data Box dal momento dell'inserimento dell'ordine alla spedizione del dispositivo? 
R.  È possibile monitorare lo stato dell'ordine di Data Box nel portale di Azure. Quando si crea l'ordine, viene richiesto di fornire un messaggio di posta elettronica di notifica. Se ne è stato fornito uno, viene inviata una notifica tramite posta elettronica di tutte le modifiche di stato per l'ordine. Altre informazioni su come [configurare i messaggi di posta elettronica di notifica](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-device"></a>Q. Qual è la procedura per restituire il dispositivo? 
R.  Microsoft consente di visualizzare un'etichetta di spedizione sullo schermo E-ink. Se l'etichetta di spedizione non viene visualizzata nella visualizzazione E-Ink, passare a **panoramica > Scarica etichetta di spedizione**. Scaricare e stampare l'etichetta, inserire l'etichetta nel tag Clear Plastic sul dispositivo ed eliminare il dispositivo nella posizione del vettore di spedizione. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>Q. Ho ricevuto una notifica di posta elettronica che indica che il dispositivo è arrivato al data center di Azure. Come posso scoprire se il caricamento dei dati è in corso?
R. È possibile accedere all'ordine nel portale di Azure e andare a **Panoramica**. Se il caricamento dei dati in Azure è stato avviato, l'avanzamento della copia viene visualizzato nel riquadro a destra. 

## <a name="migrate-data"></a>Eseguire la migrazione dei dati

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>Q. Quali sono le dimensioni massime dei dati che possono essere usate con Data Box?  
R.  Data Box ha una capacità di archiviazione usabile di 80 TB. È possibile usare un singolo dispositivo Data Box per una quantità di dati compresa tra 40 e 80 TB. Per dimensioni di dati maggiori, fino a 500 TB, è possibile ordinare più dispositivi Data Box. Per dimensioni dei dati superiori a 500 TB, iscriversi a Data Box Heavy.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>Q. Quali sono le dimensioni massime di BLOB in blocchi e BLOB di pagine supportate da Data Box? 
R.  Le dimensioni massime dipendono dai limiti di Archiviazione di Azure. Le dimensioni massime dei BLOB in blocchi sono di circa 4,768 TiB e le dimensioni massime dei BLOB di pagine sono di 8 TiB. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione BLOB](../storage/blobs/scalability-targets.md).

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Q. In quale modo si può essere certi della sicurezza dei dati in transito? 
R. Sono disponibili più funzionalità di sicurezza implementate per garantire che Data Box sia protetto durante il transito. Alcune funzionalità includono sigilli antimanomissione, rilevamento di manomissione hardware e software, password di sblocco dei dispositivi. Per altre informazioni, vedere [Azure Data Box security and data protection](data-box-security.md) (Sicurezza e protezione dei dati in Azure Data Box).

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>Q. Qual è la procedura per copiare i dati in Data Box? 
R.  Se si usa un client SMB, è possibile usare uno strumento di copia SMB, ad esempio `Robocopy` , `Diskboss` o anche il trascinamento della selezione di Esplora file di Windows per copiare i dati nel dispositivo. 

Se si usa un client NFS, è possibile usare [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) o [Ultracopier](https://ultracopier.first-world.info/). 

Per altre informazioni, vedere l'[Esercitazione: Copiare i dati in Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Q. Ci sono suggerimenti per velocizzare la copia dei dati?
R.  Per velocizzare il processo di copia:

- Usare più flussi di copia dei dati. Ad esempio, con `Robocopy` usare l'opzione multithreading. Per altre informazioni sul comando esatto da usare, vedere [Esercitazione: Copiare dati in Azure Data Box Disk ed eseguire la verifica](data-box-deploy-copy-data.md).
- Usare più sessioni.
- Anziché eseguire la copia in una condivisione di rete, in cui le velocità di rete possono limitare la velocità di copia, archiviare i dati localmente nel computer a cui è connessa la Data Box.
- Valutare le prestazioni del computer usato per copiare i dati. Scaricare e usare lo [ `Bluestop` `FIO` strumento](https://ci.appveyor.com/project/axboe/fio) per eseguire il benchmark delle prestazioni dell'hardware del server. Selezionare la build x86 o x64 più recente, selezionare la scheda **Artefatti** e scaricare il file MSI.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM's disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>Q. È possibile usare più account di archiviazione con Data Box?
R.  Sì. Con Data Box è supportato un massimo di 10 account di archiviazione, per utilizzo generico, classico o BLOB. Sono supportati BLOB sia ad accesso frequente che sporadico.


## <a name="ship-device"></a>Spedire il dispositivo

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>Q. Il dispositivo è stato recapitato, ma il dispositivo sembra essere danneggiato. Cosa devo fare?
R. Se il dispositivo è arrivato danneggiato o ci sono prove di manomissione, non usare il dispositivo. [Contattare supporto tecnico Microsoft](data-box-disk-contact-microsoft-support.md) e restituire il dispositivo il prima possibile. È anche possibile creare un nuovo ordine di Data Box per un dispositivo sostitutivo. In questo caso, non verrà addebitato alcun costo per il dispositivo sostitutivo.

### <a name="q-can-i-pick-up-my-data-box-order-myself-can-i-return-the-data-box-via-a-carrier-that-i-choose"></a>Q. È possibile ritirare personalmente l'ordine di Data Box? È possibile restituire Data Box tramite un vettore di propria scelta?
R. Sì. Microsoft offre anche spedizioni autogestite. Quando si effettua l'ordine di Data Box, è possibile scegliere tale opzione. Per ulteriori informazioni, vedere [la pagina relativa alla distribuzione self-Managed per data box](data-box-portal-customer-managed-shipping.md).

### <a name="q-will-my-data-box-devices-cross-countryregion-borders-during-shipping"></a>Q. I dispositivi Data Box possono essere spediti oltre i confini nazionali/regionali?
R. Tutti i dispositivi Data Box vengono spediti all'interno dello stesso paese o della stessa area geografica e non possono superare i confini internazionali. L'unica eccezione è data dagli ordini nell'Unione Europa (UE), in cui i dispositivi possono essere spediti da e verso i paesi o le aree geografiche dell'Unione Europea. Questo approccio si applica ai dispositivi Data Box e Data Box Heavy.

### <a name="q-i-ordered-a-data-box-in-us-east-but-i-received-a-device-that-was-shipped-from-a-location-in-us-west-where-should-i-return-the-device-to"></a>Q. Ho ordinato un Data Box negli Stati Uniti orientali, ma ho ricevuto un dispositivo spedito da una località negli Stati Uniti occidentali. A chi devo restituire il dispositivo?
R. Si tenta di ottenere un dispositivo Data Box nel minor tempo possibile. per le spedizioni viene data priorità ai data center più vicini alla località dell'account di archiviazione, tuttavia i dispositivi vengono spediti da qualsiasi data center di Azure con inventario disponibile. Il Data Box deve essere restituito all'indirizzo da cui è stato spedito, indicato nell'etichetta di spedizione.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>Q. Nello schermo E-ink non viene visualizzata l'etichetta di spedizione di restituzione. Cosa devo fare?
R. Se la visualizzazione E-Ink non Visualizza l'etichetta return Ship, seguire questa procedura:
- Rimuovere l'etichetta di spedizione e qualsiasi adesivo della spedizione precedente.
- Accedere al proprio ordine nel portale di Azure. Passare a **Panoramica** e selezionare **Scarica etichetta di spedizione**. Per altre informazioni, vedere [Download shipping label](data-box-portal-admin.md#download-shipping-label) (Scaricare l'etichetta di spedizione).
- Stampare l'etichetta di spedizione e inserirla nella busta in plastica applicata al dispositivo. 
- Verificare che l'etichetta di spedizione sia chiaramente visibile. 

### <a name="q-how-is-my-data-protected-during-transit"></a>Q. Come vengono protetti i dati durante il transito? 
R.  Durante il transito le funzionalità seguenti in Data Box consentono di proteggere i dati.
 - I dischi di Data Box vengono crittografati con tecnologia AES a 256 bit. 
 - Il dispositivo è bloccato e richiede una password di sblocco per inserire i dati e per accedervi.
Per altre informazioni, vedere [Data Box security features](data-box-security.md) (Funzionalità di sicurezza di Data Box).  

### <a name="q-i-have-finished-prepare-to-ship-for-my-import-order-and-shut-down-the-device-can-i-still-add-more-data-to-the-data-box"></a>Q. Ho terminato di preparare la spedizione per l'ordine di importazione e di arrestare il dispositivo. È ancora possibile aggiungere altri dati alla Data Box?
R. Sì. È possibile accendere il dispositivo e aggiungere altri dati. È necessario eseguire nuovamente l'operazione **Prepara per la spedizione** dopo avere completato la copia dei dati.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>Q. Il dispositivo non si avvia. Qual è la procedura per restituire il dispositivo?
R. Se il dispositivo non viene avviato, passare all'ordine nell'portale di Azure. Scaricare un'etichetta di spedizione e collegarla al dispositivo. Per altre informazioni, vedere [Download shipping label](data-box-portal-admin.md#download-shipping-label) (Scaricare l'etichetta di spedizione).

## <a name="verify-and-upload"></a>Verifica e caricamento

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>Q. Dopo quanto tempo è possibile accedere ai dati in Azure dopo aver restituito Data Box? 
R.  Quando lo stato dell'ordine per l'operazione **Copia dati** è **Completato**, dovrebbe essere possibile accedere immediatamente ai dati.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Q. Dove vengono collocati i dati in Azure dopo il caricamento?
R.  Quando si copiano i dati in Data Box, a seconda che i dati siano BLOB in blocchi o BLOB di pagine o file di Azure, i dati vengono caricati in uno dei percorsi seguenti nell'account di archiviazione di Azure:
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  In alternativa, è possibile passare all'account di archiviazione di Azure nell'portale di Azure e spostarsi da questa posizione.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Q. Se non si seguono i requisiti di denominazione di Azure per i contenitori, i dati non verranno caricati in Azure?
R.  Se i nomi dei contenitori contengono lettere maiuscole, tali nomi vengono automaticamente convertiti in caratteri minuscoli. Se i nomi non sono conformi per altri aspetti (caratteri speciali, altre lingue e così via), il caricamento non viene eseguito in modo corretto. Per ulteriori linee guida per la denominazione di condivisioni, contenitori e file, vedere:
- [Naming and referencing shares](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Assegnazione di nomi e riferimenti a condivisioni)
- [Block blobs and page blob conventions](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (Convenzioni su BLOB in blocchi e BLOB di pagine)

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>Q. Come è possibile verificare i dati copiati in Data Box?
R.  Dopo aver completato la copia dei dati, quando si esegue l'operazione **Prepara per la spedizione** i dati vengono convalidati. Data Box genera un elenco di file e di valori di checksum per i dati durante il processo di convalida. È possibile scaricare l'elenco di file e confrontarlo con i file nei dati di origine. Per altre informazioni, vedere [Prepare to ship](data-box-deploy-picked-up.md#prepare-to-ship) (Preparare per la spedizione).

### <a name="q-what-happens-to-my-data-after-i-return-the-data-box"></a>Q. Cosa accade ai dati dopo aver restituito il Data Box?
R.  Dopo che la copia dei dati in Azure è stata completata, i dati vengono cancellati dai dischi di Data Box in modo sicuro in base alle linee guida NIST SP 800-88 revisione 1. Per altre informazioni, vedere [Erasure of data from Data Box](data-box-deploy-picked-up.md#erasure-of-data-from-data-box) (Cancellazione dei dati da Data Box).

## <a name="audit-report"></a>Report di controllo

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>In che modo il servizio Azure Data Box supporta i clienti nelle procedure correlate alla catena di custodia?
R.  Il servizio Azure Data Box offre in modo nativo report che è possibile usare per la documentazione della catena di custodia. I log di copia e di controllo sono disponibili nell'account di archiviazione in Azure ed è possibile [scaricare la cronologia ordini](data-box-portal-admin.md#download-order-history) nel portale di Azure dopo il completamento dell'ordine.


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Quale tipo di documentazione è disponibile per supportare la catena di custodia?
R.  Per supportare la catena di custodia, è disponibile la documentazione seguente:

- Logistica di trasporto da UPS.
- Registrazione dell'accensione e dell'accesso condiviso degli utenti.
- Il file BOM o manifesto con un controllo di ridondanza ciclico a 64 bit (CRC-64) o un checksum per ogni file inserito correttamente nel Data Box.
- Segnalazione di file che non è stato possibile caricare nell'account di archiviazione di Azure.
- Bonifica dei processi del dispositivo Data Box (in base alle norme NIST 800 88R1) dopo che la data è stata copiata nell'account di archiviazione di Azure.

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>I documenti di accompagnamento del corriere (UPS) sono disponibili? 
R.  I documenti di accompagnamento del corriere vengono acquisiti nella cronologia ordini di Data Box. Tale report è disponibile all'utente dopo la restituzione del dispositivo al data center di Azure e dopo l'eliminazione dei dati dai dischi del dispositivo stesso. Per esigenze di controllo immediate, è anche possibile accedere direttamente al sito Web del corriere con il numero di tracciabilità dell'ordine e ottenere le informazioni necessarie.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>È possibile trasportare direttamente il dispositivo Data Box al data center di Azure? 
R.  No. Se è stato scelto Microsoft Managed shipping, non è possibile trasportare i dati. Attualmente, il Data Center di Azure non accetta la consegna del Data Box da parte dei clienti o di vettori diversi dall'UPS.

Se si sceglie il trasporto gestito autonomamente, è possibile prelevare o eliminare i Data Box dal Data Center di Azure.


## <a name="next-steps"></a>Passaggi successivi

- Vedere i [requisiti di sistema per Data Box](data-box-system-requirements.md).
- Comprendere i [limiti di Data Box](data-box-limits.md).
- Distribuire rapidamente [Azure Data Box](data-box-quickstart-portal.md) nel portale di Azure.