---
title: Scenari di sicurezza di Azure Key Vault | Microsoft Docs
description: Azure Key Vault è un servizio multi-tenant. Usa un pool di HMS in ogni area di Azure. Tutte le aree in un'area geografica condividono un limite crittografico.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 0d82a3cb4c08d47b6827072378b9827037d32412
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751815"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Scenari di sicurezza di Azure Key Vault e confini geografici

I prodotti Azure sono disponibili in diverse aree geografiche [di Azure,](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)con ogni area geografica di Azure contenente una o più aree. Ad esempio, la geografia Europa contiene due aree, Europa settentrionale ed Europa occidentale, mentre l'unica area geografica del Brasile è Brasile meridionale.

Azure Key Vault è un servizio multi-tenant che usa un pool di moduli di sicurezza hardware (HMS). Tutti gli HMS in un'area geografica condividono lo stesso limite crittografico, definito "mondo della sicurezza". Ogni area geografica corrisponde a un singolo mondo di sicurezza e viceversa.

Stati Uniti orientali e Stati Uniti occidentali condividono lo stesso mondo della sicurezza perché appartengono alla geografia (Stati Uniti). Analogamente, tutte le aree di Azure in Giappone condividono lo stesso mondo della sicurezza, come tutte le aree di Azure in Australia e così via.

>[!NOTE]
> Un'eccezione è che il Dipartimento della difesa Stati Uniti orientali e il Dipartimento della difesa Stati Uniti centrali hanno i propri scenari di sicurezza.

## <a name="backup-and-restore-behavior"></a>Comportamento di backup e ripristino

Un backup eseguito di una chiave da un insieme di credenziali delle chiavi in un'area di Azure può essere ripristinato in un insieme di credenziali delle chiavi in un'altra area di Azure, purché entrambe le condizioni siano vere:

- Entrambe le aree di Azure appartengono alla stessa area geografica.
- Entrambi gli insiemi di credenziali delle chiavi appartengono alla stessa sottoscrizione di Azure.

Ad esempio, un backup eseguito di una chiave in un insieme di credenziali delle chiavi dell'India occidentale può essere ripristinato in un altro insieme di credenziali delle chiavi nella stessa sottoscrizione nell'area geografica India (india occidentale, India centrale e India meridionale).

## <a name="next-steps"></a>Passaggi successivi

- Vedere [i prodotti Microsoft per area](https://azure.microsoft.com/regions/services/)
