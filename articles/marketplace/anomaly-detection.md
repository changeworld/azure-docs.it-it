---
title: Rilevamento di anomalie per la fatturazione a consumo | Azure Marketplace
description: Scopri come il rilevamento automatico delle anomalie per la fatturazione a consumo aiuta a garantire che i tuoi clienti vengano fatturati correttamente per l'utilizzo a consumo dell'offerta del Marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 1/09/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: d4fb88854359dcd6e383b47d2a8ce4e9c91f867a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989466"
---
# <a name="anomaly-detection-for-metered-billing"></a>Rilevamento delle anomalie per la fatturazione a consumo

Questo articolo fornisce informazioni dettagliate sul servizio di misurazione del Marketplace e la funzionalità di rilevamento automatico delle anomalie associata per garantire che i clienti vengano fatturati correttamente per l'utilizzo a consumo. L'opzione di fatturazione a consumo è attualmente disponibile per le offerte SaaS ( [software as a Service](plan-saas-offer.md) ) e per [le applicazioni Azure](plan-azure-application-offer.md#types-of-plans) con un piano di applicazione gestito. Questa opzione consente ai partner di creare offerte nel programma commerciale Marketplace addebitate in base alle unità non standard.

I partner che dispongono di contatori personalizzati distribuiti per applicazioni SaaS e gestite possono visualizzare la deviazione dal comportamento di utilizzo previsto come anomalie per gli _eventi in eccedenza_ in _contatori personalizzati_ specifici nel centro per i partner. Per attenuare il rischio, il centro per i partner usa un servizio di rilevamento delle anomalie che applica gli algoritmi di machine learning per determinare il normale comportamento di fatturazione a consumo, analizzare l'utilizzo della fatturazione a consumo e individuare le anomalie con l'intervento dell'utente minimo. Usando i _modelli di rilevamento delle anomalie_ nei set di dati di fatturazione a consumo, il centro per i partner mira ad informare l'editore quando l'utilizzo segnalato supera l'utilizzo previsto.

## <a name="usability-experience"></a>Esperienza di usabilità

Microsoft si affida al partner per segnalare l'utilizzo dell'eccedenza dei clienti delle offerte SaaS o dell'applicazione gestita di Azure prima che Microsoft informasse il cliente. Se viene segnalato un utilizzo errato, il cliente potrebbe potenzialmente ricevere una fattura non corretta, sottoporre a overmining sia Microsoft che la credibilità del partner.

Per attenuare questo problema, viene fornita una funzionalità di rilevamento automatico delle anomalie per le app SaaS e i piani delle applicazioni gestite di Azure. Questa funzionalità è un modello di apprendimento automatico che monitora in modo proattivo l'utilizzo rispetto alla fatturazione a consumo e stima il valore previsto di utilizzo entro l'intervallo previsto. Se l'utilizzo non rientra nell'intervallo previsto, viene considerato come un'anomalia e viene visualizzata una notifica di avviso per il partner nella pagina della panoramica dell'offerta nel programma commerciale Marketplace del centro per i partner.

Il modello di Machine Learning analizza l'utilizzo giornaliero. L'autore può visualizzare tutte le anomalie segnalate in base all'utilizzo in eccedenza dei clienti per le dimensioni di misurazione personalizzate di ogni offerta.

### <a name="view-and-manage-metered-usage-anomalies"></a>Visualizzare e gestire le anomalie di utilizzo a consumo

1. Accedere al centro per i [partner](https://partner.microsoft.com/dashboard/home).
1. Nel menu di spostamento a sinistra selezionare **Commercial Marketplace**  >  **Analyze**.
1. Selezionare la scheda **anomalie di utilizzo a consumo** .

    [![Viene illustrata la scheda anomalie di utilizzo a consumo nella pagina utilizzo.](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)
    ***Figura 1: scheda anomalie di utilizzo a consumo***

1. Per eventuali anomalie di utilizzo rilevate dalla fatturazione a consumo, in qualità di editore verrà richiesto di esaminare e verificare se l'anomalia è vera o meno. Selezionare **Contrassegna come anomalie** per confermare la diagnosi.

     [![Viene illustrata la finestra di dialogo Contrassegno come anomalia.](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)
    ***Figura 2: contrassegnare come finestra di dialogo anomalia***

1. Se si ritiene che l'anomalia di utilizzo in eccedenza rilevata non sia genuina, è possibile fornire il feedback selezionando **non un'anomalia** per l'anomalia contrassegnata dal centro per i partner per l'utilizzo specifico.

    [![Viene illustrata la finestra di dialogo perché non è un'anomalia.](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    ***Figura 3: perché non si tratta di un'anomalia? finestra di dialogo***

1. È possibile scorrere verso il basso la pagina per visualizzare un elenco di inventario di anomalie non riconosciute. L'elenco fornisce un inventario delle anomalie non riconosciute. È possibile scegliere di contrassegnare le anomalie contrassegnate dal centro per i partner come originali o false.

   [![Viene illustrato l'elenco di anomalie non riconosciute del centro per i partner nella pagina utilizzo.](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)
    ***Figura 4: elenco di anomalie non riconosciute del centro per i partner***

1. Viene anche visualizzato un log delle azioni anomalie che mostra le azioni eseguite sugli utilizzi in eccedenza. Nel log delle azioni sarà possibile vedere quali eventi di utilizzo in eccedenza sono stati contrassegnati come originali o false.

   [ ![ Viene illustrato il registro azioni anomalie nella pagina Usage.](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox) 
    ***Figura 5: registro azioni anomalie***

1. Il centro per i partner Analytics non supporterà la ripubblicazione degli eventi di utilizzo in eccedenza nei report di esportazione. Il centro per i partner consente di immettere l'utilizzo corretto delle eccedenze per un'anomalia e i dettagli vengono passati a Microsoft teams per l'analisi. In base all'analisi, Microsoft emetterà rimborsi di credito per il cliente con addebito superiore, a seconda dei casi. Quando si seleziona una delle anomalie contrassegnate, è possibile selezionare **Contrassegno come anomalie** per contrassegnare l'anomalia di utilizzo in eccesso come originale.

   [ ![ Viene illustrata la finestra di dialogo Contrassegno come anomalia.](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox) 
    ***Figura: 6: contrassegnare come anomala la finestra di dialogo***

La prima volta che un utilizzo in eccedenza viene contrassegnato come irregolare nel centro per i partner, si otterrà una finestra di 30 giorni da tale istanza per contrassegnare l'anomalia come originale o falsa. Dopo il periodo di 30 giorni, il server di pubblicazione non sarà in grado di agire sulle anomalie.

> [!IMPORTANT]
> Le unità di utilizzo in eccedenza segnalate non sono idonee per la rilevanza e la rettifica finanziaria.

È possibile visualizzare tutte le anomalie (confermate o altrimenti) per il periodo di calcolo selezionato. I vari periodi di calcolo sono gli ultimi 30 giorni, gli ultimi 60 giorni e gli ultimi 90 giorni.

> [!IMPORTANT]
> Viene richiesto di agire sulle anomalie contrassegnate entro 30 giorni dal momento in cui le anomalie vengono prima segnalate nel centro per i partner.

Il filtro modale nel widget consente di selezionare singole offerte e singoli contatori personalizzati.

Quando si contrassegna un utilizzo in eccedenza come anomalia o si accetta un modello che contrassegna un'anomalia come originale, non è possibile modificare la selezione in "non è un'anomalia".

> [!IMPORTANT]
> È possibile inviare nuovamente gli utilizzi in eccedenza in caso di situazioni di addebito.

## <a name="see-also"></a>Vedi anche
- [Fatturazione a consumo per SaaS tramite il servizio di misurazione del Marketplace commerciale](./partner-center-portal/saas-metered-billing.md)
- [Fatturazione a consumo delle applicazioni gestite](./partner-center-portal/azure-app-metered-billing.md)
