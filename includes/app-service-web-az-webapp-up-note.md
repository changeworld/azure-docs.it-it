---
title: includere file
description: includere file
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: d285b48606485fbd7f53511a15be1e2a31791829
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244507"
---
> [!NOTE]
> Il comando `az webapp up` esegue le azioni seguenti:
>
>- Creare un [gruppo di risorse](/cli/azure/group#az-group-create)predefinito.
>
>- Creare un [piano di servizio app](/cli/azure/appservice/plan#az-appservice-plan-create)predefinito.
>
>- [Creare un'app](/cli/azure/webapp#az-webapp-create) con il nome specificato.
>
>- [Distribuire file ZIP](../articles/app-service/deploy-zip.md) i dalla directory di lavoro corrente all'app.
>