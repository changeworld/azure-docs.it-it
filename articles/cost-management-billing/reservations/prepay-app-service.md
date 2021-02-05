---
title: Risparmiare sui costi del Servizio app di Azure con la capacità riservata
description: Scopri come risparmiare sui costi per le istanze riservate di app Azure Service Premium V3 e per la tariffa isolata per gli Stamp.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/01/2021
ms.author: banders
ms.custom: references_regions
ms.openlocfilehash: 89e0c62b580c0c354fc7277e61b452005a86e3d9
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577795"
---
# <a name="save-costs-with-azure-app-service-reserved-instances"></a>Ridurre i costi con app Azure istanze riservate del servizio

Questo articolo illustra come salvare con app Azure istanze riservate del servizio per le istanze Premium V3 e le tariffe per gli indicatori isolati.

## <a name="save-with-premium-v3-reserved-instances"></a>Salva con istanze riservate Premium V3

Quando si esegue il commit in un'istanza riservata di app Azure Service Premium V3, è possibile risparmiare denaro. Lo sconto relativo alla prenotazione viene applicato automaticamente al numero di istanze in esecuzione che corrispondono all'ambito di prenotazione e agli attributi. Non è necessario assegnare una prenotazione a un'istanza di per ottenere gli sconti.

## <a name="determine-the-right-reserved-instance-size-before-you-buy"></a>Determinare le dimensioni appropriate per le istanze riservate prima di acquistare

Prima di acquistare una prenotazione, è necessario determinare le dimensioni dell'istanza riservata Premium V3 necessaria. Le sezioni seguenti consentiranno di determinare le dimensioni appropriate per le istanze riservate Premium V3.

### <a name="use-reservation-recommendations"></a>USA consigli sulla prenotazione

È possibile usare le raccomandazioni di prenotazione per determinare le prenotazioni da acquistare.

