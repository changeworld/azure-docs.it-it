---
title: Disponibilità elevata e ripristino di emergenza
titleSuffix: Azure Digital Twins
description: Vengono descritte le funzionalità di Azure e Gemelli digitali di Azure che consentono di creare soluzioni Azure IoT a disponibilità elevata con funzionalità di ripristino di emergenza.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 41edef58910fe2b772831ef083e5aca8bb52a321
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482269"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Gemelli digitali di Azure disponibilità elevata e ripristino di emergenza

Un'area chiave da considerare per le soluzioni IoT resilienti è la continuità aziendale e il ripristino di emergenza. La progettazione **per disponibilità elevata e** ripristino di emergenza **consente** di definire e raggiungere gli obiettivi di tempo di attività appropriati per la soluzione.

Questo articolo illustra le funzionalità di ripristino di emergenza e di ripristino di emergenza offerte in modo specifico dal Gemelli digitali di Azure di ripristino di emergenza.

Gemelli digitali di Azure supporta queste opzioni di funzionalità:
* *Ha all'interno* dell'area: ridondanza incorporata per offrire il tempo di attività del servizio
* *Ripristino di emergenza tra* aree: failover in un'area di Azure con associazione geografica in caso di errore data center imprevisto

È anche possibile vedere la [*sezione Procedure consigliate per*](#best-practices) indicazioni generali di Azure sulla progettazione per la distribuzione a lungo/ripristino di emergenza.

## <a name="intra-region-ha"></a>Disponibilità elevata intra-area
 
Gemelli digitali di Azure fornisce a livello di codice a lungo disponibile all'interno dell'area implementando gli esuberi all'interno del servizio. Ciò si riflette nel contratto di [servizio per](https://azure.microsoft.com/support/legal/sla/digital-twins) il tempo di attività. **Gli sviluppatori di una soluzione Gemelli digitali di Azure per sfruttare queste funzionalità a lungo termine non sono necessarie altre operazioni.** Sebbene Gemelli digitali di Azure garantisca tempi di attività ragionevolmente elevati, è comunque possibile aspettarsi errori temporanei, come per qualsiasi piattaforma di elaborazione distribuita. I criteri di ripetizione appropriati devono essere incorporati nei componenti che interagiscono con un'applicazione cloud per gestire gli errori temporanei.

## <a name="cross-region-dr"></a>Ripristino di emergenza tra aree

Possono verificarsi alcune rare situazioni in cui data center si verificano interruzioni estese a causa di interruzioni dell'alimentazione o di altri eventi nell'area. Questi eventi sono rari e, durante questi errori, la funzionalità a livello di a lungo periodo descritta in precedenza potrebbe non essere utile. Gemelli digitali di Azure risolve questo problema con il failover avviato da Microsoft.

**Il failover avviato da Microsoft** viene eseguito da Microsoft in rare situazioni per eseguire il failover di tutte le istanze di Gemelli digitali di Azure da un'area interessata all'area geografica abbinata corrispondente. Questo processo è un'opzione predefinita (senza alcun modo per consentire agli utenti di rifiutare esplicitamente) e non richiede alcun intervento da parte dell'utente. Microsoft si riserva il diritto di effettuare una determinazione di quando questa opzione viene esercitata. Questo meccanismo non implica il consenso dell'utente prima del passaggio dell'istanza dell'utente.

>[!NOTE]
> Alcuni servizi di Azure offrono anche un'opzione aggiuntiva denominata **failover** avviato dal cliente, che consente ai clienti di avviare un failover solo per la propria istanza, ad esempio per eseguire un'esercitazione sul ripristino di emergenza. Questo meccanismo non è **attualmente supportato da** Gemelli digitali di Azure. 

## <a name="monitor-service-health"></a>Monitorare l'integrità dei servizi

Quando Gemelli digitali di Azure istanze vengono ripristinate, è possibile monitorare il processo usando lo [strumento di integrità dei servizi di Azure.](../service-health/service-health-overview.md) Integrità dei servizi tiene traccia dell'integrità dei servizi di Azure tra aree e sottoscrizioni diverse e condivide le comunicazioni con impatto sui servizi su interruzioni e tempi di inattività.

Durante un evento di failover, Integrità dei servizi può indicare quando il servizio non è disponibile e quando è in esecuzione il backup.

Per visualizzare gli eventi di Integrità dei servizi...
1. Passare a [Integrità dei servizi](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) nella portale di Azure (è possibile usare questo collegamento o cercarlo usando la barra di ricerca del portale).
1. Usare il menu a sinistra per passare alla *pagina Cronologia* integrità.
1. Cercare un Nome *problema che* inizia **con Gemelli digitali di Azure** e selezionarlo.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="Screenshot dell'portale di Azure che mostra la pagina Cronologia integrità. È disponibile un elenco di diversi problemi degli ultimi giorni ed è evidenziato un problema denominato &quot;Gemelli digitali di Azure - Europa occidentale - Mitigato&quot;." lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. Per informazioni generali sull'interruzione, visualizzare la *scheda* Riepilogo.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="Nella pagina Cronologia integrità la scheda Riepilogo è evidenziata. La scheda visualizza informazioni generali, ad esempio la risorsa interessata, la relativa area e la relativa sottoscrizione." lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. Per altre informazioni e aggiornamenti sul problema nel tempo, vedere la *scheda Aggiornamenti del* problema.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="Nella pagina Cronologia integrità la scheda Aggiornamenti del problema è evidenziata. La scheda visualizza diverse voci che mostrano lo stato corrente di un giorno fa." lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


Si noti che le informazioni visualizzate in questo strumento non sono specifiche di un'istanza di Azure Digital. Dopo aver utilizzato Integrità dei servizi per comprendere cosa succede con il servizio Gemelli digitali di Azure in una determinata [](troubleshoot-resource-health.md) area o sottoscrizione, è possibile eseguire un ulteriore monitoraggio usando lo strumento Integrità risorse per eseguire il drill-down in istanze specifiche e verificare se sono influenzate.

## <a name="best-practices"></a>Procedure consigliate

Per le procedure consigliate per la distribuzione a lungo/ripristino di emergenza, vedere le linee guida di Azure seguenti in questo argomento: 
* L'articolo [*Linee guida tecniche sulla continuità*](/azure/architecture/framework/resiliency/overview) aziendale di Azure descrive un framework generale che consente di pensare alla continuità aziendale e al ripristino di emergenza. 
* Il [*documento Ripristino di emergenza e disponibilità elevata*](/azure/architecture/framework/resiliency/backup-and-recovery) per le applicazioni di Azure fornisce indicazioni sull'architettura sulle strategie per le applicazioni di Azure per ottenere disponibilità elevata e ripristino di emergenza.

## <a name="next-steps"></a>Passaggi successivi 

Altre informazioni su come iniziare a usare Gemelli digitali di Azure soluzioni:
 
* [*Cos'è il servizio Gemelli digitali di Azure?*](overview.md)
* [*Guida introduttiva: Esplorare uno scenario di esempio*](quickstart-azure-digital-twins-explorer.md)