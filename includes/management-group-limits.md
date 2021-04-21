---
title: includere file
description: File di inclusione
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 53e3f37d14153f3a2d7b5886a49b08ca9052b128
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800240"
---
| Risorsa | Limite |
| --- | --- |
| Gruppi di gestione per tenant di Azure AD | 10,000 |
| Sottoscrizioni per gruppo di gestione | Senza limiti. |
| Livelli della gerarchia dei gruppi di gestione | Livello radice più 6 livelli<sup>1</sup> |
| Gruppo di gestione padre diretto per gruppo di gestione | Uno |
| [Distribuzioni a livello di gruppo di gestione](../articles/azure-resource-manager/templates/deploy-to-management-group.md) per località | 800<sup>2</sup> |

<sup>1</sup>I 6 livelli non includono il livello di sottoscrizione.

<sup>2</sup>Se si raggiunge il limite di 800 distribuzioni, eliminare dalla cronologia le distribuzioni che non sono più necessarie. Per eliminare le distribuzioni a livello di gruppo di gestione, usare [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) o [az deployment mg delete](/cli/azure/deployment/mg#az_deployment_mg_delete).
