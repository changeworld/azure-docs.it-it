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
ms.openlocfilehash: b3cd0643a74ccadb8390ce906eb391420de15a29
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490790"
---
# <a name="authenticate-to-azure-communication-services"></a>Eseguire l'autenticazione in servizi di comunicazione di Azure

Ogni interazione del client con i servizi di comunicazione di Azure deve essere autenticata. In un'architettura tipica, vedere l' [architettura client e server](./client-and-server-architecture.md), le *chiavi di accesso* o l' *identità gestita* vengono usate nel servizio di accesso utente attendibile per creare gli utenti e rilasciare i token. Il *token di accesso utente* rilasciato dal servizio di accesso utente attendibile viene usato per le applicazioni client per accedere ad altri servizi di comunicazione, ad esempio chat o servizio chiamante.

Il servizio SMS di servizi di comunicazione Azure accetta anche le *chiavi di accesso* o l' *identità gestita* per l'autenticazione. Questa situazione si verifica in genere in un'applicazione di servizio in esecuzione in un ambiente del servizio attendibile.

Ogni opzione di autorizzazione viene descritta brevemente di seguito:

- Autenticazione della **chiave di accesso** per SMS e operazioni di identità. L'autenticazione della chiave di accesso è adatta per le applicazioni di servizio in esecuzione in un ambiente del servizio attendibile. La chiave di accesso è disponibile nel portale dei servizi di comunicazione di Azure. Per eseguire l'autenticazione con una chiave di accesso, un'applicazione di servizio usa la chiave di accesso come credenziale per inizializzare le librerie client SMS o Identity corrispondenti, vedere [creare e gestire i token di accesso](../quickstarts/access-tokens.md). Dato che la chiave di accesso fa parte della stringa di connessione della risorsa, vedere [creare e gestire le risorse di servizi di comunicazione](../quickstarts/create-communication-resource.md), l'autenticazione con la stringa di connessione equivale all'autenticazione con la chiave di accesso.
- Autenticazione dell' **identità gestita** per le operazioni SMS e Identity. L'identità gestita, vedere [identità gestita](../quickstarts/managed-identity.md), è adatta per le applicazioni di servizio in esecuzione in un ambiente del servizio attendibile. Per eseguire l'autenticazione con un'identità gestita, un'applicazione di servizio crea una credenziale con l'ID e un segreto dell'identità gestita, quindi Inizializza le librerie client SMS o Identity corrispondenti, vedere [creare e gestire i token di accesso](../quickstarts/access-tokens.md).
- Autenticazione del **token di accesso utente** per la chat e la chiamata. I token di accesso utente consentono alle applicazioni client di eseguire l'autenticazione in chat di comunicazione di Azure e di chiamare i servizi. Questi token vengono generati in un "servizio di accesso utente attendibile" creato dall'utente. Vengono quindi forniti ai dispositivi client che usano il token per inizializzare la chat e chiamare le librerie client. Per altre informazioni, vedere [aggiungere la chat all'app](../quickstarts/chat/get-started.md) , ad esempio.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare e gestire le risorse](../quickstarts/create-communication-resource.md) 
>  di servizi di comunicazione [Creare un'applicazione di identità gestita Azure Active Directory dall'interfaccia](../quickstarts/managed-identity-from-cli.md) 
>  della riga di comando di Azure [Creazione di token di accesso utente](../quickstarts/access-tokens.md)

Per altre informazioni, vedere gli articoli seguenti:
- [Informazioni sull'architettura client e server](../concepts/client-and-server-architecture.md)
