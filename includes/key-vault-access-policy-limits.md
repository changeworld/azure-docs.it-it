---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9e4d60e501ffc5b61c87a80b8dd13698cca28737
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934536"
---
Key Vault supporta fino a 1024 voci di criteri di accesso, con ogni voce che concede un set di autorizzazioni distinto a una determinata entità di sicurezza. A causa di questa limitazione, è consigliabile assegnare i criteri di accesso a gruppi di utenti, laddove possibile, anziché a singoli utenti. L'uso dei gruppi semplifica la gestione delle autorizzazioni per più utenti dell'organizzazione. Per altre informazioni, vedere [gestire l'accesso alle app e alle risorse usando gruppi di Azure Active Directory](../articles/active-directory/fundamentals/active-directory-manage-groups.md)

Per i dettagli completi sul controllo di accesso di Key Vault, vedere [Sicurezza di Azure Key Vault: Gestione delle identità e dell'accesso](../articles/key-vault/general/security-overview.md#identity-management).