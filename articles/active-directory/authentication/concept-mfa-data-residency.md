---
title: Azure AD la residenza dei dati di autenticazione a più fattori
description: Informazioni sui dati personali e aziendali Azure AD archivi di autenticazione a più fattori per l'utente e gli utenti e i dati che rimangono all'interno del paese/area geografica di origine.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7381ab62eb39c555c6b4eb34f150fc71bea1f10f
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561465"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Residenza dei dati e dati dei clienti per l'autenticazione a più fattori Azure AD

Azure Active Directory (Azure AD) archivia i dati dei clienti in una posizione geografica in base all'indirizzo fornito da un'organizzazione durante la sottoscrizione a un servizio online Microsoft, ad esempio Microsoft 365 o Azure. Per informazioni sulla posizione in cui vengono archiviati i dati dei clienti, vedere [dove si trovano i dati?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) in Microsoft Trust Center.

Autenticazione a più fattori Azure AD basata sul cloud e processo server di autenticazione a più fattori di Azure e archiviazione dei dati personali e dei dati aziendali. Questo articolo descrive quali dati vengono archiviati e dove.

Il servizio di autenticazione a più fattori Azure AD dispone di data center in Stati Uniti, Europa e Asia Pacifico. Le attività seguenti provengono dai data center regionali, tranne nei casi indicati:

* Le chiamate telefoniche di autenticazione a più fattori provengono da Stati Uniti datacenter e vengono instradate da provider globali.
* Le richieste di autenticazione utente per utilizzo generico di altre aree sono attualmente elaborate in base alla posizione dell'utente.
* Le notifiche push che usano l'app Microsoft Authenticator vengono attualmente elaborate nei data center regionali in base alla posizione dell'utente. I servizi per dispositivi specifici del fornitore, ad esempio Apple Push Notification Service, potrebbero essere esterni alla posizione dell'utente.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Dati personali archiviati da Azure AD autenticazione a più fattori

I dati personali sono informazioni a livello di utente associate a una persona specifica. Gli archivi dati seguenti contengono informazioni personali:

* Utenti bloccati
* Utenti ignorati
* Richieste di modifica del token del dispositivo Microsoft Authenticator
* Report attività di autenticazione a più fattori
* Attivazioni di Microsoft Authenticator

Queste informazioni vengono conservate per 90 giorni.

Azure AD autenticazione a più fattori non registra i dati personali, ad esempio nomi utente, numeri di telefono o indirizzi IP. Tuttavia, *UserObjectId* identifica i tentativi di autenticazione per gli utenti. I dati di log vengono archiviati per 30 giorni.

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Dati archiviati da Azure AD autenticazione a più fattori

Per i cloud pubblici di Azure, escluso l'autenticazione Azure AD B2C, l'estensione NPS e l'adapter Windows Server 2016 o 2019 Active Directory Federation Services (AD FS), vengono archiviati i dati personali seguenti:

| Tipo di evento                           | Tipo di archivio dati |
|--------------------------------------|-----------------|
| Token OATH                           | Log di autenticazione a più fattori     |
| SMS unidirezionale                          | Log di autenticazione a più fattori     |
| Chiamata vocale                           | Log di autenticazione a più fattori<br/>Archivio dati report attività di autenticazione a più fattori<br/>Utenti bloccati (se è stata segnalata una frode) |
| Notifica di Microsoft Authenticator | Log di autenticazione a più fattori<br/>Archivio dati report attività di autenticazione a più fattori<br/>Utenti bloccati (se è stata segnalata una frode)<br/>Richieste di modifica quando il token del dispositivo Microsoft Authenticator viene modificato |

Per Microsoft Azure per enti pubblici, Microsoft Azure Germania, Microsoft Azure gestite da 21Vianet, Azure AD B2C l'autenticazione, l'estensione NPS e l'adattatore AD FS Windows Server 2016 o 2019, vengono archiviati i dati personali seguenti:

| Tipo di evento                           | Tipo di archivio dati |
|--------------------------------------|-----------------|
| Token OATH                           | Log di autenticazione a più fattori<br/>Archivio dati report attività di autenticazione a più fattori |
| SMS unidirezionale                          | Log di autenticazione a più fattori<br/>Archivio dati report attività di autenticazione a più fattori |
| Chiamata vocale                           | Log di autenticazione a più fattori<br/>Archivio dati report attività di autenticazione a più fattori<br/>Utenti bloccati (se è stata segnalata una frode) |
| Notifica di Microsoft Authenticator | Log di autenticazione a più fattori<br/>Archivio dati report attività di autenticazione a più fattori<br/>Utenti bloccati (se è stata segnalata una frode)<br/>Richieste di modifica quando il token del dispositivo Microsoft Authenticator viene modificato |

