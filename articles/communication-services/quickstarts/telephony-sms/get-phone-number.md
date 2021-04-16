---
title: 'Avvio rapido: Gestire i numeri di telefono con Servizi di comunicazione di Azure'
description: Informazioni su come gestire i numeri di telefono usando Servizi di comunicazione di Azure
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: 19bb79f9a4deaebfacc75918c46a5516d2d398be
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498195"
---
# <a name="quickstart-manage-phone-numbers"></a>Guida introduttiva: Gestire i numeri di telefono

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

[!INCLUDE [Bulk Acquisition Instructions](../../includes/phone-number-special-order.md)]

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

- L'acquisto del telefono è supportato solo negli Stati Uniti. Per acquistare i numeri di telefono, assicurarsi che:
  - L'indirizzo di fatturazione della sottoscrizione di Azure associato si trova nel Stati Uniti. Al momento non è possibile spostare una risorsa in un'altra sottoscrizione.
  - Il provisioning della risorsa di Servizi di comunicazione viene eseguito nel percorso Stati Uniti dati. Al momento non è possibile spostare una risorsa in un'altra posizione dati.

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
