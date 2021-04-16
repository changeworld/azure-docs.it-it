---
title: Errore imprevisto durante la richiesta di consenso per un'applicazione | Microsoft Docs
description: Vengono illustrati gli errori che possono verificarsi quando si tenta di ottenere il consenso per un'applicazione, nonché suggerite le operazioni da eseguire per risolverli
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad216d0062928fc16b0f2226daabb6258d09063c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378126"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Errore imprevisto durante la richiesta di consenso per un'applicazione

Questo articolo descrive gli errori che possono verificarsi quando si tenta di ottenere il consenso per un'applicazione. Per risolvere i problemi relativi a richieste di consenso impreviste che non contengono messaggi di errore, vedere [Scenari di autenticazione per Azure AD](../develop/authentication-vs-authorization.md).

Molte applicazioni integrate in Azure Active Directory richiedono autorizzazioni per accedere ad altre risorse e garantire il funzionamento. Quando anche queste risorse sono integrate in Azure Active Directory, le autorizzazioni per il relativo accesso vengono spesso richieste tramite il framework di consenso comune. Questo comporta la visualizzazione di una richiesta di consenso, che in genere viene inviata al primo utilizzo di un'applicazione ma a volte anche successivamente.

Perché un utente possa concedere le autorizzazioni richieste da un'applicazione, è necessario che siano soddisfatte determinate condizioni. In caso contrario, possono verificarsi gli errori seguenti.

## <a name="requesting-not-authorized-permissions-error"></a>Errore di richiesta di autorizzazioni non consentite
* **AADSTS90093:** &lt;NomeVisualizzatoAppClient&gt; richiede una o più autorizzazioni che l'utente non è autorizzato a concedere. Contattare un amministratore che possa concedere il consenso per l'applicazione per conto dell'utente.
* **AADSTS90094:** &lt;NomeVisualizzatoAppClient&gt; richiede l'autorizzazione per accedere alle risorse dell'organizzazione che solo un amministratore può concedere. Prima di usarla, è necessario chiedere a un amministratore di concedere l'autorizzazione a quest'app.

Questo errore si verifica quando un utente che non è un amministratore globale tenta di usare un'applicazione che richiede autorizzazioni che solo un amministratore può concedere. Il problema può essere risolto chiedendo a un amministratore di concedere l'accesso all'applicazione per conto dell'organizzazione.

Questo errore può verificarsi anche quando a un utente viene impedito di concedere il consenso a un'applicazione perché Microsoft ha rilevato che la richiesta di autorizzazioni è rischiosa. In questo caso, verrà registrato anche un evento di controllo con categoria "ApplicationManagement", tipo di attività "Consenso all'applicazione" e Motivo stato "Applicazione rischiosa rilevata".

Un altro scenario in cui questo errore può verificarsi è quando l'assegnazione utente è necessaria per l'applicazione, ma non è stato fornito il consenso dell'amministratore. In questo caso, l'amministratore deve prima fornire il consenso dell'amministratore.   

## <a name="policy-prevents-granting-permissions-error"></a>Errore dovuto a criteri che impediscono di concedere le autorizzazioni
* **AADSTS90093:** un amministratore di &lt;NomeVisualizzatoTenant&gt; ha configurato criteri che impediscono di concedere a &lt;nome dell'app&gt; le autorizzazioni che richiede. Contattare un amministratore di &lt;NomeVisualizzatoTenant&gt; che possa concedere le autorizzazioni richieste dall'app per conto dell'utente.

Questo errore si verifica quando un amministratore globale disattiva la possibilità per gli utenti di dare il consenso alle applicazioni, quindi un utente non amministratore tenta di usare un'applicazione che richiede il consenso. Il problema può essere risolto chiedendo a un amministratore di concedere l'accesso all'applicazione per conto dell'organizzazione.

## <a name="intermittent-problem-error"></a>Errore dovuto a un problema intermittente
* **AADSTS90090:** sembra che la procedura di accesso rilevi un problema intermittente durante la registrazione delle autorizzazioni che si è tentato di concedere a &lt;NomeVisualizzazioneAppClient&gt;. Riprovare in un secondo momento.

Questo errore indica che si è verificato un problema intermittente relativo al servizio. È possibile risolverlo provando a concedere di nuovo il consenso per l'applicazione.

## <a name="resource-not-available-error"></a>Errore di risorsa non disponibile
* **AADSTS65005:** l'app &lt;NomeVisualizzatoAppClient&gt; ha richiesto autorizzazioni per accedere alla risorsa &lt;NomeVisualizzatoAppRisorsa&gt; che non è disponibile. 

