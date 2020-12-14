---
title: File di inclusione
description: File di inclusione
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/11/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0056e18b6cb3aad2a4504bbe20b3b3421793489e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356269"
---
Questi esempi illustrano come usare Monitoraggio di Azure per creare avvisi per le sottoscrizioni di cui è stato eseguito l'onboarding per la gestione risorse delegate di Azure.

| **Modello** | **Descrizione** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Esegue una query sull'ultimo giorno di attività in un tenant di gestione e [segnala eventuali deleghe aggiunte o rimosse](../articles/lighthouse/how-to/monitor-delegation-changes.md) (o tentativi non riusciti).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Questo modello crea un avviso di Azure e si connette a un gruppo di azioni esistente.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Crea più avvisi dei log basati su query Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Distribuisce un avviso in un tenant quando un utente delega una sottoscrizione a un tenant per la gestione.|
| [workbook-activitylogs-by-domain](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) | Visualizza i log attività di Azure tra sottoscrizioni con un'opzione per filtrare in base a nome di dominio. |
