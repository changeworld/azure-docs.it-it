---
title: Archiviazione attendibile per servizi multimediali
description: L'autenticazione delle identità gestite consente ai servizi multimediali di accedere all'account di archiviazione che è stato configurato con un firewall o una restrizione VNet tramite accesso di archiviazione attendibile.
keywords: archiviazione attendibile, identità gestite
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 59c1eb7936bc113f8935d6fa2ad378c6994c3ca9
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99408520"
---
# <a name="trusted-storage-for-media-services"></a>Archiviazione attendibile per servizi multimediali

Quando si crea un account di servizi multimediali, è necessario associarlo a un account di archiviazione. Servizi multimediali può accedere a tale account di archiviazione usando l'autenticazione di sistema. Servizi multimediali verifica che l'account di servizi multimediali e l'account di archiviazione si trovino nella stessa sottoscrizione e che l'utente che aggiunge l'associazione abbia accesso all'account di archiviazione con Azure Resource Manager RBAC.

Tuttavia, se si vuole usare un firewall per proteggere l'account di archiviazione e abilitare l'archiviazione attendibile, è necessario usare l'autenticazione delle [identità gestite](concept-managed-identities.md) . Consente ai servizi multimediali di accedere all'account di archiviazione configurato con un firewall o una restrizione VNet tramite l'accesso di archiviazione attendibile.

Per comprendere i metodi di creazione di un archivio attendibile con identità gestite, leggere [identità gestite e servizi multimediali](concept-managed-identities.md).

Per altre informazioni sulle chiavi gestite dal cliente e Key Vault, vedere [Bring your own key (chiavi gestite dal cliente) con servizi multimediali](concept-use-customer-managed-keys-byok.md)

Per altre informazioni sui servizi Microsoft attendibili, vedere [configurare i firewall e le reti virtuali di archiviazione di Azure](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="tutorials"></a>Esercitazioni

In queste esercitazioni sono inclusi entrambi gli scenari descritti in precedenza.

- [Usare chiavi gestite dal cliente o BYOK nel portale di Azure con Servizi multimediali](tutorial-byok-portal.md)
- [Usare chiavi gestite dal cliente o BYOK con l'API REST di servizi multimediali](tutorial-byok-postman.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle identità gestite che è possibile eseguire per l'utente e le applicazioni Azure, vedere [Azure ad identità gestite](../../active-directory/managed-identities-azure-resources/overview.md).