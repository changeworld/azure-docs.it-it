---
title: includere file
description: includere file
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 04/08/2021
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 08fccf366321b075542f36b86c9bf22d5d877167
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304419"
---
L'opzione del protocollo AMQP-over-WebSockets viene eseguita sulla porta TCP 443 proprio come l'API HTTP/REST, ma è funzionalmente identica a quella di AMQP. Questa opzione ha una latenza di connessione iniziale superiore a causa di round trip di handshake aggiuntive e un sovraccarico leggermente maggiore come compromesso per la condivisione della porta HTTPS. Se questa modalità è selezionata, la porta TCP 443 è sufficiente per la comunicazione. Le opzioni seguenti consentono di selezionare la modalità WebSockets di AMQP. 

| Linguaggio | Opzione   |
| -------- | ----- |
| .NET (Azure.Messaging.ServiceBus)    | Creare [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient.-ctor) usando un costruttore che accetta [ServiceBusClientOptions](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions) come parametro. Impostare [ServiceBusClientOptions. TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions.transporttype) su [ServiceBusTransportType. AmqpWebSockets](/dotnet/api/azure.messaging.servicebus.servicebustransporttype) |
| .NET (Microsoft.Azure.ServiceBus)    | Quando si creano oggetti client, usare costruttori che accettano [TransportType](/dotnet/api/microsoft.azure.servicebus.transporttype), [ServiceBusConnection](/dotnet/api/microsoft.azure.servicebus.servicebusconnection)o [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) come parametri. <p>Per la costruzione che accetta `transportType` come parametro, impostare il parametro su [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype).</p> <p>Per il costruttore che accetta `ServiceBusConnection` come parametro, impostare [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) su [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype).</p> <p>Se si usa `ServiceBusConnectionStringBuilder` , usare costruttori che offrono un'opzione per specificare `transportType` .</p> |
| Java (com. Azure. Messaging. ServiceBus)     | Quando si creano client, impostare [ServiceBusClientBuilder. transportType](/java/api/com.azure.messaging.servicebus.servicebusclientbuilder.transporttype) su [AmqpTransportType.AMQP.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) |
| Java (com. Microsoft. Azure. ServiceBus)    | Quando si creano client, impostare `transportType` in [com. Microsoft. Azure. ServiceBus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings#com_microsoft_azure_servicebus_ClientSettings_ClientSettings_com_microsoft_azure_servicebus_security_TokenProvider_com_microsoft_azure_servicebus_primitives_RetryPolicy_java_time_Duration_com_microsoft_azure_servicebus_primitives_TransportType_)  su [com.Microsoft.Azure.ServiceBus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| JavaScript  | Quando si creano gli oggetti client del bus di servizio, usare la `webSocketOptions` Proprietà in [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions). |
| Python | Quando si creano client del bus di servizio, impostare [ServiceBusClient.transport_type](/python/api/azure-servicebus/azure.servicebus.servicebusclient) su [TransportType. AmqpOverWebSocket](/python/api/azure-servicebus/azure.servicebus.transporttype) |

