---
title: Autenticazione e autorizzazione del bus di servizio di Azure | Documentazione Microsoft
description: Autenticare le app sul bus di servizio usando l'autenticazione con firma di accesso condiviso (SAS).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ccb526abd99be50e33c8adb918186944b7af3bd6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516655"
---
# <a name="service-bus-authentication-and-authorization"></a>Autenticazione e autorizzazione del bus di servizio
Esistono due modi per autenticare e autorizzare l'accesso bus di servizio di Azure risorse: Directory attività di Azure (Azure AD) e Firme di accesso condiviso (SAS). Questo articolo fornisce informazioni dettagliate sull'uso di questi due tipi di meccanismi di sicurezza. 

## <a name="azure-active-directory"></a>Azure Active Directory
Azure AD'integrazione per le risorse del bus di servizio offre il controllo degli accessi in base al ruolo di Azure per un controllo granulare sull'accesso di un client alle risorse. È possibile usare il controllo degli accessi in base al ruolo di Azure per concedere autorizzazioni a un'entità di sicurezza, che può essere un utente, un gruppo o un'entità servizio dell'applicazione. L'entità di sicurezza viene autenticata Azure AD per restituire un token OAuth 2.0. Il token può essere usato per autorizzare una richiesta di accesso a una risorsa del bus di servizio (coda, argomento e così via).

Per altre informazioni sull'autenticazione con Azure AD, vedere gli articoli seguenti:

- [Eseguire l'autenticazione con le identità gestite](service-bus-managed-service-identity.md)
- [Eseguire l'autenticazione da un'applicazione](authenticate-application.md)

> [!NOTE]
> [L'API REST del bus di](/rest/api/servicebus/) servizio supporta l'autenticazione OAuth con Azure AD.

> [!IMPORTANT]
> L'autorizzazione di utenti o applicazioni che usano il token OAuth 2.0 restituito da Azure AD offre sicurezza e facilità d'uso superiori rispetto alle firme di accesso condiviso. Con Azure AD, non è necessario archiviare i token nel codice e rischiare potenziali vulnerabilità di sicurezza. È consigliabile usare le Azure AD con le applicazioni bus di servizio di Azure quando possibile. 

## <a name="shared-access-signature"></a>Firma di accesso condiviso
L'[autenticazione della firma di accesso condiviso](service-bus-sas.md) garantisce l'accesso dell'utente alle risorse del bus di servizio con diritti specifici. Nel bus di servizio, l'autenticazione della firma di accesso condiviso implica la configurazione di una chiave di crittografia con i relativi diritti in una risorsa del bus di servizio. I client possono quindi ottenere l'accesso a questa risorsa presentando un token di firma di accesso condiviso composto dall'URI della risorsa a cui si vuole accedere e da una scadenza firmata con la chiave configurata.

È possibile configurare le chiavi per la firma di accesso condiviso in uno spazio dei nomi del bus di servizio. La chiave si applica a tutte le entità di messaggistica nello spazio dei nomi. È anche possibile configurare le chiavi nelle code e negli argomenti del bus di servizio. SAS è anche supportato in [Inoltro di Azure](../azure-relay/relay-authentication-and-authorization.md).

Per usare la firma di accesso condiviso, è possibile configurare una regola di autorizzazione di accesso condiviso in uno spazio dei nomi, una coda o un argomento. Questa regola include gli elementi seguenti:

* *KeyName*: identifica la regola.
* *PrimaryKey*: è una chiave di crittografia usata per firmare/convalidare i token di firma di accesso condiviso.
* *SecondaryKey*: è una chiave di crittografia usata per firmare/convalidare i token di firma di accesso condiviso.
* *Rights*: rappresenta la raccolta di diritti **Listen**, **Send** o **Manage** concessi.

Le regole di autorizzazione configurate a livello di spazio dei nomi possono garantire l'accesso a tutte le entità in uno spazio dei nomi per i client con token firmati che usano la chiave corrispondente. In uno spazio dei nomi, una coda o un argomento del bus di servizio è possibile configurare fino a 12 regole di autorizzazione. Per impostazione predefinita, una regola di autorizzazione di accesso condiviso con tutti i diritti viene configurata per ogni spazio dei nomi al primo provisioning.

Per accedere a un'entità, il client richiede un token di firma di accesso condiviso generato usando una regola di autorizzazione di accesso condiviso specifica. Il token di firma di accesso condiviso viene generato usando l'algoritmo HMAC-SHA256 di una stringa di risorsa composta dall'URI della risorsa a cui si vuole accedere e da una scadenza, seguiti da una chiave di crittografia associata alla regola di autorizzazione.

Il supporto per l'autenticazione della firma di accesso condiviso per il bus di servizio è incluso in Azure .NET SDK 2.0 e versioni successive. La firma di accesso condiviso include il supporto per una regola di autorizzazione di accesso condiviso. Tutte le API che accettano una stringa di connessione come parametro includono il supporto per le stringhe di connessione della firma di accesso condiviso.

> [!IMPORTANT]
> Se si usa il controllo di accesso Azure Active Directory (noto anche come servizio di controllo di accesso o servizio di [](service-bus-migrate-acs-sas.md) controllo di accesso) con il bus di servizio, si noti che il supporto per questo metodo è ora limitato ed è consigliabile eseguire la migrazione dell'applicazione per usare la firma di accesso condiviso o l'autenticazione OAuth 2.0 con Azure AD (scelta consigliata). Per altre informazioni sulla deprecazione di ACS, vedere [questo post di blog.](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'autenticazione con Azure AD, vedere gli articoli seguenti:

- [Autenticazione con identità gestite](service-bus-managed-service-identity.md)
- [Autenticazione da un'applicazione](authenticate-application.md)

Per altre informazioni sull'autenticazione con la firma di accesso condiviso, vedere gli articoli seguenti:

- [Autenticazione con firma di accesso condiviso](service-bus-sas.md)
