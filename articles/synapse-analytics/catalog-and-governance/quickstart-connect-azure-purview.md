---
title: Connettere un account Azure Purview 
description: Connettere un account Azure Purview a un'area di lavoro di Synapse.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: f85a76a7ce01c11255f54c6b956088f9328dfc9f
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918465"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>Guida introduttiva: Connettere un account Azure Purview a un'area di lavoro di Synapse 

> [!IMPORTANT]
> L'integrazione tra Azure Synapse Analytics e Azure Purview è attualmente disponibile in anteprima. Se si è interessati a provare Azure Purview in Synapse, contattare il rappresentante Microsoft.

In questa guida di avvio rapido si registrerà un account Azure Purview in un'area di lavoro di Synapse. Tale connessione consente di individuare gli asset di Azure Purview e interagire con essi tramite le funzionalità di Synapse. 

In Synapse è possibile eseguire le attività seguenti: 
- Usare la casella di ricerca nella parte superiore per trovare asset di Purview in base a parole chiave 
- Comprendere i dati in base a metadati, derivazione, annotazioni 
- Connettere tali dati all'area di lavoro con i servizi collegati o i set di dati di integrazione 
- Analizzare tali set di dati con Synapse Apache Spark, Synapse SQL e Flusso di dati 

## <a name="prerequisites"></a>Prerequisiti 
- [Account di Azure Purview](../../purview/create-catalog-portal.md) 
- [Area di lavoro di Synapse](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Accedere a un'area di lavoro di Synapse 

Passare a https://web.azuresynapse.net e accedere all'area di lavoro. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Autorizzazioni per la connessione di un account Azure Purview 

- Per connettere un account di Azure a un'area di lavoro di Synapse, è necessario un ruolo **Collaboratore** nell'area di lavoro di Synapse nella gestione delle identità e degli accessi del portale di Azure ed è necessario accedere a tale account Azure Purview.

## <a name="connect-an-azure-purview-account"></a>Connettere un account Azure Purview  

- Nell'area di lavoro di Synapse passare a **Gestisci** -> **Azure Purview**. Selezionare **Connect to a Purview account** (Connetti a un account Purview). 
- È possibile scegliere **Dalla sottoscrizione di Azure** o **Immetti manualmente**. Con l'opzione **Dalla sottoscrizione di Azure** è possibile selezionare l'account a cui si ha accesso. 
- Dopo la connessione, sarà possibile visualizzare il nome dell'account Purview nella scheda **Azure Purview account** (Account Azure Purview). 
- Per cercare i dati, è possibile usare la barra di ricerca nella parte centrale in alto dell'area di lavoro di Synapse. 

## <a name="nextsteps"></a>Passaggi successivi 

[Registrare e analizzare gli asset di Azure Synapse in Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)

[Individuare, connettere ed esplorare dati in Synapse con Azure Purview](how-to-discover-connect-analyze-azure-purview.md)   