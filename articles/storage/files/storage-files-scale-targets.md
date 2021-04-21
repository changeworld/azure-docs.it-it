---
title: Obiettivi di scalabilità e prestazioni per File di Azure
description: Informazioni sugli obiettivi di scalabilità e prestazioni di File di Azure, incluse la capacità, la velocità di richiesta e la larghezza di banda in entrata e in uscita.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bdcee6b8ced45dba34309724e5a634cbb60a6d37
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818819"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Obiettivi di scalabilità e prestazioni per File di Azure
[File di Azure](storage-files-introduction.md) offre condivisioni file completamente gestite nel cloud accessibili tramite i protocolli di file system SMB e NFS. Questo articolo descrive gli obiettivi di scalabilità e prestazioni per File di Azure e Sincronizzazione file di Azure.

Gli obiettivi di scalabilità e prestazioni elencati di seguito sono di fascia alta, ma possono dipendere da altre variabili nella distribuzione. Ad esempio, la velocità effettiva per un file può essere limitata anche dalla larghezza di banda di rete disponibile, non solo dai server che ospitano le condivisioni file di Azure. È consigliabile eseguire il test del criterio di utilizzo per determinare se la scalabilità e le prestazioni di File di Azure soddisfano i requisiti. Microsoft è impegnata ad aumentare i limiti gradualmente. 

## <a name="azure-files-scale-targets"></a>Obiettivi di scalabilità di File di Azure
Le condivisioni file di Azure vengono distribuite in account di archiviazione, ovvero oggetti di primo livello che rappresentano un pool di archiviazione condiviso. Questo pool di archiviazione può essere usato per distribuire più condivisioni file. Esistono quindi tre categorie da considerare: account di archiviazione, condivisioni file di Azure e file.

### <a name="storage-account-scale-targets"></a>Destinazioni di scalabilità dell'account di archiviazione
Azure supporta più tipi di account di archiviazione per diversi scenari di archiviazione che i clienti possono avere, ma esistono due tipi principali di account di archiviazione per File di Azure. Il tipo di account di archiviazione da creare varia a seconda che si voglia creare una condivisione file standard o premium: 

- **Account di archiviazione per utilizzo generico v2**: gli account di archiviazione per utilizzo generico v2 consentono di distribuire condivisioni file di Azure in hardware standard o basato su disco rigido (HDD). Oltre a archiviare le condivisioni file di Azure, gli account di archiviazione per utilizzo generico v2 possono archiviare altre risorse di archiviazione, ad esempio contenitori BLOB, code o tabelle. Le condivisioni file possono essere distribuite nei livelli ottimizzati per le transazioni (impostazione predefinita), ad accesso caldo o ad accesso cool.

- **Account di archiviazione FileStorage**: gli account di archiviazione FileStorage consentono di distribuire condivisioni file di Azure in hardware Premium o basato su unità SSD. Gli account FileStorage possono essere usati solo per archiviare le condivisioni file di Azure. Non è infatti possibile distribuire altre risorse di archiviazione (contenitori BLOB, code, tabelle e così via) in un account FileStorage.

| Attributo | Account di archiviazione per gPv2 (standard) | Account di archiviazione FileStorage (Premium) |
|-|-|-|
| Numero di account di archiviazione per area per sottoscrizione | 250 | 250 |
| Capacità massima dell'account di archiviazione | 5 PiB<sup>1</sup> | 100 TiB (con provisioning) |
| Numero massimo di condivisioni file | Nessuna limitazione | Senza limiti, le dimensioni totali di cui è stato effettuato il provisioning di tutte le condivisioni devono essere inferiori al valore massimo della capacità massima dell'account di archiviazione |
| Frequenza massima di richieste simultanee | 20.000 operazioni di I/O al<sup>secondo 1</sup> | 100.000 IOPS |
| Ingresso massimo | <ul><li>Stati Uniti/Europa: 9.536 MiB/sec<sup>1</sup></li><li>Altre aree (LRS/ZRS): 9.536 MiB/sec<sup>1</sup></li><li>Altre aree (GRS): 4.768 GiB/sec<sup>1</sup></li></ul> | 4.136 MiB/sec |
| Uscita massima | 47.683 MiB/sec<sup>1</sup> | 6.204 MiB/sec |
| Numero massimo di regole di rete virtuale | 200 | 200 |
| Numero massimo di regole di indirizzi IP | 200 | 200 |
| Operazioni di lettura della gestione | 800 per 5 minuti | 800 per 5 minuti |
| Operazioni di scrittura di gestione | 10 al secondo/1200 all'ora | 10 al secondo/1200 all'ora |
| Operazioni dell'elenco di gestione | 100 per 5 minuti | 100 per 5 minuti |

