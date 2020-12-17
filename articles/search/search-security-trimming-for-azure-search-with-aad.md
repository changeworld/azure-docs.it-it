---
title: Filtri di sicurezza per tagliare i risultati usando Active Directory
titleSuffix: Azure Cognitive Search
description: Informazioni su come implementare i privilegi di sicurezza a livello di documento per i risultati di ricerca di Azure ricerca cognitiva, usando i filtri di sicurezza e le identità di Azure Active Directory (AD).
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5788585b2365b12a90a508e5a972b61f73e48c15
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629511"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Filtri di sicurezza per tagliare i risultati di ricerca cognitiva di Azure usando Active Directory identità

Questo articolo illustra come usare le identità di sicurezza di Azure Active Directory (AD) insieme ai filtri in Azure ricerca cognitiva per tagliare i risultati della ricerca in base all'appartenenza a un gruppo di utenti.

Questo articolo illustra le attività seguenti:
> [!div class="checklist"]
> - Creare gruppi di Azure AD e utenti
> - Associare l'utente al gruppo creato
> - Memorizzare i nuovi gruppi nella cache
> - Indicizzare i documenti con i gruppi associati
> - Emettere una richiesta di ricerca con il filtro degli identificatori di gruppo
> 
> [!NOTE]
> I frammenti di codice di esempio in questo articolo sono scritti in C#. Il codice sorgente completo è disponibile [in GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). 

## <a name="prerequisites"></a>Prerequisiti