- I consigli di acquisto e la quantità consigliata vengono visualizzati quando si acquista un'istanza riservata Premium V3 nel portale di Azure.
- Azure Advisor fornisce consigli di acquisto per le singole sottoscrizioni.
- È possibile usare le API per ottenere raccomandazioni di acquisto sia per l'ambito condiviso sia per l'ambito di una singola sottoscrizione. Per altre informazioni, vedere [API di raccomandazione per l'acquisto di istanze riservate per i clienti aziendali](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Per i clienti Contratto Enterprise (EA) e Microsoft Customer Agreement (MCA), i consigli di acquisto per gli ambiti di abbonamento condiviso e singolo sono disponibili con il [pacchetto di contenuto Informazioni dettagliate sul consumo di Azure Power bi](/power-bi/service-connect-to-azure-consumption-insights).

#### <a name="instance-size-flexibility-setting"></a>Impostazione della flessibilità delle dimensioni istanza

L'impostazione della flessibilità delle dimensioni dell'istanza determina quali servizi ottengono gli sconti per le istanze riservate.

Se l'impostazione è on o off, gli sconti per la prenotazione si applicano automaticamente a qualsiasi utilizzo di istanza riservata Premium V3 corrispondente.

### <a name="analyze-your-usage-information"></a>Analizzare le informazioni sull'utilizzo

Analizzare le informazioni sull'utilizzo per determinare le prenotazioni da acquistare. I dati di utilizzo sono disponibili nel file di utilizzo e nelle API. Usarli insieme per determinare la prenotazione da acquistare. Verificare la presenza di istanze Premium V3 con utilizzo elevato su base giornaliera per determinare la quantità di prenotazioni da acquistare.

Il file di utilizzo Mostra gli addebiti per periodo di fatturazione e utilizzo giornaliero. Per informazioni sul download del file di utilizzo, vedere [visualizzare e scaricare gli addebiti e l'utilizzo di Azure](../understand/download-azure-daily-usage.md). Quindi, usando le informazioni sul file di utilizzo, è possibile [determinare la prenotazione da acquistare](determine-reservation-purchase.md).

### <a name="purchase-restriction-considerations"></a>Considerazioni sulla restrizione degli acquisti

Gli sconti per le prenotazioni non sono validi per le seguenti istanze Premium V3:

- **Istanze di anteprima o Promote** : tutte le serie di istanze riservate V3 Premium V3 o le dimensioni disponibili in anteprima o usano il contatore promozionale.
- **Cloud** : le prenotazioni non sono disponibili per l'acquisto nelle aree Germania o Cina.

## <a name="buy-a-premium-v3-reserved-instance"></a>Acquistare un'istanza riservata Premium V3

È possibile acquistare un'istanza riservata Premium V3 riservata nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Usare [pagamenti anticipati o mensili](prepare-buy-reservation.md) per acquistare la prenotazione. Questi requisiti si applicano all'acquisto di un'istanza riservata Premium V3:

- È necessario avere un ruolo proprietario per almeno una sottoscrizione EA o una sottoscrizione con pagamento in base al consumo.
- Per le sottoscrizioni EA, l'opzione **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com/). Se tale impostazione è disabilitata, è necessario essere un amministratore del contratto EA della sottoscrizione.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare le prenotazioni.

Per acquistare un'istanza:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare **Aggiungi** per acquistare una nuova prenotazione e quindi fare clic su **istanza**.
4. Compilare i campi obbligatori. L'esecuzione di istanze riservate Premium V3 che corrispondono agli attributi selezionati è idonea per lo sconto della prenotazione. Il numero effettivo delle istanze riservate Premium V3 che ottengono lo sconto dipendono dall'ambito e dalla quantità selezionati.

Se si dispone di un contratto Enterprise, è possibile utilizzare l' **opzione Aggiungi ulteriore** per aggiungere rapidamente ulteriori istanze. L'opzione non è disponibile per altri tipi di sottoscrizione.

| **Campo** | **Descrizione** |
|---|---|
| Subscription | La sottoscrizione usata per pagare la prenotazione. I costi per la prenotazione vengono addebitati al metodo di pagamento specificato nella sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numeri di offerta: MS-AZR-0017P o MS-AZR-0148P) o un contratto di servizio Microsoft o una sottoscrizione singola con tariffe a consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P). Gli addebiti vengono dedotti dal saldo dell'impegno monetario, se disponibile, o vengono addebitati come eccedenza. Per una sottoscrizione con tariffe con pagamento in base al consumo, i costi vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura per la sottoscrizione. |
| Ambito | L'ambito della prenotazione può coprire una sottoscrizione o più sottoscrizioni (ambito condiviso). Se si seleziona: <ul><li>**Gruppo di risorse singolo**: lo sconto per la prenotazione si applica solo alle risorse corrispondenti incluse nel gruppo di risorse selezionato. </li><li>**Sottoscrizione singola**: lo sconto della prenotazione viene applicato alle risorse corrispondenti incluse nella sottoscrizione selezionata.</li><li>**Condiviso**: lo sconto della prenotazione viene applicato alle risorse corrispondenti nelle sottoscrizioni idonee incluse nel contesto di fatturazione. Per i clienti con contratto Enterprise, il contesto di fatturazione è la registrazione. Per le singole sottoscrizioni che prevedono tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.</li></ul> |
| Region | Area di Azure coperta dalla prenotazione. |
| Dimensioni dell'istanza riservata Premium V3 | Dimensioni delle istanze riservate Premium V3. |
| Ottimizza per | Per impostazione predefinita, è selezionata la flessibilità delle dimensioni delle istanze riservate Premium V3. Fare clic su **Impostazioni avanzate** per modificare il valore di flessibilità delle dimensioni dell'istanza per applicare lo sconto di prenotazione ad altre istanze riservate Premium V3 nello stesso [gruppo dimensioni istanza riservata Premium V3](../../virtual-machines/reserved-vm-instance-size-flexibility.md). La priorità di capacità assegna la capacità del data center dando priorità alle distribuzioni. Offre una maggiore fiducia nella capacità di avviare le istanze riservate Premium V3 quando necessario. La priorità di capacità è disponibile solo quando l'ambito della prenotazione è sottoscrizione singola. |
| Termine | Un anno o tre anni. Esiste anche un termine di 5 anni disponibile solo per le istanze riservate HBv2 Premium V3. |
| Quantità | Il numero di istanze acquistate all'interno della prenotazione. La quantità è il numero di istanze riservate Premium V3 che possono ottenere lo sconto per la fatturazione. Se, ad esempio, si eseguono 10 \_ istanze standard di D2 Premium V3 negli Stati Uniti orientali, è necessario specificare Quantity come 10 per ottimizzare il vantaggio per tutte le istanze riservate Premium V3 in esecuzione. |

## <a name="save-with-isolated-stamp-fees"></a>Salva con costi di timbro isolati

È possibile risparmiare sui costi della tariffa stamp per il servizio app di Azure per ambiente isolato impegnando una prenotazione per l'utilizzo di stamp per una durata di tre anni. Per acquistare capacità riservata per la tariffa stamp per il servizio app per ambiente isolato, è necessario scegliere l'area di Azure in cui lo stamp verrà distribuito e il numero di stamp da acquistare.

Quando si acquista una prenotazione, l'utilizzo della tariffa stamp per il servizio app per ambiente isolato corrispondente agli attributi della prenotazione non viene più addebitato in base alle tariffe con pagamento in base al consumo. La prenotazione viene applicata automaticamente al numero di stamp isolati che corrispondono all'ambito e all'area della capacità riservata. Non è necessario assegnare una prenotazione a uno stamp isolato. La prenotazione non si applica ai ruoli di lavoro, quindi qualsiasi altra risorsa associata allo stamp viene addebitata separatamente.

Allo scadere della capacità riservata, l'esecuzione degli stamp isolati continua, ma gli stamp vengono addebitati in base alla tariffa con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente.

## <a name="determine-the-right-isolated-stamp-reservation-to-purchase"></a>Determinare la prenotazione di timbro isolato corretta da acquistare

Con l'acquisto di una prenotazione, ci si impegna a usare le quantità riservate nei tre anni successivi. Controllare i dati di utilizzo per determinare quanti stamp isolati del servizio app si usano in modo coerente e potrebbero essere usati in futuro.

