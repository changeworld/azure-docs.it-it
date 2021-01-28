---
title: Configurazione della migrazione da servizi multimediali da V2 a V3
description: Questo articolo consente di configurare l'ambiente per la migrazione da servizi multimediali di Azure V2 a V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Servizi multimediali di Azure, migrazione, flusso, trasmissione, Live, SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 1f9e0816a3533008a01f26ca7c0e712abfa23e8e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946242"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>Passaggio 3: configurare per eseguire la migrazione all'API REST V3 o all'SDK client

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![passaggi della migrazione 2](./media/migration-guide/steps-3.svg)

Di seguito vengono descritti i passaggi da eseguire per configurare l'ambiente per l'utilizzo dell'API di servizi multimediali V3.

## <a name="sdk-model"></a>Modello SDK

Nell'API v2 erano presenti due SDK client diversi, uno per l'API di gestione, che consentiva la creazione a livello di codice di account e uno per la gestione delle risorse.

In precedenza, gli sviluppatori eseguivano l'uso di un ID client e di un segreto client dell'entità servizio di Azure, insieme a un endpoint dell'API REST V2 specifico per il proprio account AMS.

L'API V3 è basata su Azure Resource Manager (ARM). USA gli ID e le chiavi dell'entità servizio Azure Active Directory (Azure AD) per connettersi all'API. Gli sviluppatori dovranno creare entità servizio o identità gestite per la connessione all'API. Nell'API V3, l'API Usa endpoint ARM standard, usa un modello simile e coerente con tutti gli altri servizi di Azure.

I clienti che usano in precedenza la versione 2015-10-01 dell'API di gestione ARM per gestire gli account V2 devono usare la versione 2020-05-01 dell'API di gestione ARM supportata per l'accesso all'API V3.

## <a name="create-a-new-media-services-account-for-testing"></a>Creare un nuovo account di servizi multimediali per il test

Seguire i passaggi della Guida introduttiva per la [configurazione dell'ambiente](how-to-set-azure-subscription.md?tabs=portal) usando il portale di Azure. Selezionare accesso all'API e autenticazione basata su entità servizio per generare un nuovo ID applicazione Azure AD e i segreti da usare con questo account di test.

[Creare un account di servizi multimediali](create-account-howto.md?tabs=portal).
[Ottenere le credenziali per accedere all'API di servizi multimediali](access-api-howto.md?tabs=portal).

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>Scaricare l'SDK client preferito e configurare l'ambiente

- SDK disponibili per [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet&preserve-view=true), .NET Core, [Node.js](https://docs.microsoft.com/javascript/api/overview/azure/mediaservices/management?view=azure-node-latest&preserve-view=true), [Python](https://docs.microsoft.com/python/api/overview/azure/mediaservices/management?view=azure-python&preserve-view=true), [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices/management?view=azure-java-stable&preserve-view=true), [go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media)e [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md).
- INTERFACCIA della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest&preserve-view=true)   integrazione per il supporto di script semplici.

> [!NOTE]
> La community PHP SDK non è più disponibile per servizi multimediali di Azure in V3. Se si usa PHP in V2, è consigliabile eseguire la migrazione all'API REST direttamente nel codice.

## <a name="open-api-specifications"></a>Specifiche API aperte

- V3 si basa su una superficie API unificata, che espone sia la funzionalità di gestione che quella operativa basata su Azure Resource Manager. Azure Resource Manager modelli possono essere usati per creare e distribuire trasformazioni, endpoint di streaming, eventi live e altro ancora.

- Il documento [openapi Specification](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (noto in precedenza come spavalderia) illustra lo schema per tutti i componenti del servizio.

- Tutti gli SDK client sono derivati e generati dalla specifica API aperta pubblicata su GitHub. Al momento della pubblicazione di questo articolo, le specifiche API aperte più recenti vengono gestite pubblicamente in GitHub. La versione 2020-05-01 è la versione [stabile più recente](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01).

## <a name="rest"></a>[REST](#tab/rest)

Usare il [post](https://docs.microsoft.com/azure/media-services/latest/media-rest-apis-with-postman) per le chiamate all'API REST di servizi multimediali V3.
Leggere le [pagine di riferimento dell'API REST](https://docs.microsoft.com/rest/api/media/).

È consigliabile usare la stringa di versione 2020-05-01 nella raccolta dei post.

## <a name="net"></a>[.NET](#tab/net)

Leggere l'articolo [connettersi all'API di servizi multimediali V3 con .NET](configure-connect-dotnet-howto.md) per configurare l'ambiente.

Se si vuole semplicemente installare l'SDK più recente usando PackageManager, usare il comando seguente:

```Install-Package Microsoft.Azure.Management.Media```

In alternativa, per installare l'SDK più recente usando l'interfaccia della riga di comando di .NET usare il comando seguente:

```dotnet add package Microsoft.Azure.Management.Media```

Sono inoltre disponibili esempi completi di .NET in [Azure-Samples/Media-Services-V3-DotNet](https://github.com/Azure-Samples/media-services-v3-dotnet) per diversi scenari. I progetti in questo repository illustrano come implementare diversi scenari di servizi multimediali di Azure usando la versione V3.

### <a name="get-started-adjusting-your-code"></a>Inizia a modificare il codice

Eseguire una ricerca nella codebase per le istanze di `CloudMediaContext` utilizzo per avviare il processo di aggiornamento all'API V3.

Il codice seguente illustra il modo in cui è stato eseguito l'accesso all'API v2 usando V2 .NET SDK. Gli sviluppatori iniziano con la creazione di un `CloudMediaContext` oggetto e creano un'istanza con un `AzureAdTokenCredentials` oggetto.

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

Leggere l'articolo [connettersi all'API di servizi multimediali V3 con Java](configure-connect-java-howto.md) per configurare l'ambiente.

## <a name="python"></a>[Python](#tab/python)

Leggere l'articolo [connettersi a servizi multimediali di Azure V3 API-Python](configure-connect-python-howto.md) per configurare l'ambiente.

## <a name="nodejs"></a>[Node.js](#tab/nodejs)

Per configurare l'ambiente, vedere l'articolo [connettersi all'API di servizi multimediali di Azure V3-Node.js](configure-connect-nodejs-howto.md) .

## <a name="ruby"></a>[Ruby](#tab/ruby)

- Ottenere [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) SDK su GitHub.

## <a name="go"></a>[Go](#tab/go)

Scaricare [go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) SDK.

---

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]