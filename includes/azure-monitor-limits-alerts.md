---
title: includere file
description: File di inclusione
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 071f2849a877f4ea1e8a84eff6ccfb8343be3ec7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734065"
---
| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Avvisi delle metriche (classici) |100 regole di avviso attive per ogni sottoscrizione. | Chiama il supporto |
| Avvisi delle metriche |5\.000 regole di avviso attive per ogni sottoscrizione in cloud pubblico di Azure, Azure Cina (21Vianet) e Azure per enti pubblici. Se si raggiunge questo limite, esplorare se è possibile usare [avvisi per più risorse dello stesso tipo](../articles/azure-monitor/alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).<br/>5\.000 serie temporali di metriche per regola di avviso. | Contattare il supporto tecnico. |
| Avvisi dei log attività | 100 regole di avviso attive per sottoscrizione (non aumentabile). | Uguale all'impostazione predefinita |
| Avvisi relativi ai log | 512 regole di avviso attive per ogni sottoscrizione. 200 regole di avviso attive per ogni risorsa. | Chiama il supporto |
| Lunghezza descrizione per regole di avviso e regole di azione| Avvisi di ricerca log: 4096 caratteri<br/>Tutti gli altri: 2048 caratteri | Uguale all'impostazione predefinita |