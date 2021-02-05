---
title: Sconti per le prenotazioni per il Servizio app di Azure
description: Informazioni sul modo in cui si applicano gli sconti per le istanze di app Azure Service Premium V3 e gli indicatori isolati.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: banders
ms.openlocfilehash: debe02a89e10712ad8a0b8d61b0fdc3f8a4bd7b2
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577790"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-premium-v3-instances-and-isolated-stamps"></a>In che modo gli sconti per le prenotazioni si applicano alle istanze app Azure Service Premium V3 e agli indicatori isolati

Questo articolo consente di comprendere il modo in cui gli sconti sono applicabili alle istanze di app Azure Service Premium V3 e ai timbri isolati.

## <a name="how-reservation-discounts-apply-to-premium-v3-instances"></a>Modalità di applicazione degli sconti per le prenotazioni a istanze Premium V3

Dopo aver acquistato un'istanza riservata di app Azure Service Premium V3, lo sconto relativo alla prenotazione viene applicato automaticamente alle istanze del servizio app che corrispondono agli attributi e alla quantità della prenotazione. Una prenotazione copre il costo delle istanze Premium V3. 

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>Come viene applicato lo sconto al servizio app Azure 

Uno sconto per la prenotazione è *use-it-o-lose-it*. Quindi, se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.
Quando si arresta una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non si trovano risorse corrispondenti nell'ambito specificato, le ore prenotate vanno perse.

### <a name="reservation-discount-for-premium-v3-instances"></a>Sconto per le prenotazioni per le istanze Premium V3

Lo sconto di prenotazione di Azure viene applicato per l'esecuzione di istanze Premium V3 su base oraria. Le prenotazioni acquistate vengono associate all'utilizzo emesso dalle istanze Premium V3 in esecuzione per applicare lo sconto per la prenotazione. Per le istanze Premium V3 che potrebbero non essere eseguite nell'ora completa, la prenotazione verrà compilata da altre istanze che non utilizzano una prenotazione, incluse le istanze in esecuzione simultanea. Alla fine dell'ora, l'applicazione di prenotazione per le istanze nell'ora è bloccata. Nel caso in cui un'istanza non venga eseguita per un'ora o le istanze simultanee entro l'ora non riempiono l'ora della prenotazione, la prenotazione è sottoutilizzata per quell'ora. Il grafico seguente illustra l'applicazione di una prenotazione all'utilizzo fatturabile delle VM. L'illustrazione si basa sull'acquisto di una prenotazione e su due istanze di VM corrispondenti.

![Immagine che mostra l'applicazione di una prenotazione per l'utilizzo della macchina virtuale fatturabile](./media/reservation-discount-app-service/reserved-premium-v3-instance-application.png)

1.  Per l'uso al di sopra della linea della prenotazione vengono addebitate le normali tariffe con pagamento in base al consumo. L'uso al di sotto della linea della prenotazione non viene addebitato perché è già stato pagato con l'acquisto della prenotazione.
2.  Nell'ora 1 l'istanza 1 viene eseguita per 0,75 ore e l'istanza 2 per 0,5 ore. L'utilizzo totale per l'ora 1 è di 1,25 ore. Vengono addebitate le tariffe con pagamento in base al consumo per le rimanenti 0,25 ore.
3.  Nell'ora 2 e nell'ora 3 ognuna delle istanze è stata eseguita per 1 ora. Un'istanza è coperta dalla prenotazione e per l'altra vengono addebitate le tariffe con pagamento in base al consumo.
4.  Nell'ora 4 l'istanza 1 viene eseguita per 0,5 ore e l'istanza 2 per 1 ora. L'istanza 1 è completamente coperta dalla prenotazione, come anche 0,5 ore dell'istanza 2. Viene addebitata la tariffa con pagamento in base al consumo per le rimanenti 0,5 ore.

