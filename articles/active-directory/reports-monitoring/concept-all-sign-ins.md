---
title: Azure Active Directory di attività di accesso - Anteprima | Microsoft Docs
description: Introduzione ai report delle attività di accesso nel portale Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/16/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 781cafd9b382868d0aa4f6b77ff7338c4ee15ed2
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589654"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Azure Active Directory di attività di accesso - anteprima

Il Azure Active Directory consente di accedere a tre log attività:

- **Sign-ins (Account** di accesso) : informazioni sugli account di accesso e sul modo in cui le risorse vengono usate dagli utenti.
- **[Controllo:](concept-audit-logs.md)** informazioni sulle modifiche applicate al tenant, ad esempio la gestione di utenti e gruppi o gli aggiornamenti applicati alle risorse del tenant.
- **[Provisioning:](concept-provisioning-logs.md)** attività eseguite dal servizio di provisioning, ad esempio la creazione di un gruppo in ServiceNow o un utente importato da Workday.


Il report degli account di accesso classici in Azure Active Directory offre una panoramica degli account di accesso utente interattivi. È ora anche possibile accedere a tre report di accesso aggiuntivi ora disponibili in anteprima:

- Accesso utente non interattivo

- Account di accesso dell'entità servizio

- Identità gestite per gli account di accesso alle risorse di Azure

Questo articolo offre una panoramica del report delle attività di accesso con l'anteprima degli account di accesso non interattivi, dell'applicazione e delle identità gestite per le risorse di Azure. Per informazioni sul report di accesso senza le funzionalità di anteprima, vedere Report delle attività di [accesso nel portale Azure Active Directory .](concept-sign-ins.md)



## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a usare questa funzionalità, è necessario conoscere le risposte a:

- Chi può accedere ai dati?

- Quale licenza di Azure AD è necessaria per visualizzare le attività di accesso?

### <a name="who-can-access-the-data"></a>Chi può accedere ai dati?

- Utenti con i ruoli Amministratore della sicurezza, Ruolo con autorizzazioni di lettura per la sicurezza e Lettore report

- Amministratori globali

- Qualsiasi utente (non amministratore) può visualizzare i propri accessi 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Quale licenza di Azure AD è necessaria per visualizzare le attività di accesso?

