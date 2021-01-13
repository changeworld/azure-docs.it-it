---
title: Attributi del profilo utente in Azure Active Directory B2C
description: Informazioni sugli attributi del tipo di risorsa utente supportati dal profilo utente della directory Azure AD B2C. Informazioni sugli attributi predefiniti, sulle estensioni e sul modo in cui gli attributi vengono mappati a Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7b134c4e9e980104a54f6a96d45445ee114556a5
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178722"
---
# <a name="user-profile-attributes"></a>Attributi del profilo utente

Il profilo utente della directory di Azure Active Directory (Azure AD) B2C viene fornito con un set predefinito di attributi, quali nome, cognome, città, codice postale e numero di telefono. È possibile estendere il profilo utente con i dati dell'applicazione senza che sia necessario un archivio dati esterno. 

La maggior parte degli attributi che possono essere usati con profili utente Azure AD B2C è supportata anche da Microsoft Graph. Questo articolo descrive gli attributi del profilo utente Azure AD B2C supportati. Vengono illustrati anche gli attributi non supportati da Microsoft Graph, nonché gli attributi Microsoft Graph che non devono essere usati con Azure AD B2C.

> [!IMPORTANT]
> Non usare gli attributi predefiniti o di estensione per archiviare dati personali sensibili, come ad esempio credenziali dell'account, numeri identificativi presso enti pubblici, dati di titolari di carte, dati finanziari, informazioni sanitarie o riservate.

È anche possibile integrare sistemi esterni. Ad esempio, è possibile usare Azure AD B2C per l'autenticazione, ma delegare a un database CRM (Customer Relationship Management) o di gestione della fedeltà clienti esterno come fonte autorevole di dati dei clienti. Per altre informazioni, vedere la soluzione [Profilo remoto](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile).

## <a name="azure-ad-user-resource-type"></a>Tipo di risorsa utente Azure AD

La tabella seguente illustra gli attributi del [tipo di risorsa utente](/graph/api/resources/user) supportati dal profilo utente della directory Azure AD B2C. Essa fornisce le informazioni seguenti su ogni attributo:

- Nome dell'attributo usato da Azure AD B2C (seguito dal nome Microsoft Graph tra parentesi, se diverso)
- Tipo di dati dell'attributo
- Descrizione dell'attributo
- Se l'attributo è disponibile nel portale di Azure
- Se l'attributo può essere usato in un flusso utente
- Se l'attributo può essere usato in un [profilo tecnico di Azure AD](active-directory-technical-profile.md) di un criterio personalizzato e in quale sezione (&lt;InputClaims&gt;, &lt;OutputClaims&gt;o &lt;PersistedClaims&gt;)

