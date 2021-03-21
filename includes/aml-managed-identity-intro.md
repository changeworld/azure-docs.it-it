---
title: includere file
description: includere file
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: 70b636b7bb508b71475a7464983b091d5d10e0e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027159"
---
 I cluster di calcolo Azure Machine Learning supportano anche le [identità gestite](../articles/active-directory/managed-identities-azure-resources/overview.md) per autenticare l'accesso alle risorse di Azure senza includere le credenziali nel codice. Sono disponibili due tipi di identità gestite:

* Un' **identità gestita assegnata dal sistema** è abilitata direttamente nel cluster di calcolo Azure Machine Learning. Il ciclo di vita di un'identità assegnata dal sistema è direttamente associato al cluster di elaborazione. Se il cluster di calcolo viene eliminato, Azure pulisce automaticamente le credenziali e l'identità in Azure AD.
* Un' **identità gestita assegnata dall'utente** è una risorsa di Azure autonoma fornita tramite il servizio di gestione delle identità di Azure. È possibile assegnare un'identità gestita assegnata dall'utente a più risorse e renderla permanente fino a quando si desidera.