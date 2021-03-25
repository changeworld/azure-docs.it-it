---
title: Eseguire l'autenticazione in servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Informazioni sui vari modi in cui un'app o un servizio può eseguire l'autenticazione ai servizi di comunicazione.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 9edfb63f5ce43ed325b4c4a1fa67e0e9ca52dc89
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110866"
---
# <a name="authenticate-to-azure-communication-services"></a>Eseguire l'autenticazione in servizi di comunicazione di Azure

Ogni interazione del client con i servizi di comunicazione di Azure deve essere autenticata. In un'architettura tipica, vedere [architettura client e server](./client-and-server-architecture.md), *chiavi di accesso* o *identità gestite* vengono usate per l'autenticazione.

Un altro tipo di autenticazione usa i *token di accesso utente* per l'autenticazione in servizi che richiedono la partecipazione dell'utente. Ad esempio, la chat o il servizio chiamante usa i *token di accesso utente* per consentire agli utenti di essere aggiunti in un thread e avere conversazioni reciproche.

## <a name="authentication-options"></a>Opzioni di autenticazione

La tabella seguente illustra gli SDK di servizi di comunicazione Azure e le relative opzioni di autenticazione:

| SDK    | Opzione di autenticazione                               |
| ----------------- | ----------------------------------------------------|
| Identità          | Chiave di accesso o identità gestita                      |
| SMS               | Chiave di accesso o identità gestita                      |
| Numeri di telefono     | Chiave di accesso o identità gestita                      |
| Chiamata           | Token di accesso utente                                   |
| Chat              | Token di accesso utente                                   |

Ogni opzione di autorizzazione viene descritta brevemente di seguito:

### <a name="access-key"></a>Chiave di accesso

L'autenticazione della chiave di accesso è adatta per le applicazioni di servizio in esecuzione in un ambiente del servizio attendibile. La chiave di accesso è disponibile nel portale dei servizi di comunicazione di Azure. L'applicazione di servizio la utilizza come credenziale per inizializzare gli SDK corrispondenti. Vedere un esempio di come viene usato in [Identity SDK](../quickstarts/access-tokens.md). 

Poiché la chiave di accesso fa parte della stringa di connessione della risorsa, l'autenticazione con una stringa di connessione equivale all'autenticazione con una chiave di accesso.

Se si vuole chiamare manualmente le API ACS usando una chiave di accesso, sarà necessario firmare la richiesta. La firma della richiesta viene illustrata in dettaglio in un' [esercitazione](../tutorials/hmac-header-tutorial.md).

### <a name="managed-identity"></a>Identità gestita

Identità gestite offre una maggiore sicurezza e facilità d'uso rispetto ad altre opzioni di autorizzazione. Con Azure AD, ad esempio, si evita di dover archiviare la chiave di accesso dell'account nel codice, come avviene con l'autorizzazione della chiave di accesso. Sebbene sia possibile continuare a utilizzare l'autorizzazione della chiave di accesso con le applicazioni di servizi di comunicazione, Microsoft consiglia di passare a Azure AD laddove possibile. 

Per configurare un'identità gestita, [creare un'applicazione registrata dall'interfaccia della riga di](../quickstarts/managed-identity-from-cli.md)comando di Azure. Quindi, l'endpoint e le credenziali possono essere usati per autenticare gli SDK. Vedere esempi di come viene usata l' [identità gestita](../quickstarts/managed-identity.md) .

### <a name="user-access-tokens"></a>Token di accesso utente

I token di accesso utente vengono generati usando Identity SDK e sono associati agli utenti creati in Identity SDK. Vedere un esempio di come [creare utenti e generare token](../quickstarts/access-tokens.md). I token di accesso utente vengono quindi usati per autenticare i partecipanti aggiunti alle conversazioni nella chat o nell'SDK chiamante. Per altre informazioni, vedere [aggiungere la chat all'app](../quickstarts/chat/get-started.md). L'autenticazione dei token di accesso utente è diversa rispetto alla chiave di accesso e all'autenticazione di identità gestita perché viene usata per autenticare un utente anziché una risorsa di Azure protetta.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare e gestire le risorse](../quickstarts/create-communication-resource.md) 
>  di servizi di comunicazione [Creare un'applicazione di identità gestita Azure Active Directory dall'interfaccia](../quickstarts/managed-identity-from-cli.md) 
>  della riga di comando di Azure [Creare token di accesso utente](../quickstarts/access-tokens.md)

Per altre informazioni, vedere gli articoli seguenti:
- [Informazioni sull'architettura client e server](../concepts/client-and-server-architecture.md)
