---
title: Eseguire la migrazione a condivisioni file di Azure
description: Informazioni sulle migrazioni alle condivisioni file di Azure e guida alla migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: a6335d90625f860984ccbfd224955a97a32b731f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785218"
---
# <a name="migrate-to-azure-file-shares"></a>Eseguire la migrazione a condivisioni file di Azure

Questo articolo illustra gli aspetti di base di una migrazione alle condivisioni file di Azure.

Questo articolo contiene le nozioni di base sulla migrazione e una tabella di guide alla migrazione. Queste guide consentono di spostare i file nelle condivisioni file di Azure. Le guide sono organizzate in base alla posizione dei dati e al modello di distribuzione (solo cloud o ibrido) a cui si sta passando.

## <a name="migration-basics"></a>Nozioni di base sulla migrazione

Azure ha più tipi di archiviazione cloud disponibili. Un aspetto fondamentale delle migrazioni di file in Azure è la determinazione dell'opzione di archiviazione di Azure più appropriata per i dati.

[Le condivisioni file di Azure](storage-files-introduction.md) sono adatte per i dati di file per utilizzo generico. Questi dati includono tutto ciò per cui si usa una condivisione SMB o NFS locale. Con [Sincronizzazione file di Azure](../file-sync/file-sync-planning.md), è possibile memorizzare nella cache il contenuto di diverse condivisioni file di Azure nei server che eseguono Windows Server in locale.

Per un'app attualmente in esecuzione in un server locale, l'archiviazione di file in una condivisione file di Azure potrebbe essere una scelta ottimale. È possibile spostare l'app in Azure e usare le condivisioni file di Azure come risorsa di archiviazione condivisa. Per questo scenario è anche [possibile prendere](../../virtual-machines/managed-disks-overview.md) in considerazione Dischi di Azure.

Alcune app cloud non dipendono da SMB, dall'accesso ai dati locale del computer o dall'accesso condiviso. Per queste app, l'archiviazione di oggetti come [i BLOB di Azure](../blobs/storage-blobs-overview.md) è spesso la scelta migliore.

La chiave in qualsiasi migrazione è acquisire tutta la fedeltà dei file applicabile quando si spostano i file dalla posizione di archiviazione corrente ad Azure. La fedeltà che l'opzione di archiviazione di Azure supporta e quanto richiede lo scenario consente anche di scegliere l'archiviazione di Azure appropriata. I dati di file per utilizzo generico dipendono in genere dai metadati dei file. I dati dell'app potrebbero non essere così.

Ecco i due componenti di base di un file:

- **Flusso di dati:** il flusso di dati di un file archivia il contenuto del file.
- **Metadati del** file: i metadati del file hanno questi sottocomponenti:
   * Attributi di file come di sola lettura
   * Autorizzazioni per file, che possono essere definite *autorizzazioni NTFS* o *ACL di file e cartelle*
   * Timestamp, in particolare i timestamp di creazione e di ultima modifica
   * Flusso di dati alternativo, ovvero uno spazio in cui archiviare grandi quantità di proprietà non standard

La fedeltà dei file in una migrazione può essere definita come la possibilità di:

- Archiviare tutte le informazioni sui file applicabili nell'origine.
- Trasferire file con lo strumento di migrazione.
- Archiviare i file nell'archiviazione di destinazione della migrazione.

