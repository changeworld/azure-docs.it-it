---
title: Opzioni di supporto e guida per gli sviluppatori di app Azure AD
description: Scoprire come ottenere assistenza e supporto per domande e problemi correlati allo sviluppo durante la creazione di applicazioni che si integrano con le identità di Microsoft (Azure Active Directory e account Microsoft)
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 43990952f6cbe90c729ac2df421c682fe8d42b1b
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517940"
---
# <a name="support-and-help-options-for-developers"></a>Opzioni di supporto tecnico e assistenza per gli sviluppatori

Se si stia iniziando l'integrazione con Azure Active Directory (Azure AD), con le identità di Microsoft o con l'API Microsoft Graph o se si sta implementando una nuova funzionalità nell'applicazione, in alcuni momenti è necessario l'aiuto della community o la comprensione approfondita delle opzioni di supporto disponibili per gli sviluppatori. Ecco alcuni suggerimenti in cui è possibile ottenere assistenza durante lo sviluppo di soluzioni della piattaforma Microsoft Identity.

## <a name="create-an-azure-support-request"></a>Creare una richiesta di supporto in Azure

<div class='icon is-large'>
    <img alt='Azure support' src='https://docs.microsoft.com/media/logos/logo_azure.svg'>
</div>

Esplorare la gamma di [opzioni di supporto tecnico di Azure e scegliere il piano](https://azure.microsoft.com/support/plans) più adatto, a seconda che l'utente sia uno sviluppatore alle prime armi con la tecnologia cloud o il titolare di una grande impresa che distribuisce applicazioni aziendali strategiche. I clienti di Azure possono creare e gestire le richieste di supporto nel portale di Azure.

- Se si ha già un piano di supporto di Azure, [aprire una richiesta di supporto qui](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- Se l'utente non è un cliente Azure, è anche possibile aprire una richiesta di supporto con Microsoft tramite [il supporto commerciale](https://support.serviceshub.microsoft.com/supportforbusiness).

## <a name="post-a-question-to-microsoft-qa"></a>Inviare una domanda a Microsoft Q&A
<div class='icon is-large'>
    <img alt='Microsoft Q&A' src='./media/common/question-mark-icon.png'>
</div>             

Ottieni risposte alle tue domande sullo sviluppo di app Identity direttamente da tecnici Microsoft, MVP (Most Valuable Professional) e membri della nostra community di esperti.

[Microsoft Q&A](/answers/products/) è la fonte consigliata di Azure per il supporto della community.

Se non si riesce a trovare una risposta al problema cercando Microsoft Q&A, inviare una nuova domanda. Quando si pone una [domanda di qualità elevata](https://docs.microsoft.com/answers/articles/24951/how-to-write-a-quality-question.html), usare uno dei tag seguenti:

| Componente/area| Tag  |
|------------|---------------------------|
| Azure Active Directory Authentication Library (ADAL)                              | [adal](https://docs.microsoft.com/answers/topics/azure-ad-adal-deprecation.html)                |
| Microsoft Authentication Library (MSAL)                                     | [MSAL](https://docs.microsoft.com/answers/topics/azure-ad-msal.html)                            |
| Aprire il middleware OWIN (Web Interface for .NET)                               | [[Azure-Active-Directory]](https://docs.microsoft.com/answers/topics/azure-active-directory.html) |
| [Azure AD le identità B2B/esterne](../external-identities/what-is-b2b.md) | [[azure-ad-b2b]](https://docs.microsoft.com/answers/topics/azure-ad-b2b.html)                     |
| [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://docs.microsoft.com/answers/topics/azure-ad-b2c.html)                     |
| [API Microsoft Graph](https://developer.microsoft.com/graph/)               | [[Azure-AD-Graph]](https://docs.microsoft.com/answers/topics/azure-ad-graph.html)                 |
| Tutte le altre aree di autenticazione e autorizzazione                            | [[Azure-Active-Directory]](https://docs.microsoft.com/answers/topics/azure-active-directory.html) |

## <a name="create-a-github-issue"></a>Segnalare un problema in GitHub

<div class='icon is-large'>
    <img alt='GitHub-image' src='./media/common/github.svg'>
</div>

Se è necessaria assistenza per una delle librerie di autenticazione Microsoft (MSAL), aprire un problema nel repository in GitHub.

| Libreria MSAL | URL dei problemi di GitHub|
| --- | --- |
| MSAL per Android | https://github.com/AzureAD/microsoft-authentication-library-for-android/issues |
| MSAL angolare | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues |
| MSAL per iOS e MacOS| https://github.com/AzureAD/microsoft-authentication-library-for-objc/issues |
| MSAL Java | https://github.com/AzureAD/microsoft-authentication-library-for-java/issues |
| MSAL.js | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues |
|MSAL.NET| https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues |
| MSAL Node | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues |
| MSAL Python | https://github.com/AzureAD/microsoft-authentication-library-for-python/issues |
| MSAL React | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues |

## <a name="submit-feedback-on-azure-feedback"></a>Inviare commenti e suggerimenti nel forum dei commenti di Azure

<div class='icon is-large'>
    <img alt='UserVoice' src='https://docs.microsoft.com/media/logos/logo-uservoice.svg'>
</div>

Per richiedere nuove funzionalità, pubblicare la richiesta nel forum dei commenti di Azure. Condividi le tue idee per migliorare le prestazioni della piattaforma Microsoft Identity per le applicazioni sviluppate.

| Service                       | URL del forum dei commenti di Azure |
|-------------------------------|---------------|
| Azure Active Directory | https://feedback.azure.com/forums/169401-azure-active-directory |
| Esperienze Azure Active Directory-Developer             | https://feedback.azure.com/forums/169401-azure-active-directory?category_id=164757 |
| Autenticazione Azure Active Directory             | https://feedback.azure.com/forums/169401-azure-active-directory?category_id=167256 |

## <a name="stay-informed-of-updates-and-new-releases"></a>Rimanere informati sugli aggiornamenti e le nuove versioni

<div class='icon is-large'>
    <img alt='Stay informed' src='https://docs.microsoft.com/media/common/i_blog.svg'>
</div>

- [Aggiornamenti di Azure](https://azure.microsoft.com/updates/?category=identity): informazioni sugli aggiornamenti, la roadmap e gli annunci importanti del prodotto.

- [Novità di docs](https://docs.microsoft.com/azure/active-directory/develop/whats-new-docs): informazioni sulle novità della documentazione della piattaforma Microsoft Identity.

- [Blog sull'identità Azure Active Directory](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/bg-p/Identity): informazioni su Azure ad.

- [Community tecnica](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/bg-p/Identity/): Condividi le tue esperienze, coinvolgi e impara dagli esperti.