Il tenant deve avere una Azure AD Premium di accesso associata per visualizzare le attività di accesso. vedere [Procedura: Effettuare l'iscrizione alle edizioni Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) per aggiornare l'edizione di Azure Active Directory in uso. La visualizzazione dei dati nei report dopo l'aggiornamento a una licenza Premium senza attività di dati prima dell'aggiornamento può richiedere un paio di giorni.



## <a name="sign-ins-report"></a>Report sugli accessi

Il report degli accesso fornisce le risposte alle domande seguenti:

- Qual è il modello di accesso di un utente, un'applicazione o un servizio?
- Quanti utenti, app o servizi hanno eseguito l'accesso in una settimana?
- Qual è lo stato di questi accessi?


Nel pannello del report degli errori di accesso è possibile passare tra:

- **Accesso utente interattivo:** accesso in cui un utente fornisce un fattore di autenticazione, ad esempio una password, una risposta tramite un'app MFA, un fattore biometrico o un codice a matrice.

- **Accesso utente non interattivo:** gli account di accesso eseguiti da un client per conto di un utente. Questi account di accesso non richiedono alcun fattore di interazione o autenticazione da parte dell'utente. Ad esempio, l'autenticazione e l'autorizzazione che usano token di accesso e aggiornamento che non richiedono l'immissione delle credenziali da parte di un utente.

- **Accessi all'entità servizio:** accessi da parte di app ed entità servizio che non coinvolgono alcun utente. In questi accessi, l'app o il servizio fornisce credenziali per conto proprio per autenticare o accedere alle risorse.

- **Identità gestite per gli account di accesso delle** risorse di Azure: accesso da parte di risorse di Azure con segreti gestiti da Azure. Per altre informazioni, vedere [Che cosa sono le identità gestite per le risorse di Azure?](../managed-identities-azure-resources/overview.md) 


![Tipi di report di accesso](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins&quot;></a>Accesso utente

Ogni scheda nel pannello Degli account di accesso mostra le colonne predefinite riportate di seguito. Alcune schede hanno colonne aggiuntive:

- Data di accesso

- ID richiesta

- Nome utente o ID utente

- Nome dell'applicazione o ID applicazione

- Stato dell'accesso

- Indirizzo IP del dispositivo usato per l'accesso



### <a name=&quot;interactive-user-sign-ins&quot;></a>Accesso utente interattivo


Gli accesso utente interattivi sono gli account di accesso in cui un utente fornisce un fattore di autenticazione per Azure AD o interagisce direttamente con Azure AD o un'app helper, ad esempio l'app Microsoft Authenticator. I fattori che gli utenti forniscono includono password, risposte alle sfide dell'autenticazione a più fattori, fattori biometrici o codici A QR forniti da un utente Azure AD o a un'app helper.

> [!NOTE]
> Questo report include anche gli accesso federati da provider di identità federati Azure AD.  



> [!NOTE] 
> Il report degli account di accesso utente interattivo usato per contenere alcuni account di accesso non interattivi dai client di Microsoft Exchange. Anche se tali account di accesso non erano interattivi, sono stati inclusi nel report degli utenti interattivi per una maggiore visibilità. Dopo che il report degli utenti non interattivi è stato visualizzato in anteprima pubblica nel mese di novembre 2020, i log eventi di accesso non interattivi sono stati spostati nel report di accesso utente non interattivo per una maggiore accuratezza. 


**Dimensioni del report:** piccole <br> 
**Esempi:**

- Un utente fornisce nome utente e password nella Azure AD di accesso.

- Un utente passa una richiesta di autenticazione a più fattori tramite SMS.

- Un utente fornisce un movimento biometrico per sbloccare il PC Windows con Windows Hello for Business.

- Un utente è federato per Azure AD con un'AD FS SAML.


Oltre ai campi predefiniti, il report degli accesso interattivi mostra anche: 

- Percorso di accesso

- Se l'accesso condizionale è stato applicato



È possibile personalizzare la visualizzazione elenco facendo clic **su Colonne** sulla barra degli strumenti.

![Colonne di accesso utente interattivo](./media/concept-all-sign-ins/columns-interactive.png &quot;Colonne di accesso utente interattivo")





La personalizzazione della visualizzazione consente di visualizzare campi aggiuntivi o rimuovere i campi già visualizzati.

![Tutte le colonne interattive](./media/concept-all-sign-ins/all-interactive-columns.png)


Selezionare un elemento nella visualizzazione elenco per ottenere informazioni più dettagliate sull'accesso correlato.

![Attività di accesso](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Accesso utente interattivo")



### <a name="non-interactive-user-sign-ins"></a>Accesso utente non interattivo

Gli account di accesso utente non interattivi sono gli account di accesso eseguiti da un'app client o da componenti del sistema operativo per conto di un utente. Come gli account di accesso degli utenti interattivi, questi vengono evasi per conto di un utente. A differenza degli account di accesso utente interattivi, questi non richiedono all'utente di fornire un fattore di autenticazione. L'app client o il dispositivo usa invece un token o un codice per autenticare o accedere a una risorsa per conto di un utente. In generale, l'utente percepirà questi accesso come in background dell'attività dell'utente.


**Dimensioni report:** Grande <br>
**Esempi:** 

- Un'app client usa un token di aggiornamento OAuth 2.0 per ottenere un token di accesso.

- Un client usa un codice di autorizzazione OAuth 2.0 per ottenere un token di accesso e un token di aggiornamento.

- Un utente esegue l'accesso Single Sign-On (SSO) a un'app Web o Windows in un PC Azure AD aggiunto.

- Un utente accede a una seconda Microsoft Office app mentre ha una sessione in un dispositivo mobile usando l'apicino DISA (Family of Client ID).




Oltre ai campi predefiniti, il report degli accesso non interattivi mostra anche: 

- ID risorsa

- Numero di accesso raggruppati




Non è possibile personalizzare i campi visualizzati in questo report.


![Colonne disabilitate](./media/concept-all-sign-ins/disabled-columns.png "Colonne disabilitate")

Per semplificare la digestione dei dati, vengono raggruppati gli eventi di accesso non interattivi. I client spesso creano molti account di accesso non interattivi per conto dello stesso utente in un breve periodo di tempo, che condividono tutte le stesse caratteristiche, ad eccezione del momento in cui è stato tentato l'accesso. Ad esempio, un client può ottenere un token di accesso una volta all'ora per conto di un utente. Se l'utente o il client non modifica lo stato, l'indirizzo IP, la risorsa e tutte le altre informazioni sono uguali per ogni richiesta di token di accesso. Quando Azure AD registra più account di accesso identici diversi da ora e data, tali account verranno aggregati in una singola riga. Una riga con più account di accesso identici (ad eccezione di data e ora di emissione) avrà un valore maggiore di 1 nella colonna #sign-ins. È possibile espandere la riga per visualizzare tutti i diversi account di accesso e i relativi timestamp diversi. Gli accesso vengono aggregati negli utenti non interattivi quando i dati seguenti corrispondono:


- Applicazione

- Utente

- indirizzo IP

- Stato

- ID risorsa


È possibile:

- Espandere un nodo per visualizzare i singoli elementi di un gruppo.  

- Fare clic su un singolo elemento per visualizzare tutti i dettagli 


![Dettagli di accesso utente non interattivo](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins&quot;></a>Account di accesso dell'entità servizio

A differenza degli account di accesso utente interattivi e non interattivi, gli account di accesso dell'entità servizio non coinvolgono un utente. Si tratta invece di accessi da parte di qualsiasi account non utente, ad esempio app o entità servizio ,ad eccezione dell'accesso con identità gestita, che sono inclusi solo nel report degli accessi con identità gestita. In questi accessi, l'app o il servizio fornisce le proprie credenziali, ad esempio un certificato o un segreto dell'app per autenticare o accedere alle risorse.


**Dimensioni report:** Grande <br>
**Esempi:**

- Un'entità servizio usa un certificato per autenticare e accedere al Microsoft Graph. 

- Un'applicazione usa un segreto client per l'autenticazione nel flusso delle credenziali client OAuth. 


Questo report ha una visualizzazione elenco predefinita che mostra:

- Data di accesso

- ID richiesta

- ID o nome dell'entità servizio

- Stato

- indirizzo IP

- Nome risorsa

- ID risorsa

- Numero di accesso

Non è possibile personalizzare i campi visualizzati in questo report.

![Colonne disabilitate](./media/concept-all-sign-ins/disabled-columns.png &quot;Colonne disabilitate")

Per semplificare il digest dei dati nei log di accesso dell'entità servizio, vengono raggruppati gli eventi di accesso dell'entità servizio. Gli accesso dalla stessa entità nelle stesse condizioni vengono aggregati in un'unica riga. È possibile espandere la riga per visualizzare tutti i diversi account di accesso e i relativi timestamp. Gli account di accesso vengono aggregati nel report dell'entità servizio quando i dati seguenti corrispondono:

- ID o nome dell'entità servizio

- Stato

- indirizzo IP

- Nome o ID risorsa

È possibile:

- Espandere un nodo per visualizzare i singoli elementi di un gruppo.  

- Fare clic su un singolo elemento per visualizzare tutti i dettagli 


![Dettagli colonna](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Dettagli colonna")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Identità gestita per gli account di accesso delle risorse di Azure 

L'identità gestita per gli account di accesso delle risorse di Azure è un accesso eseguito dalle risorse con i propri segreti gestiti da Azure per semplificare la gestione delle credenziali.

**Dimensioni report:** Piccolo <br> 
**Esempi:**

Una macchina virtuale con credenziali gestite usa Azure AD per ottenere un token di accesso.   


Questo report ha una visualizzazione elenco predefinita che mostra:


- ID identità gestita

- Nome dell'identità gestita

- Risorsa

- ID risorsa

- Numero di accesso raggruppati

Non è possibile personalizzare i campi visualizzati in questo report.

Per semplificare il digest dei dati, le identità gestite per i log di accesso delle risorse di Azure, gli eventi di accesso non interattivi vengono raggruppati. Gli accesso dalla stessa entità vengono aggregati in una singola riga. È possibile espandere la riga per visualizzare tutti i diversi account di accesso e i relativi timestamp diversi. Gli account di accesso vengono aggregati nel report delle identità gestite quando tutti i dati seguenti corrispondono:

- ID o nome dell'identità gestita

- Stato

- indirizzo IP

- Id o nome della risorsa

Selezionare un elemento nella visualizzazione elenco per visualizzare tutti gli account di accesso raggruppati in un nodo.

Selezionare un elemento raggruppato per visualizzare tutti i dettagli dell'accesso. 


## <a name="sign-in-error-code"></a>Codice dell'errore di accesso

Se un accesso non è riuscito, è possibile ottenere altre informazioni sul motivo nella sezione **Informazioni di base** dell'elemento di log correlato. 

![Screenshot che mostra una visualizzazione dettagliata delle informazioni.](./media/concept-all-sign-ins/error-code.png)
 
Anche se l'elemento di log fornisce un motivo di errore, in alcuni casi è possibile ottenere altre informazioni usando lo strumento di ricerca degli [errori di accesso](https://login.microsoftonline.com/error). Ad esempio, se disponibile, questo strumento fornisce i passaggi di correzione.  

![Strumento di ricerca del codice di errore](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>Filtrare le attività di accesso

Impostando un filtro, è possibile restringere l'ambito dei dati di accesso restituiti. Azure AD offre un'ampia gamma di filtri aggiuntivi che è possibile impostare. Quando si imposta il filtro, è sempre consigliabile prestare particolare attenzione al filtro **intervallo** di date configurato. Un filtro di intervallo di date appropriato garantisce che Azure AD restituisca solo i dati effettivamente a cui si è realmente a cui si è molto a cui si è di grande attenzione.     

Il **filtro** Intervallo di date consente di definire un intervallo di tempo per i dati restituiti.
I valori possibili sono:

- Un mese

- Sette giorni

- Ventiquattro ore

- Personalizzato

![Filtro dell'intervallo di date](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Filtrare gli account di accesso utente

Il filtro per gli accesso interattivi e non interattivi è lo stesso. Per questo problema, il filtro configurato per gli account di accesso interattivi viene salvato in modo permanente per gli account di accesso non interattivi e viceversa. 






## <a name="access-the-new-sign-in-activity-reports"></a>Accedere ai nuovi report delle attività di accesso 

Il report delle attività di accesso nel portale di Azure offre un metodo semplice per attivare e disattivare il report di anteprima. Se i report di anteprima sono abilitati, viene visualizzato un nuovo menu che consente di accedere a tutti i tipi di report attività di accesso.     


Per accedere ai nuovi report di accesso con accessi non interattivi e dell'applicazione: 

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory**.

    ![Selezionare Azure AD](./media/concept-all-sign-ins/azure-services.png)

2. Nella sezione **Monitoraggio** fare clic **su Accedi.**

    ![Selezionare gli accesso](./media/concept-all-sign-ins/sign-ins.png)

3. Fare clic sulla **barra di** anteprima.

    ![Abilitare una nuova visualizzazione](./media/concept-all-sign-ins/enable-new-preview.png)

4. Per tornare alla visualizzazione predefinita, fare di nuovo clic sulla **barra** di anteprima. 

    ![Ripristinare la visualizzazione classica](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>Scaricare i report attività di accesso

Quando si scarica un report attività di accesso, si verifica quanto segue:

- È possibile scaricare il report di accesso come file CSV o JSON.

- È possibile scaricare fino a 100.000 record. Per scaricare altri dati, usare l'API di creazione report.

- Il download si basa sulla selezione del filtro effettuata.

- Il numero di record che è possibile scaricare è limitato dai [criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md). 


![Scarica report](./media/concept-all-sign-ins/download-reports.png "Scarica report")


Ogni download CSV è costituito da sei file diversi:

- Accesso interattivo

- Dettagli dell'autenticazione degli account di accesso interattivi

- Accesso non interattivo

- Dettagli dell'autenticazione degli account di accesso non interattivi

- Account di accesso dell'entità servizio

- Identità gestita per gli account di accesso delle risorse di Azure

Ogni download JSON è costituito da quattro file diversi:

- Accesso interattivo (include i dettagli dell'autenticazione)

- Accesso non interattivo (include i dettagli dell'autenticazione)

- Account di accesso dell'entità servizio

- Identità gestita per gli account di accesso delle risorse di Azure

![Scaricare i file](./media/concept-all-sign-ins/download-files.png "Scaricare i file")




## <a name="next-steps"></a>Passaggi successivi

* [Codici di errore del report delle attività di accesso](reference-sign-ins-error-codes.md)
* [Criteri di conservazione dei report di Azure AD](reference-reports-data-retention.md)
* [Latenze dei report di Azure AD](reference-reports-latencies.md)
