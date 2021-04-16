---
title: Domande frequenti sui report di Azure Active Directory | Microsoft Docs
description: Domande frequenti sui report Azure Active Directory report.
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 05/12/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4da0083a236900037b388798d825515e94613c20
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533699"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Domande frequenti sui report di Azure Active Directory

Questo articolo include risposte alle domande frequenti sulla creazione di report in Azure Active Directory (Azure AD). Per altre informazioni, vedere [Creazione di report in Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Guida introduttiva 

**D: Attualmente si usano le API dell'endpoint per eseguire il pull Azure AD report di controllo e di utilizzo integrato delle applicazioni nei sistemi di report a `https://graph.windows.net/<tenant-name>/reports/` livello di codice. A cosa è necessario passare?**

**R:** Consultare la [documentazione di riferimento sulle API](https://developer.microsoft.com/graph/) per informazioni su come [usare le API per accedere ai report attività](concept-reporting-api.md). Questo endpoint include due report (**Audit** e **Sign-Ins**) che forniscono tutti i dati disponibili nell'endpoint API precedente. Anche questo nuovo endpoint include un report sugli accessi con la licenza Azure AD Premium, che è possibile usare per ottenere informazioni sull'utilizzo dell'app, l'utilizzo dei dispositivi e gli accessi degli utenti.

---

**D: Attualmente si usano le API dell'endpoint per eseguire il pull dei report di sicurezza di Azure AD (tipi specifici di rilevamenti, ad esempio credenziali perse o accesso da indirizzi IP anonimi) nei sistemi di report a livello di `https://graph.windows.net/<tenant-name>/reports/` codice. A cosa è necessario passare?**

**A:** È possibile usare l'API rilevamento dei [rischi di Identity Protection](../identity-protection/howto-identity-protection-graph-api.md) per accedere ai rilevamenti di sicurezza tramite Microsoft Graph. Questo nuovo formato offre maggiore flessibilità nel modo in cui è possibile eseguire query sui dati, con filtri avanzati, selezione dei campi e altro ancora, e standardizza i rilevamenti dei rischi in un tipo per semplificare l'integrazione nei SIEM e in altri strumenti di raccolta dati. Poiché i dati sono un formato diverso, non è possibile sostituire le query precedenti con una nuova query. Tuttavia, [la nuova API usa Microsoft Graph](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true), che è lo standard Microsoft per api come Microsoft 365 o Azure AD. Il lavoro richiesto può quindi estendere gli investimenti Microsoft Graph o aiutare a iniziare la transizione a questa nuova piattaforma standard.

---

**D: Come ottenere una licenza Premium?**

**R:** Vedere [Procedura: Effettuare l'iscrizione alle edizioni Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) per aggiornare l'edizione di Azure Active Directory in uso.

---

**D: In quanto tempo è possibile visualizzare le attività dopo aver acquistato una licenza Premium?**

**R:** Se si dispone già di dati sulle attività come licenza gratuita, è possibile visualizzarli immediatamente. Se non si dispone di dati, saranno disponibili fino a 3 giorni prima che i dati vengono visualizzati nei report.

---

**D: È possibile visualizzare i dati del mese precedente dopo avere acquistato una licenza Azure AD Premium?**

**A:** Se di recente è stata passata a una versione Premium (inclusa una versione di valutazione), è possibile visualizzare inizialmente i dati fino a 7 giorni. Man mano che i dati si accumulano, è possibile visualizzare quelli degli ultimi 30 giorni.

---

**D: È necessario essere amministratore globale per visualizzare gli accessi al portale di Azure o per ottenere i dati tramite l'API?**

**R:** No, è possibile accedere ai dati dei report tramite il portale o l'API anche se si ha il **ruolo con autorizzazioni di lettura per la sicurezza** o **Amministratore della sicurezza** per il tenant. Naturalmente anche gli **amministratori globali** hanno accesso a questi dati.

---


## <a name="activity-logs"></a>Log attività


**D: Qual è la conservazione dei dati per i log attività (controllo e accessi) nel portale di Azure?** 

**A:** Per altre informazioni, vedere [Criteri di conservazione dei dati per Azure AD report](reference-reports-data-retention.md).

---

**D: Quanto tempo è necessario per visualizzare i dati dell'attività dopo aver completato l'attività?**

**R:** I log di controllo hanno una latenza compresa tra 15 minuti e un'ora. Per la visualizzazione dei log delle attività di accesso possono essere necessari da 15 minuti fino a 2 ore per alcuni record.

---

**D: È possibile ottenere Microsoft 365 log attività tramite il portale di Azure?**

**A:** Anche se i log attività Microsoft 365 e Azure AD condividono molte risorse della directory, se si vuole una visualizzazione completa dei log attività di Microsoft 365, passare al [interfaccia di amministrazione di Microsoft 365 per](https://admin.microsoft.com) ottenere le informazioni del log attività di Office 365.

---

**D: Quali API si usano per ottenere informazioni sui log attività Microsoft 365 attività?**

**A:** Usare le [API Microsoft 365 Management](/office/office-365-management-api/office-365-management-apis-overview) per accedere ai log attività Microsoft 365 tramite un'API.

---

**D: Quanti record è possibile scaricare dal portale di Azure?**

**R:** Dal portale di Azure è possibile scaricare fino a 5.000 record. Per impostazione predefinita, vengono scaricati gli ultimi 5.000 record, ordinati partendo dai record *più recenti*.

---

## <a name="risky-sign-ins"></a>Accessi a rischio

**D: È presente un rilevamento dei rischi in Identity Protection, ma non viene visualizzato l'accesso corrispondente nel report degli accesso. È previsto?**

**R:** Sì, la protezione dell'identità valuta il rischio per tutti i flussi di autenticazione, sia interattivi che non interattivi. Tuttavia, tutti i report degli accessi mostrano solo gli accessi interattivi.

---

**D: Come sapere perché un accesso o un utente è stato contrassegnato come rischioso nel portale di Azure?**

**A:** Se si ha una sottoscrizione **Azure AD Premium,** è possibile ottenere altre informazioni sui rilevamenti dei rischi sottostanti selezionando l'utente **in** Utenti contrassegnati per il rischio o selezionando un record nel **report** Di accesso a rischio. Se si ha una **sottoscrizione Gratuita** o **Basic,** è possibile visualizzare gli utenti a rischio e i report di accesso a rischio, ma non è possibile visualizzare le informazioni di rilevamento dei rischi sottostanti.

---

**D: come vengono calcolati gli indirizzi IP nel report degli accessi e degli accessi a rischio?**

**R:** Gli indirizzi IP vengono rilasciati in modo che non esista una connessione certa tra un IP e la posizione in cui si trova fisicamente il computer con tale indirizzo. Il mapping degli indirizzi IP è ulteriormente complicato da fattori come la possibilità che provider di telefonia mobile e VPN rilascino indirizzi IP da pool centrali spesso molto distanti dal luogo in cui viene effettivamente usato il dispositivo client. Attualmente nei report di Azure AD la conversione di un indirizzo IP in una posizione fisica è un'approssimazione basata su tracce, dati del Registro di sistema, ricerche inverse e altre informazioni. 

---

**D: Cosa significa il rilevamento dei rischi "Accesso con rischio aggiuntivo rilevato"?**

**R:** Per offrire informazioni dettagliate su tutti gli accessi rischiosi effettuati nell'ambiente, "È stato rilevato un accesso con rischi aggiuntivi" funziona come segnaposto per gli accessi per rilevamenti esclusivi per i sottoscrittori di Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Accesso condizionale

**D: Quali sono le novità di questa funzionalità?**

**A:** I clienti possono ora risolvere i problemi relativi ai criteri di accesso condizionale tramite tutti i report di accesso. I clienti possono esaminare lo stato di accesso condizionale ed esaminare i dettagli dei criteri applicati all'accesso e il risultato per ogni criterio.

**D: Come iniziare?**

**R**: Per iniziare:

* Andare al report degli accessi nel [portale di Azure](https://portal.azure.com).
* Fare clic sull'accesso di cui si desiderano risolvere i problemi.
* Passare alla **scheda Accesso** condizionale. Qui è possibile visualizzare tutti i criteri che hanno incideto sull'accesso e il risultato per ogni criterio. 
    
**D: Quali sono tutti i valori possibili per lo stato di accesso condizionale?**

**A:** Lo stato dell'accesso condizionale può avere i valori seguenti:

* **Non applicato:** significa che non sono presenti criteri di accesso condizionale con l'utente e l'app nell'ambito. 
* **Operazione** riuscita: i criteri di accesso condizionale con l'utente e l'app nell'ambito e i criteri di accesso condizionale sono stati soddisfatti correttamente. 
* **Errore:** l'accesso ha soddisfatto la condizione dell'utente e dell'applicazione di almeno un criterio di accesso condizionale e i controlli di concessione non sono soddisfatti o impostati per bloccare l'accesso.
    
**D: Quali sono tutti i valori possibili per il risultato dei criteri di accesso condizionale?**

**A:** I criteri di accesso condizionale possono avere i risultati seguenti:

* **Operazione riuscita**: il criterio è stato soddisfatto correttamente.
* **Operazione non riuscita**: il criterio non è stato soddisfatto.
* **Non applicato**: le condizioni dei criteri potrebbero non essere soddisfatte.
* **Non abilitato**: a causa di criteri nello stato disabilitato. 
    
**D: Il nome dei criteri nel report di tutti gli account di accesso non corrisponde al nome dei criteri nell'autorità di certificazione. Perché?**

**A:** Il nome dei criteri nel report di tutti gli accessi si basa sul nome dei criteri di accesso condizionale al momento dell'accesso. Ciò può essere incoerente con il nome del criterio nell'accesso condizionale se è stato aggiornato il nome del criterio in un secondo momento, vale a dire, dopo l'accesso.

**D: L'accesso è stato bloccato a causa di un criterio di accesso condizionale, ma il report delle attività di accesso indica che l'accesso è riuscito. Perché?**

**A:** Attualmente il report di accesso potrebbe non mostrare risultati accurati per gli scenari di Exchange ActiveSync quando viene applicato l'accesso condizionale. In alcuni casi il risultato dell'accesso nel report mostra un accesso riuscito, ma l'accesso non è riuscito a causa di criteri di accesso condizionale.
