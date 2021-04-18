---
title: Funzionalità a disponibilità elevata per Oracle in Azure BareMetal
description: Informazioni sulle funzionalità disponibili in BareMetal per un database Oracle.
ms.topic: overview
ms.subservice: workloads
ms.date: 04/16/2021
ms.openlocfilehash: b27dc4b857d553be791528cbd91aee70b2294a92
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600217"
---
# <a name="high-availability-features-for-oracle-on-azure-baremetal"></a>Funzionalità a disponibilità elevata per Oracle in Azure BareMetal

Questo articolo illustra le principali funzionalità di disponibilità elevata e ripristino di emergenza di Oracle.

Oracle offre molte funzionalità per creare una piattaforma resiliente per l'esecuzione di database Oracle. Anche se nessuna singola funzionalità fornisce copertura per ogni tipo di errore, la combinazione di tecnologie su più livelli crea un sistema a disponibilità elevata. Non tutte le funzionalità sono necessarie per mantenere la disponibilità. Tuttavia, la combinazione di strategie offre la migliore protezione dall'ampia gamma di errori che si verificano. 

## <a name="flashback-database"></a>Flashback Database

La [funzionalità Database flashback](https://docs.oracle.com/en/database/oracle/oracle-database/21/rcmrf/FLASHBACK-DATABASE.html#GUID-584AC79A-40C5-45CA-8C63-DED3BE3A4511) è disponibile in Oracle Database Enterprise Edition. Il database flashback riavvolge il database a un momento specifico. Questa funzionalità è diversa da un ripristino temporato di [Gestione ripristino (RMAN)](https://docs.oracle.com/en/cloud/paas/db-backup-cloud/csdbb/performing-general-restore-and-recovery-operations.html) in quanto viene ripristinata dall'ora corrente, anziché dai venti in avanti dopo un ripristino. Il risultato è che il database flashback offre tempi di completamento molto più rapidi.
 
È possibile usare questa funzionalità insieme a [Oracle Data Guard.](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590) Il database flashback consente a un amministratore di database di creare nuovamente un'istanza di un database con errore in una configurazione di Data Guard senza un ripristino e un ripristino RMAN completi. Questa funzionalità consente di ripristinare la funzionalità di ripristino di emergenza (e i vantaggi di backup e report offloaded con Active Data Guard) molto più velocemente.
 
È possibile usare questa funzionalità invece di un'operazione di ripristino ritardata nel database di standby. È possibile eseguire il flash di un database di standby fino a un punto precedente alla creazione del problema.
 
Il Oracle Database mantiene i log di flashback nell'area di ripristino rapido (FRA). Questi log sono separati dai log di ripetizione e richiedono più spazio all'interno dell'frazione di codice. Per impostazione predefinita, vengono mantenute 24 ore di log di flashback, ma è possibile modificare questa impostazione in base alle esigenze.

## <a name="oracle-real-application-clusters"></a>Cluster di applicazioni reali Oracle

[Oracle Real Application Clusters (RAC)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92) consente a più server interconnessi di essere visualizzati come un unico servizio di database per utenti finali e applicazioni. Questa funzionalità rimuove molti punti di errore ed è una soluzione attiva/attiva a disponibilità elevata riconosciuta per i database Oracle.

Come illustrato nella figura seguente di Panoramica e procedure consigliate per la disponibilità elevata di [Oracle,](https://docs.oracle.com/en/database/oracle/oracle-database/19/haovw/ha-features.html)al livello applicazione viene presentato un singolo database RAC. Le applicazioni si connettono al listener SCAN, che indirizza il traffico a un'istanza di database specifica. RAC controlla l'accesso da più istanze per mantenere la coerenza dei dati tra nodi di calcolo separati.

![Diagramma che mostra una panoramica dell'architettura di Oracle RAC.](media/oracle-high-availability/oracle-real-application-clusters.png)

Se un'istanza ha esito negativo, il servizio continua in tutte le altre istanze rimanenti. Ogni database distribuito nella soluzione sarà in una configurazione RAC n+1, dove n è la potenza di elaborazione minima necessaria per supportare il servizio.

Oracle Database vengono usati per consentire il failover delle connessioni tra i nodi quando un'istanza non riesce in modo trasparente. Questi errori possono essere pianificati o non pianificati. Quando si lavora con l'applicazione (eventi di notifica rapida dell'applicazione), quando un'istanza viene resa non disponibile, il servizio viene spostato in un nodo esistente. Il servizio viene spostato in un nodo specificato nella configurazione del servizio come preferito o disponibile.

Un'altra funzionalità chiave Oracle Database servizi è l'avvio di un servizio solo a seconda del ruolo. Questa funzionalità viene usata in caso di failover di Data Guard. Tutti i modelli distribuiti con Data Guard sono necessari per collegare un servizio di database a un ruolo di Data Guard.

Ad esempio, è possibile creare due servizi, MY \_ DB APP e MY DB \_ \_ \_ AS. Il servizio \_ APP MY DB viene avviato solo quando \_ l'istanza del database viene avviata con il ruolo Data Guard di PRIMARY. MY \_ DB AS viene avviato solo quando il ruolo Data Guard è PHYSICAL \_ \_ STANDBY. Questa configurazione consente alle applicazioni di puntare al servizio APP, ma anche di creare report, che possono essere scaricati in standby attivo e \_ puntano al \_ servizio AS.

## <a name="oracle-data-guard"></a>Oracle Data Guard

Con Data Guard è possibile mantenere una copia identica di un database su hardware fisico separato. Idealmente, tale hardware deve essere rimosso geograficamente dal database primario. Data Guard non pone alcun limite alla distanza, anche se la distanza ha un impatto sulle modalità di protezione. L'aumento della distanza aggiunge latenza tra siti, che può causare la non più fattibile di alcune opzioni, ad esempio la replica sincrona.

Data Guard offre vantaggi rispetto alla replica a livello di archiviazione:

- Poiché la replica è in grado di riconoscere il database, viene replicato solo il traffico pertinente.
- Alcuni carichi di lavoro possono generare un input/output elevato in spazi di tabella temporanei, che non sono necessari in standby e quindi non vengono replicati.
- La convalida dei blocchi replicati viene eseguita nel database di standby, quindi i danneggiamenti fisici nel database primario non vengono replicati nel database di standby.
- Impedisce danneggiamenti logici all'interno del blocco e danneggiamenti di scrittura persi. Elimina anche il rischio di errori commersi dagli amministratori di archiviazione dalla replica in standby.
Il ripristino può essere ritardato per un periodo predeterminato, in modo che gli errori utente non vengano replicati immediatamente in standby.

## <a name="azure-netapp-files-snapshots"></a>Azure NetApp Files snapshot

La soluzione di archiviazione NetApp Files usata in BareMetal consente di creare snapshot di volumi. Gli snapshot consentono di ripristinare rapidamente un file system a un momento specifico. Le tecnologie di snapshot consentono tempi RTO (Recovery Time Objective) che sono una frazione del tempo necessario per ripristinare un backup del database.

La funzionalità snapshot per i database Oracle è disponibile tramite Azure NetApp SnapCenter. SnapCenter abilita gli snapshot per il backup, SnapVault offre l'insieme di credenziali offline e Snap Clone consente il ripristino self-service e altre operazioni.

## <a name="recovery-manager"></a>Gestione ripristino

Gestione ripristino (RMAN) è l'utilità preferita per l'esecuzione di backup fisici del database. RMAN interagisce con il file di controllo del database (o con un catalogo di ripristino centralizzato) per proteggere i vari componenti principali del database, tra cui:

- File di dati di database
- Log di replica archiviati
- File di controllo del database
- File di inizializzazione del database (spfile)

RMAN consente di eseguire backup a caldo o a freddo del database. È possibile usare questi backup per creare database standby o duplicare i database per clonare gli ambienti. RMAN ha anche una funzione di convalida del ripristino. Questa funzione legge un set di backup e determina se è possibile usarlo per ripristinare il database a un momento specifico.

Poiché RMAN è un'utilità fornita da Oracle, legge la struttura interna dei file di database. Ciò consente di eseguire controlli di danneggiamento fisico e logico durante le operazioni di backup e ripristino. È anche possibile ripristinare i file di dati del database e ripristinare singoli file di dati e spazi tabelle a un punto specifico nel tempo. Questi sono i vantaggi offerti da RMAN rispetto agli snapshot di archiviazione. I backup RMAN forniscono un'ultima linea di difesa contro la perdita completa dei dati quando non è possibile usare gli snapshot.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle opzioni e sulle raccomandazioni per ottimizzare la protezione e le prestazioni che eseguono Oracle nell'infrastruttura BareMetal:

> [!div class="nextstepaction"]
> [Opzioni per i server dell'infrastruttura Oracle BareMetal](options-considerations-high-availability.md)
