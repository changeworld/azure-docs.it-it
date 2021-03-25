---
title: Panoramica della disponibilità elevata con ridondanza della zona con il server flessibile database di Azure per MySQL
description: Informazioni sui concetti relativi alla disponibilità elevata con ridondanza della zona con il server flessibile di database di Azure per MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 6629beacb5c3edc6fe1d21509051b915c0894479
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109693"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Concetti relativi alla disponibilità elevata nel server flessibile database di Azure per MySQL (anteprima)

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Il server flessibile database di Azure per MySQL (anteprima) consente di configurare la disponibilità elevata con failover automatico usando l'opzione di disponibilità elevata con **ridondanza della zona** . Quando viene distribuito in una configurazione con ridondanza della zona, il server flessibile esegue automaticamente il provisioning e la gestione di una replica standby in una zona di disponibilità diversa. Usando la replica a livello di archiviazione, i dati vengono **replicati in modo sincrono** nel server di standby nella zona secondaria per consentire la perdita di dati dopo un failover. Il failover è completamente trasparente dall'applicazione client e non richiede alcuna azione da parte dell'utente. Il server di standby non è disponibile per le operazioni di lettura o scrittura, ma è un standby passivo per abilitare il failover rapido. I tempi di failover variano in genere da 60-120 secondi.

La configurazione della disponibilità elevata con ridondanza della zona consente il failover automatico durante gli eventi pianificati, ad esempio le operazioni di calcolo con scalabilità avviata dall'utente e gli eventi non pianificati, ad esempio errori hardware e software sottostanti, errori di rete e anche errori della zona di disponibilità.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="visualizzazione della disponibilità elevata con ridondanza della zona":::

## <a name="zone-redundancy-architecture"></a>Architettura di ridondanza della zona

Il server primario viene distribuito nell'area e in una zona di disponibilità specifica. Quando si sceglie la disponibilità elevata, un server di replica standby con la stessa configurazione del server primario viene distribuito automaticamente, inclusi il livello di calcolo, le dimensioni di calcolo, le dimensioni di archiviazione e la configurazione di rete. I dati di log vengono replicati in modo sincrono nella replica di standby per evitare la perdita di dati in qualsiasi situazione di errore. I backup automatici, sia gli snapshot che i backup del log, vengono eseguiti dal server di database primario. 

L'integrità della disponibilità elevata viene monitorata e segnalata continuamente nella pagina panoramica.

Di seguito sono elencati i vari Stati di replica:

| **Status** | **Descrizione** |
| :----- | :------ |
| <b>Notenated | La disponibilità elevata con ridondanza della zona non è abilitata |
| <b>CreatingStandby | Procedura di creazione di un nuovo standby |
| <b>ReplicatingData | Una volta creata la modalità standby, viene rilevata con il server primario. |
| <b>FailingOver | È in corso il failover del server di database dalla replica primaria alla modalità standby. |
| <b>Healthy | La disponibilità elevata con ridondanza della zona è in stato stabile e integro. |
| <b>RemovingStandby | In base all'azione dell'utente, la replica standby è in fase di eliminazione.| 

## <a name="advantages"></a>Vantaggi

Di seguito sono riportati alcuni vantaggi per l'uso della funzionalità di ridondanza della zona: 

- La replica di standby viene distribuita in una configurazione di macchina virtuale identica a quella del database primario, ad esempio Vcore, archiviazione, impostazioni di rete (VNET, firewall) e così via.
- Possibilità di rimuovere la replica standby disabilitando la disponibilità elevata.
- I backup automatici sono basati su snapshot, eseguiti dal server di database primario e archiviati in una risorsa di archiviazione con ridondanza della zona.
- In caso di failover, il database di Azure per MySQL flexible server esegue automaticamente il failover alla replica standby se è abilitata la disponibilità elevata. Il programma di installazione a disponibilità elevata monitora il server primario e lo riporta online.
- I client si connettono sempre al server di database primario.
- Se si verifica un arresto anomalo del database o un errore del nodo, il riavvio viene tentato prima nello stesso nodo. Se l'operazione ha esito negativo, viene attivato il failover automatico.
- Possibilità di riavviare il server per rilevare eventuali modifiche ai parametri del server statico.

## <a name="steady-state-operations"></a>Operazioni di stato stazionario

Le applicazioni sono connesse al server primario utilizzando il nome del server di database. Le informazioni sulla replica standby non vengono esposte per l'accesso diretto. I commit e le scritture vengono riconosciuti all'applicazione solo dopo che i file di log sono stati salvati in modo sincrono sia sul disco del server primario che sulla replica standby. A causa di questo requisito di round trip aggiuntivo, le applicazioni possono prevedere una latenza elevata per scritture e commit. È possibile monitorare l'integrità della disponibilità elevata nel portale.

## <a name="failover-process"></a>Processo di failover 
Per la continuità aziendale, è necessario disporre di un processo di failover per gli eventi pianificati e non pianificati. 

### <a name="planned-events"></a>Eventi pianificati

Gli eventi di tempo di inattività pianificato includono attività pianificate da Azure, ad esempio aggiornamenti periodici del software, aggiornamenti della versione secondaria o avviati da clienti come la scalabilità delle operazioni di archiviazione e scalabilità. Tutte queste modifiche vengono prima applicate alla replica standby. Durante tale periodo, le applicazioni continuano ad accedere al server primario. Una volta aggiornata la replica di standby, le connessioni al server primario vengono svuotate, viene attivato un failover che attiva la replica standby come primaria con lo stesso nome del server di database aggiornando il record DNS. Le connessioni client sono disconnesse e devono essere riconnesse e possono riprendere le operazioni. Un nuovo server di standby viene stabilito nella stessa zona del database primario precedente. Si prevede che il tempo di failover complessivo sia 60-120 s. 

