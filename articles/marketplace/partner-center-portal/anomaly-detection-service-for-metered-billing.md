---
title: Servizio di rilevamento anomalie per la fatturazione a consumo-Microsoft Azure Marketplace
description: Viene descritto il funzionamento del rilevamento delle anomalie, quando vengono inviate le notifiche e quali operazioni eseguire con tali funzionalità e le opzioni di supporto.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 06/10/2020
ms.openlocfilehash: ac3e07c67ca82c2960de1c4341a714b33751bfc1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092410"
---
# <a name="anomaly-detection-service-for-metered-billing"></a>Servizio di rilevamento anomalie per la fatturazione a consumo

Il [servizio di misurazione del Marketplace](marketplace-metering-service-apis-faq.md) ti permette di creare offerte nel programma per Marketplace commerciale addebitate in base alle unità non standard. Con la fatturazione a consumo, si inviano eventi di utilizzo per l'utilizzo da parte del cliente a Microsoft e si prepara la fatturazione in base a tale utilizzo.

I dati di utilizzo non corretti possono provenire da numerose cause, ad esempio bug, errori di configurazione nel rilevamento del consumo o frodi. I dati di utilizzo non corretti potrebbero comportare costi non corretti per i clienti e la fatturazione.

Per attenuare il rischio, il servizio di rilevamento delle anomalie applica gli algoritmi di machine learning per determinare il normale comportamento di fatturazione a consumo, analizzare l'utilizzo della fatturazione a consumo e individuare le anomalie con un intervento minimo dell'utente.

Si riceve una notifica in caso di anomalie rilevato nell'uso della fatturazione a consumo. In questo modo è possibile analizzare e inviare una notifica se un'anomalia viene confermata come un problema reale, a quel punto è possibile intraprendere le azioni per risolvere il problema di fatturazione dei clienti in modo proattivo.

Oltre ai picchi improvvisi, ai tuffi e alle variazioni di tendenza dell'utilizzo della fatturazione a consumo, il modello rappresenta anche gli effetti stagionali. Poiché la fatturazione a consumo viene comunicata tramite dati in eccedenza, il modello è anche in grado di gestire normalmente lunghi periodi di dati mancanti.

Di seguito sono riportati alcuni esempi di risultati del rilevamento delle anomalie. L'intervallo previsto viene visualizzato come una banda gialla. L'utilizzo della fatturazione a consumo accettabile viene visualizzato come stelle verdi nella banda. L'utilizzo della fatturazione all'esterno della banda viene visualizzato come un punto rosso.  

Anomalie rilevate al di fuori di una tendenza stimabile:

![Illustra le anomalie rilevate al di fuori di una tendenza prevedibile.](media/anomaly-1.png)

Anomalie rilevate al di fuori di una tendenza ciclica ricorrente:

![Illustra le anomalie rilevate al di fuori di una tendenza ciclica ricorrente.](media/anomaly-2.png)

Anomalie rilevate in una tendenza verso l'alto:

![Illustra le anomalie rilevate in una tendenza verso l'alto.](media/anomaly-3.png)

## <a name="how-anomaly-detection-service-works"></a>Funzionamento del servizio rilevamento anomalie

Il rilevamento delle anomalie viene abilitato automaticamente per tutto l'utilizzo della fatturazione a consumo. Quando si inviano gli eventi di utilizzo a Microsoft, il servizio di rilevamento delle anomalie crea un modello di valori previsti in base ai dati di utilizzo precedenti. Questo modello viene eseguito settimanalmente.

Funzioni di rilevamento delle anomalie a livello di contatore e per cliente. Ciò significa che ogni misuratore con ogni cliente avrà un modello sottoposto a training in base al modello di utilizzo precedente di questo contatore del cliente.

Il modello funziona generando intervalli di confidenza retrospettiva. La previsione delle serie temporali è un modello additivo generalizzato costituito da una parte della previsione di tendenza e da una parte della stagionalità. Poiché il modello viene formulato come un'attività di regressione, può gestire normalmente lunghi periodi di dati mancanti. Se un'osservazione non rientra nell'intervallo di confidenza stimato, significa che l'osservazione non può essere spiegata in base a modelli cronologici della fatturazione a consumo e pertanto può essere un'anomalia.

## <a name="anomaly-detection-notification"></a>Notifica di rilevamento anomalie

È possibile valutare, gestire e confermare le anomalie nel centro per i partner. Per informazioni, vedere [gestire le anomalie di fatturazione a consumo nel centro](../anomaly-detection.md)per i partner.

Per assicurarsi che i clienti non vengano sovraccaricati per l'utilizzo a consumo, è necessario verificare se le anomalie rilevate sono problemi reali. In tal caso, è possibile riconoscere l'utilizzo errato nel centro per i partner.

Si consiglia di verificare se le anomalie rilevate sono di utilizzo normale. Questa operazione consentirà di migliorare i dati di anomalia forniti. Se un'anomalia rappresenta un rischio finanziario potenzialmente elevato, è possibile che si contatti per confermare l'utilizzo.

## <a name="when-and-how-to-get-support"></a>Quando e come ottenere supporto

Se è stato inviato un utilizzo errato a consumo che ha fatto o comporterà un addebito per il cliente, non si avvierà una fattura per il cliente per l'utilizzo sottoposto a report o si pagherà per tale utilizzo. L'utente dovrà sostenere la perdita dei ricavi dovuta alla segnalazione di valori di utilizzo inferiori.

Se si verifica uno dei casi seguenti, è possibile modificare la quantità di utilizzo nel centro per i partner che comporterà un rimborso o una modifica della fatturazione per i clienti:

- Si è verificato che una delle anomalie rilevate è un problema reale e l'utilizzo errato comporta l'Overcharging del cliente.
- Si scopre che è stato inviato un utilizzo errato a Microsoft e l'utilizzo errato comporta l'Overcharging del cliente.

Per inviare un ticket di supporto correlato alle anomalie di fatturazione a consumo:

1. Accedere al centro per i [partner](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) con l'account aziendale.
1. Nel menu in alto a destra nella pagina selezionare l'icona del **supporto** . Il riquadro **Guida e supporto** viene visualizzato sul lato destro della pagina.
1. Per informazioni sul Marketplace commerciale, selezionare **Marketplace commerciale**.
   ![Viene illustrato il riquadro supporto.](../media/support/commercial-marketplace-support-pane.png)
1. Nella casella **Riepilogo problema** immettere **Marketplace commerciale > fatturazione a consumo**.
1. Nella casella **tipo di problema** selezionare una delle opzioni seguenti:
    - **Marketplace commerciale > la fatturazione a consumo > utilizzo errato inviato per l'offerta di applicazioni Azure**
    - **Marketplace commerciale > la fatturazione a consumo > utilizzo errato inviato per l'offerta SaaS**
1. Nel **passaggio successivo** selezionare **verifica soluzioni**.
1. Esaminare i documenti consigliati, se presenti oppure selezionare **Fornisci i dettagli del problema** per inviare un ticket di supporto.

Per altre opzioni di supporto per gli editori, vedere [supporto per il programma Commercial Marketplace nel centro per i partner](../support.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull' [API del servizio di misurazione del Marketplace](marketplace-metering-service-apis.md).
- [Gestire le anomalie di fatturazione a consumo nel centro per i partner](../anomaly-detection.md)
