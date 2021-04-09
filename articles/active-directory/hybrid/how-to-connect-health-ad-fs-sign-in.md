---
title: AD FS accessi in Azure AD con Connect Health | Microsoft Docs
description: Questo documento descrive come integrare AD FS accessi con il report degli accessi Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/16/2021
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74769feba1d717a2f1a72d311f85bdfbeac7b7db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574791"
---
# <a name="ad-fs-sign-ins-in-azure-ad-with-connect-health---preview"></a>AD FS accessi in Azure AD con Connect Health-anteprima

È ora possibile integrare AD FS accessi nel report degli accessi Azure Active Directory usando Connect Health. Il report report degli accessi [Azure ad](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-all-sign-ins#:~:text=Interactive%20user%20sign-ins%20are%20sign-ins%20where%20a%20user,to%20Azure%20AD%20or%20to%20a%20helper%20app.) include informazioni sul momento in cui gli utenti, le applicazioni e le risorse gestite accedono a Azure ad e accedono alle risorse. 

Connect Health per AD FS Agent mette in correlazione più ID evento da AD FS, a seconda della versione del server, per fornire informazioni sulla richiesta e i dettagli dell'errore se la richiesta non riesce. Queste informazioni sono correlate allo schema del report degli accessi Azure AD e vengono visualizzate nell'Azure AD Sign-In report UX. Insieme al report, è disponibile un nuovo flusso di Log Analytics con i dati AD FS e un nuovo modello di cartella di lavoro di monitoraggio di Azure. Il modello può essere usato e modificato per un'analisi approfondita per scenari quali i blocchi di account AD FS, i tentativi di accesso con password errata e i picchi di tentativi di accesso imprevisti.

## <a name="prerequisites"></a>Prerequisiti
* Azure AD Connect Health per AD FS installato e aggiornato alla versione più recente.
* Ruolo amministratore globale o lettore report per visualizzare gli accessi Azure AD

## <a name="what-data-is-displayed-in-the-report"></a>Quali dati vengono visualizzati nel report?
I dati disponibili riflettono gli stessi dati disponibili per Azure AD accessi. Cinque schede con informazioni saranno disponibili in base al tipo di accesso, Azure AD o AD FS. Connetti integrità mette in correlazione gli eventi da AD FS, a seconda della versione del server, e li associa allo schema di AD FS. 



#### <a name="user-sign-ins"></a>Accessi utente 
Ogni scheda nel pannello degli accessi Mostra i valori predefiniti seguenti:
* Data di accesso
* ID richiesta
* Nome utente o ID utente
* Stato dell'accesso
* Indirizzo IP del dispositivo usato per l'accesso
* Identificatore Sign-In

#### <a name="authentication-method-information"></a>Informazioni sul metodo di autenticazione
Nella scheda autenticazione è possibile visualizzare i valori seguenti. Il metodo di autenticazione viene tratto dai log di controllo AD FS.

|Metodo di autenticazione|Descrizione|
|-----|-----|
|Moduli|Autenticazione con nome utente/password|
|Windows|Autenticazione integrata di Windows|
|Certificato|Autenticazione con certificati SmartCard/VirtualSmart|
|WindowsHelloForBusiness|Questo campo è per l'autenticazione con Windows Hello for business. (Autenticazione Microsoft Passport)|
|Dispositivo | Visualizzato se viene selezionata l'autenticazione del dispositivo come autenticazione "primaria" da Intranet/Extranet e viene eseguita l'autenticazione del dispositivo.  In questo scenario non è presente alcuna autenticazione utente separata.| 
|Federato|Non è stato possibile eseguire l'autenticazione AD FS ma è stato inviato a un provider di identità di terze parti|
|SSO |Se è stato usato un token Single Sign-on, verrà visualizzato questo campo. Se l'accesso SSO ha un multi-factor authentication, verrà visualizzato come a più fattori|
|A più fattori|Se un token di Single Sign-On dispone di un autenticazione a più fattori e usato per l'autenticazione, questo campo verrà visualizzato come a più fattori|
|Azure MFA|Azure multi-factor authentication è selezionato come provider di autenticazione aggiuntivo in AD FS ed è stato usato per l'autenticazione|
|Adfsexternalauthenticationprovider,|Questo campo è se un provider di autenticazione di terze parti è stato registrato e usato per l'autenticazione|


#### <a name="ad-fs-additional-details"></a>AD FS dettagli aggiuntivi
Per AD FS gli accessi sono disponibili i dettagli seguenti:
* Nome server
* Catena IP
* Protocollo

### <a name="enabling-log-analytics-and-azure-monitor"></a>Abilitazione di Log Analytics e monitoraggio di Azure
Log Analytics possono essere abilitati per gli accessi AD FS e possono essere usati con qualsiasi altro componente Log Analytics integrato, ad esempio Sentinel.

> [!NOTE] 
> AD FS accessi possono aumentare significativamente Log Analytics costo, a seconda della quantità di accessi da AD FS nell'organizzazione. Per abilitare e disabilitare Log Analytics, selezionare la casella di controllo per il flusso.

Per abilitare Log Analytics per la funzionalità, passare al pannello Log Analytics e selezionare flusso "ADFSSignIns". Questa selezione consentirà la propagazione degli accessi AD FS in Log Analytics.

Per accedere al modello di cartella di lavoro di monitoraggio di Azure aggiornato, passare a "modelli di monitoraggio di Azure" e selezionare la cartella di lavoro "accessi".
Per altre informazioni sulle cartelle di lavoro, vedere [cartelle di lavoro di monitoraggio di Azure](https://aka.ms/adfssigninspreview).




### <a name="frequently-asked-questions"></a>Domande frequenti
***Quali sono i tipi di accessi che è possibile visualizzare?***
Il report di accesso supporta gli accessi tramite i protocolli O-auth, WS-Fed, SAML e WS-Trust. 

***Come vengono visualizzati diversi tipi di accessi nel report di accesso?***
Se viene eseguito un accesso SSO semplice, sarà presente una riga per l'accesso con un ID di correlazione.
Se viene eseguita un'autenticazione a fattore singolo, due righe verranno popolate con lo stesso ID di correlazione, ma con due metodi di autenticazione diversi, ad esempio Forms, SSO.
Nei casi di autenticazione a più fattori, saranno presenti tre righe con un ID correlazione condiviso e tre metodi di autenticazione corrispondenti (ad esempio, Forms, AzureMFA, Multifactor). In questo esempio, l'autenticazione a più fattori in questo caso indica che l'accesso SSO ha un multi-factor authentication.

***Quali sono gli errori che è possibile visualizzare nel report?***
Per un elenco completo degli errori AD FS correlati compilati nel report Sign-In e nelle descrizioni, vedere il [riferimento al codice di errore ad FS della Guida](https://adfshelp.microsoft.com/References/ConnectHealthErrorCodeReference)

***Viene visualizzato "00000000-0000-0000-0000-000000000000" nella sezione "User" di un accesso. Cosa significa?***
Se l'accesso ha avuto esito negativo e il tentativo UPN non corrisponde a un UPN esistente, i campi "User", "username" e "User ID" saranno "00000000-0000-0000-0000-000000000000" e l'identificatore di accesso verrà popolato con il valore del tentativo immesso dall'utente. In questi casi, l'utente che tenta di eseguire l'accesso non esiste.

***Come è possibile correlare gli eventi locali al report degli accessi Azure AD?***
L'agente di Azure AD Connect Health per AD FS mette in correlazione gli ID evento da AD FS a seconda della versione del server. Gli eventi saranno disponibili nel registro di protezione dei server AD FS. 

***Perché viene visualizzato NotSet o NONAPPLICABILE nel nome/ID applicazione per alcuni AD FS accessi?***
Il report Sign-In AD FS visualizzerà gli ID OAuth nel campo ID applicazione per gli accessi OAuth. Negli scenari di accesso WS-Trust WS-Fed, l'ID applicazione sarà NotSet o NONAPPLICABILE e gli ID risorsa e gli identificatori della relying party saranno presenti nel campo ID risorsa.

***Sono presenti altri problemi noti del report in anteprima?***
Il report presenta un problema noto in cui il campo "requisito di autenticazione" nella scheda "informazioni di base" verrà popolato come valore di autenticazione a fattore singolo per AD FS accessi indipendentemente dall'accesso. Inoltre, nella scheda Dettagli autenticazione viene visualizzato "primario o secondario" nel campo requisito con una correzione in corso per distinguere i tipi di autenticazione primari o secondari.


## <a name="related-links"></a>Collegamenti correlati
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Installazione dell'agente di Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Report IP rischioso](how-to-connect-health-adfs-risky-ip.md)





