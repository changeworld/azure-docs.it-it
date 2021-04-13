---
title: SDK e API REST per i servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Altre informazioni sugli SDK di servizi di comunicazione Azure e sulle API REST.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: d71720b446baa8e2e5e15c407a69d9ff884004b2
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307691"
---
# <a name="sdks-and-rest-apis"></a>SDK e API REST

Le funzionalità dei servizi di comunicazione di Azure sono organizzate concettualmente in sei aree. La maggior parte delle aree dispone di SDK completamente open source programmati in API REST pubblicate che è possibile usare direttamente su Internet. L'SDK chiamante utilizza interfacce di rete proprietarie ed è attualmente chiuso. Gli esempi e i dettagli più tecnici per gli SDK sono pubblicati nel [repository GitHub dei servizi di comunicazione di Azure](https://github.com/Azure/communication).

## <a name="rest-apis"></a>API REST
Le API di servizi di comunicazione sono documentate insieme ad altre API REST di Azure in [docs.Microsoft.com](/rest/api/azure/). In questa documentazione viene illustrato come strutturare i messaggi HTTP e vengono fornite informazioni aggiuntive per l'uso di post. Questa documentazione è disponibile anche in formato spavalderia su [GitHub](https://github.com/Azure/azure-rest-api-specs).


## <a name="sdks"></a>SDK

