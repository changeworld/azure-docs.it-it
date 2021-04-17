---
title: Opzioni o server Oracle BareMetal Infrastructure
description: Informazioni sulle opzioni e le considerazioni per i server Oracle BareMetal Infrastructure.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: e8a2dece11884e3a659f14b30bce51e7f0244924
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590305"
---
# <a name="options-for-oracle-baremetal-infrastructure-servers"></a>Opzioni per i server Oracle BareMetal Infrastructure

In questo articolo verranno considerate le opzioni e le raccomandazioni per ottenere il massimo livello di protezione e prestazioni che esegue Oracle nei server BareMetal Infrastructure. 

## <a name="data-guard-protection-modes"></a>Modalità di protezione di Data Guard

Data Guard offre protezione non disponibile esclusivamente tramite Oracle Real Applications Cluster (RAC), la replica logica (ad esempio GoldenGate) e la replica basata sull'archiviazione. 

| Modalità di protezione | Descrizione |
| --- | --- |
| **Prestazioni massime** | Modalità di protezione predefinita. Offre il massimo livello di protezione senza influire sulle prestazioni del database primario. I dati vengono considerati di cui è stato eseguito il commit non appena sono stati scritti nel flusso di ripristino del database primario. Viene quindi replicata nel database di standby in modo asincrono. In genere, il database di standby lo riceve entro pochi secondi, ma non viene data alcuna garanzia a tale effetto. Questa modalità soddisfa in genere i requisiti aziendali (insieme al monitoraggio del ritardo) senza la necessità di connettività di rete a bassa latenza tra i siti primario e di standby.<br /><br />Fornisce la migliore persistenza operativa; tuttavia non garantisce una perdita di dati pari a zero.   |
| **Disponibilità massima** | Offre il massimo livello di protezione senza influire sulla disponibilità del database primario. I dati non vengono mai considerati di cui è stato eseguito il commit nel database primario fino a quando non ne è stato eseguito anche il commit in almeno un database di standby. Se il database primario non è in grado di scrivere le modifiche di ripristino in almeno un database di standby, viene nuovamente attivata la modalità Prestazioni massime anziché diventare non disponibile. <br /><br />Consente al servizio di continuare se il sito di standby non è disponibile. Se un solo sito funziona, verrà mantenuta solo una copia dei dati fino a quando il secondo sito non sarà online e la sincronizzazione non verrà stabilita nuovamente. |
| **Protezione massima** | Fornisce un livello di protezione simile alla disponibilità massima. Il database primario viene arrestato con la funzionalità aggiunta se non è in grado di scrivere le modifiche di ripristino in almeno un database di standby. Ciò garantisce che non si verifichi la perdita di dati, ma a scapito di una disponibilità più debole. |

>[!IMPORTANT]
>Se è necessario un obiettivo del punto di ripristino (RPO) pari a zero, è consigliabile usare la configurazione Disponibilità massima. L'RPO può quindi essere garantito anche quando si verificano più errori. Ad esempio, anche in caso di interruzione della rete dal database primario seguito dalla perdita del database primario in un secondo momento, mentre l'interruzione della rete è ancora in vigore.

### <a name="data-guard-deployment-patterns"></a>Modelli di distribuzione di Data Guard

Oracle consente di configurare più destinazioni per la generazione del ripristino, consentendo più database standby. La configurazione più comune è illustrata nella figura seguente, un database di standby singolo in un'area diversa.

:::image type="content" source="media/oracle-high-availability/default-data-guard-deployment.png" alt-text="Diagramma che mostra la distribuzione predefinita di Data Guard di Oracle.":::

Data Guard è configurato in modalità Prestazioni massime per una distribuzione predefinita. Questa configurazione fornisce la near real-time dei dati tramite il trasporto di replica asincrono. Il database di standby non deve essere eseguito all'interno di una distribuzione RAC, ma è consigliabile che soddisfi le esigenze di prestazioni del sito primario.

È consigliabile una distribuzione simile a quella illustrata nella figura seguente per gli ambienti che richiedono tempi di attività rigorosi o un RPO pari a zero. La configurazione Disponibilità massima è costituita da un database di standby locale che applica il ripristino in modalità sincrona e da un secondo database standby in esecuzione in un'area remota.

:::image type="content" source="media/oracle-high-availability/max-availability-data-guard-deployment.png" alt-text="Diagramma che mostra la disponibilità massima della distribuzione di Data Guard.":::

È possibile creare un database di standby locale quando le prestazioni dell'applicazione subiranno eseguendo il database e i server applicazioni in aree separate. In questa configurazione viene usato un database di standby locale quando è necessaria una manutenzione pianificata o non pianificata nel cluster primario. È possibile eseguire questi database con la replica sincrona perché si verificano nella stessa area, assicurando che non si perda alcun dato tra di essi.

### <a name="data-guard-configuration-considerations"></a>Considerazioni sulla configurazione di Data Guard

Il broker di Data Guard deve essere implementato, perché semplifica l'implementazione di una configurazione di Data Guard e garantisce il rispetto delle procedure consigliate. Fornisce funzionalità di monitoraggio delle prestazioni e semplifica notevolmente le procedure di passaggio, failover e creazione di istanze.

