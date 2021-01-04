---
title: Determinare il comportamento di sicurezza per la collaborazione esterna con Azure Active Directory
description: Prima di poter eseguire un piano di sicurezza dell'accesso esterno, è necessario determinare ciò che si sta tentando di ottenere.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d6c37f6cd32985e540164ef8b308652a5e7414
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744050"
---
# <a name="determine-your-security-posture-for-external-access"></a>Determinare il comportamento di sicurezza per l'accesso esterno 

Quando si considera la regola dell'accesso esterno, è necessario valutare le esigenze di sicurezza e collaborazione per la propria organizzazione e all'interno di ogni scenario. A livello di organizzazione, prendere in considerazione la quantità di controllo necessaria al team IT per la collaborazione giornaliera. Le organizzazioni nei settori regolamentati possono richiedere un maggiore controllo IT. Ad esempio, un appaltatore della difesa potrebbe essere necessario per identificare e documentare in modo positivo ogni utente esterno, l'accesso e la rimozione dell'accesso. Questo requisito può essere a tutti gli accessi o a specifici scenari o carichi di lavoro. All'altra estremità dello spettro, una società di consulenza può in genere consentire agli utenti finali di determinare gli utenti esterni di cui hanno bisogno per collaborare, all'interno di alcune guide IT Guard. 

![Rispetto al controllo dell'utente finale della collaborazione](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> Un controllo eccessivamente rigoroso della collaborazione può comportare un budget IT più elevato, una riduzione della produttività e risultati aziendali ritardati. Quando i canali di collaborazione ufficiali vengono percepiti come troppo onerosi, gli utenti finali tendono a aggirare i sistemi per ottenere i propri processi, ad esempio la posta elettronica dei documenti non protetti.

## <a name="think-in-terms-of-scenarios"></a>Ragionare in termini di scenari

In molti casi è in grado di delegare l'accesso ai partner, almeno in alcuni scenari, fornendo Guard rails per la sicurezza. Il sistema di protezione IT può essere utile per garantire che la proprietà intellettuale rimanga protetta, garantendo al tempo stesso ai dipendenti la collaborazione con i partner per il lavoro svolto.

Quando si considerano gli scenari all'interno dell'organizzazione, valutare la necessità di un dipendente rispetto all'accesso ai partner commerciali alle risorse. Una banca può avere esigenze di conformità che limitano l'accesso a determinate risorse, ad esempio le informazioni sull'account utente, a un piccolo gruppo di dipendenti interni. Viceversa, la stessa banca può consentire l'accesso delegato per i partner che lavorano in una campagna di marketing.

![continuità della governance per ogni scenario](media\secure-external-access\1-scenarios.png)

In ogni scenario, prendere in considerazione 

* la riservatezza delle informazioni a rischio

* Se è necessario limitare i partner che possono visualizzare informazioni su altri utenti

* il costo di una violazione rispetto al peso del controllo centralizzato e dell'attrito degli utenti finali

 È anche possibile iniziare con controlli gestiti in modo centralizzato per soddisfare gli obiettivi di conformità e delegare il controllo agli utenti finali nel corso del tempo. Tutti i modelli di gestione degli accessi possono coesistere simultaneamente in un'organizzazione. 

L'uso delle [credenziali gestite dal partner](../external-identities/what-is-b2b.md) fornisce all'organizzazione un segnale essenziale che termina l'accesso alle risorse dopo che l'utente esterno ha perso l'accesso alle risorse della propria azienda.

## <a name="goals-of-securing-external-access"></a>Obiettivi della protezione dell'accesso esterno

Gli obiettivi degli accessi gestiti dall'IT e delegati sono diversi.

**Gli obiettivi principali dell'accesso governato dall'IT sono i seguenti:**

* Soddisfa le destinazioni di governance, normative e conformità (GRC). 

* Controllare rigorosamente l'accesso ai partner e i partner che possono visualizzare sugli utenti membri, i gruppi e altri partner.

**Gli obiettivi principali della delega dell'accesso sono:**

* Consentire ai proprietari aziendali di governare gli utenti con cui collaborano, all'interno di vincoli IT.

* Consentire ai partner aziendali di richiedere l'accesso in base alle regole definite dai proprietari aziendali.

A seconda di quale sia l'organizzazione e gli scenari, è necessario: 

* **Controllare l'accesso ad applicazioni, dati e contenuto**. Questa operazione può essere eseguita tramite diversi metodi, a seconda delle versioni di [Azure ad](https://azure.microsoft.com/pricing/details/active-directory/) e [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans). 

* **Ridurre la superficie di attacco**. [Privileged Identity Management](../privileged-identity-management/pim-configure.md), la [prevenzione della perdita dei dati (DLP)](https://docs.microsoft.com/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) e le [funzionalità di crittografia](https://docs.microsoft.com/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) riducono la superficie di attacco.

* **Controllare regolarmente le attività e il registro di controllo per verificare la conformità**. Può delegare le decisioni di accesso ai proprietari aziendali tramite la gestione dei diritti, mentre le verifiche di accesso consentono di confermare periodicamente l'accesso continuo. La classificazione automatica dei dati con etichette di riservatezza consente di automatizzare la crittografia dei contenuti sensibili, semplificando la conformità degli utenti finali ai dipendenti.

## <a name="next-steps"></a>Passaggi successivi 

Vedere gli articoli seguenti sulla protezione dell'accesso esterno alle risorse. Si consiglia di eseguire le azioni nell'ordine elencato.

1. [Determinare il comportamento di sicurezza per l'accesso esterno](1-secure-access-posture.md) .

2. [Individua lo stato corrente](2-secure-access-current-state.md)

3. [Creazione di un piano di governance](3-secure-access-plan.md)

4. [Usare i gruppi per la sicurezza](4-secure-access-groups.md)

5. [Transizione a Azure AD B2B](5-secure-access-b2b.md)

6. [Accesso sicuro con la gestione dei diritti](6-secure-access-entitlement-managment.md)

7. [Accesso sicuro con criteri di accesso condizionale](7-secure-access-conditional-access.md)

8. [Proteggere l'accesso con le etichette di riservatezza](8-secure-access-sensitivity-labels.md)

9. [Accesso sicuro a Microsoft teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)
 

 
