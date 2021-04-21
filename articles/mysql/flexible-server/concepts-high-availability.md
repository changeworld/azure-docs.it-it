---
title: Panoramica della disponibilità elevata con ridondanza della zona con il server flessibile di Database di Azure per MySQL
description: Informazioni sui concetti di disponibilità elevata con ridondanza della zona con il server flessibile di Database di Azure per MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 5b5e1491d7f76cd4cff76d0c9a1af4daa49fa483
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813003"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Concetti relativi alla disponibilità elevata nel server flessibile di Database di Azure per MySQL (anteprima)

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Server flessibile di Database di Azure per MySQL (anteprima) consente di configurare la disponibilità elevata con failover automatico usando l'opzione di disponibilità elevata **con ridondanza** della zona. Quando viene distribuito in una configurazione con ridondanza della zona, il server flessibile esegue automaticamente il provisioning e la gestione di una replica standby in una zona di disponibilità diversa. Usando la replica a  livello di archiviazione, i dati vengono replicati in modo sincrono nel server di standby nella zona secondaria per consentire la perdita di dati zero dopo un failover. Il failover è completamente trasparente dall'applicazione client e non richiede azioni dell'utente. Il server di standby non è disponibile per le operazioni di lettura o scrittura, ma è uno standby passivo per abilitare il failover rapido. I tempi di failover sono in genere da 60 a 120 secondi.

La configurazione della disponibilità elevata con ridondanza della zona consente il failover automatico durante gli eventi pianificati, ad esempio le operazioni di calcolo di scalabilità avviate dall'utente e gli eventi non pianificati, ad esempio gli errori hardware e software sottostanti, gli errori di rete e anche gli errori delle zone di disponibilità.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="visualizzazione della disponibilità elevata con ridondanza della zona":::

## <a name="zone-redundancy-architecture"></a>Architettura della ridondanza della zona

Il server primario viene distribuito nell'area e in una zona di disponibilità specifica. Quando si sceglie la disponibilità elevata, viene distribuito automaticamente un server di replica standby con la stessa configurazione del server primario, inclusi il livello di calcolo, le dimensioni di calcolo, le dimensioni di archiviazione e la configurazione di rete. I dati del log vengono replicati in modo sincrono nella replica di standby per garantire una perdita di dati pari a zero in qualsiasi situazione di errore. I backup automatici, sia snapshot che backup del log, vengono eseguiti dal server di database primario. 

L'integrità della ha viene monitorata e segnalata continuamente nella pagina di panoramica.

I vari stati di replica sono elencati di seguito:

| **Status** | **Descrizione** |
| :----- | :------ |
| <b>NotEnabled | La funzionalità di ha ridondanza della zona non è abilitata |
| <b>Creazione di un oggettoStandby | Nel processo di creazione di un nuovo standby |
| <b>Replica di dati | Dopo la creazione, la modalità standby viene recuperata con il server primario. |
| <b>FailOver | Il server di database sta per eseguire il failover dal server primario alla modalità standby. |
| <b>Healthy | La zona con ridondanza della zona è in stato integro e stabile. |
| <b>Rimozione diStandby | In base all'azione dell'utente, la replica standby è in corso di eliminazione.| 

## <a name="advantages"></a>Vantaggi

Di seguito sono riportati alcuni vantaggi per l'uso della funzionalità di ridondanza della zona: 

- La replica standby viene distribuita in una configurazione di macchina virtuale esatta come quella primaria, ad esempio vCore, archiviazione, impostazioni di rete (rete virtuale, firewall) e così via.
- Possibilità di rimuovere la replica standby disabilitando la disponibilità elevata.
- I backup automatici sono basati su snapshot, eseguiti dal server di database primario e archiviati in un archivio con ridondanza della zona.
- In caso di failover, il server flessibile di Database di Azure per MySQL viene automaticamente eseguito il failover nella replica di standby se è abilitata la disponibilità elevata. La configurazione a disponibilità elevata monitora il server primario e lo riporta online.
- I client si connettono sempre al server di database primario.
- Se si verifica un arresto anomalo del database o un errore del nodo, viene effettuato un primo tentativo di riavvio nello stesso nodo. In caso di errore, viene attivato il failover automatico.
- Possibilità di riavviare il server per prelevare eventuali modifiche ai parametri statici del server.

