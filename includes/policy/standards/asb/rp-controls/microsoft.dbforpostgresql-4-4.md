---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 494cf5e7e16ae7aac516eac6c8f411173256361d
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284308"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Il criterio Imponi connessione SSL deve essere abilitato per i server di database PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Il database di Azure per PostgreSQL supporta la connessione del server di database di Azure per PostgreSQL alle applicazioni client tramite SSL (Secure Sockets Layer). L'imposizione di connessioni SSL tra il server di database e le applicazioni client garantisce la protezione da attacchi 'man in the middle' tramite la crittografia del flusso di dati tra il server e l'applicazione. Questa configurazione impone che SSL sia sempre abilitato per l'accesso al server di database. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
