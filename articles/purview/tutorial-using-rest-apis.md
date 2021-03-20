---
title: 'Esercitazione: Usare le API REST'
description: Questa esercitazione descrive come usare le API REST di Azure Purview per accedere al contenuto del catalogo.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: bfb808c634ba946e1a4825d7828db6df8963352c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98951244"
---
# <a name="tutorial-use-the-rest-apis"></a>Esercitazione: Usare le API REST

Questa esercitazione illustra come usare le API REST di Azure Purview. Per inviare dati a un catalogo di Azure Purview, includere il catalogo in un processo automatizzato o creare un'esperienza personalizzate nel catalogo, è possibile usare le API REST.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare un'entità servizio (applicazione).
> * Configurare il catalogo in modo che consideri attendibile l'entità servizio (applicazione).
> * Visualizzare la documentazione delle API REST.
> * Raccogliere i valori necessari per usare le API REST.
> * Usare il client Postman per chiamare le API REST.
> * Generare un client .NET per chiamare le API REST.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Per iniziare, è necessario avere un account Azure Purview esistente. Se non è disponibile un catalogo, vedere la [guida di avvio rapido sulla creazione di un account Azure Purview](create-catalog-portal.md).

* Se è necessario aggiungere dati al catalogo, vedere l'[esercitazione su come eseguire lo starter kit e analizzare i dati](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-service-principal-application"></a>Creare un'entità servizio (applicazione)

Per accedere al catalogo, il client API REST deve avere un'entità servizio (applicazione) e un'identità che il catalogo riconosce ed è configurato per considerare attendibile. Quando si effettuano chiamate API REST al catalogo, viene usata l'identità dell'entità servizio.

I clienti che hanno usato entità servizio esistenti (ID applicazione) hanno riscontrato un tasso elevato di errori. Pertanto, è consigliabile creare una nuova entità servizio per chiamare le API.

Per creare una nuova entità servizio:

1. Nel [portale di Azure](https://portal.azure.com) cercare e selezionare **Azure Active Directory**.
1. Nella pagina **Azure Active Directory** selezionare **Registrazioni app** nel riquadro sinistro.
1. Selezionare **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione**:
    1. Immettere un **Nome** per l'applicazione (il nome dell'entità servizio).
    1. Selezionare **Account solo in questa directory dell'organizzazione (Solo _&lt;nome del tenant&gt;_ - Tenant singolo)** .
    1. In **URI di reindirizzamento (facoltativo)** selezionare **Web** e immettere un valore. Questo valore non deve necessariamente essere un URI valido.
    1. Selezionare **Registra**.
1. Nella pagina della nuova entità servizio copiare i valori di **Nome visualizzato** e **ID applicazione (client)** e salvarli per dopo.

   L'ID applicazione è il valore `client_id` nell'esempio di codice.

Per usare l'entità servizio (applicazione), è necessario ottenere la relativa password. Ecco come:

1. Nel portale di Azure cercare e selezionare **Azure Active Directory**, quindi selezionare **Registrazioni app** nel riquadro sinistro.
1. Selezionare l'entità servizio (applicazione) nell'elenco.
1. Selezionare **Certificati e segreti** nel riquadro sinistro.
1. Selezionare **Nuovo segreto client**.
1. Nella pagina **Aggiungi un segreto client** immettere una **Descrizione**, selezionare un'ora di scadenza in **Scadenza** e quindi selezionare **Aggiungi**.

   Nella pagina **Segreti client** la stringa nella colonna **Valore** del nuovo segreto corrisponde alla password. Salvare il valore.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="Screenshot che mostra un segreto client.":::

## <a name="configure-your-catalog-to-trust-the-service-principal-application"></a>Configurare il catalogo in modo che consideri attendibile l'entità servizio (applicazione)

Per configurare Azure Purview in modo che consideri attendibili la nuova entità servizio:

1. Passare all'account Purview

1. Nella pagina **Account Purview** selezionare la scheda **Controllo di accesso (IAM)**

1. Fare clic su **+ Aggiungi**

1. Selezionare **Aggiungi assegnazione di ruolo**

1. Per il tipo di ruolo digitare **Curatore dei dati di Purview**

    > [!Note]
    > Per altre informazioni sulle autorizzazioni di Azure Purview Data Catalog, vedere [Autorizzazioni del catalogo](catalog-permissions.md). Se ad esempio è necessario l'autorizzazione per attivare un'analisi, il tipo di ruolo deve essere **Amministratore delle origini dati di Purview**.

