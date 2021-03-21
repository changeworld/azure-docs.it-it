---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023186"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se l'archivio dati si trova all'interno di una rete locale, una rete virtuale di Azure o un cloud privato virtuale di Amazon, è necessario configurare un [runtime di integrazione self-hosted](../articles/data-factory/create-self-hosted-integration-runtime.md) per connettersi.

In alternativa, se l'archivio dati è un servizio dati del cloud gestito, è possibile usare Azure Integration Runtime. Se l'accesso è limitato solo agli indirizzi IP consentiti nelle regole del firewall, è possibile aggiungere gli [IP di Azure Integration Runtime](../articles/data-factory/azure-integration-runtime-ip-addresses.md) nell'elenco elementi consentiti. 

Per altre informazioni sui meccanismi di sicurezza di rete e sulle opzioni supportate da Data Factory, vedere [strategie di accesso ai dati](../articles/data-factory/data-access-strategies.md).
