---
title: Controllare l'accesso esterno alle risorse in Azure Active Directory con le etichette di riservatezza.
description: Usare le etichette di riservatezza come parte del piano di sicurezza complessivo per l'accesso esterno.
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
ms.openlocfilehash: 6fc8ceddce4d41244d72632db058aa58c0d919db
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565173"
---
# <a name="control-access-with-sensitivity-labels"></a>Controllare l'accesso con le etichette di riservatezza 

Le [etichette di riservatezza](/microsoft-365/compliance/sensitivity-labels) consentono di controllare l'accesso al contenuto nelle applicazioni Office 365 e in contenitori quali Microsoft teams, Microsoft 365 Groups e siti di SharePoint. Possono proteggere i contenuti senza compromettere le capacità di collaborazione e produzione degli utenti. Le etichette di riservatezza consentono di inviare il contenuto dell'organizzazione tra dispositivi, app e servizi, proteggendo al tempo stesso i dati e rispettando i criteri di conformità e sicurezza. 

Con le etichette di riservatezza è possibile:

* **Classificare il contenuto senza aggiungere impostazioni di protezione**. È possibile assegnare una classificazione al contenuto (ad esempio un adesivo) che rende permanente e roaming con il contenuto mentre viene usato e condiviso. La classificazione può essere usata per generare report sull'utilizzo e visualizzare i dati delle attività per il contenuto sensibile.

* **Applicare le impostazioni di protezione, ad esempio crittografia, filigrane e restrizioni di accesso**. Ad esempio, gli utenti possono applicare un'etichetta riservata a un documento o a un messaggio di posta elettronica e tale etichetta può [crittografare il contenuto](/microsoft-365/compliance/encryption-sensitivity-labels) e aggiungere una filigrana "riservata". Inoltre, è possibile [applicare un'etichetta di riservatezza a un contenitore](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites) come un sito di SharePoint e applicare se gli utenti esterni possono accedere al contenuto che contiene.

Le etichette di riservatezza per la posta elettronica e altri contenuti viaggiano con il contenuto. Le etichette di riservatezza nei contenitori possono limitare l'accesso al contenitore, ma il contenuto nel contenitore non eredita l'etichetta. Ad esempio, un utente può prendere il contenuto da un sito protetto, scaricarlo e condividerlo senza restrizioni, a meno che il contenuto non abbia anche un'etichetta di riservatezza.

 >[!NOTE]
>Per applicare le etichette di riservatezza, gli utenti devono essere connessi all'account Microsoft aziendale o dell'Istituto di istruzione. 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>Autorizzazioni necessarie per creare e gestire i livelli di sensibilità

I membri del team di conformità che creerà etichette di riservatezza devono disporre delle autorizzazioni per il centro di conformità Microsoft 365, Microsoft 365 Centro sicurezza o il Centro sicurezza & conformità.

Per impostazione predefinita, gli amministratori globali per il tenant hanno accesso a questi centri di amministrazione e possono concedere ai responsabili della conformità e ad altri utenti l'accesso, senza concedere loro tutte le autorizzazioni di un amministratore tenant. Per questo accesso delegato amministratore limitato, aggiungere gli utenti al gruppo di ruoli amministratore dati di conformità, amministratore conformità o amministratore sicurezza.

 

## <a name="determine-your-sensitivity-label-strategy"></a>Determinare la strategia dell'etichetta di riservatezza

Quando si pensa di governare l'accesso esterno al contenuto, determinare quanto segue:

**Per tutti i contenuti e i contenitori**

* In che modo si definisce qual è l'effetto aziendale elevato, medio o basso (HBI, MBI, LBI)? Prendere in considerazione l'effetto sull'organizzazione se i tipi specifici di contenuto sono condivisi in modo non appropriato.

   * Contenuto con tipi specifici di contenuto intrinsecamente [sensibile](/microsoft-365/compliance/apply-sensitivity-label-automatically), ad esempio carte di credito o numeri di passaporto

   * Contenuto creato da gruppi o persone specifiche (ad esempio, responsabili della conformità, responsabili finanziari o dirigenti)

   * Contenuto in librerie o siti specifici. Ad esempio, i contenitori che ospitano la strategia organizzativa o i dati finanziari privati

   * Altri criteri

* Quali categorie di contenuto, ad esempio il contenuto di HBI, devono essere limitate dall'accesso da parte di utenti esterni?

   * Le restrizioni possono includere azioni come la restrizione dell'accesso ai contenitori e la crittografia del contenuto.

* Quali impostazioni predefinite devono essere applicate per i dati HBI, i siti o i gruppi di Microsoft 365?

* Dove si useranno le etichette di riservatezza per [etichettare e monitorare](/microsoft-365/compliance/sensitivity-labels), anziché [applicare la crittografia](/microsoft-365/compliance/encryption-sensitivity-labels) o [applicare restrizioni di accesso ai contenitori](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)?

**Per la posta elettronica e il contenuto**

