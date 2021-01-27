---
title: File di inclusione
description: File di inclusione
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 34388fe975df359c695b9358cafcd3ae711af0cf
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901019"
---
Dopo l'applicazione dei tag alle risorse è possibile visualizzare i costi per le risorse con i tag specificati. La visualizzazione dell'utilizzo più recente nell'analisi dei costi richiede tempo, quindi è possibile che i costi non siano ancora visualizzati. Quando i costi sono disponibili, è possibile visualizzare i costi per le risorse nei gruppi di risorse della sottoscrizione. Per visualizzare i costi, gli utenti devono avere l'[accesso a livello di sottoscrizione alle informazioni di fatturazione](../articles/cost-management-billing/manage/manage-billing-access.md).

Per visualizzare i costi in base al tag nel portale, selezionare la sottoscrizione e quindi **Analisi dei costi**.

![Analisi dei costi](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Filtrare quindi in base al valore del tag e selezionare **Applica**.

![Visualizzare il costo in base al tag](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

È anche possibile usare la [Panoramica dell'API utilizzo di Azure](../articles/cost-management-billing/manage/consumption-api-overview.md) per visualizzare i costi a livello di codice.
