---
title: Ammortizzare i costi di Prenotazione di Azure
description: Informazioni su come visualizzare i costi di Prenotazione di Azure per il chargeback.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/10/2021
ms.author: banders
ms.openlocfilehash: 4fb15a7e677d566454d5d487c1cf69767d7f3a30
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368745"
---
# <a name="charge-back-azure-reservation-costs"></a>Ammortizzare i costi di Prenotazione di Azure

I lettori di fatturazione del Contratto Enterprise e del Contratto del cliente Microsoft possono visualizzare i dati sui costi ammortizzati per le prenotazioni. Possono usare i dati sui costi per addebitare il valore monetario per una sottoscrizione, un gruppo di risorse, una risorsa o un tag ai partner. Nei dati ammortizzati il prezzo effettivo è il costo di prenotazione oraria ripartito. Il costo è il costo totale di utilizzo della prenotazione da parte della risorsa in quel giorno.

Gli utenti con una singola sottoscrizione possono ottenere i dati sui costi ammortizzati dal relativo file di utilizzo. Quando una risorsa riceve uno sconto per la prenotazione, la sezione *AdditionalInfo* nel file di utilizzo contiene i dettagli della prenotazione. Per altre informazioni, vedere [Scaricare l’utilizzo dal portale di Azure](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv).

## <a name="see-reservation-usage-data-for-show-back-and-charge-back"></a>Vedere i dati di utilizzo delle prenotazioni per Mostra indietro e addebita indietro

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passa a **Gestione costi e fatturazione** 
3. Selezionare **analisi dei costi** dal percorso di spostamento a sinistra 
4. In **Costo effettivo**, selezionare la metrica **Costo ammortizzato**.
5. Per vedere quali risorse sono state usate da una prenotazione, applicare un filtro per **Prenotazione** e quindi selezionare prenotazioni.
6. Impostare la **Granularità** su **Mensile** o **Giornaliera**.
7. Impostare il tipo di grafico su **Tabella**.
8. Impostare l'opzione **Raggruppa per** su **Risorsa**.

[![Esempio che mostra i costi delle risorse di prenotazione che è possibile usare per il chargeback](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Ecco un video che illustra come visualizzare i costi di utilizzo della prenotazione nel portale di Azure.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="get-the-data-for-show-back-and-charge-back"></a>Ottenere i dati da visualizzare e addebitare nuovamente
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passa a **Gestione costi e fatturazione** 
3. Selezionare **Esporta** dal percorso di spostamento a sinistra 
4. Fare clic sul pulsante **Aggiungi**
5. Selezionare il costo ammortizzato come pulsante della metrica e configurare l'esportazione

il valore di EffectivePrice per l'utilizzo che ottiene lo sconto per la prenotazione è il costo rivalutato della prenotazione (anziché zero). Questo consente di conoscere il valore monetario del consumo della prenotazione da parte di una sottoscrizione, di un gruppo di risorse o di una risorsa e di stornare internamente l'addebito per l'utilizzo della prenotazione. Il set di dati dispone anche di ore di prenotazione inutilizzate. 

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Ottenere i dati di consumo e di utilizzo delle prenotazioni di Azure tramite l'API

È possibile ottenere i dati usando l'API o scaricarli dal portale di Azure.

Per ottenere i nuovi dati, chiamare l'[API Dettagli utilizzo](/rest/api/consumption/usagedetails/list). Per informazioni dettagliate sulla terminologia, vedere [Comprendere i termini di utilizzo nel file su utilizzo e costi di Azure](../understand/understand-usage.md).

Ecco una chiamata di esempio all'API Dettagli sull'utilizzo:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

Per altre informazioni su {enrollmentId} e {billingPeriodId}, vedere l'articolo [Dettagli utilizzo - Elenco](/rest/api/consumption/usagedetails/list).

Le informazioni riportate nella tabella seguente sulla metrica e sul filtro possono essere utili per risolvere i problemi comuni delle prenotazioni.

| **Tipo di dati API** | Azione di chiamata API |
| --- | --- |
| **Tutti gli addebiti (utilizzo e acquisti)** | Sostituire {metric} con ActualCost |
| **Utilizzo che ha ottenuto lo sconto per la prenotazione** | Sostituire {metric} con ActualCost<br><br>Sostituire {filter} con: properties/reservationId%20ne%20 |
| **Utilizzo che non ha ottenuto lo sconto per la prenotazione** | Sostituire {metric} con ActualCost<br><br>Sostituire {filter} con: properties/reservationId%20eq%20 |
| **Addebiti ammortizzati (utilizzo e acquisti)** | Sostituire {metric} con AmortizedCost |
| **Report sulle prenotazioni non usate** | Sostituire {metric} con AmortizedCost<br><br>Sostituire {filter} con: properties/ChargeType%20eq%20'UnusedReservation' |
| **Acquisti di prenotazioni** | Sostituire {metric} con ActualCost<br><br>Sostituire {filter} con: properties/ChargeType%20eq%20'Purchase'  |
| **Rimborsi** | Sostituire {metric} con ActualCost<br><br>Sostituire {filter} con: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Scaricare il file CSV dei dati di utilizzo con i nuovi dati

Se si è un amministratore EA, è possibile scaricare il file CSV contenente i nuovi dati di utilizzo da portale di Azure. Questi dati non sono disponibili in EA Portal (ea.azure.com), occorre scaricare il file di utilizzo dal portale di Azure (portal.azure.com) per visualizzare i nuovi dati.

Nel portale di Azure passare a [Gestione dei costi e fatturazione](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Selezionare l'account di fatturazione.
2. Fare clic su **Utilizzo e addebiti**.
3. Fare clic su **Download**.  
![Esempio che mostra dove scaricare il file CSV dei dati di utilizzo nel portale di Azure](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. In **Dettagli sull'utilizzo** selezionare **dati di utilizzo ammortizzati**.

I file CSV scaricati contengono i costi effettivi e i costi ammortizzati.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sui dati di utilizzo delle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Costi di prenotazione e utilizzo per Contratto Enterprise e contratto di servizio Microsoft](understand-reserved-instance-usage-ea.md)
 
