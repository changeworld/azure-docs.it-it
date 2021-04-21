---
title: Modifiche importanti previste per il Centro sicurezza di Azure
description: Prossime modifiche al Centro sicurezza di Azure che può essere opportuno conoscere e per le quali potrebbe essere necessaria una pianificazione
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/20/2021
ms.author: memildin
ms.openlocfilehash: 3307d3aed422c3eab63412388244ef14ef3be699
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750996"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Modifiche importanti che interesseranno il Centro sicurezza di Azure

> [!IMPORTANT]
> Le informazioni contenute in questa pagina si riferiscono a prodotti o funzionalità non definitivi che possono subire modifiche sostanziali prima dell'eventuale rilascio della versione commerciale. Microsoft non rilascia alcuna garanzia, espressa o implicita, in merito alle informazioni fornite in questa pagina.

Questa pagina descrive le modifiche pianificate per il Centro sicurezza. Illustra le modifiche al prodotto previste che potrebbero influire su elementi come il punteggio di sicurezza o i flussi di lavoro.

Se si cercano le note sulla versione più recenti, vedere [Novità del Centro sicurezza di Azure](release-notes.md).


## <a name="planned-changes"></a>Modifiche pianificate

| Modifica pianificata                                                                                                                                                        | Data stimata per la modifica |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [21 raccomandazioni che si spostano tra i controlli di sicurezza](#21-recommendations-moving-between-security-controls)                                                           | Aprile 2021                |
| [Due raccomandazioni del controllo di sicurezza "Applica aggiornamenti di sistema" sono deprecate](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | Aprile 2021                |
| [L'implementazione legacy di ISO 27001 viene sostituita con il nuovo ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)          | Giugno 2021                 |
| [Le raccomandazioni di AWS verranno rilasciate per la disponibilità generale](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | **Agosto** 2021           |
| [Miglioramenti alla raccomandazione di classificazione dei dati SQL](#enhancements-to-sql-data-classification-recommendation)                                                     | Q2 2021                   |
|                                                                                                                                                                       |                           |


### <a name="21-recommendations-moving-between-security-controls"></a>21 raccomandazioni che si spostano tra i controlli di sicurezza 

**Data stimata per la modifica:** Aprile 2021

Le raccomandazioni seguenti vengono spostate in un controllo di sicurezza diverso. I controlli di sicurezza sono gruppi logici di raccomandazioni di sicurezza correlate e riflettono le superfici di attacco vulnerabili. Questo spostamento garantisce che ognuna di queste raccomandazioni sia nel controllo più appropriato per soddisfare l'obiettivo. 

Per informazioni sulle raccomandazioni disponibili in ogni controllo di sicurezza, vedere Controlli di sicurezza e relative raccomandazioni.

|Raccomandazione |Modifica e impatto  |
|---------|---------|
|La valutazione delle vulnerabilità deve essere abilitata nei server SQL<br>La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL<br>È consigliabile correggere nuove vulnerabilità nei database SQL<br>Le vulnerabilità nei database SQL delle macchine virtuali devono essere corrette     |Passaggio dalla correzione delle vulnerabilità (vale 6 punti)<br>per correggere le configurazioni di sicurezza (vale 4 punti).<br>A seconda dell'ambiente, queste raccomandazioni avranno un impatto ridotto sul punteggio.|
|Alla sottoscrizione deve essere assegnato più di un proprietario<br>Le variabili dell'account di automazione devono essere crittografate<br>Dispositivi IoT - Il processo di controllo ha interrotto l'invio di eventi<br>Dispositivi IoT - Errore di convalida della baseline del sistema operativo<br>Dispositivi IoT: è necessario l'aggiornamento della suite di crittografia TLS<br>Dispositivi IoT - Aprire le porte nel dispositivo<br>Dispositivi IoT: sono stati trovati criteri firewall permissivi in una delle catene<br>Dispositivi IoT: è stata trovata una regola del firewall permissiva nella catena di input<br>Dispositivi IoT: è stata trovata una regola del firewall permissiva nella catena di output<br>È consigliabile abilitare i log di diagnostica nell'hub IoT<br>Dispositivi IoT - Invio di messaggi sottoutilizzati da parte dell'agente<br>Dispositivi IoT: i criteri di filtro IP predefiniti devono essere Nega<br>Dispositivi IoT - Intervallo di indirizzi IP di grandi dimensioni della regola del filtro IP<br>Dispositivi IoT: è consigliabile regolare gli intervalli e le dimensioni dei messaggi dell'agente<br>Dispositivi IoT - Credenziali di autenticazione identiche<br>Dispositivi IoT - Il processo di controllo ha interrotto l'invio di eventi<br>Dispositivi IoT: la configurazione di base del sistema operativo deve essere corretta|Passaggio a **Implementare le procedure consigliate per la sicurezza**.<br>Quando una raccomandazione passa al controllo di sicurezza Implementa procedure consigliate per la sicurezza, che non vale alcun punto, la raccomandazione non influisce più sul punteggio di sicurezza.|
|||


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Due raccomandazioni del controllo di sicurezza "Applica aggiornamenti del sistema" sono deprecate

**Data stimata per la modifica:** Aprile 2021

Le due raccomandazioni seguenti sono deprecate:

- La versione del sistema operativo deve essere aggiornata per i ruoli del servizio **cloud:** per impostazione predefinita, Azure aggiorna periodicamente il sistema operativo guest all'immagine supportata più recente all'interno della famiglia di sistemi operativi specificata nella configurazione del servizio (con estensione cscfg), ad esempio Windows Server 2016.
- **Kubernetes Services** deve essere aggiornato a una versione kubernetes non vulnerabile. Le valutazioni di questa raccomandazione non sono così ampie come si vuole che siano. La versione corrente di questa raccomandazione verrà infine sostituita con una versione avanzata più allineata alle esigenze di sicurezza del cliente.


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>L'implementazione legacy di ISO 27001 viene sostituita con il nuovo ISO 27001:2013

L'implementazione legacy di ISO 27001 verrà rimossa dal dashboard di conformità alle normative del Centro sicurezza. Se si sta verificando la conformità ISO 27001 con il Centro sicurezza, eseguire l'onboardimento del nuovo standard ISO 27001:2013 per tutti i gruppi di gestione o le sottoscrizioni rilevanti e l'ISO 27001 legacy corrente verrà presto rimosso dal dashboard.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Dashboard di conformità alle normative del Centro sicurezza che mostra il messaggio sulla rimozione dell'implementazione legacy di ISO 27001." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Le raccomandazioni di AWS verranno rilasciate per la disponibilità generale (GA)

**Data stimata per la modifica:** Agosto 2021

Il Centro sicurezza di Azure protegge i carichi di lavoro in Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

Le raccomandazioni provenienti dall'hub di sicurezza AWS sono state in anteprima dopo l'introduzione dei connettori cloud. Le raccomandazioni  contrassegnate come anteprima non sono incluse nei calcoli del punteggio di sicurezza, ma devono comunque essere corretti laddove possibile, in modo che al termine del periodo di anteprima contribuiscano al punteggio.

Con questa modifica, due set di raccomandazioni AWS verranno spostati a ga:

- [Controlli PCI DSS sicurezza](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Controlli CIS AWS Foundations Benchmark dell'hub di sicurezza](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Quando si tratta di una valutazione globale e le valutazioni vengono eseguite sulle risorse AWS, i risultati incideranno sul punteggio di sicurezza combinato per tutte le risorse cloud multi e ibrido.



### <a name="enhancements-to-sql-data-classification-recommendation"></a>Miglioramenti alla raccomandazione per la classificazione dei dati SQL

**Data stimata per la modifica:** Q2 2021

La **raccomandazione Dati** sensibili nei database  SQL deve essere classificata nel controllo di sicurezza Applica classificazione dei dati verrà sostituita con una nuova versione più allineata alla strategia di classificazione dei dati di Microsoft. Di conseguenza, anche l'ID della raccomandazione cambierà (attualmente è b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Passaggi successivi

Per tutte le modifiche recenti al prodotto, vedere [Novità del Centro sicurezza di Azure](release-notes.md).