È importante anche comprendere i meccanismi in base a cui lo stamp isolato genera un contatore Linux o Windows.

- Per impostazione predefinita, con uno stamp isolato vuoto viene generato il contatore dello stamp Windows, ad esempio senza lavoratori distribuiti. Continua a generare questo contatore se nello stamp vengono distribuiti ruoli di lavoro di Windows.
- Se si distribuisce un ruolo di lavoro Linux, il contatore passa al contatore dello stamp Linux.
- Nei casi in cui vengono distribuiti ruoli di lavoro sia Linux che Windows, lo stamp genera il contatore di Windows.

Il contatore dello stamp può quindi passare da Windows a Linux e viceversa durante il ciclo di vita dello stamp.

Acquistare prenotazioni dello stamp di Windows se nello stamp sono presenti uno o più ruoli di lavoro di Windows. Una prenotazione dello stamp di Linux deve essere acquistata solo se si prevede che lo stamp conterrà _solo_ ruoli di lavoro di Linux.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Acquistare capacità riservata per uno stamp isolato

È possibile acquistare capacità riservata per uno stamp isolato nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Usare [pagamenti anticipati o mensili](./prepare-buy-reservation.md) per acquistare la prenotazione. Per acquistare capacità riservata, è necessario avere il ruolo di proprietario per almeno una sottoscrizione Enterprise o una sottoscrizione individuale con pagamento in base al consumo.

- Per le sottoscrizioni Enterprise, l'opzione **Aggiungi istanze riservate** deve essere abilitata in [EA Portal](https://ea.azure.com/). Se questa impostazione è disabilitata, è necessario essere un amministratore EA.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o di vendita possono acquistare capacità riservata di Azure Synapse Analytics.

**Per effettuare l'acquisto:**

1. Accedere al [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Selezionare una sottoscrizione. Usare l'elenco **Sottoscrizione** per scegliere la sottoscrizione usata per pagare la capacità riservata. I costi relativi alla capacità riservata vengono addebitati in base al metodo di pagamento della sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri offerta: MS-AZR-0017P o MS-AZR-0148P) o con pagamento in base al consumo (numeri offerta: MS-AZR-0003P o MS-AZR-0023P) o sottoscrizione CSP.
    - Per le sottoscrizioni Enterprise, gli addebiti vengono dedotti dal saldo del pagamento anticipato di Azure (in precedenza detto impegno monetario) oppure applicati come eccedenza.
    - Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo viene addebitato alla carta di credito o al metodo di pagamento tramite fattura per la sottoscrizione.
1. Usare l'elenco **Ambito** per scegliere un ambito della sottoscrizione.
    - **Gruppo di risorse singolo**: lo sconto per la prenotazione si applica solo alle risorse corrispondenti incluse nel gruppo di risorse selezionato.
    - **Sottoscrizione singola**: lo sconto della prenotazione viene applicato alle risorse corrispondenti incluse nella sottoscrizione selezionata.
    - **Condiviso**: lo sconto della prenotazione viene applicato alle risorse corrispondenti nelle sottoscrizioni idonee incluse nel contesto di fatturazione. Per i clienti con contratto Enterprise Agreement, il contesto di fatturazione è la registrazione. Per le singole sottoscrizioni che prevedono tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.
1. In **Area** selezionare un'area di Azure coperta dalla capacità riservata e aggiungere la prenotazione al carrello.
1. Selezionare un tipo di piano Isolato, quindi fare clic su **Seleziona**.  
    ![Esempio ](./media/prepay-app-service/app-service-isolated-stamp-select.png)
1. Immettere la quantità di stamp del servizio app Isolato da riservare. Ad esempio, una quantità pari a tre assegna tre stamp riservati a un'area. Fare clic su **Avanti: Rivedi e acquista**.
1. Controllare le informazioni e fare clic su **Acquista ora**.

Dopo l'acquisto, passare a [Prenotazioni](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) per visualizzare lo stato di acquisto e monitorarlo in qualsiasi momento.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](exchange-and-refund-azure-reservations.md).

## <a name="discount-application-shown-in-usage-data"></a>Applicazione dello sconto visualizzata nei dati di utilizzo

I dati di utilizzo hanno un prezzo effettivo pari a zero per l'utilizzo che ottiene uno sconto sulla prenotazione. I dati di utilizzo mostrano lo sconto sulla prenotazione per ogni istanza di stamp in ogni prenotazione.

Per altre informazioni sul modo in cui lo sconto sulla prenotazione viene visualizzato nei dati di utilizzo, vedere [Ottenere informazioni sui costi di prenotazione e l'utilizzo dei contratti Enterprise Agreement](understand-reserved-instance-usage-ea.md), se si ha un contratto Enterprise Agreement. In caso contrario, vedere [Informazioni sull'utilizzo della prenotazione di Azure per la sottoscrizione singola con pagamento in base al consumo](understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md)
  - [Informazioni sull'applicazione dello sconto sulla prenotazione per uno stamp isolato del servizio app di Azure](reservation-discount-app-service.md)
  - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](understand-reserved-instance-usage-ea.md)