<sup>1</sup> Gli account di archiviazione per utilizzo generico versione 2 supportano limiti di capacità più elevati e limiti più elevati per l'ingresso tramite richiesta. Per richiedere un incremento dei limiti di archiviazione, contattare il [supporto di Azure](https://azure.microsoft.com/support/faq/).

### <a name="azure-file-share-scale-targets"></a>Destinazioni di scalabilità di condivisione file di Azure
| Attributo | Condivisioni file standard<sup>1</sup> | Condivisioni file Premium |
|-|-|-|
| Dimensione massima di una condivisione file | Nessun minimo | 100 GiB (con provisioning) |
| Unità di aumento/riduzione delle dimensioni di cui è stato effettuato il provisioning | N/D | 1 GiB |
| Dimensioni massime di una condivisione file | <ul><li>100 TiB, con la funzionalità di condivisione file di grandi dimensioni abilitata<sup>2</sup></li><li>5 TiB, impostazione predefinita</li></ul> | 100 TiB |
| Numero massimo di file in una condivisione file | Nessun limite | Nessun limite |
| Frequenza massima delle richieste (numero massimo di operazioni di I/O al secondo) | <ul><li>10.000, con la funzionalità di condivisione file di grandi dimensioni abilitata<sup>2</sup></li><li>1.000 o 100 richieste ogni 100 ms, valore predefinito</li></ul> | <ul><li>IoPS di base: 400 + 1 IOPS per GiB, fino a 100.000</li><li>Bursting delle operazioni di I/O al secondo: max (4000,3 x IOPS per GiB), fino a 100.000</li></ul> |
| Valore massimo in ingresso per una singola condivisione file | <ul><li>Fino a 300 MiB/sec, con la funzionalità di condivisione file di grandi dimensioni abilitata<sup>2</sup></li><li>Fino a 60 MiB/sec, valore predefinito</li></ul> | 40 MiB/s + 0,04 * GiB con provisioning |
| Valore massimo in uscita per una singola condivisione file | <ul><li>Fino a 300 MiB/sec, con la funzionalità di condivisione file di grandi dimensioni abilitata<sup>2</sup></li><li>Fino a 60 MiB/sec, valore predefinito</li></ul> | 60 MiB/s + 0,06 * GiB con provisioning |
| Numero massimo di condivisioni snapshot | 200 snapshot | 200 snapshot |
| Lunghezza massima del nome dell'oggetto (directory e file) | 2\.048 caratteri | 2\.048 caratteri |
| Numero massimo di componenti del percorso (nel percorso \A\B\C\D ogni lettera è un componente) | 255 caratteri | 255 caratteri |
| Limite di collegamenti reali (solo NFS) | N/D | 178 |
| Numero massimo di canali di SMB Multichannel | N/D | 4 |
| Numero massimo di criteri di accesso archiviati per ogni condivisione file | 5 | 5 |

<sup>1</sup> I limiti per le condivisioni file standard si applicano a tutti e tre i livelli disponibili per le condivisioni file standard: ottimizzato per le transazioni, ad accesso più caldo e ad accesso freddo.