Per assicurarsi che la migrazione proceda senza problemi, [identificare](#migration-toolbox) lo strumento di copia più adatto alle proprie esigenze e associare una destinazione di archiviazione all'origine.

Tenendo conto delle informazioni precedenti, è possibile vedere che l'archiviazione di destinazione per i file per utilizzo generico in Azure è [condivisioni file di Azure.](storage-files-introduction.md)

A differenza dell'archiviazione di oggetti nei BLOB di Azure, una condivisione file di Azure può archiviare in modo nativo i metadati dei file. Le condivisioni file di Azure mantengono anche la gerarchia di file e cartelle, gli attributi e le autorizzazioni. Le autorizzazioni NTFS possono essere archiviate in file e cartelle perché sono locali.

Un utente di Active Directory, che è il controller di dominio locale, può accedere in modo nativo a una condivisione file di Azure. Anche un utente di Azure Active Directory Domain Services (Azure AD DS). Ognuno usa la propria identità corrente per ottenere l'accesso in base alle autorizzazioni di condivisione e agli ACL di file e cartelle. Questo comportamento è simile a quello di un utente che si connette a una condivisione file locale.

Il flusso di dati alternativo è l'aspetto principale della fedeltà dei file che attualmente non può essere archiviato in un file in una condivisione file di Azure. Viene mantenuta in locale quando Sincronizzazione file di Azure viene usato.

Altre informazioni [sull'Azure AD e l'autenticazione](storage-files-identity-auth-active-directory-enable.md) [Azure AD DS per](storage-files-identity-auth-active-directory-domain-service-enable.md) le condivisioni file di Azure.

## <a name="migration-guides"></a>Guide alla migrazione

Nella tabella seguente sono elencate le guide dettagliate per la migrazione.

Come usare la tabella:

1. Individuare la riga per il sistema di origine in cui sono attualmente archiviati i file.

1. Scegliere una di queste destinazioni:

   - Una distribuzione ibrida che usa Sincronizzazione file di Azure per memorizzare nella cache il contenuto delle condivisioni file di Azure in locale
   - Condivisioni file di Azure nel cloud

   Selezionare la colonna di destinazione corrispondente a quella scelta.

1. All'interno dell'intersezione tra origine e destinazione, una cella della tabella elenca gli scenari di migrazione disponibili. Selezionarne uno per collegarsi direttamente alla guida dettagliata alla migrazione.

Uno scenario senza un collegamento non ha ancora una guida alla migrazione pubblicata. Controllare occasionalmente questa tabella per gli aggiornamenti. Le nuove guide verranno pubblicate quando saranno disponibili.

| Source (Sorgente) | Destinazione: </br>Distribuzione ibrida | Destinazione: </br>Distribuzione solo cloud |
|:---|:--|:--|
| | Combinazione di strumenti:| Combinazione di strumenti: |
| Windows Server 2012 R2 e versioni successive | <ul><li>[Sincronizzazione file di Azure](../file-sync/file-sync-deployment-guide.md)</li><li>[Sincronizzazione file di Azure e Azure DataBox](../file-sync/file-sync-offline-data-transfer.md)</li></ul> | <ul><li>Tramite RoboCopy in una condivisione file di Azure montata</li><li>Tramite Sincronizzazione file di Azure</li></ul> |
| Windows Server 2012 e versioni precedenti | <ul><li>Tramite DataBox e Sincronizzazione file di Azure al sistema operativo server recente</li><li>Tramite il Servizio Migrazione archiviazione nel server recente con Sincronizzazione file di Azure, quindi caricare</li></ul> | <ul><li>Tramite il Servizio Migrazione archiviazione al server recente con Sincronizzazione file di Azure</li><li>Tramite RoboCopy in una condivisione file di Azure montata</li></ul> |
| Archiviazione collegata alla rete (NAS) | <ul><li>[Tramite Sincronizzazione file di Azure caricamento](storage-files-migration-nas-hybrid.md)</li><li>[Tramite DataBox + Sincronizzazione file di Azure](storage-files-migration-nas-hybrid-databox.md)</li></ul> | <ul><li>[Tramite DataBox](storage-files-migration-nas-cloud-databox.md)</li><li>Tramite RoboCopy in una condivisione file di Azure montata</li></ul> |
| Linux/Samba | <ul><li>[Sincronizzazione file di Azure e RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Tramite RoboCopy in una condivisione file di Azure montata</li></ul> |
| Microsoft Azure StorSimple'appliance cloud 8100 o StorSimple Cloud Appliance 8600 | <ul><li>[Tramite il servizio cloud dedicato per la migrazione dei dati](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Tramite Sincronizzazione file di Azure](storage-files-migration-storsimple-1200.md)</li></ul> | |

## <a name="migration-toolbox"></a>Casella degli strumenti di migrazione

### <a name="file-copy-tools"></a>Strumenti per la copia di file

Microsoft e altri strumenti per la copia di file sono diversi. Per selezionare lo strumento più opportuno per lo scenario di migrazione, è necessario considerare le domande fondamentali seguenti:

* Lo strumento supporta i percorsi di origine e di destinazione per la copia del file?

* Lo strumento supporta il percorso di rete o i protocolli disponibili (ad esempio REST, SMB o NFS) tra i percorsi di archiviazione di origine e di destinazione?

* Lo strumento mantiene la fedeltà dei file necessaria supportata dai percorsi di origine e di destinazione?

    In alcuni casi, l'archiviazione di destinazione non supporta la stessa fedeltà dell'origine. Se l'archiviazione di destinazione è sufficiente per le proprie esigenze, lo strumento deve corrispondere solo alle funzionalità di fedeltà dei file della destinazione.

* Lo strumento dispone di funzionalità che lo consentono di adattarsi alla strategia di migrazione?

    Si consideri ad esempio se lo strumento consente di ridurre al minimo i tempi di inattività.
    
    Quando uno strumento supporta un'opzione per eseguire il mirroring di un'origine in una destinazione, è spesso possibile eseguirla più volte nella stessa origine e nella stessa destinazione mentre l'origine rimane accessibile.

    La prima volta che si esegue lo strumento, viene copiata la maggior parte dei dati. Questa esecuzione iniziale potrebbe durare un po' di tempo. Spesso dura più tempo del necessario per portare offline l'origine dati per i processi aziendali.

    Tramite il mirroring di un'origine in una destinazione (come con **robocopy /MIR),** è possibile eseguire nuovamente lo strumento nella stessa origine e destinazione. L'esecuzione è molto più veloce perché deve trasportare solo le modifiche di origine che si verificano dopo l'esecuzione precedente. La rieseguizione di uno strumento di copia in questo modo può ridurre significativamente i tempi di inattività.

La tabella seguente classifica gli strumenti Microsoft e la relativa idoneità corrente per le condivisioni file di Azure:

| Consigliato | Strumento | Supporto per le condivisioni file di Azure | Conservazione della fedeltà dei file |
| :-: | :-- | :---- | :---- |
|![Sì, consigliato](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Supportata. Le condivisioni file di Azure possono essere montate come unità di rete. | Piena fedeltà.* |
|![Sì, consigliato](media/storage-files-migration-overview/circle-green-checkmark.png)| Sincronizzazione file di Azure | Integrato in modo nativo nelle condivisioni file di Azure. | Piena fedeltà.* |
|![Sì, consigliato](media/storage-files-migration-overview/circle-green-checkmark.png)| Servizio di migrazione della risorsa di archiviazione | Supportato indirettamente. Le condivisioni file di Azure possono essere montate come unità di rete nei server di destinazione SMS. | Piena fedeltà.* |
|![Sì, consigliato](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy </br>versione 10.6 | Supportata. | Non supporta la copia dell'elenco di controllo di accesso radice di origine, in caso contrario la fedeltà completa.* </br>[Informazioni su come usare AzCopy con condivisioni file di Azure](../common/storage-use-azcopy-files.md) |
|![Sì, consigliato](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | Supportata. | DataBox supporta completamente i metadati. |
|![Non completamente consigliato](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure Storage Explorer </br>versione 1.14 | Supportata. | Non copia gli ACL. Supporta i timestamp.  |
|![Non consigliata](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Supportata. | Non copia i metadati. |
|||||

*\* Piena fedeltà: soddisfa o supera le funzionalità di condivisione file di Azure.*

### <a name="migration-helper-tools"></a>Strumenti helper di migrazione

Questa sezione descrive gli strumenti che consentono di pianificare ed eseguire le migrazioni.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy da Microsoft Corporation

RoboCopy è uno degli strumenti più applicabili alle migrazioni di file. Viene fornito come parte di Windows. La documentazione [principale di RoboCopy](/windows-server/administration/windows-commands/robocopy) è una risorsa utile per le numerose opzioni di questo strumento.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize da JAM Software GmbH

Sincronizzazione file di Azure ridimensiona principalmente con il numero di elementi (file e cartelle) e non con lo spazio di archiviazione totale. Lo strumento TreeSize consente di determinare il numero di elementi nei volumi di Windows Server.

È possibile usare lo strumento per creare una prospettiva prima di una [Sincronizzazione file di Azure distribuzione](../file-sync/file-sync-deployment-guide.md). È anche possibile usarlo quando il cloud a livelli è attivato dopo la distribuzione. In questo scenario viene visualizzato il numero di elementi e le directory che usano maggiormente la cache del server.

La versione testata dello strumento è la 4.4.1. È compatibile con i file a livello di cloud. Lo strumento non causerà il richiamo di file a livelli durante il normale funzionamento.

## <a name="next-steps"></a>Passaggi successivi

1. Creare un piano per la distribuzione di condivisioni file di Azure (solo cloud o ibrida) desiderata.
1. Esaminare l'elenco delle guide alla migrazione disponibili per trovare la guida dettagliata corrispondente all'origine e alla distribuzione delle condivisioni file di Azure.

Altre informazioni sulle tecnologie File di Azure menzionate in questo articolo:

* [Panoramica della condivisione file di Azure](storage-files-introduction.md)
* [Pianificazione per la distribuzione di Sincronizzazione file di Azure](../file-sync/file-sync-planning.md)
* [Sincronizzazione file di Azure: a livelli cloud](../file-sync/file-sync-cloud-tiering-overview.md)