|Nome     |Tipo     |Descrizione|Portale di Azure|Flussi degli utenti|Criteri personalizzati|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|Indica se l'account utente è abilitato o disabilitato: **true** se l'account è abilitato, **false** in caso contrario.|Sì|No|Persisted, Output|
|ageGroup        |string|Gruppo di età dell'utente. Valori possibili: null, Undefined, Minor, Adult, NotAdult.|Sì|No|Persisted, Output|
|alternativeSecurityId ([Identità](#identities-attribute))|string|Singola identità utente fornita dal provider di identità esterno.|No|No|Input, Persisted, Output|
|alternativeSecurityIds ([Identità](#identities-attribute))|raccolta securityId alternativa|Raccolta di identità utente da provider di identità esterni.|No|No|Persisted, Output|
|city            |string|Città dell'utente. Lunghezza massima 128.|Sì|Sì|Persisted, Output|
|consentProvidedForMinor|string|Indica se il consenso è stato fornito per un minore. Valori consentiti: null, granted, denied o notRequired.|Sì|No|Persisted, Output|
|country         |string|Paese/regione in cui si trova l'utente. Esempio: "US" o "UK". Lunghezza massima 128.|Sì|Sì|Persisted, Output|
|createdDateTime|Datetime|Data di creazione dell'oggetto utente. Sola lettura.|No|No|Persisted, Output|
|creationType    |string|Se l'account utente è stato creato come account locale per un tenant di Azure Active Directory B2C, il valore è LocalAccount o nameCoexistence. Sola lettura.|No|No|Persisted, Output|
|dateOfBirth     |Data|Data di nascita.|No|No|Persisted, Output|
|department      |string|Nome del reparto in cui lavora l'utente. Lunghezza massima 64.|Sì|No|Persisted, Output|
|displayName     |string|Nome visualizzato per l'utente. Lunghezza massima 256.|Sì|Sì|Persisted, Output|
|facsimileTelephoneNumber<sup>1</sup>|string|Numero del fax aziendale dell'utente.|Sì|No|Persisted, Output|
|givenName       |string|Nome (di battesimo) dell'utente. Lunghezza massima 64.|Sì|Sì|Persisted, Output|
|jobTitle        |string|Posizione dell'utente. Lunghezza massima 128.|Sì|Sì|Persisted, Output|
|immutableId     |string|Identificatore che in genere viene usato per gli utenti migrati da Active Directory locali.|No|No|Persisted, Output|
|legalAgeGroupClassification|string|Classificazione legale per fascia d'età. Di sola lettura e calcolata in base alle proprietà ageGroup e consentProvidedForMinor. Valori consentiti: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult e Adult.|Sì|No|Persisted, Output|
|legalCountry<sup>1</sup>  |string|Paese/regione per finalità legali.|No|No|Persisted, Output|
|mail            |string|Indirizzo SMTP dell'utente, ad esempio "bob@contoso.com". Di sola lettura.|No|No|Persisted, Output|
|mailNickName    |string|Alias di posta elettronica dell'utente. Lunghezza massima 64.|No|No|Persisted, Output|
|cellulare (mobilePhone) |string|Numero di telefono cellulare principale dell'utente. Lunghezza massima 64.|Sì|No|Persisted, Output|
|netId           |string|ID di rete.|No|No|Persisted, Output|
|objectId        |string|Identificatore univoco globale (GUID) che rappresenta l'identificatore univoco per l'utente. Esempio: 12345678-9abc-def0-1234-56789abcde. Di sola lettura, non modificabile.|Sola lettura|Sì|Input, Persisted, Output|
|otherMails      |Raccolta di tipi string|Elenco di altri indirizzi di posta elettronica per l'utente. Esempio: ["bob@contoso.com", "Robert@fabrikam.com"].|Sì (indirizzo di posta elettronica alternativo)|No|Persisted, Output|
|password        |string|Password per l'account locale durante la creazione dell'utente.|No|No|Persisted|
|passwordPolicies     |string|Criteri della password. Stringa costituita da nomi di criteri diversi separati da virgola. Ad esempio, "DisablePasswordExpiration, DisableStrongPassword".|No|No|Persisted, Output|
|physicalDeliveryOfficeName (officeLocation)|string|Posizione dell'ufficio nel luogo di lavoro dell'utente. Lunghezza massima 128.|Sì|No|Persisted, Output|
|postalCode      |string|Il CAP dell'indirizzo postale dell'utente. Il CAP è specifico del Paese/della regione dell'utente. Nel Stati Uniti, questo attributo contiene il CAP. Lunghezza massima 40.|Sì|No|Persisted, Output|
|preferredLanguage    |string|Lingua preferita per l'utente. Deve seguire il codice ISO 639-1. Esempio: "en-US".|No|No|Persisted, Output|
|refreshTokensValidFromDateTime|Datetime|Tutti i token di aggiornamento emessi prima di questa ora non sono validi; quando si usa un token di aggiornamento non valido per acquisire un nuovo token di accesso, le applicazioni riportano un errore. In tal caso, l'applicazione dovrà acquisire un nuovo token di aggiornamento effettuando una richiesta all'endpoint di autorizzazione. Di sola lettura.|No|No|Output|
|signInNames ([Identità](#identities-attribute)) |string|Nome di accesso univoco dell'utente dell'account locale di qualsiasi tipo nella directory. Usare questo attributo per ottenere un utente con valore di accesso senza specificare il tipo di account locale.|No|No|Input|
|signInNames.userName ([Identità](#identities-attribute)) |string|Nome utente univoco dell'utente dell'account locale nella directory. Usare questo attributo per creare o ottenere un utente con un nome utente di accesso specifico. Specificando questo valore solo in PersistedClaims durante l'operazione Patch, gli altri tipi di valori signInNames vengono rimossi. Per aggiungere un nuovo tipo di signInNames, è necessario salvare anche in modo permanente il valore signInNames esistente.|No|No|Input, Persisted, Output|
|signInNames.phoneNumber ([Identità](#identities-attribute)) |string|Numero di telefono univoco dell'utente dell'account locale nella directory. Usare questo attributo per creare o ottenere un utente con un numero di telefono di accesso specifico. Se si specifica questo attributo solo in PersistedClaims durante l'operazione patch, gli altri tipi di signInNames vengono rimossi. Per aggiungere un nuovo tipo di signInNames, è necessario salvare anche in modo permanente il valore signInNames esistente.|No|No|Input, Persisted, Output|
|signInNames.emailAddress ([Identità](#identities-attribute))|string|Indirizzo di posta elettronica univoco dell'utente dell'account locale nella directory. Usare questo valore per creare od ottenere un utente con un indirizzo di posta elettronica di accesso specifico. Se si specifica questo attributo solo in PersistedClaims durante l'operazione patch, gli altri tipi di signInNames vengono rimossi. Per aggiungere un nuovo tipo di signInNames, è necessario salvare anche in modo permanente il valore signInNames esistente.|No|No|Input, Persisted, Output|
|state           |string|Stato o provincia dell'utente. Lunghezza massima 128.|Sì|Sì|Persisted, Output|
|streetAddress   |string|Indirizzo della sede di lavoro dell'utente. Lunghezza massima 1024.|Sì|Sì|Persisted, Output|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|string|Numero di telefono secondario dell'utente, usato per l'autenticazione a più fattori.|Sì|No|Persisted, Output|
|strongAuthenticationEmailAddress<sup>1</sup>|string|Indirizzo SMTP dell'utente. Esempio: "bob@contoso.com" Questo attributo viene usato per l'accesso con il criterio username, per archiviare l'indirizzo di posta elettronica dell'utente. Indirizzo di posta elettronica usato in un flusso di reimpostazione della password.|Sì|No|Persisted, Output|
|strongAuthenticationPhoneNumber<sup>2</sup>|string|Numero di telefono principale dell'utente, usato per l'autenticazione a più fattori.|Sì|No|Persisted, Output|
|surname         |string|Cognome dell'utente. Lunghezza massima 64.|Sì|Sì|Persisted, Output|
|telephoneNumber (prima voce del valore businessPhones)|string|Numero di telefono principale della sede di lavoro dell'utente.|Sì|No|Persisted, Output|
|userPrincipalName    |string|Nome dell'entità utente (UPN) dell'utente. L'UPN è un nome di accesso Internet per un utente basato sullo standard Internet RFC 822. Il dominio deve essere presente nella raccolta di tenant dei domini verificati. Questa proprietà è obbligatoria quando viene creato un account. Non modificabile.|No|No|Input, Persisted, Output|
|usageLocation   |string|Obbligatorio per gli utenti a cui verranno assegnate licenze, a causa del requisito legale che richiede la verifica della disponibilità dei servizi nei Paesi/nelle regioni. Non ammette i valori NULL. Codice Paese/regione di due lettere (standard ISO 3166). Esempi: "US", "JP" e "GB".|Sì|No|Persisted, Output|
|userType        |string|Valore stringa che può essere usato per classificare i tipi di utente nella directory. Il valore deve essere "Member". Di sola lettura.|Sola lettura|No|Persisted, Output|
|userState (externalUserState)<sup>3</sup>|string|Solo per gli account Azure AD B2B, indica se l'invito è in stato PendingAcceptance o Accepted.|No|No|Persisted, Output|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|Datetime|Mostra il timestamp delle modifiche più recenti alla proprietà UserState.|No|No|Persisted, Output|

<sup>1 </sup>Non supportato da Microsoft Graph<br><sup>2</sup> Per ulteriori informazioni, vedere l' [attributo del numero di telefono](#mfa-phone-number-attribute) dell'autenticazione a più fattori<br><sup>3 </sup> Non deve essere usato con Azure AD B2C

## <a name="display-name-attribute"></a>Nome visualizzato (attributo)

`displayName`È il nome da visualizzare in portale di Azure Gestione utenti per l'utente e nel token di accesso Azure ad B2C torna all'applicazione. Questa proprietà è obbligatoria.

## <a name="identities-attribute"></a>Attributo identitys

Un account cliente, che può essere un consumatore, un partner o un cittadino, può essere associato a questi tipi di identità:

- Identità **locale** : il nome utente e la password vengono archiviati localmente nella directory Azure ad B2C. Queste identità sono spesso dette "account locali".
- Identità **federata** , nota anche come account *aziendali* o *sociali* , l'identità dell'utente è gestita da un provider di identità FEDERATO come Facebook, Microsoft, ADFS o Salesforce.

Un utente con un account cliente può accedere con più identità. Ad esempio nome utente, indirizzo di posta elettronica, ID dipendente, ID pubblico e altro. Un singolo account può avere più identità, sia locali che sociali, con la stessa password.

Nell'API Microsoft Graph, sia le identità locali che quelle federate vengono archiviate nell' `identities` attributo User, che è di tipo [objectIdentity] [Graph-objectIdentity]. La `identities` raccolta rappresenta un set di identità usate per accedere a un account utente. Questa raccolta consente all'utente di accedere all'account utente con qualsiasi identità associata.

| Nome   | Tipo |Descrizione|
|:---------------|:--------|:----------|
|signInType|string| Specifica i tipi di accesso utente nella directory. Per l'account locale:  `emailAddress` , `emailAddress1` , `emailAddress2` , `emailAddress3` ,  `userName` o qualsiasi altro tipo. L'account di social networking deve essere impostato su  `federated` .|
|autorità di certificazione|string|Specifica l'emittente dell'identità. Per gli account locali (dove **signInType** non è `federated` ), questa proprietà corrisponde al nome di dominio predefinito del tenant B2C locale, ad esempio `contoso.onmicrosoft.com` . Per l'identità sociale (dove **signInType** è  `federated` ), il valore è il nome dell'autorità emittente, ad esempio `facebook.com`|
|issuerAssignedId|string|Specifica l'identificatore univoco assegnato all'utente dall'emittente. La combinazione di **Issuer** e **issuerAssignedId** deve essere univoca all'interno del tenant. Per l'account locale, quando **signInType** è impostato su `emailAddress` o `userName` , rappresenta il nome di accesso per l'utente.<br>Quando **signInType** è impostato su: <ul><li>`emailAddress` (o inizia con `emailAddress` like `emailAddress1` ) **issuerAssignedId** deve essere un indirizzo di posta elettronica valido</li><li>`userName` (o qualsiasi altro valore), **issuerAssignedId** deve essere una [parte locale valida di un indirizzo di posta elettronica](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **issuerAssignedId** rappresenta l'identificatore univoco dell'account federato</li></ul>|

Il seguente **attributo** identitys, con un'identità dell'account locale con un nome di accesso, un indirizzo di posta elettronica come accesso e con un'identità di social networking. 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Per le identità federate, a seconda del provider di identità, **issuerAssignedId** è un valore univoco per un determinato utente per ogni applicazione o account di sviluppo. Configurare i criteri di Azure AD B2C con lo stesso ID applicazione assegnato in precedenza dal provider di social networking o da un'altra applicazione all'interno dello stesso account di sviluppo. 

## <a name="password-profile-property"></a>Proprietà profilo password

Per un'identità locale, l'attributo **passwordProfile** è obbligatorio e contiene la password dell'utente. L' `forceChangePasswordNextSignIn` attributo deve essere impostato su `false` .

Per un'identità federata (social), l'attributo **passwordProfile** non è obbligatorio.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>Attributo criteri password

I criteri di Azure AD B2C password (per gli account locali) si basano sui criteri di Azure Active Directory [complessità delle password complesse](../active-directory/authentication/concept-sspr-policy.md) . I criteri di registrazione o accesso e reimpostazione della password di Azure AD B2C richiedono questo livello di complessità della password complessa e non scadono le password.

Negli scenari di migrazione degli utenti, se gli account di cui si vuole eseguire la migrazione hanno un livello di complessità della password più debole rispetto al livello di [attendibilità delle password complesse](../active-directory/authentication/concept-sspr-policy.md) applicato da Azure ad B2C, è possibile disabilitare il requisito per la password complessa. Per modificare i criteri password predefiniti, impostare l' `passwordPolicies` attributo su `DisableStrongPassword` . È ad esempio possibile modificare la richiesta di creazione utente come segue:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>Attributo numero di telefono multi-factor authentication

Quando si usa un telefono per multi-factor authentication, il telefono cellulare viene usato per verificare l'identità dell'utente. Per [aggiungere](https://docs.microsoft.com/graph/api/authentication-post-phonemethods) un nuovo numero di telefono a livello, [aggiornare](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-update), [ottenere](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-get)o [eliminare](https://docs.microsoft.com/graph/api/phoneauthenticationmethod-delete) il numero di telefono, usare il [metodo di autenticazione](https://docs.microsoft.com/graph/api/resources/phoneauthenticationmethod)MS API Graph Phone.

In Azure AD B2C [criteri personalizzati](custom-policy-overview.md), il numero di telefono è disponibile tramite il `strongAuthenticationPhoneNumber` tipo di attestazione.

## <a name="extension-attributes"></a>Attributi di estensione

Ogni applicazione rivolte ai clienti presenta requisiti specifici per la raccolta delle informazioni. Il tenant di Azure AD B2C viene fornito con un set predefinito di informazioni archiviate in proprietà, ad esempio nome, cognome e codice postale specificati. Con Azure AD B2C è possibile estendere il set di proprietà archiviate in ogni account del cliente. Per altre informazioni, vedere [aggiungere attributi utente e personalizzare l'input dell'utente in Azure Active Directory B2C](configure-user-input.md)

Gli attributi di estensione [estendono lo schema](/graph/extensibility-overview#schema-extensions) degli oggetti utente nella directory. Gli attributi di estensione possono essere registrati solo su un oggetto applicazione anche se possono contenere dati per un utente. L'attributo di estensione è associato all'applicazione denominata `b2c-extensions-app` . Non modificare questa applicazione, perché viene usata da Azure AD B2C per archiviare i dati utente. Questa applicazione è reperibile in Registrazioni app di Azure Active Directory.

> [!NOTE]
> - È possibile scrivere fino a 100 attributi di estensione in qualsiasi account utente.
> - Se l'applicazione b2c-extensions-app viene eliminata, gli attributi di estensione vengono rimossi da tutti gli utenti assieme ai dati che contengono.
> - Se l'applicazione elimina un attributo di estensione, questo viene rimosso da tutti gli account utente e i valori vengono eliminati.

Gli attributi di estensione nel API Graph vengono denominati usando la convenzione `extension_ApplicationClientID_AttributeName` , dove `ApplicationClientID` è l'ID dell'applicazione **(client)** dell' `b2c-extensions-app` applicazione (presente in **registrazioni app**  >  **tutte le applicazioni** nel portale di Azure). Si noti che l' **ID dell'applicazione (client)** come è rappresentato nel nome dell'attributo di estensione non include trattini. Ad esempio:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Quando si definisce un attributo in un'estensione dello schema sono supportati i tipi di dati seguenti:

|Tipo |Osservazioni  |
|--------------|---------|
|Boolean    | Valori possibili: **true** o **false**. |
|Datetime   | Deve essere specificato nel formato ISO 8601. Verrà archiviato in formato UTC.   |
|Integer    | Valore a 32 bit.               |
|string     | 256 caratteri al massimo.     |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sugli attributi di estensione:

- [Estensioni dello schema](/graph/extensibility-overview#schema-extensions)
- [Definire attributi personalizzati](user-flow-custom-attributes.md)