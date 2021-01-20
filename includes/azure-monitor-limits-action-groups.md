---
title: File di inclusione
description: File di inclusione
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 963e49ea0e5536be0fca6d565b439aef4a08793d
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98605249"
---
| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Push dell'app Azure | 10 azioni dell'app Azure per gruppo di azioni. | Uguale all'impostazione predefinita |
| E-mail | 1\.000 azioni di posta elettronica in un gruppo di azioni.<br>non più di 100 messaggi di posta elettronica in un'ora.<br>Vedere anche le [informazioni sulla limitazione della frequenza](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Uguale all'impostazione predefinita |
| Gestione dei servizi IT | 10 azioni di Gestione dei servizi IT in un gruppo di azioni. | Uguale all'impostazione predefinita | 
| App per la logica | 10 azioni di app per la logica in un gruppo di azioni. | Uguale all'impostazione predefinita |
| Runbook | 10 azioni di runbook in un gruppo di azioni. | Uguale all'impostazione predefinita |
| SMS | 10 azioni di SMS in un gruppo di azioni.<br>Non più di 1 messaggio SMS ogni 5 minuti.<br>Vedere anche le [informazioni sulla limitazione della frequenza](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Uguale all'impostazione predefinita |
| Chiamata vocale | 10 azioni di voce in un gruppo di azioni.<br>Non più di 1 chiamata vocale ogni 5 minuti.<br>Vedere anche le [informazioni sulla limitazione della frequenza](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Uguale all'impostazione predefinita |
| webhook | 10 azioni di webhook in un gruppo di azioni.  Il numero massimo di chiamate al webhook è 1500 al minuto per sottoscrizione. Altri limiti sono disponibili nelle [informazioni specifiche delle azioni](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Uguale all'impostazione predefinita |
