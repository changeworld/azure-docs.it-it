---
title: Creare una risorsa di Servizi cognitivi con la libreria client di gestione di Azure
titleSuffix: Azure Cognitive Services
description: Creare e gestire le risorse di Servizi cognitivi di Azure con la libreria client di gestione di Azure.
services: cognitive-services
keywords: servizi cognitivi, intelligenza cognitiva, soluzioni cognitive, servizi di intelligenza artificiale
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 03/15/2021
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: e042ac263d3a30a9790ba6a3a3d404e5e9cb9aad
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472163"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Avvio rapido: Creare una risorsa di Servizi cognitivi con la libreria client di gestione di Azure

Seguire questo argomento di avvio rapido per creare e gestire risorse di Servizi cognitivi di Azure con la libreria client di gestione di Azure.

Servizi cognitivi di Azure è una famiglia di servizi basati sul cloud con API REST e librerie client disponibili per aiutare gli sviluppatori a creare Intelligence cognitive nelle proprie applicazioni. Gli sviluppatori non necessitano di intelligenza artificiale diretta (AI) o di data science competenze o conoscenze per ottenere risultati positivi. I Servizi cognitivi di Azure consentono agli sviluppatori di aggiungere facilmente funzionalità cognitive alle applicazioni con soluzioni cognitive in grado di vedere, ascoltare, parlare, comprendere e persino di iniziare a ragionare.

I singoli servizi di intelligenza artificiale sono rappresentati da [risorse](../azure-resource-manager/management/manage-resources-portal.md) di Azure create nella sottoscrizione di Azure. Dopo aver creato una risorsa, è possibile usare le chiavi e gli endpoint generati per autenticare le applicazioni.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end
