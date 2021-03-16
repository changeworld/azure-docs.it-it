---
title: includere file
description: File di inclusione
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/15/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 97d80e999ac61a2c2f8f561dc19213419014beb8
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471012"
---
| Risorsa | Limite |
| --- | --- |
| Sottoscrizioni [associate a un tenant di Azure Active Directory](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) | Nessuna limitazione |
| [Coamministratori](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per ogni sottoscrizione |Nessuna limitazione |
| [Gruppi di risorse](../articles/azure-resource-manager/management/overview.md) per sottoscrizione |980 |
| Dimensione delle richieste delle API di Azure Resource Manager |4.194.304 byte |
| Tag per sottoscrizione<sup>1</sup> |50 |
| Calcoli di tag univoci per sottoscrizione<sup>1</sup> | 80.000 |
| [Distribuzioni a livello di sottoscrizione](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per sede | 800<sup>2</sup> |

<sup>1</sup>È possibile applicare fino a 50 tag direttamente a una sottoscrizione. La sottoscrizione può tuttavia contenere un numero illimitato di tag che vengono applicati ai gruppi di risorse e alle risorse all'interno della sottoscrizione. Il numero di tag per risorsa o per gruppo di risorse è limitato a 50. Gestione risorse restituisce un [elenco di nomi e valori di tag univoci](/rest/api/resources/tags) nella sottoscrizione solo se il numero di tag è 80.000 o meno. È ancora possibile trovare una risorsa in base al tag quando il numero supera 80.000.

<sup>2</sup>Se si raggiunge il limite di 800 distribuzioni, eliminare quelle non più necessarie dalla cronologia. Per eliminare le distribuzioni a livello di sottoscrizione, usare [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) o [az deployment sub delete](/cli/azure/deployment/sub#az-deployment-sub-delete).
