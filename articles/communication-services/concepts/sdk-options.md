---
title: Librerie client e API REST per i servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Altre informazioni sugli SDK di servizi di comunicazione Azure e sulle API REST.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: effd7658bbfe7359e1f99f9452857824c2c45c2f
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107891"
---
# <a name="client-libraries-and-rest-apis"></a>Librerie client e API REST

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Le funzionalità dei servizi di comunicazione di Azure sono organizzate concettualmente in sei aree. Alcune aree hanno SDK completamente open source. L'SDK chiamante usa interfacce di rete proprietarie ed è attualmente closed-source e la libreria di chat include una dipendenza di origine chiusa. Gli esempi e i dettagli tecnici aggiuntivi per gli SDK sono pubblicati nel [repository GitHub dei servizi di comunicazione di Azure](https://github.com/Azure/communication).

## <a name="client-libraries"></a>Librerie client

| Assembly               | Protocolli             |Apri rispetto a origine chiusa| Spazi dei nomi                          | Funzionalità                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Apri            | Azure. ResourceManager. Communication | Provisioning e gestione delle risorse di servizi di comunicazione             |
| Comuni                 | REST | Apri               | Azure. Communication. Common          | Fornisce tipi di base per altri SDK |
| Identità         | REST | Apri               | Azure. Communication. Identity  | Gestire gli utenti, i token di accesso |
| Numeri di telefono         | REST | Apri               | Azure. Communication. PhoneNumbers  | Gestione dei numeri di telefono |
| Chat                   | REST con segnalazione proprietaria | Apri con pacchetto di segnalazione origine chiuso    | Azure. Communication. chat            | Aggiungere chat basate su testo in tempo reale alle applicazioni  |
| SMS                    | REST | Apri              | Azure. Communication. SMS             | Inviare e ricevere messaggi SMS |
| Chiamata                | Trasporto proprietario | Chiusa |Azure. Communication. Calling         | Sfrutta le funzionalità di comunicazione, video, condivisione dello schermo e altre funzionalità di comunicazione dati in tempo reale          |

Si noti che gli SDK Azure Resource Manager, Identity e SMS sono incentrati sull'integrazione del servizio e in molti casi si verificano problemi di sicurezza se si integrano queste funzioni nelle applicazioni dell'utente finale. Gli SDK comuni e chat sono adatti per le applicazioni client e di servizio. L'SDK chiamante è progettato per le applicazioni client. Un SDK incentrato sugli scenari del servizio è in fase di sviluppo.

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
| Documentazione di riferimento     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | [docs](/objectivec/communication-services/calling/)      | [docs](/java/api/com.azure.communication.calling)            | -                              |

## <a name="rest-apis"></a>API REST

Le API di servizi di comunicazione sono documentate insieme ad altre API REST di Azure in [docs.Microsoft.com](/rest/api/azure/). In questa documentazione viene illustrato come strutturare i messaggi HTTP e vengono fornite informazioni aggiuntive per l'uso di post. Questa documentazione è disponibile anche in formato spavalderia su [GitHub](https://github.com/Azure/azure-rest-api-specs).

## <a name="additional-support-details"></a>Ulteriori dettagli sul supporto

### <a name="ios-and-android-support-details"></a>dettagli del supporto per iOS e Android

- I servizi di comunicazione iOS SDK hanno come destinazione iOS versione 13 + e Xcode 11 +.
- Android Java SDK destinazione API Android livello 21 + e Android Studio 4.0 +

### <a name="net-support-details"></a>Dettagli del supporto .NET

Fatta eccezione per chiamare, i pacchetti di servizi di comunicazione sono destinati .NET Standard 2,0, che supporta le piattaforme elencate di seguito.

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

## <a name="calling-sdk-timeouts"></a>Timeout della chiamata di SDK

I timeout seguenti si applicano ai servizi di comunicazione che chiamano gli SDK:

| Azione           | Timeout in secondi |
| -------------- | ---------- |
| Riconnessione/rimozione del partecipante | 120 |
| Aggiungere o rimuovere una nuova modalità da una chiamata (avvio/arresto di video o condivisione dello schermo) | 40 |
| Timeout dell'operazione di trasferimento chiamate | 60 |
| 1:1 timeout di definizione della chiamata | 85 |
| Timeout stabilimento chiamata gruppo | 85 |
| Timeout di definizione della chiamata PSTN | 115 |
| Alza di livello la chiamata 1:1 al timeout di una chiamata di gruppo | 115 |


## <a name="api-stability-expectations"></a>Aspettative di stabilità dell'API

> [!IMPORTANT]
> Questa sezione fornisce indicazioni sulle API REST e sugli SDK contrassegnati come **stabili**. Le API contrassegnate come versione non definitiva, anteprima o beta possono essere modificate o deprecate **senza preavviso**.

In futuro si potrebbero ritirare le versioni degli SDK di servizi di comunicazione ed è possibile che vengano introdotte modifiche di rilievo per le API REST e gli SDK rilasciati. I servizi di comunicazione di Azure seguiranno *in genere* due criteri di supporto per la disattivazione delle versioni del servizio:

- Si riceverà una notifica per almeno tre anni prima che venga richiesto di modificare il codice a causa di una modifica dell'interfaccia di servizi di comunicazione. Tutte le API REST e le API SDK documentate si riferiscono in genere a almeno tre anni prima di rimuovere le interfacce.
- Si riceverà una notifica almeno un anno prima di dover aggiornare gli assembly SDK alla versione secondaria più recente. Questi aggiornamenti richiesti non devono richiedere modifiche al codice perché si trovano nella stessa versione principale. Questa operazione è particolarmente valida per le librerie di chiamata e di chat con componenti in tempo reale che richiedono spesso aggiornamenti della sicurezza e delle prestazioni. Si consiglia vivamente di proteggere gli SDK di servizi di comunicazione aggiornati.

### <a name="api-and-sdk-decommissioning-examples"></a>Esempi di rimozione di API e SDK

**La versione V24 dell'API REST SMS è stata integrata nell'applicazione. Azure Communication releases V25.**

Si otterrà un avviso di 3 anni prima che queste API smettano di funzionare e vengano forzate a eseguire l'aggiornamento a V25. Questo aggiornamento potrebbe richiedere una modifica del codice.

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
