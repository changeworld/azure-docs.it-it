---
title: Panoramica delle chiavi dei criteri - Azure Active Directory B2C
description: Informazioni sui tipi di chiavi dei criteri di crittografia che possono essere usati in Azure Active Directory B2C per la firma e la convalida di token, segreti client, certificati e password.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a41717e9be0918dead9f77a5f5472494d734b38a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717532"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Panoramica delle chiavi dei criteri in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) archivia segreti e certificati sotto forma di chiavi dei criteri per stabilire una relazione di trust con i servizi con cui si integra. Questi trust sono costituiti da:

- Provider di identità esterni
- Connessione con [i servizi API REST](restful-technical-profile.md)
- Firma e crittografia dei token

 Questo articolo illustra le informazioni necessarie sulle chiavi dei criteri usate da Azure AD B2C.

> [!NOTE]
> Attualmente, la configurazione delle chiavi dei criteri è limitata [solo ai criteri](./user-flow-overview.md) personalizzati.

È possibile configurare segreti e certificati per stabilire una relazione di trust tra i servizi nel portale di Azure nel menu **Chiavi dei** criteri. Le chiavi possono essere simmetriche o asimmetriche. *La crittografia simmetrica,* o crittografia a chiave privata, è la posizione in cui viene usato un segreto condiviso per crittografare e decrittografare i dati.  La crittografia asimmetrica, o crittografia a chiave pubblica, è un sistema di crittografia che usa coppie di chiavi, costituite da chiavi pubbliche condivise con l'applicazione relying party e chiavi private note solo a Azure AD B2C.

## <a name="policy-keyset-and-keys"></a>Keyset e chiavi dei criteri

La risorsa di primo livello per le chiavi dei criteri Azure AD B2C è il **contenitore Keyset.** Ogni keyset contiene almeno una **chiave**. Una chiave ha gli attributi seguenti:

| Attributo |  Obbligatoria | Commenti |
| --- | --- |--- |
| `use` | Sì | Utilizzo: identifica l'uso previsto della chiave pubblica. Crittografia dei dati `enc` o verifica della firma nei dati `sig` .|
| `nbf`| No | Data e ora di attivazione. |
| `exp`| No | Data e ora di scadenza. |

È consigliabile impostare i valori di attivazione e scadenza delle chiavi in base agli standard PKI. Potrebbe essere necessario ruotare periodicamente questi certificati per motivi di sicurezza o criteri. Ad esempio, potrebbe essere necessario un criterio per ruotare tutti i certificati ogni anno.

Per creare una chiave, è possibile scegliere uno dei metodi seguenti:

- **Manuale:** creare un segreto con una stringa definita. Il segreto è una chiave simmetrica. È possibile impostare le date di attivazione e scadenza.
- **Generato:** genera automaticamente una chiave. È possibile impostare le date di attivazione e scadenza. Sono disponibili due opzioni:
  - **Segreto:** genera una chiave simmetrica.
  - **RSA:** genera una coppia di chiavi (chiavi asimmetriche).
- **Carica:** caricare un certificato o una chiave PKCS12. Il certificato deve contenere le chiavi private e pubbliche (chiavi asimmetriche).

## <a name="key-rollover"></a>Rollover delle chiavi

Per motivi di sicurezza, Azure AD B2C eseguire il roll over delle chiavi periodicamente o immediatamente in caso di emergenza. Qualsiasi applicazione, provider di identità o API REST che si integra con Azure AD B2C deve essere preparata per gestire un evento di rollover della chiave, indipendentemente dalla frequenza con cui può verificarsi. In caso contrario, se l'applicazione o Azure AD B2C tenta di usare una chiave scaduta per eseguire un'operazione di crittografia, la richiesta di accesso avrà esito negativo.

Se un Azure AD B2C keyset ha più chiavi, solo una delle chiavi è attiva contemporaneamente, in base ai criteri seguenti:

- L'attivazione della chiave è basata sulla data **di attivazione**.
  - Le chiavi vengono ordinate in base alla data di attivazione in ordine crescente. Le chiavi con date di attivazione più in futuro vengono visualizzate più in basso nell'elenco. I codici senza una data di attivazione si trovano nella parte inferiore dell'elenco.
  - Quando la data e l'ora correnti sono maggiori della data di attivazione di una chiave, Azure AD B2C attiva la chiave e smette di usare la chiave attiva precedente.
- Quando è trascorsa l'ora di scadenza della chiave corrente e  il contenitore di chiavi contiene una nuova chiave con una chiave valida non prima e l'ora di scadenza, la nuova chiave diventerà attiva automaticamente. 
- Quando è trascorsa l'ora di scadenza della chiave corrente e il  contenitore  di chiavi non contiene una nuova chiave con una chiave valida non prima e le ore di scadenza, Azure AD B2C non sarà in grado di usare la chiave scaduta.  Azure AD B2C genererà un messaggio di errore all'interno di un componente dipendente dei criteri personalizzati. Per evitare questo problema, è possibile creare una chiave predefinita senza date di attivazione e scadenza come rete di sicurezza.
- L'endpoint della chiave (URI JWKS) dell'endpoint di configurazione noto di OpenId Connect riflette le chiavi configurate nel contenitore di chiavi, quando si fa riferimento alla chiave nel profilo [tecnico JwtIssuer.](./jwt-issuer-technical-profile.md) Un'applicazione che usa una libreria OIDC recupererà automaticamente questi metadati per assicurarsi che usi le chiavi corrette per convalidare i token. Per altre informazioni, vedere Come usare [Microsoft Authentication Library,](../active-directory/develop/msal-b2c-overview.md)che recupera sempre automaticamente le chiavi di firma del token più recenti.

## <a name="policy-key-management"></a>Gestione delle chiavi dei criteri

Per ottenere la chiave attiva corrente all'interno di un contenitore di chiavi, usare l'endpoint [getActiveKey](/graph/api/trustframeworkkeyset-getactivekey) dell Microsoft Graph API.

Per aggiungere o eliminare chiavi di firma e crittografia:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica, nella sezione **Criteri**, selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Chiavi dei criteri** 
    1. Per aggiungere una nuova chiave, selezionare **Aggiungi**.
    1. Per rimuovere una nuova chiave, selezionarla e quindi selezionare **Elimina.** Per eliminare la chiave, digitare il nome del contenitore di chiavi da eliminare. Azure AD B2C eliminerà la chiave e creerà una copia della chiave con il suffisso .bak.

### <a name="replace-a-key"></a>Sostituire una chiave

Le chiavi in un keyset non sono sostituibili o rimovibili. Se è necessario modificare una chiave esistente:

- È consigliabile aggiungere una nuova chiave con la **data di attivazione** impostata sulla data e sull'ora correnti. Azure AD B2C attiverà la nuova chiave e smetterà di usare la chiave attiva precedente.
- In alternativa, è possibile creare un nuovo keyset con le chiavi corrette. Aggiornare i criteri per usare il nuovo keyset e quindi rimuovere il set di chiavi precedente. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare Microsoft Graph per automatizzare la distribuzione di un [keyset](microsoft-graph-operations.md#trust-framework-policy-keyset) e delle [chiavi dei](microsoft-graph-operations.md#trust-framework-policy-key) criteri.

::: zone-end
