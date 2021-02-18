---
title: Compilare un endpoint SCIM per il provisioning degli utenti nelle app da Azure Active Directory
description: Informazioni su come sviluppare un endpoint SCIM, integrare l'API SCIM con Azure AD ed eseguire automaticamente il provisioning di utenti e gruppi nelle applicazioni cloud con Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 9fac8db6747d1e081fa884ed794e55b98f44577f
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652038"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Esercitazione: sviluppare un endpoint SCIM di esempio

Se non si vuole creare un nuovo endpoint da zero, è stato creato un [codice di riferimento](https://aka.ms/scimreferencecode) per iniziare a usare [System for Cross-Domain Identity Management (SCIM)](https://aka.ms/scimoverview). È possibile fare in modo che l'endpoint SCIM sia operativo senza codice in soli cinque minuti.

In questa esercitazione viene descritto come distribuire il codice di riferimento SCIM in Azure e come testarlo tramite il post-o l'integrazione con il client SCIM Azure Active Directory (Azure AD). Questa esercitazione è destinata agli sviluppatori che vogliono iniziare a usare SCIM o a chiunque sia interessato a testare un endpoint SCIM.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Distribuire l'endpoint SCIM in Azure.
> * Testare l'endpoint SCIM.

## <a name="deploy-your-scim-endpoint-in-azure"></a>Distribuire l'endpoint SCIM in Azure

I passaggi qui illustrano come distribuire l'endpoint SCIM a un servizio usando [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) e il [servizio app Azure](https://docs.microsoft.com/azure/app-service/). Il codice di riferimento SCIM può essere eseguito anche localmente, ospitato da un server locale o distribuito a un altro servizio esterno.

1. Passare al [codice di riferimento](https://github.com/AzureAD/SCIMReferenceCode) da GitHub e selezionare **clona o Scarica**.

1. Selezionare **Apri in desktop** oppure copiare il collegamento, aprire Visual Studio e selezionare **clona o Estrai codice** per immettere il collegamento copiato ed effettuare una copia locale.

1. In Visual Studio, assicurarsi di accedere all'account che ha accesso alle risorse di hosting.

1. In Esplora soluzioni aprire *Microsoft. SCIM. sln* e fare clic con il pulsante destro del mouse sul file *Microsoft. SCIM. WebHostSample* . Selezionare **Pubblica**.

    ![Screenshot che mostra il file di esempio.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Per eseguire questa soluzione localmente, fare doppio clic sul progetto e selezionare **IIS Express** per avviare il progetto come pagina Web con un URL host locale.

1. Selezionare **Crea profilo** e assicurarsi che il **servizio app** e **Crea nuovo** siano selezionati.

    ![Screenshot che mostra la finestra di pubblicazione.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Scorrere le opzioni della finestra di dialogo e rinominare l'app con un nome a scelta. Questo nome viene usato sia nell'app che nell'URL dell'endpoint SCIM.

    ![Screenshot che illustra la creazione di un nuovo servizio app.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Selezionare il gruppo di risorse da usare e selezionare **pubblica**.

1. Passare all'applicazione in **app Azure**  >  **configurazione** del servizio e selezionare **nuova impostazione applicazione** per aggiungere l'impostazione *Token__TokenIssuer* con il valore `https://sts.windows.net/<tenant_id>/` . Sostituire `<tenant_id>` con l'ID tenant del Azure ad. Se si vuole testare l'endpoint SCIM usando il [post](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint), aggiungere un'impostazione di *ASPNETCORE_ENVIRONMENT* con il valore `Development` .

   ![Screenshot che mostra la finestra delle impostazioni dell'applicazione.](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Quando si esegue il test dell'endpoint con un'applicazione aziendale nella [portale di Azure](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client), sono disponibili due opzioni. È possibile mantenere l'ambiente in `Development` e fornire il token di test dall' `/scim/token` endpoint oppure è possibile modificare l'ambiente in `Production` e lasciare vuoto il campo del token.

Questo è tutto. L'endpoint SCIM è ora pubblicato ed è possibile usare l'URL del servizio app Azure per testare l'endpoint SCIM.

## <a name="test-your-scim-endpoint"></a>Testare l'endpoint SCIM

Le richieste a un endpoint SCIM richiedono l'autorizzazione. Lo standard SCIM include più opzioni per l'autenticazione e l'autorizzazione, inclusi cookie, autenticazione di base, autenticazione client TLS o uno dei metodi elencati nella [specifica RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Assicurarsi di evitare i metodi che non sono sicuri, ad esempio nome utente e password, a favore di un metodo più sicuro come OAuth. Azure AD supporta token di collegamento di lunga durata (per le applicazioni della raccolta e non della raccolta) e la concessione di autorizzazione OAuth (per le applicazioni della raccolta).

> [!NOTE]
> I metodi di autorizzazione forniti nel repository sono destinati solo ai test. Quando si integra con Azure AD, è possibile esaminare le linee guida per l'autorizzazione. Vedere [pianificare il provisioning per un endpoint scim](use-scim-to-provision-users-and-groups.md).

L'ambiente di sviluppo Abilita le funzionalità non sicure per la produzione, ad esempio il codice di riferimento per controllare il comportamento della convalida del token di sicurezza. Il codice di convalida del token usa un token di sicurezza autofirmato e la chiave di firma viene archiviata nel file di configurazione. Vedere il parametro **token: IssuerSigningKey** nella *appsettings.Development.jssu* file.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Quando si invia una richiesta **Get** all' `/scim/token` endpoint, viene emesso un token usando la chiave configurata. Questo token può essere usato come bearer token per l'autorizzazione successiva.

Il codice di convalida del token predefinito è configurato per l'uso di un token di Azure AD e richiede che il tenant emittente venga configurato usando il parametro **token: TokenIssuer** nell' *appsettings.jssul* file.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Usare il post per testare gli endpoint

Dopo aver distribuito l'endpoint SCIM, è possibile eseguire un test per assicurarsi che sia conforme a SCIM RFC. Questo esempio fornisce un set di test nel post che convalidano le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) su utenti e gruppi, l'applicazione di filtri, gli aggiornamenti per l'appartenenza al gruppo e la disabilitazione degli utenti.

Gli endpoint si trovano nella `{host}/scim/` Directory ed è possibile usare le richieste HTTP standard per interagire con esse. Per modificare la `/scim/` Route, vedere *ControllerConstant.cs* in **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**  >  **Controllers**.

> [!NOTE]
> È possibile usare solo endpoint HTTP per i test locali. Il servizio di provisioning di Azure AD richiede che l'endpoint supporti HTTPS.

1. Scaricare il [post](https://www.getpostman.com/downloads/) e avviare l'applicazione.
1. Copiare e incollare il collegamento in postin per importare la raccolta di test: `https://aka.ms/ProvisioningPostman` .

    ![Screenshot che mostra l'importazione della raccolta di test in post.](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Creare un ambiente di test con le variabili seguenti:

   |Ambiente|Variabile|valore|
   |-|-|-|
   |Eseguire il progetto in locale usando IIS Express|||
   ||**Server**|`localhost`|
   ||**Porta**|`:44359`*(non dimenticare **`:`** )*|
   ||**API**|`scim`|
   |Eseguire il progetto localmente usando gheppio|||
   ||**Server**|`localhost`|
   ||**Porta**|`:5001`*(non dimenticare **`:`** )*|
   ||**API**|`scim`|
   |Ospitare l'endpoint in Azure|||
   ||**Server**|*(immettere l'URL di SCIM)*|
   ||**Porta**|*(lasciare vuoto)*|
   ||**API**|`scim`|

1. Usare **Get Key dalla raccolta Substart** per inviare una richiesta **Get** all'endpoint token e recuperare un token di sicurezza da archiviare nella variabile **token** per le richieste successive.

   ![Screenshot che mostra la cartella della chiave Get del post.](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Per rendere protetto un endpoint SCIM, è necessario un token di sicurezza prima di connettersi. L'esercitazione usa l' `{host}/scim/token` endpoint per generare un token autofirmato.

Questo è tutto. È ora possibile eseguire la raccolta dei **post** per testare la funzionalità dell'endpoint SCIM.

## <a name="next-steps"></a>Passaggi successivi

Per sviluppare un endpoint utente e gruppo conforme a SCIM con interoperabilità per un client, vedere [implementazione del client scim](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Esercitazione: sviluppare e pianificare il provisioning per un endpoint scim](use-scim-to-provision-users-and-groups.md) 
>  [Esercitazione: configurare il provisioning per un'app della raccolta](configure-automatic-user-provisioning-portal.md)