## <a name="steady-state-operations"></a>Operazioni con stato stabile

Le applicazioni vengono connesse al server primario usando il nome del server di database. Le informazioni sulla replica standby non vengono esposte per l'accesso diretto. I commit e le scritture vengono riconosciuti all'applicazione solo dopo che i file di log sono stati resi persistenti sia nel disco del server primario che nella replica standby in modo sincrono. A causa di questo requisito aggiuntivo di round trip, le applicazioni possono prevedere una latenza elevata per scritture e commit. È possibile monitorare l'integrità della disponibilità elevata nel portale.

## <a name="failover-process"></a>Processo di failover 
Per la continuità aziendale, è necessario disporre di un processo di failover per gli eventi pianificati e non pianificati. 

### <a name="planned-events"></a>Eventi pianificati

Gli eventi di tempo di inattività pianificati includono attività pianificate da Azure, ad esempio aggiornamenti software periodici, aggiornamenti delle versioni secondarie o avviati dai clienti, ad esempio le operazioni di calcolo e ridimensionamento dell'archiviazione. Tutte queste modifiche vengono prima applicate alla replica di standby. Durante questo periodo, le applicazioni continuano ad accedere al server primario. Dopo l'aggiornamento della replica di standby, le connessioni al server primario vengono svuotate, viene attivato un failover che attiva la replica di standby come replica primaria con lo stesso nome del server di database aggiornando il record DNS. Le connessioni client vengono disconnesse e devono riconnettersi e possono riprendere le operazioni. Viene stabilito un nuovo server di standby nella stessa zona del server primario precedente. Il tempo di failover complessivo dovrebbe essere di 60-120 s. 

>[!NOTE]
> In caso di operazione di ridimensionamento del calcolo, viene ridimensionato il server di replica secondaria seguito dal server primario. Non è coinvolto alcun failover.

### <a name="failover-process---unplanned-events"></a>Processo di failover - Eventi non pianificati
I tempi di inattività del servizio non pianificati includono bug software che o errori dell'infrastruttura, ad esempio calcolo, rete, errori di archiviazione o interruzioni dell'alimentazione, influiscono sulla disponibilità del database. In caso di invariabilità del database, la replica nella replica standby viene erta e la replica standby viene attivata come database primario. DNS viene aggiornato e i client si riconnettono quindi al server di database e riprendono le operazioni. Il tempo di failover complessivo dovrebbe richiedere 60-120 s. Tuttavia, a seconda dell'attività nel server di database primario al momento del failover, ad esempio transazioni di grandi dimensioni e tempo di ripristino, il failover potrebbe richiedere più tempo.

### <a name="forced-failover"></a>Failover forzato
Il failover forzato di Database di Azure per MySQL consente di forzare manualmente un failover, consentendo di testare la funzionalità con gli scenari dell'applicazione e di essere pronti in caso di interruzioni. Il failover forzato imposta il server di standby come server primario attivando un failover che attiva la replica di standby per diventare il server primario con lo stesso nome del server di database aggiornando il record DNS. Il server primario originale verrà riavviato e verrà passata alla replica di standby. Le connessioni client vengono disconnesse e devono essere riconnesse per riprendere le operazioni. A seconda del carico di lavoro corrente e dell'ultimo checkpoint, verrà misurato il tempo di failover complessivo. In generale, è previsto un valore compreso tra 60 e 120.

## <a name="schedule-maintenance-window"></a>Pianificazione della finestra di manutenzione 

I server flessibili offrono funzionalità di pianificazione della manutenzione in cui è possibile scegliere una finestra di 30 minuti in un giorno di preferenza durante il quale funziona la manutenzione di Azure, ad esempio l'applicazione di patch o gli aggiornamenti delle versioni secondarie. Per i server flessibili configurati con disponibilità elevata, queste attività di manutenzione vengono eseguite prima nella replica di standby. 

