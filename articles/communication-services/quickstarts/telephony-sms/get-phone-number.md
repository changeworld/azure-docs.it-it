---
title: Guida introduttiva-gestire i numeri di telefono usando i servizi di comunicazione di Azure
description: Informazioni su come gestire i numeri di telefono usando i servizi di comunicazione di Azure
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: e28b8581342824032921ad75e0297b3604fc0bb4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629346"
---
# <a name="quickstart-manage-phone-numbers"></a>Guida introduttiva: gestire i numeri di telefono

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Per iniziare a usare i servizi di comunicazione di Azure, usare la portale di Azure o la libreria client dei numeri di telefono dei servizi di comunicazione per gestire i numeri di telefono.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/phone-numbers-portal.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Azure portal](./includes/phone-numbers-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/phone-numbers-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/phone-numbers-python.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/phone-numbers-js.md)]
::: zone-end

## <a name="troubleshooting"></a>Risoluzione dei problemi

Domande e problemi comuni:

- Il telefono per l'acquisto è supportato solo negli Stati Uniti. Per acquistare i numeri di telefono, verificare quanto segue:
  - L'indirizzo di fatturazione della sottoscrizione di Azure associato si trova nel Stati Uniti. Non è possibile spostare una risorsa in un'altra sottoscrizione in questo momento.
  - Il provisioning della risorsa di servizi di comunicazione viene effettuato nel percorso dei dati Stati Uniti. In questo momento non è possibile spostare una risorsa in un altro percorso dati.

- I numeri di telefono rilasciati non potranno essere rilasciati o riacquistati fino al termine del ciclo di fatturazione.

- Quando una risorsa di Servizi di comunicazione viene eliminata, i numeri di telefono associati verranno rilasciati automaticamente allo stesso tempo.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come:

> [!div class="checklist"]
> * Acquistare un numero di telefono
> * Gestire il numero di telefono
> * Rilasciare un numero di telefono

> [!div class="nextstepaction"]
> [Inviare un SMS](../telephony-sms/send.md)
> [Introduzione alle chiamate](../voice-video-calling/getting-started-with-calling.md)
