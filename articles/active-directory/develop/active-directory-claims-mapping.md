---
title: Personalizzare le attestazioni delle app tenant di Azure AD (PowerShell)
titleSuffix: Microsoft identity platform
description: Questa pagina descrive il mapping delle attestazioni di Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: e77155f8a6efd3916ae90fcb562d688bb5b5126f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598891"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Procedura: Personalizzare le attestazioni generate nei token per un'app specifica in un tenant (anteprima)

> [!NOTE]
> Questa funzionalità sostituisce e prevale sulla funzionalità di [personalizzazione delle attestazioni](active-directory-saml-claims-customization.md) offerta attualmente tramite il portale. Nella stessa applicazione, se si personalizzano le attestazioni con il portale in aggiunta alla modalità Graph/PowerShell descritta in dettaglio in questo documento, i token emessi per l'applicazione ignoreranno la configurazione nel portale. Le configurazioni eseguite nei modi descritti in dettaglio in questo documento non si rifletteranno nel portale.

> [!NOTE]
> Questa funzionalità è attualmente disponibile in anteprima pubblica. Potrebbe essere necessario ripristinare o rimuovere eventuali modifiche. La funzionalità è disponibile in tutte le sottoscrizioni di Azure Active Directory (Azure AD) durante l'anteprima pubblica. Quando la funzionalità sarà disponibile a livello generale, alcuni aspetti potrebbero tuttavia richiedere una sottoscrizione Azure AD Premium. Questa funzionalità supporta la configurazione di criteri di mapping di attestazioni per i protocolli WS-Fed, SAML, OAuth e OpenID Connect.

Questa funzionalità viene usata dagli amministratori tenant per personalizzare le attestazioni generate nei token per un'applicazione specifica nel tenant. È possibile usare i criteri di mapping delle attestazioni per:

- Selezionare le attestazioni che vengono incluse nei token.
- Creare tipi di attestazione non esistenti.
- Scegliere o modificare l'origine dei dati generati in attestazioni specifiche.

In questo articolo vengono descritti alcuni scenari comuni che consentono di comprendere come usare il tipo di criteri [di mapping delle attestazioni](reference-claims-mapping-policy-type.md).

Quando si creano criteri di mapping delle attestazioni, è anche possibile generare un'attestazione da un attributo di estensione dello schema di directory nei token. Usare *ExtensionID per* l'attributo di estensione anziché *ID* nell'elemento `ClaimsSchema` .  Per altre informazioni sugli attributi di estensione, vedere [Uso degli attributi dell'estensione dello schema di directory](active-directory-schema-extensions.md).

## <a name="prerequisites"></a>Prerequisiti

Gli esempi seguenti mostrano come creare, aggiornare, collegare ed eliminare criteri per le entità servizio. I criteri di mapping delle attestazioni possono essere assegnati solo a oggetti entità servizio. Se non si ha familiarità con Azure AD, è consigliabile [capire come ottenere un tenant di Azure AD](quickstart-create-new-tenant.md) prima di procedere con questi esempi.

Per iniziare, seguire questa procedura:

1. Scaricare l'ultima [versione di anteprima pubblica del modulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Eseguire il comando Connect per accedere all'account amministratore di Azure AD. Eseguire questo comando ogni volta che si avvia una nuova sessione.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Per visualizzare tutti i criteri creati nell'organizzazione, eseguire questo comando. È consigliabile eseguire questo comando dopo la maggior parte delle operazioni negli scenari seguenti per verificare che i criteri siano stati creati come previsto.

   ``` powershell
   Get-AzureADPolicy
   ```

## <a name="omit-the-basic-claims-from-tokens"></a>Omettere le attestazioni di base dai token

