---
title: Esperienze degli utenti finali per le applicazioni - Azure Active Directory
description: Azure Active Directory (Azure AD) fornisce diverse soluzioni personalizzabili per distribuire le applicazioni agli utenti finali all'interno dell'organizzazione.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iangithinji
ms.reviewer: arvindh
ms.openlocfilehash: c555899a65a5e8cf4c8fcc6214e4dcbda3931f08
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374234"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Esperienze per gli utenti finali per le applicazioni in Azure Active Directory

Azure Active Directory (Azure AD) fornisce diverse soluzioni personalizzabili per distribuire le applicazioni agli utenti finali all'interno dell'organizzazione:

* Azure AD App personali
* Microsoft 365 di avvio delle applicazioni
* Accesso diretto alle applicazioni federate
* Collegamenti diretti per applicazioni federate, basate su password o esistenti

I metodi che è possibile scegliere per la distribuzione nell'organizzazione sono a discrezione dell'utente.

## <a name="azure-ad-my-apps"></a>Azure AD App personali

App personali in è un portale basato sul Web che consente a un utente finale con un account aziendale in Azure Active Directory di visualizzare e avviare le applicazioni a cui è stato concesso l'accesso dall'amministratore https://myapps.microsoft.com di Azure AD. Se si è un utente finale con [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), è anche possibile usare le funzionalità di gestione self-service dei gruppi tramite App personali.

Per impostazione predefinita, tutte le applicazioni sono elencate insieme in un'unica pagina. È tuttavia possibile usare le raccolte per raggruppare le applicazioni correlate e presentarle in una scheda separata, semplificando la ricerca. Ad esempio, è possibile usare le raccolte per creare raggruppamenti logici di applicazioni per ruoli processo, attività, progetti e così via specifici. Per informazioni, vedere [Creare raccolte nel portale App personali .](access-panel-collections.md) 

App personali è separato dal portale di Azure e non richiede agli utenti di avere una sottoscrizione di Azure o Microsoft 365 sottoscrizione.

Per altre informazioni su Azure AD App personali, vedere [l'introduzione a App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="microsoft-365-application-launcher"></a>Microsoft 365 di avvio delle applicazioni

Per le organizzazioni che hanno distribuito Microsoft 365, le applicazioni assegnate agli utenti tramite Azure AD verranno visualizzate anche nel portale di Office 365 all'indirizzo [https://portal.office.com/myapps](https://portal.office.com/myapps) . In questo modo gli utenti di un'organizzazione possono avviare le app in modo semplice e pratico senza dover usare un secondo portale ed è la soluzione consigliata per l'avvio di app per le organizzazioni che usano Microsoft 365.

Per ulteriori informazioni sull'avvio di applicazioni di Office 365, vedere [Visualizzazione dell'applicazione nell’applicazione di avvio di Office 365](/previous-versions/office/office-365-api/).

## <a name="direct-sign-on-to-federated-apps"></a>Accesso diretto alle applicazioni federate

La maggior parte delle applicazioni federate che supportano OpenID Connect, WS-Federation o SAML 2.0 supportano anche la possibilità per gli utenti di avviare l'applicazione e quindi ottenere l’accesso tramite Azure AD con il reindirizzamento automatico oppure facendo clic su un collegamento per accedere. Questo è noto come accesso avviato dal provider di servizi e la maggior parte delle applicazioni federate nella raccolta di applicazioni di Azure AD lo supporta. Per informazioni dettagliate, vedere la documentazione collegata dalla configurazione guidata dell'accesso Single Sign-On dell'app nel portale di Azure.

## <a name="direct-sign-on-links"></a>Collegamenti diretti Single Sign-On

Azure AD supporta anche collegamenti diretti Single Sign-On alle singole applicazioni che supportano Single Sign-On basato su password, Single Sign-On collegato e qualunque forma di Single Sign-On federato.

Questi collegamenti sono URL appositamente creati che inviano un utente tramite il processo di accesso Azure AD per un'applicazione specifica senza richiedere all'utente di avviarlo da Azure AD App personali o Microsoft 365. Questi **URL di accesso utente** sono disponibili nelle proprietà delle applicazioni aziendali disponibili. Nel portale di Azure selezionare **Azure Active Directory** > **Applicazioni aziendali**. Selezionare l'applicazione e quindi selezionare **Proprietà**.

![Esempio dell'URL di accesso utente nelle proprietà di Twitter](media/end-user-experiences/direct-sign-on-link.png)

Questi collegamenti possono essere copiati e incollati ovunque per fornire un collegamento di accesso all'applicazione selezionata. Questo potrebbe essere all’interno di un messaggio di posta elettronica o in qualsiasi portale personalizzato basato sul Web configurato per l'accesso alle applicazioni dell’utente. Di seguito è riportato un esempio di URL Single Sign-On diretto di Azure AD per Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Analogamente agli URL specifici dell'organizzazione per App personali, è possibile personalizzare ulteriormente questo URL aggiungendo uno dei domini attivi *o* verificati per la directory dopo il myapps.microsoft.com dominio. Questo assicura che eventuali personalizzazioni dell’organizzazione vengano caricate immediatamente nella pagina di accesso senza che l'utente debba immettere prima l'ID utente:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Quando un utente autorizzato fa clic su uno di questi collegamenti specifici dell'applicazione, viene visualizzata prima la pagina di accesso dell'organizzazione (presupponendo che non sia già stato eseguito l'accesso) e dopo l'accesso viene reindirizzato all'app senza arrestarsi App personali prima. Se l'utente non dispone dei prerequisiti per l'accesso all'applicazione, ad esempio l'estensione browser di accesso singolo basato su password, il collegamento richiederà all'utente di installare l'estensione mancante. L'URL del collegamento rimane costante se la configurazione Single Sign-On per l'applicazione viene modificata.

Questi collegamenti usano gli stessi meccanismi di controllo di accesso di App personali e Microsoft 365 e solo gli utenti o i gruppi assegnati all'applicazione nel portale di Azure saranno in grado di eseguire correttamente l'autenticazione. Tuttavia, qualsiasi utente non autorizzato visualizza un messaggio che spiega che non è stato concesso l'accesso e riceve un collegamento per caricare App personali per visualizzare le applicazioni disponibili per le quali ha accesso.

## <a name="next-steps"></a>Passaggi successivi

* [Serie di guide di avvio rapido sulla gestione delle applicazioni](view-applications-portal.md)
* [Che cos'è l'accesso Single Sign-On?](what-is-single-sign-on.md)
* [Guida introduttiva all'integrazione di Azure Active Directory con le applicazioni](plan-an-application-integration.md)