| Assembly | Spazi dei nomi| Protocolli | Funzionalità |
|------------------------|-------------------------------------|---------------------------------|--------------------------------------------------------------------------------------------|
| Azure Resource Manager | Azure. ResourceManager. Communication | [REST](https://docs.microsoft.com/rest/api/communication/communicationservice)| Provisioning e gestione delle risorse di servizi di comunicazione|
| Comuni | Azure. Communication. Common| REST | Fornisce tipi di base per altri SDK |
| Identità | Azure. Communication. Identity| [REST](https://docs.microsoft.com/rest/api/communication/communicationidentity)| Gestire gli utenti, i token di accesso|
| Numeri di telefono _(beta)_| Azure. Communication. PhoneNumbers| [REST](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)| Acquisisci e Gestisci i numeri di telefono |
| Chat | Azure. Communication. chat| [Rest](https://docs.microsoft.com/rest/api/communication/) con segnalazione proprietaria | Aggiungere chat basate su testo in tempo reale alle applicazioni |
| SMS| Azure. Communication. SMS | [REST](https://docs.microsoft.com/rest/api/communication/sms)| Inviare e ricevere messaggi SMS|
| Chiamata| Azure. Communication. Calling | Trasporto proprietario | USA funzionalità per la comunicazione di dati in tempo reale, video, condivisione dello schermo e altro |

Gli SDK Azure Resource Manager, Identity e SMS sono incentrati sull'integrazione del servizio e in molti casi si verificano problemi di sicurezza se si integrano queste funzioni nelle applicazioni dell'utente finale. Gli SDK comuni e chat sono adatti per le applicazioni client e di servizio. L'SDK chiamante è progettato per le applicazioni client. Un SDK incentrato sugli scenari del servizio è in fase di sviluppo.


### <a name="languages-and-publishing-locations"></a>Linguaggi e percorsi di pubblicazione

I percorsi di pubblicazione per i singoli pacchetti SDK sono descritti in dettaglio di seguito.

| Area           | JavaScript | .NET | Python | Java SE | iOS | Android | Altro                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Go tramite GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Comuni         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | N/D      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Identità | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [PyPi](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| Numeri di telefono | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| Chat           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| sms            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Chiamata        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Documentazione di riferimento     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | [docs](/objectivec/communication-services/calling/)      | [docs](/java/api/com.azure.android.communication.calling)            | -                              |


## <a name="rest-api-throttles"></a>Limitazioni dell'API REST
Alcune API REST e i corrispondenti metodi SDK hanno limiti di limitazione che è necessario tenere presenti. Il superamento di questi limiti di limitazione attiverà una  `429 - Too Many Requests` risposta di errore. Questi limiti possono essere aumentati tramite [una richiesta al supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

| API                                                                                                                          | Limitazione            |
|------------------------------------------------------------------------------------------------------------------------------|---------------------|
| [Tutte le API del piano di numero di telefono di ricerca](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)         | 4 richieste/giorno      |
| [Piano di acquisto di numeri di telefono](https://docs.microsoft.com/rest/api/communication/phonenumberadministration/purchasesearch) | 1 Acquista un mese  |
| [Invia SMS](https://docs.microsoft.com/rest/api/communication/sms/send)                                                       | 200 richieste al minuto |


## <a name="sdk-platform-support-details"></a>Dettagli del supporto della piattaforma SDK

### <a name="ios-and-android"></a>iOS e Android 

- I servizi di comunicazione iOS SDK hanno come destinazione iOS versione 13 + e Xcode 11 +.
- Android Java SDK destinazione API Android livello 21 + e Android Studio 4.0 +

### <a name="net"></a>.NET 

Ad eccezione della chiamata, i pacchetti di servizi di comunicazione sono destinati .NET Standard 2,0, che supporta le piattaforme elencate di seguito.

Supporto tramite .NET Framework 4.6.1
- Windows 10, 8,1, 8 e 7
- Windows Server 2012 R2, 2012 e 2008 R2 SP1

Supporto tramite .NET Core 2,0:
- Windows 10 (1607 +), 7 SP1 +, 8,1
- Windows Server 2008 R2 SP1+
- Max OS X 10.12 +
- Più versioni/distribuzioni di Linux
- UWP) 10.0.16299 (RS3) settembre 2017
- Unity 2018,1
- Mono 5.4
- Novell iOS 10,14
- Novell Mac 3,8

## <a name="api-stability-expectations"></a>Aspettative di stabilità dell'API

> [!IMPORTANT]
> Questa sezione fornisce indicazioni sulle API REST e sugli SDK contrassegnati come **stabili**. Le API contrassegnate come versione non definitiva, anteprima o beta possono essere modificate o deprecate **senza preavviso**.

In futuro si potrebbero ritirare le versioni degli SDK di servizi di comunicazione ed è possibile che vengano introdotte modifiche di rilievo per le API REST e gli SDK rilasciati. I servizi di comunicazione di Azure seguiranno *in genere* due criteri di supporto per la disattivazione delle versioni del servizio:

- Si riceverà una notifica per almeno tre anni prima che venga richiesto di modificare il codice a causa di una modifica dell'interfaccia di servizi di comunicazione. Tutte le API REST e le API SDK documentate si riferiscono in genere a almeno tre anni prima di rimuovere le interfacce.
- Si riceverà una notifica almeno un anno prima di dover aggiornare gli assembly SDK alla versione secondaria più recente. Questi aggiornamenti richiesti non devono richiedere modifiche al codice perché si trovano nella stessa versione principale. Questa operazione è particolarmente valida per le librerie di chiamata e di chat con componenti in tempo reale che richiedono spesso aggiornamenti della sicurezza e delle prestazioni. Si consiglia vivamente di proteggere gli SDK di servizi di comunicazione aggiornati.

### <a name="api-and-sdk-decommissioning-examples"></a>Esempi di rimozione di API e SDK

**La versione V24 dell'API REST SMS è stata integrata nell'applicazione. Azure Communication releases V25.**

Si otterrà un avviso di tre anni prima che queste API smettano di funzionare e che siano forzate a eseguire l'aggiornamento a V25. Questo aggiornamento potrebbe richiedere una modifica del codice.

**La versione v 2.02 dell'SDK chiamante è stata integrata nell'applicazione. Azure Communication releases v 2.05.**

Potrebbe essere necessario eseguire l'aggiornamento alla versione v 2.05 dell'SDK chiamante entro 12 mesi dal rilascio di v 2.05. Questa operazione deve essere una semplice sostituzione dell'artefatto senza richiedere una modifica del codice perché v 2.05 è nella versione principale V2 e non ha modifiche di rilievo.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere le panoramiche sull'SDK seguenti:

- [Panoramica dell'SDK chiamante](../concepts/voice-video-calling/calling-sdk-features.md)
- [Panoramica di Chat SDK](../concepts/chat/sdk-features.md)
- [Panoramica di SMS SDK](../concepts/telephony-sms/sdk-features.md)

Per iniziare a usare i servizi di comunicazione di Azure:

- [Creare risorse di comunicazione di Azure](../quickstarts/create-communication-resource.md)
- Generare [token di accesso utente](../quickstarts/access-tokens.md)