L'indice in Azure ricerca cognitiva deve avere un [campo di sicurezza](search-security-trimming-for-azure-search.md) per archiviare l'elenco di identità di gruppo con accesso in lettura al documento. Questo caso d'uso presuppone una corrispondenza uno a uno tra un elemento a protezione diretta (come la domanda di iscrizione all'università di un candidato) e un campo di sicurezza che specifica chi ha accesso a tale elemento (ad esempio, il personale dell'ufficio ammissioni).

Per la creazione di utenti, gruppi e associazioni è necessario disporre delle autorizzazioni di amministratore di Azure AD, necessarie in questa procedura dettagliata. 

L'applicazione deve anche essere registrata con Azure AD come app multi-tenant, come descritto nella procedura seguente.

### <a name="register-your-application-with-azure-active-directory"></a>Registrare l'applicazione con Azure Active Directory

Questo passaggio integra l'applicazione con Azure AD allo scopo di accettare gli accessi degli account utente e di gruppo. Se non si è un amministratore tenant nell'organizzazione, potrebbe essere necessario [creare un nuovo tenant](../active-directory/develop/quickstart-create-new-tenant.md) per eseguire i passaggi seguenti.

1. In [portale di Azure](https://portal.azure.com)trovare la risorsa Azure Active Directory per la sottoscrizione.

1. A sinistra, in **Gestisci** selezionare **registrazioni app**, quindi selezionare **nuova registrazione**.

1. Assegnare un nome alla registrazione, ad esempio un nome simile al nome dell'applicazione di ricerca. Selezionare **Registra**.

1. Dopo la creazione della registrazione dell'app, copiare l'ID applicazione. Sarà necessario specificare questa stringa per l'applicazione.

   Se si esegue l'istruzione/routine di [DotNetHowToSecurityTrimming](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK), incollare questo valore nel file di **app.config** .

   Ripetere l'ID tenant.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="ID applicazione nella sezione Essentials":::

1. A sinistra selezionare autorizzazioni per le **API** e quindi selezionare **Aggiungi un'autorizzazione**. 

1. Selezionare **Microsoft Graph** e quindi selezionare **autorizzazioni delegate**.

1. Cercare e aggiungere le autorizzazioni delegate seguenti:

   - **Directory.ReadWrite.All**
   - **Group.ReadWrite.All**
   - **User.ReadWrite.All**

Microsoft Graph fornisce un'API che consente l'accesso programmatico ai Azure AD tramite un'API REST. Il codice di esempio per questa procedura dettagliata usa le autorizzazioni per chiamare l'API Microsoft Graph per la creazione di gruppi, utenti e associazioni. Le API vengono usate anche per memorizzare nella cache gli identificatori di gruppo per un filtraggio più rapido.

## <a name="create-users-and-groups"></a>Creare utenti e gruppi

Se si aggiunge la ricerca a un'applicazione stabilita, è possibile che in Azure AD siano presenti identificatori utente e gruppo esistenti. In questo caso è possibile ignorare i tre passaggi successivi. 

Invece, se non ci sono utenti esistenti, è possibile usare le API Microsoft Graph per creare le entità di sicurezza. I frammenti di codice seguenti illustrano come generare gli identificatori, che diventano valori di dati per il campo di sicurezza nell'indice del ricerca cognitiva di Azure. Nell'ipotetica domanda di iscrizione all'università si tratterebbe degli identificatori di sicurezza per il personale dell'ufficio ammissioni.

L'appartenenza di utenti e gruppi potrebbe essere molto fluida, soprattutto nelle organizzazioni di grandi dimensioni. Il codice per la creazione delle identità di utenti e gruppi dovrebbe essere eseguito abbastanza spesso per individuare i cambiamenti intervenuti nell'appartenenza dell'organizzazione. Analogamente, l'indice di Azure ricerca cognitiva richiede una pianificazione di aggiornamento simile per riflettere lo stato corrente delle risorse e degli utenti autorizzati.

### <a name="step-1-create-group"></a>Passaggio 1: [creare un gruppo](/graph/api/group-post-groups) 

```csharp
private static Dictionary<Group, List<User>> CreateGroupsWithUsers(string tenant)
{
    Group group = new Group()
    {
        DisplayName = "My First Prog Group",
        SecurityEnabled = true,
        MailEnabled = false,
        MailNickname = "group1"
    };
```

### <a name="step-2-create-user"></a>Passaggio 2: [creare un utente](/graph/api/user-post-users)

```csharp
User user1 = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = String.Format("user1@{0}", tenant),
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
```

### <a name="step-3-associate-user-and-group"></a>Passaggio 3: associare utente e gruppo

```csharp
List<User> users = new List<User>() { user1, user2 };
Dictionary<Group, List<User>> groups = new Dictionary<Group, List<User>>() { { group, users } };
```

### <a name="step-4-cache-the-groups-identifiers"></a>Passaggio 4: memorizzare nella cache gli identificatori di gruppo

Facoltativamente, per ridurre la latenza di rete, è possibile memorizzare nella cache le associazioni utente-gruppo in modo che, quando viene eseguita una richiesta di ricerca, i gruppi vengano restituiti dalla cache, salvando un round trip in Azure AD. È possibile usare [Azure ad API batch](/graph/json-batching) per inviare una singola richiesta HTTP con più utenti e compilare la cache.

Microsoft Graph è progettato per gestire un volume elevato di richieste. Se si verifica un numero eccessivo di richieste, la richiesta con codice di stato HTTP 429 non verrà eseguita. Per altre informazioni, vedere [Microsoft Graph throttling](/graph/throttling) (Limitazione delle richieste di Microsoft Graph).

## <a name="index-document-with-their-permitted-groups"></a>Indicizzare un documento con i relativi gruppi consentiti

Le operazioni di query in Azure ricerca cognitiva vengono eseguite su un indice di ricerca cognitiva di Azure. In questo passaggio un'operazione di indicizzazione importa i dati ricercabili in un indice, includendo gli identificatori usati come filtri di sicurezza. 

Azure ricerca cognitiva non autentica le identità utente o fornisce la logica per stabilire quale contenuto un utente dispone delle autorizzazioni per la visualizzazione. Il caso d'uso per la limitazione per motivi di sicurezza presuppone che venga fornita l'associazione tra un documento riservato e l'identificatore di gruppo che ha accesso a tale documento, importata intatta in un indice di ricerca. 

Nell'esempio ipotetico, il corpo della richiesta PUT in un indice di ricerca cognitiva di Azure includerebbe un saggio o una trascrizione del Collegio del richiedente insieme all'identificatore di gruppo che dispone dell'autorizzazione per visualizzare il contenuto. 

Nell'esempio generico usato nel codice di esempio per questa procedura dettagliata l'operazione relativa all'indice potrebbe avere l'aspetto seguente:

```csharp
private static void IndexDocuments(string indexName, List<string> groups)
{
    IndexDocumentsBatch<SecuredFiles> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new SecuredFiles()
            {
                FileId = "1",
                Name = "secured_file_a",
                GroupIds = new[] { groups[0] }
            }),
              ...
            };

IndexDocumentsResult result = searchClient.IndexDocuments(batch);
```

## <a name="issue-a-search-request"></a>Emettere una richiesta di ricerca

A scopo di limitazione per motivi di sicurezza, i valori nel campo di sicurezza nell'indice sono valori statici, usati per includere o escludere i documenti nei risultati della ricerca. Se, ad esempio, l'identificatore di gruppo per l'ammissione è "A11B22C33D44-E55F66G77-H88I99JKK", tutti i documenti in un indice di ricerca cognitiva di Azure con tale identificatore nel file di sicurezza sono inclusi (o esclusi) nei risultati della ricerca restituiti al richiedente.

Per filtrare i documenti restituiti nei risultati della ricerca in base ai gruppi dell'utente che ha emesso la richiesta, esaminare i passaggi seguenti.

### <a name="step-1-retrieve-users-group-identifiers"></a>Passaggio 1: recuperare gli identificatori di gruppo dell'utente

Se i gruppi dell'utente non sono già memorizzati nella cache o la cache è scaduta, emettere la richiesta di [gruppi](/graph/api/directoryobject-getmembergroups) .

```csharp
private static async void RefreshCache(IEnumerable<User> users)
{
    HttpClient client = new HttpClient();
    var userGroups = await _microsoftGraphHelper.GetGroupsForUsers(client, users);
    _groupsCache = new ConcurrentDictionary<string, List<string>>(userGroups);
}
```

### <a name="step-2-compose-the-search-request"></a>Passaggio 2: comporre la richiesta di ricerca

Presupponendo che si disponga dell'appartenenza ai gruppi dell'utente, è possibile emettere la richiesta di ricerca con i valori di filtro appropriati.

```csharp
private static void SearchQueryWithFilter(string user)
{
    // Using the filter below, the search result will contain all documents that their GroupIds field   
    // contain any one of the Ids in the groups list
    string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", String.Join(",", _groupsCache[user])));
    SearchOptions searchOptions =
        new SearchOptions()
        {
            Filter = filter
        };
    searchOptions.Select.Add("name");

    SearchResults<SecuredFiles> results = searchClient.Search<SecuredFiles>("*", searchOptions);

    Console.WriteLine("Results for groups '{0}' : {1}", _groupsCache[user], results.GetResults().Select(r => r.Document.Name));
}
```

### <a name="step-3-handle-the-results"></a>Passaggio 3: gestire i risultati

La risposta include un elenco filtrato dei documenti, costituito da quelli che l'utente è autorizzato a visualizzare. A seconda di come viene creata la pagina dei risultati della ricerca, potrebbero essere inclusi segnali visivi per riflettere il set di risultati filtrato.

## <a name="next-steps"></a>Passaggi successivi

In questa procedura dettagliata è stato illustrato un modello per l'uso di Azure AD accessi per filtrare i documenti in Azure ricerca cognitiva risultati, tagliando i risultati dei documenti che non corrispondono al filtro specificato nella richiesta. Per un modello alternativo che può essere più semplice o per rivedere altre funzionalità di sicurezza, vedere i collegamenti seguenti.

- [Filtri di sicurezza per tagliare i risultati](search-security-trimming-for-azure-search.md)
- [Sicurezza in Azure ricerca cognitiva](search-security-overview.md)