### <a name="data-stored-by-azure-multifactor-authentication-server"></a>Dati archiviati dal server di autenticazione a più fattori di Azure

Se si usa il server di autenticazione a più fattori di Azure, vengono archiviati i dati personali seguenti.

> [!IMPORTANT]
> A partire dal 1 ° luglio 2019, Microsoft non offre più il server di autenticazione a più fattori per le nuove distribuzioni. I nuovi clienti che vogliono richiedere l'autenticazione a più fattori dagli utenti devono usare l'autenticazione a più fattori basata sul Cloud Azure AD. I clienti esistenti che hanno attivato il server di autenticazione a più fattori prima del 1 ° luglio 2019 possono scaricare la versione più recente e gli aggiornamenti e generare le credenziali di attivazione come di consueto.

| Tipo di evento                           | Tipo di archivio dati |
|--------------------------------------|-----------------|
| Token OATH                           | Log di autenticazione a più fattori<br />Archivio dati report attività di autenticazione a più fattori |
| SMS unidirezionale                          | Log di autenticazione a più fattori<br />Archivio dati report attività di autenticazione a più fattori |
| Chiamata vocale                           | Log di autenticazione a più fattori<br />Archivio dati report attività di autenticazione a più fattori<br />Utenti bloccati (se è stata segnalata una frode) |
| Notifica di Microsoft Authenticator | Log di autenticazione a più fattori<br />Archivio dati report attività di autenticazione a più fattori<br />Utenti bloccati (se è stata segnalata una frode)<br />Richieste di modifica quando viene modificato il token del dispositivo di Microsoft Authenticator |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Dati aziendali archiviati da Azure AD autenticazione a più fattori

I dati dell'organizzazione sono informazioni a livello di tenant che possono esporre la configurazione o l'installazione dell'ambiente. Le impostazioni del tenant dalle seguenti portale di Azure pagine di autenticazione a più fattori potrebbero archiviare dati aziendali, ad esempio soglie di blocco o informazioni sull'ID chiamante per le richieste di autenticazione del telefono in arrivo:

* Blocco dell'account
* Avviso di illecito
* Notifiche
* Impostazioni telefonata

Per il server di autenticazione a più fattori di Azure, le pagine di portale di Azure seguenti potrebbero contenere dati aziendali:

* Impostazioni del server
* Bypass monouso
* Regole di memorizzazione nella cache
* Stato del server di autenticazione a più fattori

## <a name="multifactor-authentication-logs-location"></a>Percorso dei log di autenticazione a più fattori

La tabella seguente illustra il percorso dei log dei servizi per i cloud pubblici.

| Cloud pubblico| Log di accesso | Report attività di autenticazione a più fattori        | Log del servizio di autenticazione a più fattori       |
|-------------|--------------|----------------------------------------|------------------------|
| Stati Uniti          | Stati Uniti           | Stati Uniti                                     | Stati Uniti                     |
| Europa      | Europa       | Stati Uniti                                     | Europa <sup>2</sup>    |
| Australia   | Australia    | Stati Uniti<sup>1</sup>                         | Australia <sup>2</sup> |

<sup>1</sup> I log del codice del GIURAmento sono archiviati in Australia.

<sup>2</sup> I log del servizio di autenticazione a più fattori vengono archiviati nel Stati Uniti.

La tabella seguente illustra il percorso dei log dei servizi per i cloud sovrani.

| Cloud sovrano                      | Log di accesso                         | Report attività di autenticazione a più fattori (include dati personali)| Log del servizio di autenticazione a più fattori |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Germania              | Germania                              | Stati Uniti                            | Stati Uniti               |
| Azure Cina 21Vianet                 | Cina                                | Stati Uniti                            | Stati Uniti               |
| Cloud di Microsoft Government           | Stati Uniti                                   | Stati Uniti                            | Stati Uniti               |

I report delle attività di autenticazione a più fattori contengono dati personali, ad esempio il nome dell'entità utente (UPN) e il numero di telefono completo.

Per il funzionamento del servizio vengono usati i log del servizio di autenticazione a più fattori.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle informazioni sugli utenti raccolte dall'autenticazione a più fattori Azure AD basata sul cloud e dal server di autenticazione a più fattori di Azure, vedere [Azure ad raccolta di dati utente per l'autenticazione](howto-mfa-reporting-datacollection.md)a più fattori.