## <a name="point-in-time-restore"></a>Ripristino temporizzato 
Poiché il server flessibile è configurato nella disponibilità elevata replica in modo sincrono i dati, il server di standby è aggiornato con il server primario. Eventuali errori utente nel database primario, ad esempio un'eliminazione accidentale di una tabella o aggiornamenti non corretti, vengono replicati fedelmente nella modalità standby. Di conseguenza, non è possibile usare la modalità standby per eseguire il ripristino da tali errori logici. Per eseguire il ripristino da questi errori logici, è necessario eseguire il ripristino temporato all'ora precedente all'errore. Usando la funzionalità di ripristino tempormente flessibile del server, quando si ripristina il database configurato con disponibilità elevata, un nuovo server di database viene ripristinato come nuovo server flessibile con un nome fornito dall'utente. È quindi possibile esportare l'oggetto dal server di database e importarlo nel server di database di produzione. Analogamente, se si vuole clonare il server di database a scopo di test e sviluppo o si vuole eseguire il ripristino per altri scopi, è possibile scegliere il punto di ripristino più recente o un punto di ripristino personalizzato. L'operazione di ripristino crea un server flessibile a zona singola.

## <a name="mitigate-downtime"></a>Ridurre i tempi di inattività 
Quando non si usa la funzionalità di ha di ridondanza della zona, è comunque necessario essere in grado di ridurre i tempi di inattività per l'applicazione. I tempi di inattività del servizio di pianificazione, ad esempio le patch pianificate, gli aggiornamenti delle versioni secondarie o le operazioni avviate dall'utente, possono essere eseguiti come parte delle finestre di manutenzione pianificate. I tempi di inattività del servizio pianificati, ad esempio le patch pianificate, gli aggiornamenti delle versioni secondarie o le operazioni avviate dall'utente, ad esempio la scalabilità delle risorse di calcolo, comportano tempi di inattività. Per ridurre l'impatto dell'applicazione per le attività di manutenzione avviate da Azure, è possibile scegliere di pianificarle durante il giorno della settimana e l'ora che influisce meno sull'applicazione. 

Durante eventi di tempo di inattività non pianificati, ad esempio un arresto anomalo del database o un errore del server, il server in cui si è verificato l'errore viene riavviato all'interno della stessa zona. Anche se raramente, se l'intera zona è influenzata, è possibile ripristinare il database in un'altra zona all'interno dell'area. 

## <a name="things-to-know-with-zone-redundancy"></a>Aspetti da conoscere con la ridondanza della zona 

Di seguito sono riportate alcune considerazioni da tenere presenti quando si usa la disponibilità elevata con ridondanza della zona: 

- La disponibilità elevata può **essere impostata** solo durante l'ora di creazione del server flessibile.
- La disponibilità elevata non è supportata nel livello di calcolo con burst.
- A causa della replica sincrona in un'altra zona di disponibilità, il server di database primario può avere una latenza di scrittura e commit elevata.
- La replica standby non può essere usata per le query di sola lettura.
- A seconda dell'attività nel server primario al momento del failover, il completamento del failover potrebbe richiedere fino a 60-120 secondi o più.
- Il riavvio del server di database primario per prelevare le modifiche dei parametri statici riavvia anche la replica di standby.
- La configurazione delle repliche di lettura per i server a disponibilità elevata con ridondanza della zona non è supportata.
- La configurazione delle attività di gestione avviate dal cliente non può essere automatizzata durante la finestra di manutenzione gestita.
- Gli eventi pianificati, ad esempio il calcolo della scalabilità e gli aggiornamenti delle versioni secondarie, si verificano contemporaneamente sia in modalità primaria che in standby. 


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [continuità aziendale](./concepts-business-continuity.md)
- Informazioni sulla [disponibilità elevata con ridondanza della zona](./concepts-high-availability.md)
- Informazioni su [backup e ripristino](./concepts-backup-restore.md)