In questo esempio viene creato un criterio che rimuove il set di [attestazioni](reference-claims-mapping-policy-type.md#claim-sets) di base dai token rilasciati alle entità servizio collegate.

1. Creare i criteri di mapping delle attestazioni. Questi criteri, che vengono collegati a specifiche entità servizio, rimuovono il set di attestazioni di base dai token.
   1. Per creare i criteri, eseguire questo comando:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Per visualizzare il nuovo criterio e ottenere il relativo ObjectId, eseguire questo comando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Assegnare i criteri all'entità servizio. È necessario ottenere anche l'ObjectId dell'entità servizio.
   1. Per visualizzare tutte le entità servizio dell'organizzazione, è possibile [eseguire query nell'API di Microsoft Graph](/graph/traverse-the-graph). In alternativa, eseguire l'accesso all'account Azure AD dallo strumento [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
   2. Dopo aver ottenuto l'ObjectId dell'entità servizio, eseguire questo comando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>Includere EmployeeID e TenantCountry come attestazioni nei token

In questo esempio si creano criteri che aggiungono EmployeeID e TenantCountry ai token emessi per le entità servizio collegate. EmployeeID viene emesso come tipo di attestazione nome sia nei token SAML sia nei token JWT. TenantCountry viene emesso come tipo di attestazione paese/area geografica sia nei token SAML sia nei token JWT. In questo esempio si continua a includere il set di attestazioni di base nei token.

1. Creare i criteri di mapping delle attestazioni. Questi criteri, che vengono collegati a specifiche entità servizio, aggiungono le attestazioni EmployeeID e TenantCountry ai token.
   1. Per creare i criteri, eseguire questo comando:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Per visualizzare il nuovo criterio e ottenere il relativo ObjectId, eseguire questo comando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Assegnare i criteri all'entità servizio. È necessario ottenere anche l'ObjectId dell'entità servizio.
   1. Per visualizzare tutte le entità servizio dell'organizzazione, è possibile [eseguire query nell'API di Microsoft Graph](/graph/traverse-the-graph). In alternativa, eseguire l'accesso all'account Azure AD dallo strumento [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
   2. Dopo aver ottenuto l'ObjectId dell'entità servizio, eseguire questo comando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>Usare una trasformazione delle attestazioni nei token

In questo esempio si creano i criteri che generano un'attestazione personalizzata "JoinedData" nei token JWT emessi per le entità servizio collegate. Questa attestazione contiene un valore creato aggiungendo i dati archiviati nell'attributo extensionattribute1 all'oggetto utente con ".sandbox". In questo esempio si esclude il set di attestazioni di base nei token.

1. Creare i criteri di mapping delle attestazioni. Questi criteri, che vengono collegati a specifiche entità servizio, aggiungono le attestazioni EmployeeID e TenantCountry ai token.
   1. Per creare i criteri, eseguire questo comando:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Per visualizzare il nuovo criterio e ottenere il relativo ObjectId, eseguire questo comando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Assegnare i criteri all'entità servizio. È necessario ottenere anche l'ObjectId dell'entità servizio.
   1. Per visualizzare tutte le entità servizio dell'organizzazione, è possibile [eseguire query nell'API di Microsoft Graph](/graph/traverse-the-graph). In alternativa, eseguire l'accesso all'account Azure AD dallo strumento [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
   2. Dopo aver ottenuto l'ObjectId dell'entità servizio, eseguire questo comando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Le applicazioni che ricevono token si basano sul fatto che i valori dell'attestazione vengono emessi in modo autorevole da Azure AD e non possono essere manomessi. Tuttavia, quando si modifica il contenuto del token tramite i criteri di mapping delle attestazioni, questi presupposti potrebbero non essere più corretti. Le applicazioni devono riconoscere in modo esplicito che i token sono stati modificati dall'autore dei criteri di mapping delle attestazioni per proteggersi dai criteri di mapping delle attestazioni creati da attori malintenzionati. Questa operazione può essere eseguita nei modi seguenti:

- Configurare una chiave di firma personalizzata
- Aggiornare il manifesto dell'applicazione per accettare attestazioni mappate.
 
In caso contrario, Azure AD restituirà un [ `AADSTS50146` codice di errore](reference-aadsts-error-codes.md#aadsts-error-codes).

### <a name="custom-signing-key"></a>Chiave di firma personalizzata

Per aggiungere una chiave di firma personalizzata all'oggetto entità servizio, è possibile usare il cmdlet Azure PowerShell per creare una credenziale della chiave del certificato per [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) l'oggetto Applicazione.

Per le app con mapping delle attestazioni abilitato è necessario convalidare le chiavi di firma del token aggiungendo `appid={client_id}` alle [richieste di metadati OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). Di seguito è riportato il formato del documento di metadati OpenID Connect.

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>Aggiornare il manifesto dell'applicazione

In alternativa, è possibile impostare la `acceptMappedClaims` proprietà su nel manifesto `true` [dell'applicazione.](reference-app-manifest.md) Come documentato nel tipo di risorsa [apiApplication](/graph/api/resources/apiapplication#properties), questo consente a un'applicazione di usare il mapping delle attestazioni senza specificare una chiave di firma personalizzata.

Ciò richiede che i destinatari del token richiesti usino un nome di dominio verificato del tenant di Azure AD, pertanto è necessario assicurarsi di impostare (rappresentato da nel manifesto dell'applicazione) ad esempio su o (semplicemente usando il nome `Application ID URI` `identifierUris` del tenant `https://contoso.com/my-api` `https://contoso.onmicrosoft.com/my-api` predefinito).

Se non si usa un dominio verificato, Azure AD restituirà un codice di errore con il messaggio `AADSTS501461` "AcceptMappedClaims è supportato solo per un gruppo di destinatari di token corrispondente al GUID dell'applicazione o a un gruppo di destinatari all'interno dei domini verificati del *tenant. Modificare l'identificatore di risorsa o usare una chiave di firma specifica dell'applicazione."*

## <a name="next-steps"></a>Passaggi successivi

- Per altre [informazioni, vedere l'articolo](reference-claims-mapping-policy-type.md) di riferimento sui tipi di criteri di mapping delle attestazioni.
- Per informazioni su come personalizzare le attestazioni rilasciate nel token SAML tramite il portale di Azure, vedere [Procedura: Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](active-directory-saml-claims-customization.md)
- Per altre informazioni sugli attributi di estensione, vedere [Uso degli attributi di estensione dello schema della directory nelle attestazioni.](active-directory-schema-extensions.md)
