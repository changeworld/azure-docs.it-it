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
ms.openlocfilehash: 788a666e8ec509bbd29a8dbd503a60b3dddefd6b
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208353"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Residenza dei dati e dati dei clienti per l'autenticazione a più fattori Azure AD

I dati dei clienti vengono archiviati da Azure AD in una posizione geografica in base all'indirizzo fornito dall'organizzazione quando si sottoscrive un servizio online Microsoft, ad esempio Microsoft 365 e Azure. Per informazioni sulla posizione di archiviazione dei dati dei clienti, è possibile usare la sezione [Where is your data located?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) (Percorso di archiviazione dei dati) di Microsoft Trust Center.

Autenticazione a più fattori Azure AD basata sul cloud e Azure AD il processo del server di autenticazione a più fattori e archiviazione di una quantità di dati personali e di dati aziendali. Questo articolo descrive quali dati vengono archiviati e dove.

Il servizio di autenticazione a più fattori Azure AD dispone di data center negli Stati Uniti, in Europa e Asia Pacifico. Le attività seguenti provengono dai data center regionali, tranne nei casi indicati:

* L'autenticazione a più fattori tramite chiamate telefoniche ha origine da Data Center statunitensi e viene instradata dai provider globali.
* Le richieste di autenticazione utente per utilizzo generico di altre aree, ad esempio Europa o Australia, sono attualmente elaborate in base alla posizione dell'utente.
* Le notifiche push che usano l'app Microsoft Authenticator vengono attualmente elaborate nei data center locali in base alla posizione dell'utente.
    * I servizi specifici del fornitore di dispositivi, ad esempio le notifiche push Apple, possono essere esterni alla posizione dell'utente.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Dati personali archiviati da Azure AD autenticazione a più fattori

I dati personali sono informazioni a livello di utente associate a un utente specifico. Gli archivi dati seguenti contengono informazioni personali:

* Utenti bloccati
* Utenti ignorati
* Richieste di modifica del token del dispositivo Microsoft Authenticator
* Report attività di autenticazione a più fattori
* Attivazioni di Microsoft Authenticator

Queste informazioni vengono conservate per 90 giorni.

Azure AD autenticazione a più fattori non registra i dati personali, ad esempio nome utente, numero di telefono o indirizzo IP, ma esiste una *UserObjectId* che identifica i tentativi di autenticazione a più fattori per gli utenti. I dati di log vengono archiviati per 30 giorni.

### <a name="azure-ad-multifactor-authentication"></a>Autenticazione a più fattori Azure AD

Per i cloud pubblici di Azure, ad eccezione dell'autenticazione Azure B2C, dell'estensione NPS e della scheda AD FS di Windows Server 2016 o 2019, vengono archiviati i dati personali seguenti:

| Tipo di evento                           | Tipo di archivio dati |
|--------------------------------------|-----------------|
| Token OATH                           | Nei log di autenticazione a più fattori     |
| SMS unidirezionale                          | Nei log di autenticazione a più fattori     |
| Chiamata vocale                           | Nei log di autenticazione a più fattori<br />Archivio dati report attività di autenticazione a più fattori<br />Utenti bloccati in caso di segnalazione di frode |
| Notifica di Microsoft Authenticator | Nei log di autenticazione a più fattori<br />Archivio dati report attività di autenticazione a più fattori<br />Utenti bloccati in caso di segnalazione di frode<br />Richieste di modifica quando viene modificato il token del dispositivo di Microsoft Authenticator |

Per Microsoft Azure per enti pubblici, Microsoft Azure Germania, Microsoft Azure gestito da 21Vianet, l'autenticazione Azure B2C, l'estensione NPS e la scheda AD FS di Windows Server 2016 o 2019 vengono archiviati i dati personali seguenti:

| Tipo di evento                           | Tipo di archivio dati |
|--------------------------------------|-----------------|
| Token OATH                           | Nei log di autenticazione a più fattori<br />Archivio dati report attività di autenticazione a più fattori |
| SMS unidirezionale                          | Nei log di autenticazione a più fattori<br />Archivio dati report attività di autenticazione a più fattori |
| Chiamata vocale                           | Nei log di autenticazione a più fattori<br />Archivio dati report attività di autenticazione a più fattori<br />Utenti bloccati in caso di segnalazione di frode |
| Notifica di Microsoft Authenticator | Nei log di autenticazione a più fattori<br />Archivio dati report attività di autenticazione a più fattori<br />Utenti bloccati in caso di segnalazione di frode<br />Richieste di modifica quando viene modificato il token del dispositivo di Microsoft Authenticator |