<sup>2</sup> L'impostazione predefinita per le condivisioni file standard è 5 TiB. Per informazioni dettagliate su come aumentare la scalabilità delle condivisioni file standard fino a 100 TiB, vedere Abilitare e creare condivisioni [file](./storage-files-how-to-create-large-file-share.md) di grandi dimensioni.

### <a name="file-scale-targets"></a>Destinazioni di scalabilità di file
| Attributo | File in condivisioni file standard  | File nelle condivisioni file Premium  |
|-|-|-|
| Dimensione massima dei file | 4 TiB | 4 TiB |
| Frequenza massima di richieste simultanee | 1.000 operazioni di I/O al secondo | Fino a 8.000<sup>1</sup> |
| Ingresso massimo per un file | 60 MiB/sec | 200 MiB/sec (fino a 1 GiB/s con anteprima multicanale SMB)<sup>2</sup>|
| Uscita massima per un file | 60 MiB/sec | 300 MiB/sec (fino a 1 GiB/s con anteprima multicanale SMB)<sup>2</sup> |
| Numero massimo di handle simultanei | 2.000 handle | 2.000 handle  |

1 Si applica agli I/O di lettura e scrittura (in genere dimensioni di I/O inferiori o <sup>uguali a 64 KiB). Le operazioni sui metadati, diverse da operazioni di lettura e scrittura, possono essere inferiori.</sup> 
 <sup>2 Soggetto a limiti di rete del computer, larghezza di banda disponibile, dimensioni di I/O, profondità della coda e altri fattori. Per informazioni dettagliate, [vedere Prestazioni multicanale SMB.](./storage-files-smb-multichannel-performance.md)</sup>

## <a name="azure-file-sync-scale-targets"></a>Obiettivi di scalabilità di Sincronizzazione file di Azure
La tabella seguente indica i limiti dei test Microsoft e anche le destinazioni con limiti rigidi:

| Risorsa | Destinazione | Limite rigido |
|----------|--------------|------------|
| Servizi di sincronizzazione archiviazione per area | 100 servizi di sincronizzazione archiviazione | Sì |
| Gruppi di sincronizzazione per servizio di sincronizzazione archiviazione | 200 gruppi di sincronizzazione | Sì |
| Server registrati per servizio di sincronizzazione archiviazione | 99 server | Sì |
| Endpoint cloud per gruppo di sincronizzazione | 1 endpoint cloud | Sì |
| Endpoint server per gruppo di sincronizzazione | 100 endpoint server | Sì |
| Endpoint server per server | 30 endpoint server | Sì |
| Oggetti file system (directory e file) per gruppo di sincronizzazione | 100 milioni di oggetti | No |
| Numero massimo di oggetti file system (directory e file) in una directory | 5 milioni di oggetti | Sì |
| Dimensioni massime del descrittore di protezione (directory e file) dell'oggetto | 64 KiB | Sì |
| Dimensione del file | 100 GiB | No |
| Dimensioni minime per un file da rendere a livelli | V9 e più recenti: basate sulle dimensioni del cluster del file system (raddoppia le dimensioni del cluster del file system). Ad esempio, se le file system del cluster sono 4 KiB, le dimensioni minime del file saranno 8 KiB.<br> V8 e versioni precedenti: 64 KiB  | Sì |

> [!Note]  
> Le dimensioni di un endpoint di Sincronizzazione file di Azure possono aumentare fino a quelle di una condivisione file di Azure. Se viene raggiunto il limite delle dimensioni della condivisione file di Azure, la sincronizzazione non funzionerà.

