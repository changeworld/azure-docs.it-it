---
title: Introduzione all'interoperabilità di Teams con Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services quickstart
description: Questa guida di avvio rapido illustra come partecipare a una riunione di Teams con la libreria client per le chat di Servizi di comunicazione di Azure
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 75580931b2df33b02b9759b6faaa5bec548daaae
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496176"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Avvio rapido: Aggiungere l'app di chat a una riunione di Teams

> [!IMPORTANT]
> Per abilitare o disabilitare l' [interoperabilità del tenant teams](../../concepts/teams-interop.md), completare [il modulo](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Iniziare a usare Servizi di comunicazione di Azure connettendo la soluzione per la chat a Microsoft Teams tramite la libreria client JavaScript. 

## <a name="prerequisites"></a>Prerequisiti 

1. Una  [distribuzione di Teams](/deployoffice/teams-install). 
2. Un'[app di chat](./get-started.md) funzionante. 

## <a name="enable-teams-interoperability"></a>Abilitare l'interoperabilità di Teams 

Un utente di Servizi di comunicazione che partecipa a una riunione di Teams come utente guest può accedere alla chat della riunione solo dopo essere stato aggiunto alla chiamata della riunione di Teams. Per informazioni su come aggiungere un utente di Servizi di comunicazione a una chiamata di riunione di Teams, vedere la documentazione sull'[interoperabilità di Teams](../voice-video-calling/get-started-teams-interop.md).

Per usare questa funzionalità, è necessario essere un membro dell'organizzazione proprietaria di entrambe le entità.

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Vedere altre informazioni sulla [pulizia delle risorse](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

- Consultare l'[esempio hero per le chat](../../samples/chat-hero-sample.md)
- Altre informazioni sul [funzionamento della chat](../../concepts/chat/concepts.md)