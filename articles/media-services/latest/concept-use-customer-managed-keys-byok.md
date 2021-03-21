---
title: Bring your own key (chiavi gestite dal cliente)
description: Con servizi multimediali è possibile usare una chiave gestita dal cliente (ovvero Bring your own key).
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 4564e28f76aebe7f708c2b6f68903fe67bcefe26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698859"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>Bring your own key (chiavi gestite dal cliente) con servizi multimediali

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bring Your Own Key (BYOK) è un'iniziativa di Azure Wide che consente ai clienti di spostare i carichi di lavoro nel cloud. Le chiavi gestite dal cliente consentono ai clienti di rispettare le normative di conformità del settore e di migliorare l'isolamento dei tenant di un servizio. Fornire ai clienti il controllo delle chiavi di crittografia è un modo per ridurre al minimo l'accesso e il controllo e la confidenza di compilazione nei servizi Microsoft.

## <a name="keys-and-key-management"></a>Chiavi e gestione delle chiavi

Quando si usa l'API di servizi multimediali 2020-05-01, è possibile usare la propria chiave con servizi multimediali. Viene creata una chiave di account predefinita per tutti gli account crittografati con una chiave di sistema di proprietà di servizi multimediali. Quando si usa una chiave personalizzata, la chiave dell'account viene crittografata con la chiave. Le chiavi simmetriche vengono crittografate tramite la chiave dell'account. Vengono crittografati anche gli URL JobInputHttp e le chiavi di convalida del token simmetrico.

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="Una chiave gestita dal cliente sostituisce una chiave gestita dal sistema":::

Servizi multimediali usa l'identità gestita dell'account di servizi multimediali per leggere la chiave da un Key Vault di proprietà dell'utente. Servizi multimediali richiede che il Key Vault si trovi nella stessa area dell'account e che sia abilitata la protezione dell'eliminazione temporanea e della ripulitura.

La chiave può essere una chiave di 2048, 3072 o una chiave RSA 4096, mentre le chiavi HSM e software sono supportate.

> [!NOTE]
> Le chiavi EC non sono supportate.

È possibile specificare il nome della chiave e la versione della chiave oppure solo un nome di chiave. Quando si usa solo un nome di chiave, servizi multimediali userà la versione più recente della chiave. Vengono rilevate automaticamente nuove versioni delle chiavi del cliente e la chiave dell'account viene nuovamente crittografata.

> [!WARNING]
> Servizi multimediali monitora l'accesso alla chiave del cliente. Se la chiave del cliente diventa inaccessibile, ad esempio se la chiave è stata eliminata o se la Key Vault è stata eliminata o se la concessione di accesso è stata rimossa, servizi multimediali eseguirà la transizione dell'account allo stato inaccessibile della chiave del cliente (disabilitando in modo efficace l'account). Tuttavia, l'account può essere eliminato in questo stato. Le uniche operazioni supportate sono GET, LIST e DELETE per l'account. tutte le altre richieste (codifica, streaming e così via) avranno esito negativo fino a quando non viene ripristinato l'accesso alla chiave dell'account.

## <a name="double-encryption"></a>Crittografia doppia

Servizi multimediali supporta automaticamente la crittografia doppia. Per i dati inattivi, il primo livello di crittografia usa una chiave gestita dal cliente o una chiave gestita da Microsoft, a seconda dell' `AccountEncryption` impostazione dell'account.  Il secondo livello di crittografia per i dati inattivi viene fornito automaticamente usando una chiave gestita Microsoft separata. Per altre informazioni sulla crittografia doppia, vedere [crittografia doppia di Azure](../../security/fundamentals/double-encryption.md).

> [!NOTE]
> La crittografia doppia viene abilitata automaticamente nell'account di servizi multimediali. Tuttavia, è necessario configurare separatamente la chiave gestita dal cliente e la doppia crittografia nell'account di archiviazione. Vedere [crittografia storege](../../storage/common/storage-service-encryption.md).

## <a name="tutorials"></a>Esercitazioni

- [Usare chiavi gestite dal cliente o BYOK nel portale di Azure con Servizi multimediali](tutorial-byok-portal.md)
- [Usare chiavi gestite dal cliente o BYOK con l'API REST di servizi multimediali](tutorial-byok-postman.md).

## <a name="next-steps"></a>Passaggi successivi

[Proteggi i tuoi contenuti con la crittografia dinamica di servizi multimediali](content-protection-overview.md)