### <a name="azure-file-sync-performance-metrics"></a>Metriche delle prestazioni di Sincronizzazione file di Azure
Poiché l'agente di Sincronizzazione file di Azure viene eseguito in un computer Windows Server che si connette alle condivisioni file di Azure, le effettive prestazioni di sincronizzazione dipendono da diversi fattori nell'infrastruttura: Windows Server e configurazione dei dischi sottostante, larghezza di banda tra il server e l'archiviazione di Azure, dimensioni dei file, dimensioni totali del set di dati e attività nel set di dati. Poiché Sincronizzazione file di Azure opera a livello di file, le caratteristiche in termini di prestazioni di una soluzione basata su Sincronizzazione file di Azure possono essere misurate meglio in base al numero di oggetti (file e directory) elaborati al secondo.

Per Sincronizzazione file di Azure, le prestazioni sono critiche in due fasi:

1. **Provisioning monouso iniziale**: per ottimizzare le prestazioni in fase di provisioning iniziale, fare riferimento a [Onboarding con Sincronizzazione file di Azure](../file-sync/file-sync-deployment-guide.md#onboarding-with-azure-file-sync) per informazioni dettagliate sulla distribuzione ottimale.
2. **Sincronizzazione continua**: dopo il seeding iniziale dei dati nelle condivisioni file di Azure, Sincronizzazione file di Azure mantiene sincronizzati più endpoint.

Per semplificare la pianificazione della distribuzione per ognuna delle fasi, di seguito vengono presentati i risultati osservati durante i test interni su un sistema con una configurazione specifica

| Configurazione del sistema | Dettagli |
|-|-|
| CPU | 64 core virtuali con cache L3 da 64 MiB |
| Memoria | 128 GiB |
| Disco | Dischi SAS con RAID 10 con cache supportata da batteria |
| Rete | Rete a 1 Gbps |
| Carico di lavoro | File server per utilizzo generico|

| Provisioning monouso iniziale  | Dettagli |
|-|-|
| Numero di oggetti | 25 milioni di oggetti |
| Dimensioni del set di dati| ~4.7 TiB |
| Dimensioni medie dei file | ~200 KiB (file più grande: 100 GiB) |
| Enumerazione delle modifiche cloud iniziale | 20 oggetti al secondo  |
| Velocità effettiva di caricamento | 20 oggetti al secondo per gruppo di sincronizzazione |
| Velocità effettiva di download dello spazio dei nomi | 400 oggetti al secondo |

### <a name="initial-one-time-provisioning"></a>Provisioning monouso iniziale

**Enumerazione delle modifiche cloud iniziale:** quando viene creato un nuovo gruppo di sincronizzazione, l'enumerazione delle modifiche cloud iniziale è il primo passaggio che verrà eseguito. In questo processo il sistema enumera tutti gli elementi nella condivisione file di Azure. Durante questo processo, non sarà presente alcuna attività di sincronizzazione, ad esempio nessun elemento verrà scaricato dall'endpoint cloud all'endpoint server e nessun elemento verrà caricato dall'endpoint server all'endpoint cloud. L'attività di sincronizzazione riprenderà al termine dell'enumerazione delle modifiche cloud iniziali.
La frequenza delle prestazioni è di 20 oggetti al secondo. I clienti possono stimare il tempo necessario per completare l'enumerazione iniziale delle modifiche cloud determinando il numero di elementi nella condivisione cloud e usando le formule seguenti per ottenere il tempo in giorni. 

   **Tempo (in giorni) per l'enumerazione cloud iniziale = (numero di oggetti nell'endpoint cloud)/(20 * 60 * 60 * 24)**

**Sincronizzazione iniziale dei** dati da Windows Server alla condivisione file di Azure: molte distribuzioni di Sincronizzazione file di Azure iniziano con una condivisione file di Azure vuota perché tutti i dati si trova in Windows Server. In questi casi, l'enumerazione delle modifiche cloud iniziale è veloce e la maggior parte del tempo verrà impiegato per sincronizzare le modifiche da Windows Server alle condivisie file di Azure. 

