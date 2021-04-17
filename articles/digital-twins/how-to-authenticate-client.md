---
title: Scrivere il codice di autenticazione dell'app
titleSuffix: Azure Digital Twins
description: Informazioni su come scrivere il codice di autenticazione in un'applicazione client
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 958b0de97b79b447f2570dd9c57c87f380bcd551
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589395"
---
# <a name="write-client-app-authentication-code"></a>Scrivere il codice di autenticazione dell'app client

Dopo aver [configurato un'Gemelli digitali di Azure e](how-to-set-up-instance-portal.md)l'autenticazione , è possibile creare un'applicazione client che verrà utilizzata per interagire con l'istanza. Dopo aver configurato un progetto client iniziale, è necessario scrivere codice **nell'app client** per autenticarlo nell'istanza Gemelli digitali di Azure iniziale.

Gemelli digitali di Azure esegue l'autenticazione [Azure AD token di sicurezza basati su OAUTH 2.0.](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) Per autenticare l'SDK, è necessario ottenere un bearer token con le autorizzazioni appropriate per Gemelli digitali di Azure e passarlo insieme alle chiamate API. 

Questo articolo descrive come ottenere le credenziali usando la `Azure.Identity` libreria client. Anche se questo articolo illustra esempi di codice in C#, ad esempio ciò che si scrive per [.NET (C#) SDK,](/dotnet/api/overview/azure/digitaltwins/client)è possibile usare una versione di indipendentemente dall'SDK in uso. Per altre informazioni sugli SDK disponibili per Gemelli digitali di Azure, vedere Procedura: Usare le API Gemelli digitali di Azure e `Azure.Identity` [*gli SDK).*](how-to-use-apis-sdks.md)

## <a name="prerequisites"></a>Prerequisiti

Per prima cosa, completare i passaggi di configurazione in [*Procedura: Configurare un'istanza e l'autenticazione*](how-to-set-up-instance-portal.md). In questo modo si avrà la sicurezza di avere un'Gemelli digitali di Azure e che l'utente abbia le autorizzazioni di accesso. Dopo la configurazione, si è pronti per scrivere il codice dell'app client.

Per continuare, è necessario un progetto di app client in cui scrivere il codice. Se non è già stato configurato un progetto di app client, creare un progetto di base nel linguaggio preferito da usare con questa esercitazione.

## <a name="common-authentication-methods-with-azureidentity"></a>Metodi di autenticazione comuni con Azure.Identity

`Azure.Identity` è una libreria client che fornisce diversi metodi di recupero delle credenziali che è possibile usare per ottenere un bearer token ed eseguire l'autenticazione con l'SDK. Anche se questo articolo fornisce esempi in C#, è possibile visualizzare `Azure.Identity` per diversi linguaggi, tra cui...

* [.NET (C#)](/dotnet/api/azure.identity)
* [Java](/java/api/overview/azure/identity-readme)
* [JavaScript](/javascript/api/overview/azure/identity-readme)
* [Python](/python/api/overview/azure/identity-readme)

Tre metodi comuni per ottenere credenziali in `Azure.Identity` sono:

* [DefaultAzureCredential fornisce](/dotnet/api/azure.identity.defaultazurecredential) un flusso di autenticazione predefinito per le applicazioni che verranno distribuite in Azure ed è la scelta `TokenCredential` consigliata per lo sviluppo **locale.** Può anche essere abilitato per provare gli altri due metodi consigliati in questo articolo. esegue il wrapping `ManagedIdentityCredential` e può accedere con una variabile di `InteractiveBrowserCredential` configurazione.
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) funziona bene nei casi in cui sono necessarie identità gestite [(MSI)](../active-directory/managed-identities-azure-resources/overview.md)ed è un buon candidato per lavorare con Funzioni di Azure e distribuirsi nei servizi di Azure.
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) è destinato alle applicazioni interattive e può essere usato per creare un client SDK autenticato

L'esempio seguente illustra come usare ognuno di questi elementi con .NET (C#) SDK.

## <a name="authentication-examples-net-c-sdk"></a>Esempi di autenticazione: .NET (C#) SDK

Questa sezione illustra un esempio in C# per l'uso dell'SDK .NET fornito per scrivere il codice di autenticazione.

Prima di tutto, includere il pacchetto SDK `Azure.DigitalTwins.Core` e il pacchetto nel `Azure.Identity` progetto. A seconda degli strumenti selezionati, è possibile includere i pacchetti usando gestione pacchetti Visual Studio o lo strumento `dotnet` da riga di comando. 

È anche necessario aggiungere le istruzioni using seguenti al codice del progetto:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

Aggiungere quindi il codice per ottenere le credenziali usando uno dei metodi in `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>Metodo DefaultAzureCredential

[DefaultAzureCredential fornisce](/dotnet/api/azure.identity.defaultazurecredential) un flusso di autenticazione predefinito per le applicazioni che verranno distribuite in Azure ed è la scelta `TokenCredential` consigliata per lo sviluppo **locale.**

Per usare le credenziali di Azure predefinite, è necessario l'URL dell'istanza Gemelli digitali di Azure (istruzioni[per trovare](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Di seguito è riportato un esempio di codice per `DefaultAzureCredential` aggiungere un oggetto al progetto:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>Configurare le credenziali di Azure locali

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>Metodo ManagedIdentityCredential

Il [metodo ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) funziona bene nei casi in cui sono necessarie identità [gestite,](../active-directory/managed-identities-azure-resources/overview.md)ad esempio quando si lavora con Funzioni di Azure.

Ciò significa che è possibile usare `ManagedIdentityCredential` nello stesso progetto di o per `DefaultAzureCredential` `InteractiveBrowserCredential` autenticare una parte diversa del progetto.

Per usare le credenziali predefinite di Azure, è necessario l'URL dell'istanza Gemelli digitali di Azure ([istruzioni per trovare](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

In una funzione di Azure è possibile usare le credenziali dell'identità gestita come queste:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>Metodo InteractiveBrowserCredential

Il [metodo InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) è destinato alle applicazioni interattive e verrà visualizzato un Web browser per l'autenticazione. È possibile usarlo invece di `DefaultAzureCredential` nei casi in cui è necessaria l'autenticazione interattiva.

Per usare le credenziali del browser interattivo, è necessaria una registrazione **dell'app** con autorizzazioni per le API Gemelli digitali di Azure interattive. Per la procedura per configurare la registrazione dell'app, vedere [*Procedura: Creare una registrazione dell'app.*](how-to-create-app-registration.md) Dopo aver configurato la registrazione dell'app, è necessario...
* ID applicazione *(client) della registrazione dell'app* ([istruzioni per trovare](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* ID directory *(tenant) della* registrazione dell'app ([istruzioni per trovare](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* URL Gemelli digitali di Azure dell'istanza ([istruzioni per trovare](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

Di seguito è riportato un esempio di codice per creare un client SDK autenticato usando `InteractiveBrowserCredential` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> Anche se è possibile inserire l'ID client, l'ID tenant e l'URL dell'istanza direttamente nel codice, come illustrato in precedenza, è buona idea che il codice otterrà questi valori da un file di configurazione o da una variabile di ambiente.

#### <a name="other-notes-about-authenticating-azure-functions"></a>Altre note sull'autenticazione Funzioni di Azure

Vedere [*Procedura: Configurare una*](how-to-create-azure-function.md) funzione di Azure per l'elaborazione dei dati per un esempio più completo che illustra alcune delle opzioni di configurazione importanti nel contesto delle funzioni.

Inoltre, per usare l'autenticazione in una funzione, ricordarsi di:
* [Abilitare l'identità gestita](../app-service/overview-managed-identity.md?tabs=dotnet)
* Usare [le variabili di ambiente](/sandbox/functions-recipes/environment-variables?tabs=csharp) in base alle esigenze
* Assegnare le autorizzazioni all'app per le funzioni che le consentono di accedere alle API di Gemelli digitali. Per altre informazioni sui processi Funzioni di Azure, vedere Procedura: Configurare una funzione [*di Azure per l'elaborazione dei dati.*](how-to-create-azure-function.md)

## <a name="authenticate-across-tenants"></a>Eseguire l'autenticazione su più tenant

Gemelli digitali di Azure è un servizio che supporta solo un  [tenant Azure Active Directory (Azure AD):](../active-directory/develop/quickstart-create-new-tenant.md)il tenant principale della sottoscrizione in cui si trova l'istanza Gemelli digitali di Azure.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

Se è necessario accedere all'istanza di Gemelli digitali di Azure usando un'entità servizio o un account utente appartenente a un tenant diverso dall'istanza, è possibile fare in modo che ogni identità federata di un altro tenant chieda un **token** dal tenant "home" dell'istanza di Gemelli digitali di Azure. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

È anche possibile specificare il tenant principale nelle opzioni delle credenziali nel codice. 

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="other-credential-methods"></a>Altri metodi di credenziale

Se gli scenari di autenticazione evidenziati sopra non riguardano le esigenze dell'app, è possibile esplorare altri tipi di autenticazione offerti in [**Microsoft Identity Platform.**](../active-directory/develop/v2-overview.md#getting-started) La documentazione per questa piattaforma illustra altri scenari di autenticazione, organizzati in base al tipo di applicazione.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul funzionamento della sicurezza in Gemelli digitali di Azure:
* [*Concetti: Sicurezza per Gemelli digitali di Azure soluzioni*](concepts-security.md)

Ora che l'autenticazione è stata impostata, passare alla creazione e alla gestione dei modelli nell'istanza:
* [*Procedura: Gestire modelli DTDL*](how-to-manage-model.md)