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
ms.openlocfilehash: fd92eed127ec50a3d3a86f667d9aa764b79c190a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585393"
---
# <a name="trusted-storage-for-media-services"></a>Archiviazione attendibile per servizi multimediali

Quando si crea un account di servizi multimediali, è necessario associarlo a un account di archiviazione. Servizi multimediali può accedere a tale account di archiviazione usando l'autenticazione di sistema o l'autenticazione dell'identità gestita. Servizi multimediali verifica che l'account di servizi multimediali e l'account di archiviazione si trovino nella stessa sottoscrizione e che l'utente che aggiunge l'associazione abbia accesso all'account di archiviazione con Azure Resource Manager RBAC.

>[!NOTE]
>L'archiviazione attendibile è disponibile solo nell'API e non è attualmente abilitata nel portale di Azure.

## <a name="trusted-storage-with-a-firewall"></a>Archiviazione attendibile con un firewall

Tuttavia, se si vuole usare un firewall per proteggere l'account di archiviazione e abilitare l'archiviazione attendibile, l'autenticazione [identità gestite](concept-managed-identities.md) è l'opzione preferita. Consente ai servizi multimediali di accedere all'account di archiviazione configurato con un firewall o una restrizione VNet tramite l'accesso di archiviazione attendibile.

## <a name="tutorial"></a>Esercitazione

Per altre informazioni sull'abilitazione dell'archiviazione attendibile, vedere l'esercitazione sull' [archiviazione attendibile di servizi multimediali](tutorial-trusted-storage-rest.md) .

> [!NOTE]
> Per consentire a servizi multimediali di leggere e scrivere nell'account di archiviazione, è necessario concedere l'accesso ai dati BLOB di archiviazione delle identità gestite di AMS.  La concessione del ruolo di collaboratore generico non funzionerà perché non Abilita le autorizzazioni corrette sul piano dati.

## <a name="further-reading"></a>Ulteriori informazioni

Per comprendere i metodi di creazione di un archivio attendibile con identità gestite, leggere [identità gestite e servizi multimediali](concept-managed-identities.md).

Per altre informazioni sui servizi Microsoft attendibili, vedere [configurare i firewall e le reti virtuali di archiviazione di Azure](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle identità gestite che è possibile eseguire per l'utente e le applicazioni Azure, vedere [Azure ad identità gestite](../../active-directory/managed-identities-azure-resources/overview.md).