Mentre la sincronizzazione carica i dati nella condivisione file di Azure, non ci [](../file-sync/file-sync-server-registration.md#set-azure-file-sync-network-limits) sono tempi di inattività nel file server locale e gli amministratori possono configurare limiti di rete per limitare la quantità di larghezza di banda usata per il caricamento dei dati in background.

La sincronizzazione iniziale è in genere limitata dalla frequenza di caricamento iniziale di 20 file al secondo per ogni gruppo di sincronizzazione. I clienti possono stimare il tempo necessario per caricare tutti i dati in Azure usando le formule seguenti per ottenere tempo in giorni:  

   **Tempo (in giorni) per il caricamento di file in un gruppo di sincronizzazione = (numero di oggetti nell'endpoint server)/(20 * 60 * 60 * 24)**

La suddivisione dei dati in più endpoint server e gruppi di sincronizzazione può velocizzare il caricamento iniziale dei dati, perché il caricamento può essere eseguito in parallelo per più gruppi di sincronizzazione con una frequenza di 20 elementi al secondo ciascuno. Due gruppi di sincronizzazione verranno quindi eseguiti a una velocità combinata di 40 elementi al secondo. Il tempo totale per il completamento è la stima del tempo per il gruppo di sincronizzazione con il maggior numero di file da sincronizzare.

**Velocità effettiva di download dello spazio dei nomi** Quando un nuovo endpoint server viene aggiunto a un gruppo di sincronizzazione esistente, l'agente Sincronizzazione file di Azure non scarica il contenuto del file dall'endpoint cloud. Sincronizza prima di tutto lo spazio dei nomi completo e quindi attiva il richiamo in background per scaricare i file, interamente o, se è abilitato il cloud a più livelli, in base ai criteri di suddivisione in livelli cloud impostati nell'endpoint del server.

| Sincronizzazione continua  | Dettagli  |
|-|--|
| Numero di oggetti sincronizzati| 125.000 oggetti (circa 1% di varianza) |
| Dimensioni del set di dati| 50 GiB |
| Dimensioni medie dei file | ~500 KiB |
| Velocità effettiva di caricamento | 20 oggetti al secondo per gruppo di sincronizzazione |
| Velocità effettiva per il download completo* | 60 oggetti al secondo |

*Se è abilitato il cloud a più livelli, è probabile che si osserveranno prestazioni migliori, in quanto vengono scaricati solo alcuni dei dati dei file. Sincronizzazione file di Azure scarica solo i dati dei file memorizzati nella cache quando vengono modificati in uno degli endpoint. Per tutti i file a più livelli o appena creati, l'agente non scarica i dati dei file e sincronizza invece solo lo spazio dei nomi per tutti gli endpoint del server. L'agente supporta anche download parziali di file a più livelli man mano che vi accedono gli utenti. 

> [!Note]  
> I numeri forniti sopra non sono un'indicazione delle prestazioni che si riscontreranno. Le prestazioni effettive dipenderanno da diversi fattori, come descritto all'inizio di questa sezione.

Come indicazione generale per la distribuzione, è necessario tenere presenti alcuni aspetti:

- La velocità effettiva degli oggetti cambia all'incirca in misura proporzionale al numero di gruppi di sincronizzazione nel server. La suddivisione dei dati in più gruppi di sincronizzazione in un server produce una maggiore velocità effettiva, che è limitata anche dal server e dalla rete.
- La velocità effettiva degli oggetti è inversamente proporzionale alla velocità effettiva in MiB al secondo. Per i file più piccoli, si riscontrerà una velocità effettiva maggiore per quanto riguarda il numero di oggetti elaborati al secondo, ma con una minore velocità effettiva in MiB al secondo. Al contrario, per i file di dimensioni maggiori, si otterrà un numero minore di oggetti elaborati al secondo, ma con una maggiore velocità effettiva in MiB al secondo. La velocità effettiva in MiB al secondo è limitata dagli obiettivi di scalabilità di File di Azure.

## <a name="see-also"></a>Vedi anche
- [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
- [Pianificazione per la distribuzione di Sincronizzazione file di Azure](../file-sync/file-sync-planning.md)