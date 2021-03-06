---
title: Impostazioni delle porte di Inoltro di Azure | Documentazione Microsoft
description: Questo articolo include una tabella che descrive la configurazione necessaria per i valori di porta per il servizio di inoltro di Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 81551055d967babaac6f12c3a23ce6b1e78afbd5
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314168"
---
# <a name="azure-relay-port-settings"></a>Impostazioni delle porte di inoltro di Azure

La tabella seguente descrive la configurazione necessaria per i valori della porta del servizio Inoltro di Azure.

## <a name="hybrid-connections"></a>connessioni ibride

Connessioni ibride Usa WebSocket sulla porta 443 con TLS come meccanismo di trasporto sottostante, che usa solo **https** . 

## <a name="wcf-relays"></a>Inoltri WCF
  
|Binding|Protezione del trasporto|Porta|  
|-------------|------------------------|----------|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (client)|Sì|HTTPS| 
|" |No|HTTP|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (servizio)|Prima o dopo|9351/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (client)|Sì|9351/HTTPS|  
|" |No|9350/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (servizio)|Prima o dopo|9351/HTTP|  
|[Classe NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (client/servizio)|Prima o dopo|5671/9352/HTTP (9352/9353 in caso di implementazione ibrida)|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (client)|Sì|9351/HTTPS|  
|" |No|9350/HTTP|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (servizio)|Prima o dopo|9351/HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (client)|Sì|HTTPS|  
|" |No|HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (servizio)|Prima o dopo|9351/HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (client)|Sì|HTTPS|  
|" |No|HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (servizio)|Prima o dopo|9351/HTTP|

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul servizio di inoltro di Azure, vedere i collegamenti seguenti:
* [Che cos'è Inoltro di Azure?](relay-what-is-it.md)
* [Domande frequenti sul servizio di inoltro](relay-faq.yml)