>[!NOTE]
> Nel caso dell'operazione di ridimensionamento del calcolo, viene scalato il server di replica secondaria seguito dal server primario. Nessun failover è necessario.

### <a name="failover-process---unplanned-events"></a>Processo di failover-eventi non pianificati
I tempi di inattività del servizio non pianificato includono bug software che o errori dell'infrastruttura, ad esempio calcolo, rete, errori di archiviazione o interruzioni dell'alimentazione, influiscano sulla disponibilità del database. In caso di indisponibilità del database, la replica alla replica di standby viene interrotta e la replica standby viene attivata come database primario. DNS viene aggiornato e i client riconnettono al server di database e riprendono le operazioni. Il tempo di failover complessivo dovrebbe richiedere 60-120 s. Tuttavia, a seconda dell'attività nel server di database primario al momento del failover, ad esempio transazioni di grandi dimensioni e tempi di ripristino, il failover potrebbe richiedere più tempo.

## <a name="schedule-maintenance-window"></a>Pianifica finestra di manutenzione 

I server flessibili offrono funzionalità di pianificazione della manutenzione in cui è possibile scegliere un intervallo di 30 minuti in un giorno di preferenza durante il quale il funzionamento della manutenzione di Azure, ad esempio l'applicazione di patch o gli aggiornamenti della versione secondaria. Per i server flessibili configurati con la disponibilità elevata, queste attività di manutenzione vengono eseguite prima sulla replica standby. 

## <a name="point-in-time-restore"></a>Ripristino temporizzato 
Poiché il server flessibile è configurato in disponibilità elevata, replica in modo sincrono i dati, il server di standby è aggiornato con quello primario. Tutti gli errori dell'utente nel database primario, ad esempio l'eliminazione accidentale di una tabella o di aggiornamenti non corretti, vengono replicati fedelmente in modalità standby. Di conseguenza, non è possibile utilizzare la modalità standby per il ripristino da errori logici. Per risolvere questi errori logici, è necessario eseguire il ripristino temporizzato fino a quando si è verificato l'errore. Grazie alla funzionalità di ripristino temporizzato del server flessibile, quando si ripristina il database configurato con la disponibilità elevata, un nuovo server di database viene ripristinato come nuovo server flessibile con un nome fornito dall'utente. È quindi possibile esportare l'oggetto dal server di database e importarlo nel server di database di produzione. Analogamente, se si desidera clonare il server di database a scopo di test e sviluppo oppure si desidera eseguire il ripristino per altri scopi, è possibile scegliere il punto di ripristino più recente o un punto di ripristino personalizzato. L'operazione di ripristino consente di creare un server con una singola zona flessibile.

## <a name="mitigate-downtime"></a>Attenuazione del tempo di inattività 
Quando non si usa la funzionalità di disponibilità elevata con ridondanza della zona, è comunque necessario essere in grado di ridurre i tempi di inattività per l'applicazione. È possibile pianificare i tempi di inattività del servizio, ad esempio le patch pianificate, gli aggiornamenti delle versioni secondarie o le operazioni avviate dall'utente come parte delle finestre di manutenzione pianificate. I tempi di inattività del servizio pianificati, ad esempio le patch pianificate, gli aggiornamenti delle versioni secondarie o le operazioni avviate dall'utente, ad esempio il calcolo della scala, Per attenuare l'effetto dell'applicazione per le attività di manutenzione avviate da Azure, è possibile scegliere di pianificarle durante il giorno della settimana e l'ora che meno influisca sull'applicazione. 

Durante gli eventi di inattività non pianificati, ad esempio l'arresto anomalo del database o l'errore del server, il server interessato viene riavviato all'interno della stessa zona. Sebbene raro, se l'intera zona è interessata, è possibile ripristinare il database in un'altra area all'interno dell'area. 

## <a name="things-to-know-with-zone-redundancy"></a>Informazioni importanti con la ridondanza della zona 

Di seguito sono riportate alcune considerazioni da tenere presenti quando si usa la disponibilità elevata di ridondanza della zona: 

- La disponibilità elevata può essere impostata **solo** durante l'ora di creazione flessibile del server.
- La disponibilità elevata non è supportata nel livello di calcolo in sequenza.
- A causa della replica sincrona in un'altra zona di disponibilità, il server di database primario può riscontrare una latenza di scrittura e commit elevata.
- Non è possibile usare la replica standby per le query di sola lettura.
- A seconda dell'attività del server primario al momento del failover, il completamento del failover potrebbe richiedere fino a 60-120 secondi o più.
- Il riavvio del server di database primario per la selezione delle modifiche dei parametri statici consente inoltre di riavviare la replica standby.
- La configurazione delle repliche di lettura per i server a disponibilità elevata con ridondanza della zona non è supportata.
- Non è possibile automatizzare la configurazione delle attività di gestione avviate dal cliente durante la finestra di manutenzione gestita.
- Gli eventi pianificati, ad esempio la scalabilità delle risorse di calcolo e di aggiornamento della versione secondaria, vengono eseguiti contemporaneamente in modalità primaria e standby. 


## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sulla [continuità aziendale](./concepts-business-continuity.md)
- Informazioni sulla [disponibilità elevata con ridondanza della zona](./concepts-high-availability.md)
- Informazioni su [backup e ripristino](./concepts-backup-restore.md)
