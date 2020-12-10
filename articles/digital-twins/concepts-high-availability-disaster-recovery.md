---
title: Disponibilità elevata e ripristino di emergenza
titleSuffix: Azure Digital Twins
description: Descrive le funzionalità di Azure e dei dispositivi gemelli digitali di Azure che consentono di creare soluzioni di Azure per la disponibilità elevata con funzionalità di ripristino di emergenza.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 35f4aae246f105d832aaf92c5c5797c8a65b44f1
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938547"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Disponibilità elevata e ripristino di emergenza di Azure Digital Twins

Un'area di considerazione chiave per le soluzioni resilienti delle cose è la continuità aziendale e il ripristino di emergenza. La progettazione per la **disponibilità elevata** e **il ripristino di emergenza** consentono di definire e raggiungere gli obiettivi di tempo di esecuzione appropriati per la soluzione.

Questo articolo illustra le funzionalità di disponibilità elevata e ripristino di emergenza offerte specificamente dal servizio Azure Digital Twins.

I dispositivi gemelli digitali di Azure supportano queste opzioni di funzionalità:
* *Disponibilità elevata intra-Region* : ridondanza incorporata per garantire il tempo di esecuzione del servizio
* *Ripristino di emergenza tra aree* : failover in un'area geografica di Azure abbinata in caso di errore imprevisto Data Center

È anche possibile vedere la sezione [*procedure consigliate*](#best-practices) per indicazioni generali su Azure per la progettazione di disponibilità elevata/ripristino di emergenza.

## <a name="intra-region-ha"></a>Disponibilità elevata intra-area
 
I dispositivi gemelli digitali di Azure forniscono una disponibilità elevata all'interno dell'area implementando le ridondanze nel servizio. Questo problema si riflette nel [contratto di servizio del servizio](https://azure.microsoft.com/support/legal/sla/digital-twins) per il tempo di esecuzione. **Non sono necessarie altre operazioni da parte degli sviluppatori di una soluzione di i dispositivi gemelli digitali di Azure per sfruttare i vantaggi di queste funzionalità di disponibilità elevata.** Anche se i dispositivi gemelli digitali di Azure offrono una garanzia di tempo di esecuzione ragionevolmente elevata, è possibile che si verifichino errori temporanei, come per qualsiasi piattaforma di elaborazione distribuita. I criteri di ripetizione dei tentativi appropriati devono essere incorporati nei componenti che interagiscono con un'applicazione cloud per gestire gli errori temporanei.

## <a name="cross-region-dr"></a>Ripristino di emergenza tra aree

È possibile che si verifichino rari casi in cui un data center presenta interruzioni estese a causa di interruzioni dell'alimentazione o di altri eventi nell'area. Tali eventi sono rari e, durante tali errori, la funzionalità di disponibilità elevata di tipo intra Region descritta sopra potrebbe non essere utile. Azure Digital gemelli risolve questo problema con il failover avviato da Microsoft.

Il **failover avviato da Microsoft** viene eseguito da Microsoft in rari casi per eseguire il failover di tutte le istanze di Azure Digital Twins da un'area interessata all'area geografica corrispondente. Questo processo è un'opzione predefinita (senza alcun modo per consentire agli utenti di rifiutare esplicitamente) e non richiede alcun intervento da parte dell'utente. Microsoft si riserva il diritto di effettuare una determinazione di quando questa opzione viene esercitata. Questo meccanismo non implica il consenso dell'utente prima del failover dell'istanza dell'utente.

>[!NOTE]
> Alcuni servizi di Azure forniscono anche un'opzione aggiuntiva denominata **failover avviato dal cliente**, che consente ai clienti di avviare un failover solo per la propria istanza, ad esempio per eseguire un'esercitazione sul ripristino di emergenza. Questo meccanismo non è attualmente **supportato** dai dispositivi gemelli digitali di Azure. 

## <a name="monitor-service-health"></a>Monitorare l'integrità dei servizi

Quando si esegue il failover e il ripristino delle istanze dei dispositivi gemelli digitali di Azure, è possibile monitorare il processo usando lo strumento di [integrità dei servizi di Azure](https://docs.microsoft.com/azure/service-health/service-health-overview) . L'integrità del servizio tiene traccia dell'integrità dei servizi di Azure in diverse aree e sottoscrizioni e condivide le comunicazioni che interessano il servizio in caso di interruzioni e tempi di inattività.

Durante un evento di failover, l'integrità del servizio può indicare quando il servizio è inattivo e quando viene eseguito il backup.

Per visualizzare gli eventi di integrità del servizio...
1. Passare a [integrità del servizio](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) nel portale di Azure (è possibile usare questo collegamento o cercarlo tramite la barra di ricerca del portale).
1. Usare il menu a sinistra per passare alla pagina *cronologia integrità* .
1. Cercare un *nome di problema* che inizia con i dispositivi **gemelli digitali di Azure** e selezionarlo.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="Screenshot della portale di Azure che mostra la pagina Cronologia integrità. È presente un elenco di diversi problemi degli ultimi giorni e viene evidenziato un problema denominato &quot;Azure Digital Twins-Europa occidentale-mitigato&quot;." lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. Per informazioni generali sull'interruzione, vedere la scheda *Riepilogo* .

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="Nella pagina Cronologia integrità la scheda Riepilogo è evidenziata. La scheda Visualizza informazioni generali, ad esempio la risorsa interessata, la relativa area e la relativa sottoscrizione." lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. Per ulteriori informazioni e aggiornamenti sul problema nel corso del tempo, vedere la scheda *Issue Updates* .

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="Nella pagina Cronologia integrità la scheda aggiornamenti problema è evidenziata. Nella scheda vengono visualizzate diverse voci che mostrano lo stato corrente di un giorno fa." lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


Si noti che le informazioni visualizzate in questo strumento non sono specifiche di un'istanza digitale di Azure. Dopo aver usato l'integrità del servizio per capire cosa accade con il servizio Azure Digital Twins in una determinata area o sottoscrizione, è possibile eseguire il monitoraggio di un ulteriore passaggio usando lo [strumento integrità risorse](troubleshoot-resource-health.md) per eseguire il drill-down in istanze specifiche e verificare se sono interessate.

## <a name="best-practices"></a>Procedure consigliate

Per le procedure consigliate per la disponibilità elevata/ripristino di emergenza, vedere le seguenti indicazioni su Azure per questo argomento: 
* L'articolo [*informazioni tecniche sulla continuità aziendale di Azure*](/azure/architecture/framework/resiliency/overview) descrive un framework generale che consente di valutare la continuità aziendale e il ripristino di emergenza. 
* Il documento sul [*ripristino di emergenza e la disponibilità elevata per le applicazioni Azure*](/azure/architecture/framework/resiliency/backup-and-recovery) fornisce indicazioni sull'architettura sulle strategie per le applicazioni Azure per ottenere la disponibilità elevata (ha) e il ripristino di emergenza.

## <a name="next-steps"></a>Passaggi successivi 

Scopri di più su come iniziare a usare le soluzioni di dispositivi gemelli digitali di Azure:
 
* [*Cos'è il servizio Gemelli digitali di Azure?*](overview.md)
* [*Guida introduttiva: esplorare uno scenario di esempio*](quickstart-adt-explorer.md)