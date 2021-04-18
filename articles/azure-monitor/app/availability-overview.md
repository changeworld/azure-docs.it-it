---
title: Application Insights panoramica della disponibilità
description: Configurare test Web ricorrenti per monitorare la disponibilità e la velocità di risposta dell'app o del sito Web.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 3c2d248df98fcb9f75f5de9c05b9f600955cbac7
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600795"
---
# <a name="availability-tests-overview"></a>Panoramica dei test di disponibilità

Dopo aver distribuito l'app Web o il sito Web, è possibile configurare test ricorrenti per monitorare la disponibilità e la velocità di risposta. [Application Insights](./app-insights-overview.md) le richieste Web all'applicazione a intervalli regolari da punti di tutto il mondo. Può avvisare l'utente se l'applicazione non risponde o se risponde troppo lentamente.

È possibile configurare test di disponibilità per qualsiasi endpoint HTTP o HTTPS accessibile dalla rete Internet pubblica. Non è necessario apportare modifiche al sito Web che si sta testando. In realtà, non deve nemmeno essere un sito di cui si è proprietari. È possibile testare la disponibilità di un'API REST da cui dipende il servizio.

## <a name="types-of-availability-tests"></a>Tipi di test di disponibilità

Esistono tre tipi di test di disponibilità:

* [Test ping URL:](monitor-web-app-availability.md)questa categoria include due semplici test che è possibile creare tramite il portale.
* [Test Web in più passaggi:](availability-multistep.md)registrazione di una sequenza di richieste Web, che possono essere riprodotte per testare scenari più complessi. I test Web in più passaggi vengono creati in Visual Studio Enterprise e caricati nel portale per l'esecuzione.
* [Test di disponibilità personalizzati:](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)se si decide di creare un'applicazione personalizzata per eseguire i test di disponibilità, è possibile usare il metodo per inviare i risultati `TrackAvailability()` Application Insights.

> [!IMPORTANT]
> Sia il [test ping url](monitor-web-app-availability.md) che il test [Web](availability-multistep.md) in più passaggi si basano sull'infrastruttura DNS Internet pubblica per risolvere i nomi di dominio degli endpoint testati. Ciò significa che se si usa DNS privato, è necessario assicurarsi che anche ogni nome di dominio del test sia risolvibile dai server [](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) dei nomi di dominio pubblici oppure, quando non è possibile, è possibile usare test di disponibilità personalizzati.

**È possibile creare fino a 100 test di disponibilità per ogni Application Insights risorsa.**

## <a name="troubleshooting"></a>Risoluzione dei problemi

[Articolo sulla risoluzione dei problemi](troubleshoot-availability.md) dedicato.

## <a name="next-step"></a>Passaggio successivo

* [Avvisi di disponibilità](availability-alerts.md)
* [Test Web in più passaggi](availability-multistep.md)
* [Test tramite URL](monitor-web-app-availability.md)
* [Creare ed eseguire test di disponibilità personalizzati usando Funzioni di Azure.](availability-azure-functions.md)