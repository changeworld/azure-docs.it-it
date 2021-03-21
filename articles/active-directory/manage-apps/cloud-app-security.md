---
title: Visibilità e controllo delle app con Microsoft Cloud App Security
description: Questa guida di avvio rapido illustra come identificare i livelli di rischio delle app, impedire violazioni e perdite in tempo reale e usare i connettori delle app per sfruttare le API del provider per la visibilità e la governance.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 682549b5b99169060437c8c91d465121002edc6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259605"
---
# <a name="cloud-app-visibility-and-control"></a>Visibilità e controllo delle app cloud

Per sfruttare tutti i vantaggi offerti dalle app e dai servizi cloud, un team IT deve trovare il giusto equilibrio tra il supporto dell'accesso e il mantenimento del controllo per proteggere i dati critici. Microsoft Cloud App Security offre ampia visibilità, controllo sul transito dei dati e funzionalità di analisi sofisticate per identificare e contrastare le minacce informatiche in tutti i servizi cloud Microsoft e di terze parti.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Individuare e gestire shadow IT nella rete

quando viene chiesto agli amministratori IT di indicare il numero di app cloud usate dai dipendenti, la risposta è in media 30 o 40, nonostante la media effettiva sia di oltre 1.000 app distinte usate dai dipendenti di un'organizzazione. Shadow IT consente di conoscere e identificare le app usate e il livello di rischio. L'80% dei dipendenti usa app non approvate che non sono state esaminate e che potrebbero non essere conformi ai criteri di sicurezza e conformità. Poiché, inoltre, i dipendenti possono accedere alle risorse e alle app anche dall'esterno della rete aziendale, non è più sufficiente avere criteri e regole per i firewall.

È possibile usare Microsoft Cloud App Discovery (una funzionalità di Azure Active Directory Premium P1) per individuare le app in uso, esplorare il rischio a esse associato, configurare i criteri per identificare nuove app rischiose e annullare l'approvazione di queste app per bloccarle in modo nativo con il dispositivo proxy o firewall.

- Individuare e identificare gli utenti non autorizzati
- Valutare e analizzare
- Gestire le proprie app
- Creazione avanzata di report di Shadow IT Discovery
- Controllare le app approvate
 
### <a name="learn-more"></a>Altre informazioni

- [Individuare e gestire shadow IT nella rete ](/cloud-app-security/tutorial-shadow-it)
- [App individuate con Cloud App Security](/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Visibilità e controllo della sessione utente 

Nelle aree di lavoro odierne, spesso non è sufficiente sapere cosa accade nell'ambiente cloud a cose fatte. Si vogliono impedire violazioni e perdite in tempo reale, prima che i dipendenti mettano intenzionalmente o inavvertitamente i dati e l'organizzazione a rischio. In combinazione con Azure Active Directory (Azure AD), Microsoft Cloud App Security offre queste funzionalità in un'esperienza olistica e integrata con Controllo app per l'accesso condizionale. 

Il controllo della sessione usa un'architettura di proxy inverso ed è integrato in modo univoco con Accesso condizionale di Azure AD. Accesso condizionale di Azure AD consente di applicare i controlli di accesso sulle app dell'organizzazione in base a determinate condizioni. Le condizioni definiscono chi (utente o gruppo di utenti), cosa (quali app cloud) e dove (a quali posizioni e reti) viene applicato un criterio di accesso condizionale. Dopo aver determinato le condizioni, è possibile indirizzare gli utenti a Cloud App Security in cui è possibile proteggere i dati in tempo reale.  

Con questo controllo è possibile:  
- Controllare i download di file
- Monitorare gli scenari B2B  
- Controllare l'accesso ai file  
- Proteggere i documenti al download  
 
### <a name="learn-more"></a>Altre informazioni

- [Proteggere le app con il controllo sessioni in Cloud App Security](/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Controlli e visibilità avanzati delle app 

I connettori di app usano i vantaggi offerti dalle API dei provider di app per consentire a Microsoft Cloud App Security maggiore visibilità e controllo delle app a cui si esegue la connessione. Cloud App Security sfrutta le API fornite dal provider di servizi cloud. Ogni servizio ha un proprio framework e proprie limitazioni dell'API, ad esempio la limitazione della larghezza di banda della rete, i limiti delle API, le finestre API dinamiche di spostamento temporale e altre ancora. Il team del prodotto Cloud App Security ha usato questi servizi per ottimizzare l'uso delle API e garantire prestazioni ottimali. Prendendo in considerazione le diverse limitazioni imposte dai servizi sulle API, i motori Cloud App Security usano la capacità massima consentita. Alcune operazioni, ad esempio l'analisi di tutti i file nel tenant, richiedono numerose chiamate API in modo da essere distribuite in un periodo più lungo. Si prevede che alcuni criteri vengano eseguiti per diverse ore o giorni. 
 
### <a name="learn-more"></a>Altre informazioni  

- [Connettere le app in Cloud App Security](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Passaggi successivi

- [Individuare e gestire shadow IT nella rete ](/cloud-app-security/tutorial-shadow-it)
- [App individuate con Cloud App Security](/cloud-app-security/discovered-apps)
- [Proteggere le app con il controllo sessioni in Cloud App Security](/cloud-app-security/proxy-intro-aad)
- [Connettere le app in Cloud App Security](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)