Per informazioni sull'applicazione delle prenotazioni di Azure nei report sull'utilizzo per la fatturazione, vedere [Understand reservation usage](understand-reserved-instance-usage-ea.md) (Informazioni sull'utilizzo della prenotazione).

### <a name="discount-can-apply-to-different-sizes"></a>Lo sconto può essere applicato a diverse dimensioni

Quando si acquista un'istanza Premium V3 riservata e si seleziona **ottimizzato per la flessibilità delle dimensioni dell'istanza**, il code coverage si applica alle dimensioni dell'istanza Premium V3 selezionate. Può anche essere applicabile ad altre dimensioni di istanze che si trovano nello stesso gruppo di flessibilità delle dimensioni delle istanze della serie.

## <a name="how-reservation-discounts-apply-to-isolated-stamps"></a>Come vengono applicati gli sconti per le prenotazioni a indicatori isolati

Dopo aver acquistato la capacità riservata della tariffa stamp per il servizio app Isolato, lo sconto per la prenotazione viene applicato automaticamente alla tariffa stamp in un'area. Lo sconto per la prenotazione si applica ai dati di utilizzo generati dal contatore della tariffa stamp per ambiente isolato. I ruoli di lavoro, i front-end aggiuntivi ed eventuali altre risorse associate allo stamp continueranno a essere fatturati in base alla tariffa standard.

### <a name="reservation-discount-application"></a>Applicazione dello sconto per la prenotazione

Lo sconto per la tariffa stamp per il servizio app Isolato viene applicato all'esecuzione di stamp per ambiente isolato su base oraria. Se non è presente uno stamp distribuito per un'ora, la capacità riservata risulta sprecata per tale ora e non viene mantenuta.

Dopo l'acquisto la prenotazione acquistata viene confrontata con uno stamp per ambiente isolato in esecuzione in un'area specifica. Se si arresta tale stamp, gli sconti per le prenotazioni vengono applicati automaticamente a qualsiasi altro stamp in esecuzione nell'area. Quando non sono presenti stamp, la prenotazione viene applicata allo stamp successivo creato nell'area.

Quando gli stamp non vengono eseguiti per un'intera ora, la prenotazione viene applicata automaticamente ad altri stamp corrispondenti presenti nella stessa area nella stessa ora.

### <a name="choose-a-stamp-type---windows-or-linux"></a>Scegliere un tipo di stamp: Windows o Linux

Per impostazione predefinita, con uno stamp per ambiente isolato vuoto viene generato il contatore dello stamp Windows, ad esempio quando non viene distribuito alcun ruolo di lavoro. Continua a generare il contatore quando vengono distribuiti ruoli di lavoro di Windows. Se si distribuisce un ruolo di lavoro Linux, il contatore passa al contatore dello stamp Linux. Quando vengono distribuiti ruoli di lavoro sia Linux che Windows, lo stamp genera il contatore di Windows.

Di conseguenza, il contatore dello stamp può quindi passare da Windows a Linux e viceversa durante il ciclo di vita dello stamp. Nel frattempo le prenotazioni sono specifiche del sistema operativo. È necessario acquistare una prenotazione che supporti i ruoli di lavoro che si prevede di distribuire nello stamp. Per gli stamp solo Windows e quelli misti viene usata la prenotazione di Windows. Per gli stamp con solo ruoli di lavoro Linux viene usata la prenotazione di Linux.

Una prenotazione di Linux deve essere acquistata esclusivamente se si prevede che lo stamp conterrà _solo_ ruoli di lavoro di Linux.

### <a name="discount-examples"></a>Esempi di sconto

Gli esempi seguenti illustrano la modalità di applicazione dello sconto per l'istanza riservata della tariffa stamp per ambiente isolato a seconda delle distribuzioni.

- **Esempio 1**: si acquista un'istanza della capacità riservata per lo stamp per ambiente isolato in un'area senza stamp per il servizio app Isolato. Si distribuisce un nuovo stamp nell'area e si pagano le tariffe riservate per tale stamp.
- **Esempio 2:** si acquista un'istanza della capacità riservata per lo stamp per ambiente isolato in un'area in cui è già distribuito uno stamp per il servizio app Isolato. Si inizia a ricevere la tariffa riservata per lo stamp distribuito.
- **Esempio 3**: si acquista un'istanza della capacità riservata per lo stamp per ambiente isolato in un'area con uno stamp per il servizio app Isolato già distribuito. Si inizia a ricevere la tariffa riservata per lo stamp distribuito. Successivamente, si elimina lo stamp e se ne distribuisce uno nuovo. Si riceve la tariffa riservata per il nuovo stamp. Gli sconti non vengono mantenuti per l'intera durata senza stamp distribuiti.
- **Esempio 4**: si acquista un'istanza della capacità riservata per lo stamp Linux per ambiente isolato in un'area e quindi si distribuisce un nuovo stamp nell'area. Quando lo stamp viene inizialmente distribuito senza ruoli di lavoro, viene generato il contatore di stamp Windows. Non viene ricevuto alcuno sconto. Quando nello stamp viene distribuito il primo ruolo di lavoro Linux, viene generato il contatore di stamp Linux e viene applicato lo sconto relativo alla prenotazione. Se successivamente nello stamp viene distribuito un ruolo di lavoro di Windows, viene ripristinato il contatore di stamp Windows. Non si riceve più uno sconto per la prenotazione dello stamp riservato Linux per ambiente isolato.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](manage-reserved-vm-instance.md).
- Per altre informazioni sulla pre-acquisto del servizio App Premium V3 e sulla capacità riservata dello Stamp isolato per risparmiare denaro, vedere [PrePay per app Azure servizio con capacità riservata](prepay-app-service.md).
- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md)
  - [Gestire le prenotazioni in Azure](manage-reserved-vm-instance.md)
  - [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](understand-reserved-instance-usage.md)
  - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](understand-reserved-instance-usage-ea.md)
