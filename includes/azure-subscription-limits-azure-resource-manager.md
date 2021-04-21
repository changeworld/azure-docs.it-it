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
ms.openlocfilehash: f02b7f0f80cfb875cc6207b542db90607b379b67
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800377"
---
| Risorsa | Limite |
| --- | --- |
| Sottoscrizioni [associate a un tenant Azure Active Directory](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) | Nessuna limitazione |
| [Coamministratori](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per ogni sottoscrizione |Nessuna limitazione |
| [Gruppi di risorse](../articles/azure-resource-manager/management/overview.md) per sottoscrizione |980 |
| Dimensione delle richieste delle API di Azure Resource Manager |4.194.304 byte |
| Tag per sottoscrizione<sup>1</sup> |50 |
| Calcoli di tag univoci per sottoscrizione<sup>1</sup> | 80.000 |
| [Distribuzioni a livello di sottoscrizione](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per sede | 800<sup>2</sup> |

<sup>1</sup>È possibile applicare fino a 50 tag direttamente a una sottoscrizione. La sottoscrizione può tuttavia contenere un numero illimitato di tag che vengono applicati ai gruppi di risorse e alle risorse all'interno della sottoscrizione. Il numero di tag per risorsa o per gruppo di risorse è limitato a 50. Resource Manager restituisce un [elenco di](/rest/api/resources/tags) valori e nomi di tag univoci nella sottoscrizione solo quando il numero di tag è 80.000 o inferiore. È comunque possibile trovare una risorsa in base al tag quando il numero supera 80.000.

<sup>2</sup>Se si raggiunge il limite di 800 distribuzioni, eliminare quelle non più necessarie dalla cronologia. Per eliminare le distribuzioni a livello di sottoscrizione, usare [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) o [az deployment sub delete](/cli/azure/deployment/sub#az_deployment_sub_delete).
