---
title: includere file
description: includere file
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: ca483d0b71bde945a7e46da785dd6a76b3a8f177
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693401"
---
L'opzione del protocollo AMQP-over-WebSockets viene eseguita sulla porta TCP 443 proprio come l'API HTTP/REST, ma è funzionalmente identica a quella di AMQP. Questa opzione presenta una latenza di connessione iniziale leggermente superiore a causa di round trip di handshake aggiuntive e un sovraccarico leggermente maggiore come compromesso per la condivisione della porta HTTPS. Se questa modalità è selezionata, la porta TCP 443 è sufficiente per la comunicazione. Le opzioni seguenti consentono di selezionare la modalità AMQP normale o AMQP WebSocket:

| Linguaggio | Opzione   |
| -------- | ----- |
| .NET     | Proprietà [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) con [TransportType. AMQP](/dotnet/api/microsoft.azure.servicebus.transporttype) o [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype) |
| Java     | [com. Microsoft. Azure. ServiceBus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings) con [com. Microsoft. Azure. ServiceBus. Primitives. TransportType. AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) o [com.Microsoft.Azure.ServiceBus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| Nodo  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions) ha un `webSocket` argomento del costruttore. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) con [TransportType. AMQP](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) o [TransportType. AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |