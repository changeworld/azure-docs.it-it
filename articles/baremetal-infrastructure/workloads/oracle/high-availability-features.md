---
title: Funzionalità a disponibilità elevata per Oracle in Azure BareMetal
description: Informazioni sulle funzionalità disponibili in BareMetal per un database Oracle.
ms.topic: overview
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: 75032cc6351504a8400be43d05091d2b47484229
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590428"
---
# <a name="high-availability-features-for-oracle-on-azure-baremetal"></a>Funzionalità a disponibilità elevata per Oracle in Azure BareMetal

In questo articolo verranno trattate le principali funzionalità di disponibilità elevata e ripristino di emergenza di Oracle.

Oracle offre molte funzionalità per creare una piattaforma resiliente per l'esecuzione di database Oracle. Anche se nessuna singola funzionalità fornisce copertura per ogni tipo di errore, la combinazione di tecnologie a più livelli crea un sistema a disponibilità elevata. Non tutte le funzionalità sono necessarie per mantenere la disponibilità. La combinazione di strategie offre tuttavia la protezione migliore dall'ampia gamma di errori che si verificano. 

## <a name="flashback-database"></a>Flashback Database

La [funzionalità Database flashback](https://docs.oracle.com/en/database/oracle/oracle-database/21/rcmrf/FLASHBACK-DATABASE.html#GUID-584AC79A-40C5-45CA-8C63-DED3BE3A4511) è disponibile Oracle Database Enterprise Edition. Il database viene riavvolto in un momento specifico. Questa funzionalità è distinta da un ripristino temporato di Gestione ripristino [(RMAN)](https://docs.oracle.com/en/cloud/paas/db-backup-cloud/csdbb/performing-general-restore-and-recovery-operations.html) in quanto si riavvolge dal momento corrente, anziché in avanti dopo un ripristino. I tempi di completamento sono molto più rapidi.
 
È possibile usare questa funzionalità insieme a [Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590). Il database flashback consente a un amministratore del database di creare nuovamente un'istanza di un database non riuscito in una configurazione di Data Guard senza un ripristino e un ripristino RMAN completi. Questa funzionalità consente di ripristinare la funzionalità di ripristino di emergenza (ed eventuali vantaggi di creazione di report e backup scaricati con Active Data Guard) molto più velocemente.
 
È possibile usare questa funzionalità invece di un ripristino ritardato nel database di standby. Un database di standby può essere lampeggiato fino a un punto precedente a un problema.
 
Il Oracle Database mantiene i log flashback nell'area di ripristino rapido (FRA). Questi log sono separati dai log di ripetizione e richiedono più spazio all'interno della frazione di codice. Per impostazione predefinita, vengono conservati 24 ore di log di flashback, ma è possibile modificare questa impostazione in base alle esigenze.

## <a name="oracle-real-application-clusters"></a>Cluster di applicazioni reali Oracle

[Oracle Real Application Clusters (RAC)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92) consente a più server interconnessi di apparire come un unico servizio di database per gli utenti finali e le applicazioni. Questa funzionalità rimuove molti punti di errore ed è una soluzione attiva/attiva a disponibilità elevata riconosciuta per i database Oracle.

Come illustrato nella figura seguente di Panoramica e procedure consigliate per la disponibilità elevata di [Oracle,](https://docs.oracle.com/en/database/oracle/oracle-database/19/haovw/ha-features.html)al livello dell'applicazione viene presentato un singolo database RAC. Le applicazioni si connettono al listener SCAN, che indirizza il traffico a un'istanza di database specifica. RAC controlla l'accesso da più istanze per mantenere la coerenza dei dati tra nodi di calcolo separati.

![Diagramma che mostra una panoramica dell'architettura di Oracle RAC.](media/oracle-high-availability/oracle-real-application-clusters.png)

Se un'istanza ha esito negativo, il servizio continua in tutte le altre istanze rimanenti. Ogni database distribuito nella soluzione avrà una configurazione RAC n+1, dove n è la potenza di elaborazione minima necessaria per supportare il servizio.

Oracle Database servizi vengono usati per consentire il failover delle connessioni tra nodi quando un'istanza non riesce in modo trasparente. Tali errori possono essere pianificati o non pianificati. Uso dell'applicazione (eventi di notifica rapida dell'applicazione), quando un'istanza viene resa non disponibile, il servizio viene rilocato in un nodo esistente. Il servizio passa a un nodo specificato nella configurazione del servizio come preferito o disponibile.

Un'altra funzionalità chiave Oracle Database servizi è l'avvio di un servizio solo a seconda del ruolo. Questa funzionalità viene usata in caso di failover di Data Guard. Tutti i modelli distribuiti con Data Guard sono necessari per collegare un servizio di database a un ruolo di Data Guard.

Ad esempio, è possibile creare due servizi, MY \_ DB APP e MY DB \_ \_ \_ AS. Il servizio \_ APP MY DB viene avviato solo quando \_ l'istanza del database viene avviata con il ruolo Data Guard di PRIMARY. MY \_ DB AS viene avviato solo quando il ruolo Data Guard è PHYSICAL \_ \_ STANDBY. Questa configurazione consente alle applicazioni di puntare al servizio APP, oltre a creare report, che possono essere scaricate in Active Standby e \_ puntate al \_ servizio AS.

## <a name="oracle-data-guard"></a>Oracle Data Guard

Con Data Guard è possibile mantenere una copia identica di un database in hardware fisico separato. Idealmente, l'hardware deve essere separato geograficamente. Data Guard non pone limiti alla distanza, anche se la distanza ha un impatto sulle modalità di protezione. L'aumento della distanza aggiunge latenza tra i siti, che può causare la non più praticabile di alcune opzioni, ad esempio la replica sincrona.

Data Guard offre vantaggi rispetto alla replica a livello di archiviazione:

- Poiché la replica è in grado di riconoscere il database, viene replicato solo il traffico pertinente.
- Alcuni carichi di lavoro possono generare un input/output elevato in spazi tabelle temporanei, che non sono necessari in standby e quindi non vengono replicati.
- La convalida dei blocchi replicati viene eseguita nel database di standby, assicurando che i danneggiamenti fisici introdotti nel database primario non vengano replicati nel database standby.
- Impedisce danneggiamenti logici all'interno del blocco e danneggiamenti di scrittura persi. Elimina inoltre il rischio di errori commersi dagli amministratori di archiviazione dalla replica alla modalità standby.
Il ripristino può essere ritardato per un periodo predeterminato, quindi gli errori utente non vengono replicati immediatamente nella modalità standby.

## <a name="azure-netapp-files-snapshots"></a>Azure NetApp Files snapshot

La soluzione di archiviazione NetApp Files usata in BareMetal consente di creare snapshot di volumi. Gli snapshot consentono di ripristinare rapidamente un file system a un punto specifico nel tempo. Le tecnologie di snapshot consentono tempi RTO (Recovery Time Objective) che sono solo una frazione del tempo associato al ripristino di un backup del database.

La funzionalità snapshot per i database Oracle è disponibile tramite Azure NetApp SnapCenter. SnapCenter consente di pianificare e automatizzare la creazione e il ripristino di snapshot del volume.

## <a name="recovery-manager"></a>Gestione ripristino

Gestione ripristino (RMAN) è l'utilità preferita per l'esecuzione di backup fisici del database. RMAN interagisce con il file di controllo del database (o un catalogo di ripristino centralizzato) per proteggere i vari componenti principali del database, tra cui:

- File di dati del database
- Log di replica archiviati
- File di controllo del database
- File di inizializzazione del database (spfile)

RMAN consente di eseguire backup di database ad accesso caldo o a freddo. È possibile usare questi backup per creare database di standby o duplicare i database per clonare gli ambienti. RMAN ha anche una funzione di convalida del ripristino. Questa funzione legge un set di backup e determina se è possibile usarlo per ripristinare il database a un momento specifico.

Poiché RMAN è un'utilità fornita da Oracle, può leggere la struttura interna dei file di database. In questo modo è possibile eseguire controlli di danneggiamento fisico e logico durante le operazioni di backup e ripristino. È anche possibile ripristinare i file di dati del database e ripristinare singoli file di dati e spazi di tabella a un momento specifico. Questi sono i vantaggi offerti da RMAN rispetto agli snapshot di archiviazione. I backup RMAN offrono un'ultima linea di difesa contro la perdita completa di dati quando non è possibile usare gli snapshot.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle opzioni e le raccomandazioni per ottimizzare la protezione e le prestazioni che eseguono Oracle nell'infrastruttura BareMetal:

> [!div class="nextstepaction"]
> [Opzioni per i server Oracle BareMetal Infrastructure](options-considerations-high-availability.md)