* [Applicare automaticamente le etichette di riservatezza](/microsoft-365/compliance/apply-sensitivity-label-automatically) al contenuto oppure procedere manualmente?

   * Se si sceglie di eseguire questa operazione manualmente, è opportuno [consigliare agli utenti di applicare un'etichetta](/microsoft-365/compliance/apply-sensitivity-label-automatically)?

**Per i contenitori**

* Quali criteri determineranno se i gruppi di M365, i team o i siti di SharePoint necessitano dell'accesso per limitare l'utilizzo di etichette di riservatezza?

* Si vuole solo etichettare il contenuto in questi contenitori in avanti o si vuole [etichettare automaticamente](/microsoft-365/compliance/apply-sensitivity-label-automatically) i file esistenti in SharePoint e OneDrive?

Vedere questi [scenari comuni per le etichette di riservatezza](/microsoft-365/compliance/get-started-with-sensitivity-labels) per altre idee su come usare le etichette di riservatezza.

### <a name="sensitivity-labels-on-email-and-content"></a>Etichette di riservatezza per la posta elettronica e il contenuto

Quando si assegna un'etichetta di riservatezza a un documento o a un messaggio di posta elettronica, è come un timbro applicato al contenuto personalizzabile, testo non crittografato e persistente. 

* **Personalizzabile** significa che è possibile creare etichette appropriate per l'organizzazione e determinare cosa accade quando vengono applicate.

* **Testo non crittografato** significa che fa parte dei metadati dell'elemento ed è leggibile dalle applicazioni e dai servizi in modo che possano applicare le proprie azioni protettive.

* **Persistente** indica che l'etichetta e le protezioni associate eseguono il roaming con il contenuto e diventano la base per l'applicazione e l'applicazione dei criteri.

 

> [!NOTE]
> A ogni elemento di contenuto può essere applicata una singola etichetta di riservatezza.


### <a name="sensitivity-labels-on-containers"></a>Etichette di riservatezza nei contenitori

È possibile applicare etichette di riservatezza a contenitori come [Microsoft 365 gruppi](../enterprise-users/groups-assign-sensitivity-labels.md), [Microsoft teams](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)e [siti di SharePoint](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites). Quando si applica questa etichetta di riservatezza a un contenitore supportato, l'etichetta applica automaticamente le impostazioni di classificazione e protezione al sito o al gruppo connesso. Le etichette di riservatezza in questi contenitori possono controllare i seguenti aspetti dei contenitori:

* **Privacy**. È possibile scegliere chi può visualizzare il sito: utenti specifici, tutti gli utenti interni o chiunque.

* **Accesso utente esterno**. Controlla se il proprietario del gruppo può aggiungere Guest al gruppo.

* **Accesso da dispositivi non gestiti**. Determina se e come i dispositivi non gestiti possono accedere al contenuto.

 

![Screenshot della modifica delle etichette di riservatezza](media/secure-external-access/8-edit-label.png)

 

Quando si applica un'etichetta di riservatezza a un contenitore, ad esempio un sito di SharePoint, questo non viene applicato al contenuto: le etichette di riservatezza sui contenitori controllano l'accesso al contenuto all'interno del contenitore. 

* Se si desidera applicare automaticamente le etichette al contenuto all'interno del contenitore, vedere [applicare automaticamente una sensibilità al contenuto](/microsoft-365/compliance/apply-sensitivity-label-automatically).

* Se si vuole che gli utenti siano in grado di applicare manualmente le etichette a questo contenuto, assicurarsi di avere [abilitato le etichette di riservatezza per i file di Office in SharePoint e OneDrive](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files).

### <a name="plan-to-implement-sensitivity-labels"></a>Pianificare l'implementazione di etichette di riservatezza

Una volta stabilito come si desidera usare le etichette di riservatezza e il contenuto e i siti da applicare, vedere la documentazione seguente per informazioni su come eseguire l'implementazione.

1. [Introduzione alle etichette di riservatezza](/microsoft-365/compliance/get-started-with-sensitivity-labels)

2. [Creare una strategia di distribuzione](/microsoft-365/compliance/get-started-with-sensitivity-labels)

3. [Creazione e pubblicazione di etichette di riservatezza](/microsoft-365/compliance/create-sensitivity-labels)

4. [Limitare l'accesso al contenuto usando le etichette di riservatezza per applicare la crittografia](/microsoft-365/compliance/encryption-sensitivity-labels)

5. [Usare le etichette di riservatezza con team, gruppi e siti](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)

6. [Abilitare le etichette di riservatezza per i file di Office in SharePoint e OneDrive](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files)

### <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti sulla protezione dell'accesso esterno alle risorse. Si consiglia di eseguire le azioni nell'ordine elencato.

1. [Determinare la postura di sicurezza desiderata per l'accesso esterno](1-secure-access-posture.md)

2. [Individua lo stato corrente](2-secure-access-current-state.md)

3. [Creazione di un piano di governance](3-secure-access-plan.md)

4. [Usare i gruppi per la sicurezza](4-secure-access-groups.md)

5. [Transizione a Azure AD B2B](5-secure-access-b2b.md)

6. [Accesso sicuro con la gestione dei diritti](6-secure-access-entitlement-managment.md)

7. [Accesso sicuro con criteri di accesso condizionale](7-secure-access-conditional-access.md)

8. [Proteggere l'accesso con le etichette di riservatezza](8-secure-access-sensitivity-labels.md) .

9. [Accesso sicuro a Microsoft teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)