Data Guard consente di eseguire un processo osservatore, che monitora tutti i database in una configurazione di Data Guard per determinare la disponibilità del database. Se si verifica un errore in un database primario, l'osservatore di Data Guard può avviare automaticamente un failover in un database di standby nella configurazione. È possibile implementare l'osservatore di Data Guard con più osservatori in base al numero di siti fisici (fino a tre). 

Questo osservatore deve trovarsi nell'infrastruttura che supporterà il livello applicazione. L'osservatore primario deve essere sempre presente nel sito fisico in cui non si trova il database primario. È consigliabile prestare attenzione nell'automazione delle operazioni di failover attivate da un osservatore di Data Guard. Assicurarsi prima di tutto che le applicazioni siano progettate e testate per fornire un servizio accettabile quando il database viene eseguito in una posizione separata.

Se l'applicazione è in grado di operare solo in locale, il failover nel sito secondario deve essere manuale. Gli ambienti che richiedono livelli di disponibilità elevati (99,99% o 99,999% di tempo di attività) devono usare sia un database locale che un database di standby remoto, come illustrato nella figura precedente. In questi casi, il parametro FastStartFailoverTarget verrà impostato solo sul database di standby locale.

Per tutte le applicazioni che supportano l'accesso tra siti/database, FastStartFailoverTarget è impostato su tutti i database di standby nella configurazione di Data Guard.

### <a name="active-data-guard"></a>Active Data Guard

Oracle Active Data Guard (ADG) è un superset di funzionalità di base di Data Guard incluse in Oracle Database Enterprise Edition. Offre le funzionalità seguenti aggiunte, che verranno usate nella distribuzione Oracle Exadata:

- Rilevamento di danneggiamento univoco e ripristino automatico.
- Failover rapido alla replica sincronizzata di produzione, manuale o automatica.
- Offload del carico di lavoro di produzione in uno standby sincronizzato aperto di sola lettura.
- Aggiornamenti in sequenza del database e standby. Prima di tutto l'applicazione di patch usando lo standby fisico.
- Eseguire l'offload dei backup incrementali in standby.
- Protezione del ripristino dei dati senza perdita di dati su qualsiasi distanza senza influire sulle prestazioni.

La white paper disponibile in [https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf](https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf) offre una buona panoramica delle funzionalità precedenti, come illustrato nella figura seguente.

:::image type="content" source="media/oracle-high-availability/active-data-guard-features.png" alt-text="Diagramma che mostra una panoramica delle funzionalità Active Data Guard di Oracle.":::

## <a name="backup-recommendations"></a>Raccomandazioni per il backup

Assicurarsi di eseguire il backup dei database. Usare le funzionalità di ripristino e ripristino per ripristinare un database nello stesso sistema o in un altro sistema o per ripristinare i file di database.

È importante creare una strategia di ripristino di backup per proteggere i Oracle Database appliance dalla perdita di dati. Tale perdita può essere causata da un problema fisico con un disco che causa un errore di lettura o scrittura in un file su disco necessario per eseguire il database. L'errore dell'utente può anche causare la perdita di dati. La funzionalità di backup consente di ripristinare tempormente il database, il ripristino **SCN (System Change Number)** e il ripristino più recente. È possibile creare criteri di backup nel browser Interfaccia utente o dall'interfaccia della riga di comando.

Sono disponibili le opzioni di backup seguenti:

- Eseguire il backup nel volume di archiviazione NFS (Area di ripristino rapido-FRA- /u98).
- Uso Azure NetApp Files SnapCenter - snapshot.

Processo da considerare:

- Backup manuali o automatici.
- I backup automatici vengono scritti nei volumi di archiviazione NFS (ad esempio/ u98).
- I backup vengono eseguiti tra le 12:00 e le 06:00 nel fuso orario del sistema di database.
- Periodi di conservazione presenti: 7, 15, 30, 45 e 60 giorni.

- Ripristinare il database da un backup archiviato nell'archivio oggetti:
  - All'ultimo stato valido noto con la perdita di dati minima possibile.
  - Utilizzo del timestamp specificato.
  - Uso del nome scn specificato.
  - BackupReport: _usa scn dal report di backup anziché scn specificato._

:::image type="content" source="media/oracle-high-availability/customer-db-backup-to-fra.png" alt-text="Diagramma che illustra il backup del database del cliente in FRA (/u98) e non FRA (/u95).":::

### <a name="backup-policy"></a>Criteri di backup

I criteri di backup definiscono i dettagli del backup. Quando si crea un criterio di backup, si definisce la destinazione per i backup del database FRA (posizione NFS) e si definisce la finestra di ripristino.

Per impostazione predefinita, viene utilizzato l'algoritmo di compressione BASIC. Quando si usano algoritmi di compressione LOW, MEDIUM o HIGH per i criteri di backup su disco o NFS, è necessario tenere presenti alcune considerazioni sulle licenze.

### <a name="backup-levels"></a>Livelli di backup

Specificare il livello di backup quando si esegue un backup.

- Livello 0 - Completo
- Livello 1 : incrementale
- LongTerm/Archievelog: ad eccezione dei criteri di conservazione dei backup, usare un percorso non FRA ,ad esempio /u95.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come ripristinare il database Oracle in caso di errore:

> [!div class="nextstepaction"]
> [Ripristinare il database Oracle nell'infrastruttura BareMetal di Azure](oracle-high-availability-recovery.md)
