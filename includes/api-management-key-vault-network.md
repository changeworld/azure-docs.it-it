---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: a9dbedd8516f3a3a592c7fd4f4f5563011d6c6db
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491013"
---
#### <a name="requirements-for-key-vault-firewall"></a>Requisiti per Key Vault firewall

Se [Key Vault firewall](../articles/key-vault/general/network-security.md) è abilitato nell'insieme di credenziali delle chiavi, sono necessari i requisiti aggiuntivi seguenti:

* Per accedere all'insieme di credenziali delle chiavi, è necessario usare l'identità gestita **assegnata dal sistema** dell'istanza di gestione API.
* In Key Vault Firewall abilitare l'opzione **Consenti ai servizi Microsoft attendibili di ignorare questo firewall** .

#### <a name="virtual-network-requirements"></a>Requisiti della rete virtuale

Se l'istanza di gestione API viene distribuita in una rete virtuale, configurare anche le impostazioni di rete seguenti:

* Abilitare un [endpoint di servizio](../articles/key-vault/general/overview-vnet-service-endpoints.md) per Azure Key Vault nella subnet di gestione API.
* Configurare una regola del gruppo di sicurezza di rete (NSG) per consentire il traffico in uscita ai [tag del servizio](../articles/virtual-network/service-tags-overview.md)AzureKeyVault e AzureActiveDirectory. 

Per informazioni dettagliate, vedere la pagina relativa ai dettagli di configurazione di rete in [connettersi a una rete virtuale](../articles/api-management/api-management-using-with-vnet.md#-common-network-configuration-issues).