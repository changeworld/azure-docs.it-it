---
title: Identità gestite
description: Servizi multimediali può essere usato con le identità gestite di Azure.
keywords: ''
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 71a2b8f0734de80f71dbb2372f8600b464d6c606
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258440"
---
# <a name="managed-identities"></a>Identità gestite

Uno dei problemi a cui devono far fronte gli sviluppatori riguarda la gestione di segreti e credenziali per proteggere le comunicazioni tra servizi diversi. In Azure le identità gestite eliminano la necessità di gestire le credenziali fornendo un'identità per la risorsa di Azure in Azure AD che è possibile usare per ottenere i token di Azure Active Directory (Azure AD).

Esistono attualmente due scenari in cui è possibile usare le identità gestite con servizi multimediali:

- Usare l'identità gestita dell'account di servizi multimediali per accedere agli account di archiviazione.

- Usare l'identità gestita dell'account di servizi multimediali per accedere a Key Vault per accedere alle chiavi del cliente.

Nelle due sezioni successive vengono descritti i passaggi dei due scenari.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Usare l'identità gestita dell'account di servizi multimediali per accedere agli account di archiviazione

1. Creare un account di servizi multimediali con un'identità gestita.
1. Concedere all'entità di identità gestita l'accesso a un account di archiviazione di cui si è proprietari.
1. Servizi multimediali può quindi accedere all'account di archiviazione per conto dell'utente usando l'identità gestita.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Usare l'identità gestita dell'account di servizi multimediali per accedere a Key Vault per accedere alle chiavi del cliente

1. Creare un account di servizi multimediali con un'identità gestita.
1. Concedere all'entità di identità gestita l'accesso a un Key Vault di cui si è proprietari.
1. Configurare l'account di servizi multimediali per l'uso della crittografia dell'account basata sulla chiave del cliente.
1. Servizi multimediali accede Key Vault per conto dell'utente usando l'identità gestita.

Per altre informazioni sulle chiavi gestite dal cliente e Key Vault, vedere [Bring your own key (chiavi gestite dal cliente) con servizi multimediali](concept-use-customer-managed-keys-byok.md)

## <a name="tutorials"></a>Esercitazioni

In queste esercitazioni sono inclusi entrambi gli scenari descritti in precedenza.

- [Usare chiavi gestite dal cliente o BYOK nel portale di Azure con Servizi multimediali](tutorial-byok-portal.md)
- [Usare chiavi gestite dal cliente o BYOK con l'API REST di servizi multimediali](tutorial-byok-postman.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle identità gestite che è possibile eseguire per l'utente e le applicazioni Azure, vedere [Azure ad identità gestite](../../active-directory/managed-identities-azure-resources/overview.md).