Contattare lo sviluppatore dell'applicazione.

##  <a name="resource-not-available-in-tenant-error"></a>Errore di risorsa non disponibile nel tenant
* **AADSTS65005:** &lt;NomeVisualizzatoAppClient&gt; richiede l'accesso alla risorsa &lt;NomeVisualizzatoAppRisorsa&gt; che non è disponibile all'interno dell'organizzazione &lt;NomeVisualizzatoTenant&gt;. 

Assicurarsi che la risorsa sia disponibile o contattare un amministratore di &lt;NomeVisualizzatoTenant&gt;.

## <a name="permissions-mismatch-error"></a>Errore di mancata corrispondenza delle autorizzazioni
* **AADSTS65005:** l'app ha richiesto il consenso per accedere alla risorsa &lt;NomeVisualizzatoAppRisorsa&gt;. Questa richiesta ha avuto esito negativo perché non corrisponde al modo in cui l'app è stata preconfigurata durante la registrazione. Contattare il fornitore dell'app.**

Tutti questi errori si verificano quando l'applicazione per la quale un utente sta provando a concedere il consenso richiede autorizzazioni per accedere a un'applicazione della risorsa non trovata nella directory dell'organizzazione (tenant). Questa situazione può verificarsi per diversi motivi:

-   Lo sviluppatore dell'applicazione client ha configurato l'applicazione in modo errato per cui questa richiede l'accesso a una risorsa non valida. In questo caso, lo sviluppatore dell'applicazione deve aggiornare la configurazione dell'applicazione client per risolvere il problema.

-   Un'entità servizio che rappresenta l'applicazione della risorsa di destinazione non esiste nell'organizzazione o esisteva in precedenza ma è stata rimossa. Per risolvere questo problema, è necessario eseguire il provisioning di un'entità servizio per l'applicazione della risorsa all'interno dell'organizzazione, in modo che l'applicazione client possa richiedere le autorizzazioni per tale entità. È possibile eseguire il provisioning dell'entità servizio in diversi modi, in funzione del tipo di applicazione, incluso:

    -   Acquisendo una sottoscrizione per l'applicazione della risorsa (applicazioni pubblicate da Microsoft)

    -   Concedendo il consenso per l'applicazione della risorsa

    -   Concedendo le autorizzazioni all'applicazione tramite il portale di Azure

    -   Aggiungendo l'applicazione dalla raccolta di applicazioni di Azure AD

## <a name="risky-app-error-and-warning"></a>Errore e avviso dell'app rischioso
* **AADSTS900941:** È necessario il consenso dell'amministratore. L'app è considerata rischiosa. (AdminConsentRequiredDueToRiskyApp)
* Questa app può essere rischiosa. Se si considera attendibile questa app, chiedere all'amministratore di concedere l'accesso.
* **AADSTS900981:** È stata ricevuta una richiesta di consenso dell'amministratore per un'app rischiosa. (AdminConsentRequestRiskyAppWarning)
* Questa app può essere rischiosa. Continuare solo se si considera attendibile questa app.

Entrambi questi messaggi verranno visualizzati quando Microsoft ha stabilito che la richiesta di consenso potrebbe essere rischiosa. Tra gli altri fattori, questo può verificarsi se un editore verificato [non](../develop/publisher-verification-overview.md) è stato aggiunto alla registrazione dell'app. Il primo codice di errore e il primo messaggio verranno visualizzati agli utenti finali quando il flusso di [lavoro di](configure-admin-consent-workflow.md) consenso amministratore è disabilitato. Il secondo codice e il secondo messaggio verranno visualizzati agli utenti finali quando il flusso di lavoro di consenso dell'amministratore è abilitato e agli amministratori. 

Gli utenti finali non potranno concedere il consenso alle app rilevate come rischiose. Gli amministratori possono, ma devono valutare l'app con molta attenzione e procedere con cautela. Se l'app sembra sospetta dopo un'ulteriore revisione, può essere segnalata a Microsoft dalla schermata di consenso. 

## <a name="next-steps"></a>Passaggi successivi 

[App, autorizzazioni e consenso in Azure Active Directory](../develop/quickstart-register-app.md)<br>

[Ambiti, autorizzazioni e consenso in Azure Active Directory (endpoint v2.0)](../develop/v2-permissions-and-consent.md)