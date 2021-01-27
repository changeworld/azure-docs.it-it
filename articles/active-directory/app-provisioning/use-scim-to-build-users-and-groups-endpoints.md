---
title: Compilare un endpoint SCIM per il provisioning degli utenti nelle app da Azure AD
description: System for Cross-domain Identity Management (SCIM) standardizza il provisioning utenti automatico. Questo articolo illustra come sviluppare un endpoint SCIM, integrare l'API SCIM con Azure Active Directory e iniziare ad automatizzare il provisioning di utenti e gruppi nelle applicazioni cloud.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ae36af981b113d44ac1b8fd45a1d084760b0294
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900163"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Esercitazione: sviluppare un endpoint SCIM di esempio

Nessuno vuole creare un nuovo endpoint da zero, quindi è stato creato un codice di [riferimento](https://aka.ms/scimreferencecode) per iniziare a usare [scim](https://aka.ms/scimoverview). Questa esercitazione descrive come distribuire il codice di riferimento di SCIM in Azure e come testarlo con il post o con l'integrazione con il client SCIM Azure AD. È possibile fare in modo che l'endpoint SCIM sia operativo senza codice in soli 5 minuti. Questa esercitazione è destinata agli sviluppatori che vogliono iniziare a usare SCIM o ad altri utenti interessati a testare un endpoint SICM. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Scaricare il codice di riferimento
> * Distribuire l'endpoint SCIM in Azure
> * Testare l'endpoint SCIM

Le funzionalità dell'endpoint, incluse sono:

|Endpoint|Descrizione|
|---|---|
|`/User`|Eseguire operazioni CRUD su una risorsa utente: **creare**, **aggiornare**, **eliminare**, **ottenere**, **elencare**, **filtrare**|
|`/Group`|Eseguire operazioni CRUD su una risorsa di gruppo: **creare**, **aggiornare**, **eliminare**, **ottenere**, **elencare**, **filtrare**|
|`/Schemas`|Recuperare uno o più schemi supportati.<br/><br/>Il set di attributi di una risorsa supportata da ogni provider di servizi può variare, ad esempio il provider di servizi A supporta "Name", "title" e "email" mentre il provider di Servizi B supporta "Name", "title" e "phoneNumbers" per gli utenti.|
|`/ResourceTypes`|Recuperare i tipi di risorse supportati.<br/><br/>Il numero e i tipi di risorse supportati da ogni provider di servizi possono variare, ad esempio il provider di servizi A supporta gli utenti mentre il provider di Servizi B supporta utenti e gruppi.|
|`/ServiceProviderConfig`|Recuperare la configurazione SCIM del provider di servizi<br/><br/>Le funzionalità di SCIM supportate da ogni provider di servizi possono variare, ad esempio il provider di servizi A supporta le operazioni patch mentre il provider di Servizi B supporta le operazioni patch e l'individuazione dello schema.|

## <a name="download-the-reference-code"></a>Scaricare il codice di riferimento

Il [codice di riferimento](https://github.com/AzureAD/SCIMReferenceCode) da scaricare include i progetti seguenti:

- **Microsoft.SystemForCrossDomainIdentityManagement**, l'API Web .NET Core MVC per compilare ed eseguire il provisioning di un'API scim
- **Microsoft. SCIM. WebHostSample**, un esempio funzionante di endpoint scim

I progetti contengono le cartelle e i file seguenti:

|File/cartella|Descrizione|
|-|-|
|Cartella **schemi**| Modelli per le risorse **utente** e **gruppo** insieme ad alcune classi astratte come schematizzato per le funzionalità condivise.<br/><br/> Una cartella **Attributes** che contiene le definizioni di classe per attributi complessi di **utenti** e **gruppi** , ad esempio indirizzi.|
|Cartella del **servizio** | Contiene la logica per le azioni relative al modo in cui le risorse vengono sottoposte a query e aggiornate.<br/><br/> Il codice di riferimento dispone di servizi per la restituzione di utenti e gruppi.<br/><br/>La cartella **Controllers** contiene i vari endpoint SCIM. I controller delle risorse includono verbi HTTP per eseguire operazioni CRUD sulla risorsa (**Get**, **post**, **put**, **patch**, **Delete**). I controller si basano sui servizi per eseguire le azioni.|
|Cartella **protocollo**|Contiene la logica per le azioni relative al modo in cui le risorse vengono restituite in base alla RFC SCIM, ad esempio:<br/><ul><li>Restituzione di più risorse come elenco.</li><li>Restituzione solo di risorse specifiche in base a un filtro.</li><li>Trasformare una query in un elenco di elenchi collegati di filtri singoli.</li><li>Trasformazione di una richiesta PATCH in un'operazione con attributi relativi al percorso del valore.</li><li>Definizione del tipo di operazione che può essere usata per applicare le modifiche agli oggetti risorsa.</li></ul>|
|`Microsoft.SystemForCrossDomainIdentityManagement`| Codice sorgente di esempio.|
|`Microsoft.SCIM.WebHostSample`| Implementazione di esempio della libreria SCIM.|
|*. gitignore*|Definire gli elementi da ignorare in fase di commit.|
|*CHANGELOG.md*|Elenco delle modifiche apportate all'esempio.|
|*CONTRIBUTING.md*|Linee guida per contribuire all'esempio.|
|*README.md*|Il file **Leggimi** .|
|*LICENZA*|Licenza per l'esempio.|

> [!NOTE]
> Questo codice è utile per iniziare a compilare un endpoint SCIM e viene fornito **così com'è**. I riferimenti inclusi non hanno alcuna garanzia di manutenzione o supporto attivo.
>
> Questo progetto ha adottato il [Codice di comportamento di Microsoft per l'open source](https://opensource.microsoft.com/codeofconduct/). Dato che i [contributi](https://github.com/AzureAD/SCIMReferenceCode/wiki/Contributing-Overview) della community sono benvenuti per facilitare la creazione e la gestione del repository e come altri contributi open source, si accetterà un contratto di licenza con collaboratore (CLA). Il presente contratto dichiara di avere e concedere i diritti per l'uso del contributo. per informazioni dettagliate, vedere [Microsoft Open Source](https://cla.opensource.microsoft.com).
>
> Per altre informazioni, vedere le [Domande frequenti sul codice di comportamento](https://opensource.microsoft.com/codeofconduct/faq/) o scrivere a [opencode@microsoft.com](mailto:opencode@microsoft.com) per domande aggiuntive o commenti.

###  <a name="use-multiple-environments"></a>Usare più ambienti

Il codice SCIM incluso usa un ambiente ASP.NET Core per controllare l'autorizzazione da usare in fase di sviluppo e dopo la distribuzione, vedere [usare più ambienti in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments?view=aspnetcore-3.1).

```csharp
private readonly IWebHostEnvironment _env;
...

public void ConfigureServices(IServiceCollection services)
{
    if (_env.IsDevelopment())
    {
        ...
    }
    else
    {
        ...
    }
```

## <a name="deploy-your-scim-endpoint-in-azure"></a>Distribuire l'endpoint SCIM in Azure

La procedura descritta in questo articolo consente di distribuire l'endpoint SCIM a un servizio usando [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) e i [Servizi app Azure](https://docs.microsoft.com/azure/app-service/). Il codice di riferimento SCIM può essere eseguito anche localmente, ospitato da un server locale o distribuito a un altro servizio esterno. 

### <a name="open-solution-and-deploy-to-azure-app-service"></a>Aprire la soluzione e distribuirla nel servizio app Azure

1. Passare al [codice di riferimento](https://github.com/AzureAD/SCIMReferenceCode) da GitHub e selezionare **clona o Scarica**.

1. Scegliere **Apri in desktop** oppure copiare il collegamento, aprire **Visual Studio** e selezionare **clona o Estrai codice** per immettere il collegamento copiato ed effettuare una copia locale.

1. In **Visual Studio**, assicurarsi di accedere all'account che ha accesso alle risorse di hosting.

1. In **Esplora soluzioni** aprire *Microsoft. SCIM. sln* e fare clic con il pulsante destro del mouse sul file *Microsoft. SCIM. WebHostSample* . Selezionare **Pubblica**.

    ![pubblicazione nel cloud](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Per eseguire questa soluzione localmente, fare doppio clic sul progetto e selezionare **IIS Express** per avviare il progetto come pagina Web con un URL host locale.

1. Selezionare **Crea profilo** e assicurarsi che **servizio app** e **Crea nuovo** siano selezionati.

    ![pubblicazione Cloud 2](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Scorrere le opzioni della finestra di dialogo e rinominare l'app con un nome a scelta. Questo nome viene usato sia nell'app che nell'URL dell'endpoint SCIM.

    ![pubblicazione Cloud 3](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Selezionare il gruppo di risorse da usare e scegliere **pubblica**.

1. Passare all'applicazione in configurazione **app Azure Services**  >   e selezionare **nuova impostazione applicazione** per aggiungere l'impostazione *Token__TokenIssuer* con il valore `https://sts.windows.net/<tenant_id>/` . Sostituire `<tenant_id>` con il Azure AD tenant_id e se si sta cercando di testare l'endpoint scim usando [](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)l'utente, aggiungere anche un'impostazione di *ASPNETCORE_ENVIRONMENT* con il valore `Development` . 

   ![impostazioni Appservice](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Quando si esegue il test dell'endpoint con un'applicazione aziendale nella portale di Azure, scegliere di mantenere l'ambiente come `Development` e fornire il token generato dall' `/scim/token` endpoint per il test o modificare l'ambiente in `Production` e lasciare vuoto il campo token nell'applicazione aziendale nell' [portale di Azure](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client). 

Questo è tutto. L'endpoint SCIM è ora pubblicato e consente di usare l'URL del servizio app Azure per testare l'endpoint SCIM.

## <a name="test-your-scim-endpoint"></a>Testare l'endpoint SCIM

Per le richieste a un endpoint SCIM è richiesta l'autorizzazione e lo standard SCIM lascia più opzioni per l'autenticazione e l'autorizzazione, ad esempio cookie, autenticazione di base, autenticazione client TLS o uno dei metodi elencati nella [specifica RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Assicurarsi di evitare metodi non sicuri, ad esempio nome utente e password, a favore di un metodo più sicuro come OAuth. Azure AD supporta token di collegamento di lunga durata (per le applicazioni di raccolta e non di raccolta) e la concessione di autorizzazione OAuth (per le applicazioni pubblicate nella raccolta di app).

> [!NOTE]
> I metodi di autorizzazione forniti nel repository sono destinati solo ai test. Quando si esegue l'integrazione con Azure AD, è possibile esaminare le linee guida per l'autorizzazione, vedere [pianificare il provisioning per un endpoint scim](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#authorization-for-provisioning-connectors-in-the-application-gallery). 

L'ambiente di sviluppo Abilita le funzionalità non sicure per la produzione, ad esempio il codice di riferimento per controllare il comportamento della convalida del token di sicurezza. Il codice di convalida del token è configurato per l'uso di un token di sicurezza autofirmato e la chiave di firma viene archiviata nel file di configurazione, vedere il parametro **token: IssuerSigningKey** nella *appsettings.Development.jssu* file.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Inviando una richiesta **Get** all' `/scim/token` endpoint, viene emesso un token usando la chiave configurata e può essere usato come Bearer token per l'autorizzazione successiva.

Il codice di convalida del token predefinito è configurato per l'uso di un token emesso da Azure Active Directory e richiede che il tenant emittente venga configurato usando il parametro **token: TokenIssuer** nell' *appsettings.jssu* file.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Usare il post per testare gli endpoint

Dopo aver distribuito l'endpoint SCIM, è possibile eseguire il test per assicurarsi che sia conforme a SCIM RFC. Questo esempio fornisce un set di test nel **post** per convalidare le operazioni CRUD su utenti e gruppi, i filtri, gli aggiornamenti per l'appartenenza al gruppo e la disabilitazione degli utenti.

Gli endpoint si trovano nella `{host}/scim/` Directory e possono interagire con le richieste HTTP standard. Per modificare la `/scim/` Route, vedere *ControllerConstant.cs* in **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**  >  **Controllers**.

> [!NOTE]
> È possibile usare solo endpoint HTTP per i test locali perché il servizio di provisioning di Azure AD richiede che l'endpoint supporti HTTPS.

#### <a name="open-postman-and-run-tests"></a>Apri post e Esegui test

1. Scaricare il [post](https://www.getpostman.com/downloads/) e avviare l'applicazione.
1. Copiare il collegamento [https://aka.ms/ProvisioningPostman](https://aka.ms/ProvisioningPostman) e incollarlo in posting per importare la raccolta di test.

    ![raccolta di post](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Creare un ambiente di test con le variabili seguenti:

   |Ambiente|Variabile|valore|
   |-|-|-|
   |Esegui il progetto localmente usando IIS Express|||
   ||**Server**|`localhost`|
   ||**Porta**|`:44359`*(non dimenticare **:**)*|
   ||**API**|`scim`|
   |Eseguire il progetto localmente usando gheppio|||
   ||**Server**|`localhost`|
   ||**Porta**|`:5001`*(non dimenticare **:**)*|
   ||**API**|`scim`|
   |Hosting dell'endpoint in Azure|||
   ||**Server**|*(immettere l'URL di SCIM)*|
   ||**Porta**|*(lasciare vuoto)*|
   ||**API**|`scim`|

1. Usare **Get Key dalla raccolta Substart** per inviare una richiesta **Get** all'endpoint token e recuperare un token di sicurezza da archiviare nella variabile **token** per le richieste successive. 

   ![postazione Get (chiave)](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Per rendere protetti gli endpoint SCIM, è necessario un token di sicurezza prima della connessione e l'esercitazione usa l' `{host}/scim/token` endpoint per generare un token autofirmato.

Questo è tutto. È ora possibile eseguire la raccolta dei **post** per testare la funzionalità dell'endpoint SCIM.

## <a name="next-steps"></a>Passaggi successivi

Per sviluppare un endpoint utente e gruppo conforme a SCIM con interoperabilità per un client, vedere [implementazione del client scim](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Esercitazione: sviluppare e pianificare il provisioning per un endpoint scim](use-scim-to-provision-users-and-groups.md) 
>  [Esercitazione: configurare il provisioning per un'app della raccolta](configure-automatic-user-provisioning-portal.md)