1. Per **Assegna accesso a**, lasciare l'impostazione predefinita **Utente, gruppo o entità servizio**

1. Per **Select** immettere il nome dell'entità servizio creata da previosly che si vuole assegnare e quindi fare clic sul relativo nome nel riquadro dei risultati.

1. Fare clic su **Salva**

A questo punto l'entità servizio è stata configurata come amministratore dell'applicazione e può quindi inviare contenuto al catalogo.

## <a name="view-the-rest-apis-documentation"></a>Visualizzare la documentazione delle API REST

Per visualizzare la documentazione Swagger dell'API, scaricare [PurviewCatalogAPISwagger.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/PurviewCatalogAPISwagger.zip), estrarre i file e aprire index.html.

Per altre informazioni sull'API avanzata di ricerca/suggerimenti offerta da Azure Purview, vedere la documentazione sui filtri di ricerca delle API REST. Il client AutoRest generato attualmente non supporta parametri di ricerca personalizzati. Come soluzione alternativa, seguire il documento sul filtro di ricerca per definire classi di filtro nel codice come parametri di chiamata API. Il documento index.html include esempi di queste API.

## <a name="collect-the-necessary-values-to-use-the-rest-apis"></a>Raccogliere i valori necessari per usare le API REST

Trovare e salvare i valori seguenti:

* ID tenant:
  * Nel [portale di Azure](https://portal.azure.com) cercare e selezionare **Azure Active Directory**.
  * Nella sezione **Gestisci** del riquadro sinistro selezionare **Proprietà**, trovare il valore di **ID tenant**, quindi selezionare l'icona **Copia negli Appunti** per salvarlo.
* Endpoint Atlas:
  * Nella [pagina di account Azure Purview](https://aka.ms/purviewportal) nel portale di Azure trovare e selezionare il proprio account Azure Purview nell'elenco.
  * Selezionare **Proprietà**, trovare il valore di **Atlas Endpoint** e quindi selezionare l'icona **Copia negli Appunti** per salvarlo. Rimuovere la parte *https://* della stringa quando verrà usata in seguito.
* Nome account:
  * Estrarre il nome del catalogo dalla stringa dell'endpoint Atlas. Se ad esempio l'endpoint Atlas è `https://ThisIsMyCatalog.catalog.purview.azure.com`, il nome dell'account è `ThisIsMyCatalog`.

## <a name="use-the-postman-client-to-call-the-rest-apis"></a>Usare il client Postman per chiamare le API REST

1. Installare il [client Postman](https://www.getpostman.com/).
1. Nel client selezionare **Importa** e usare [Test.postman_collection.json](https://raw.githubusercontent.com/Azure/Purview-Samples/master/rest-api/Test.postman_collection.json).
1. Selezionare **Raccolte** e quindi **Test**.
1. Selezionare **Recupera token**:
    1. Nell'URL accanto a POST sostituire *&lt;your-tenant-id&gt;* con l'ID tenant copiato nella sezione precedente.
    1. Selezionare la scheda **Corpo** e sostituire i segnaposto nel percorso e nel corpo con i valori del passaggio precedente.

       Dopo aver selezionato **Invia**, il corpo della risposta contiene una struttura JSON che include il nome *access_token* e un valore di stringa tra virgolette. 
    1. Copiare il valore del token di connessione (senza virgolette) per usarlo nel passaggio successivo.

1. Selezionare **/v2/types/typedefs**:
    1. Sostituire il segnaposto nel percorso con il valore dell'endpoint Atlas. Questo valore si può ottenere passando all'istanza del catalogo nel portale Ibiza e facendo clic sull'opzione della panoramica. 

       Il token di connessione viene impostato nel primo passaggio. In alternativa è possibile copiarlo manualmente nella scheda "Autorizzazione".

    1. Selezionare **Invia** per ottenere la risposta.

## <a name="generate-a-net-client-to-call-the-rest-apis"></a>Generare un client .NET per chiamare le API REST

### <a name="install-autorest"></a>Installare AutoRest



1. [Installare Node.js](https://github.com/Azure/autorest/blob/v2/docs/installing-autorest.md).
1. Aprire PowerShell ed eseguire il comando seguente:

   ```powershell
   npm install -g autorest@V2
   ```

### <a name="download-rest-api-specszip-and-create-the-client"></a>Scaricare il file rest-api-specs.zip e creare il client

1. Scaricare il file [rest-api-specs.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/rest-api-specs.zip) e decomprimerlo.
1. Eseguire il comando seguente in PowerShell nella cartella rest-api-specs estratta:

   ```powershell
   autorest --input-file=data-plane/preview/purviewcatalog.json --csharp --output-folder=PurviewCatalogClient/CSharp --namespace=PurviewCatalog --add-credentials
   ```

   L'output di questo processo crea una cartella PurviewCatalogClient e una sottocartella CSharp nella cartella rest-api-specs.

### <a name="create-a-sample-net-console-application"></a>Creare un'applicazione console .NET di esempio

1. Aprire Visual Studio 2019. Queste istruzioni sono state testate con l'edizione Community gratuita.
1. Nella pagina **Crea un nuovo progetto** creare un progetto **App console (.NET Core)** in C#.
1. Copiare e incollare l'[esempio di codice](#sample-code-for-the-console-application) fornito.
1. Sostituire *accountName*, *servicePrincipalId*, *servicePrincipalKey* e *tenantId* con i valori raccolti in precedenza.
1. Usare **Esplora soluzioni** per aggiungere una cartella denominata **Generated** nel progetto di Visual Studio.
1. Copiare la cartella rest-api-specs\PurviewCatalogClient\CSharp creata in precedenza nella cartella \Generated. Usare Esplora file o il prompt della riga di comando per l'operazione di copia, che attiverà l'aggiunta automatica dei file nel progetto in Visual Studio.
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.
1. Selezionare la scheda **Sfoglia**. Trovare e selezionare **Microsoft.Rest.ClientRuntime**.
1. Assicurarsi che la versione sia almeno 2.3.21 e quindi selezionare **Installa**.
1. Compilare ed eseguire l'applicazione.

L'esempio di codice restituisce il numero di typedef presenti nel catalogo e mostra come gestire le assegnazioni di ruoli. Per i dettagli, vedere `DoRoleAssignmentOperations()` nell'esempio di codice. Per altre informazioni sul progetto, vedere [Configurazione del progetto](https://github.com/Azure/autorest/blob/v2/docs/client/proj-setup.md).

### <a name="sample-code-for-the-console-application"></a>Esempio di codice per l'applicazione console

```csharp
using Microsoft.Rest;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PurviewCatalogSdkTest
{
    class Program
    {
        private static string accountName = "{account-name}";
        private static string servicePrincipalId = "{service-principal-id}";
        private static string servicePrincipalKey = "{service-principal-key}";
        private static string tenantId = "{tenant-id}";

        static void Main(string[] args)
        {
            Console.WriteLine("Azure Purview client");

            // You need to change the api path below (e.g. /api) based on what you're trying to call
            string baseUri = string.Format("https://{0}.catalog.purview.azure.com/api", accountName);

            // Get token and set auth
            var svcClientCreds = new TokenCredentials(getToken(), "Bearer");
            var client = new PurviewCatalog.PurviewCatalogServiceRESTAPIDocument(svcClientCreds);
            client.BaseUri = new System.Uri(baseUri);

            // /v2/types/typedefs
            var task = client.TypesREST.GetAllTypeDefsWithHttpMessagesAsync();
            Console.WriteLine("\nURI:\n" + task.Result.Request.RequestUri);
            Console.WriteLine("\nStatus Code:\n" + task.Result.Response.StatusCode);
            Console.WriteLine("\nResult:\n" + JsonConvert.SerializeObject(task.Result.Body, Formatting.Indented));
        }

        // Replace client_id and client_secret with application ID and password value from service principal
        private static string getToken()
        {
            var values = new Dictionary<string, string>

            // The "resource" could be "https://purview.azure.net" or "73c2949e-da2d-457a-9607-fcc665198967"
            {
                { "grant_type", "client_credentials" },
                { "client_id", servicePrincipalId },
                { "client_secret", servicePrincipalKey },
                { "resource", "73c2949e-da2d-457a-9607-fcc665198967" }
            };

            string authUrl = string.Format("https://login.windows.net/{0}/oauth2/token", tenantId);
            var content = new FormUrlEncodedContent(values);

            HttpClient authClient = new HttpClient();
            var bearerResult = authClient.PostAsync(authUrl, content);
            bearerResult.Wait();
            var resultContent = bearerResult.Result.Content.ReadAsStringAsync();
            resultContent.Wait();
            var bearerToken = JObject.Parse(resultContent.Result)["access_token"].ToString();
            var svcClientCreds = new TokenCredentials(bearerToken, "Bearer");

            return bearerToken;
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire le origini dati](manage-data-sources.md)