### <a name="multifactor-authentication-server"></a>Server di autenticazione a più fattori

Se si distribuisce ed esegue Azure AD server di autenticazione a più fattori, vengono archiviati i dati personali seguenti:

> [!IMPORTANT]
> A partire dal 1 ° luglio 2019, Microsoft non fornirà più il server di autenticazione a più fattori per le nuove distribuzioni. I nuovi clienti che desiderano richiedere l'autenticazione a più fattori dagli utenti devono usare l'autenticazione a più fattori basata sul Cloud Azure AD. I clienti esistenti che hanno attivato il server di autenticazione a più fattori prima del 1 ° luglio potranno scaricare la versione più recente, gli aggiornamenti futuri e generare le credenziali di attivazione come di consueto.

| Tipo di evento                           | Tipo di archivio dati |
|--------------------------------------|-----------------|
| Token OATH                           | Nei log di autenticazione a più fattori<br />Archivio dati report attività di autenticazione a più fattori |
| SMS unidirezionale                          | Nei log di autenticazione a più fattori<br />Archivio dati report attività di autenticazione a più fattori |
| Chiamata vocale                           | Nei log di autenticazione a più fattori<br />Archivio dati report attività di autenticazione a più fattori<br />Utenti bloccati in caso di segnalazione di frode |
| Notifica di Microsoft Authenticator | Nei log di autenticazione a più fattori<br />Archivio dati report attività di autenticazione a più fattori<br />Utenti bloccati in caso di segnalazione di frode<br />Richieste di modifica quando viene modificato il token del dispositivo di Microsoft Authenticator |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Dati aziendali archiviati da Azure AD autenticazione a più fattori

I dati aziendali sono informazioni a livello di tenant che potrebbero esporre informazioni sulla configurazione o l'installazione dell'ambiente. Le impostazioni del tenant dalle seguenti portale di Azure pagine di autenticazione a più fattori possono archiviare dati aziendali, ad esempio soglie di blocco o informazioni sull'ID chiamante per le richieste di autenticazione del telefono in arrivo:

* Blocco dell'account
* Avviso di illecito
* Notifiche
* Impostazioni telefonata

Per Azure AD server di autenticazione a più fattori, le pagine portale di Azure seguenti possono contenere dati aziendali:

* Impostazioni del server
* Bypass monouso
* Regole di memorizzazione nella cache
* Stato del server di autenticazione a più fattori

## <a name="multifactor-authentication-logs-location"></a>Percorso dei log di autenticazione a più fattori

La tabella seguente illustra il percorso dei log dei servizi per i cloud pubblici.

| Cloud pubblico| Log di accesso | Report attività di autenticazione a più fattori        | Log del servizio di autenticazione a più fattori       |
|-------------|--------------|----------------------------------------|------------------------|
| US          | US           | US                                     | US                     |
| Europa      | Europa       | US                                     | Europa <sup>2</sup>    |
| Australia   | Australia    | US<sup>1</sup>                         | Australia <sup>2</sup> |

<sup>1</sup> I log del codice del GIURAmento sono archiviati in Australia

<sup>2</sup> I log del servizio di autenticazione a più fattori delle chiamate vocali vengono archiviati negli Stati Uniti

La tabella seguente illustra il percorso dei log dei servizi per i cloud sovrani.

| Cloud sovrano                      | Log di accesso                         | Report attività di autenticazione a più fattori (include dati personali)| Log del servizio di autenticazione a più fattori |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Germania              | Germania                              | US                            | US               |
| Microsoft Azure gestite da 21Vianet | Cina                                | US                            | US               |
| Cloud di Microsoft Government           | US                                   | US                            | US               |

I dati del report dell'attività di autenticazione a più fattori contengono dati personali, ad esempio il nome dell'entità utente (UPN) e il numero di telefono completo.

Per il funzionamento del servizio vengono usati i log del servizio di autenticazione a più fattori.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle informazioni sugli utenti raccolte dall'autenticazione a più fattori Azure AD basata sul cloud e Azure AD server di autenticazione a più fattori, vedere [Azure ad raccolta di dati utente di autenticazione](howto-mfa-reporting-datacollection.md)a più fattori.
