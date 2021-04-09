---
title: Panoramica dei log di provisioning nel portale di Azure (anteprima) | Microsoft Docs
description: Introduzione ai report dei log di provisioning in Azure Active Directory tramite il portale di Azure.
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
ms.date: 1/29/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d8c4876faf9ebc2619309aa0095a8ffe1e9e93d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500547"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Panoramica dei log di provisioning nel portale di Azure (anteprima)

L'architettura di report in Azure Active Directory (Azure AD) include i componenti seguenti:

- Attività: 
    - **Accessi**: informazioni sull'uso delle applicazioni gestite e delle attività di accesso degli utenti.
    - [Log di controllo](concept-audit-logs.md): informazioni relative alle attività di sistema sulla gestione di utenti e gruppi, sulle applicazioni gestite e sulle attività di directory.
    - **Log di provisioning**: attività di sistema relative a utenti, gruppi e ruoli di cui è stato effettuato il provisioning dal servizio di provisioning di Azure ad. 

- Sicurezza: 
    - **Accessi a rischio**: un [accesso rischioso](../identity-protection/overview-identity-protection.md) è un indicatore di un tentativo di accesso che potrebbe essere stato eseguito da un utente che non è il legittimo proprietario di un account utente.
    - **Utenti contrassegnati per il rischio**: un [utente rischioso](../identity-protection/overview-identity-protection.md) è un indicatore per un account utente che potrebbe essere stato compromesso.

Questo argomento offre una panoramica dei log di provisioning. I log forniscono risposte a domande come: 

* Quali gruppi sono stati creati correttamente in ServiceNow?
* Quali utenti sono stati rimossi correttamente da Adobe?
* Quali utenti della giornata lavorativa sono stati creati correttamente in Active Directory? 

## <a name="prerequisites"></a>Prerequisiti

Questi utenti possono accedere ai dati nei log di provisioning:

* Proprietari dell'applicazione (log per le proprie applicazioni)
* Utenti nei ruoli amministratore sicurezza, lettore sicurezza, lettore report, operatore di sicurezza, amministratore applicazione e amministratore applicazione cloud
* Utenti in un ruolo personalizzato con l' [autorizzazione provisioningLogs](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)
* Amministratori globali


Per visualizzare il report delle attività di provisioning, è necessario che al tenant sia associata una licenza di Azure AD Premium. Per aggiornare la Azure AD Edition, vedere la pagina relativa all' [Introduzione a Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md). 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>Modalità di interazione con i log di provisioning 
I clienti possono interagire con i log di provisioning in quattro modi:

- Accesso ai log dalla portale di Azure, come descritto nella sezione successiva.
- Streaming dei log di provisioning in [monitoraggio di Azure](../app-provisioning/application-provisioning-log-analytics.md). Questo metodo consente la conservazione estesa dei dati e la creazione di dashboard, avvisi e query personalizzati.
- Esecuzione di query sull' [API Microsoft Graph](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta) per i log di provisioning.
- Download dei log di provisioning come file CSV o JSON.

## <a name="access-the-logs-from-the-azure-portal"></a>Accedere ai log dalla portale di Azure
È possibile accedere ai log di provisioning selezionando **log di provisioning** nella sezione **monitoraggio** del riquadro **Azure Active Directory** della [portale di Azure](https://portal.azure.com). Potrebbero essere necessarie fino a due ore per la visualizzazione di alcuni record di provisioning nel portale.

![Screenshot che mostra le selezioni per accedere ai log di provisioning.](./media/concept-provisioning-logs/access-provisioning-logs.png "Log di provisioning")


Un log di provisioning ha una visualizzazione elenco predefinita che mostra:

- Identità
- Azione
- Sistema di origine
- Sistema di destinazione
- Stato
- Data


![Screenshot che mostra le colonne predefinite in un log di provisioning.](./media/concept-provisioning-logs/default-columns.png "Colonne predefinite")

È possibile personalizzare la visualizzazione elenco selezionando le **colonne** sulla barra degli strumenti.

![Screenshot che mostra il pulsante per la personalizzazione delle colonne.](./media/concept-provisioning-logs/column-chooser.png "Selezione colonne")

Quest'area consente di visualizzare campi aggiuntivi o di rimuovere i campi già visualizzati.

![Screenshot che mostra le colonne disponibili con alcune selezionate.](./media/concept-provisioning-logs/available-columns.png "Colonne disponibili")

Selezionare un elemento nella visualizzazione elenco per ottenere maggiori informazioni dettagliate.

![Screenshot che mostra informazioni dettagliate.](./media/concept-provisioning-logs/steps.png "Filtra")


## <a name="filter-provisioning-activities"></a>Filtrare le attività di provisioning

È possibile filtrare i dati di provisioning. Alcuni valori di filtro vengono popolati dinamicamente in base al tenant. Se, ad esempio, non sono presenti eventi di "creazione" nel tenant, non sarà disponibile un'opzione **Crea** filtro.

Nella visualizzazione predefinita è possibile selezionare i filtri seguenti:

- **Identità**
- **Data**
- **Status**
- **Azione**


![Screenshot che mostra i valori di filtro.](./media/concept-provisioning-logs/default-filter.png "Filtra")

Il filtro di **identità** consente di specificare il nome o l'identità a cui si è interessati. Questa identità può essere un utente, un gruppo, un ruolo o un altro oggetto. 

È possibile eseguire la ricerca in base al nome o all'ID dell'oggetto. L'ID varia in base allo scenario. Ad esempio, quando si esegue il provisioning di un oggetto da Azure AD a Salesforce, l'ID di origine è l'ID oggetto dell'utente in Azure AD. L'ID di destinazione è l'ID dell'utente in Salesforce. Quando si esegue il provisioning dalla giornata lavorativa alla Active Directory, l'ID di origine è l'ID del dipendente del lavoro lavorativo. 

> [!NOTE]
> Il nome dell'utente potrebbe non essere sempre presente nella colonna **Identity** . Ci sarà sempre un ID. 


Il filtro **Date** (Data) permette di definire un intervallo di tempo per i dati restituiti. I valori possibili sono:

- 1 mese
- 7 giorni
- 30 giorni
- 24 ore
- Intervallo di tempo personalizzato

Quando si seleziona un intervallo di tempo personalizzato, è possibile configurare una data di inizio e una data di fine.

Il filtro **Stato** consente di selezionare:

- **Tutto**
- **Success**
- **Errore**
- **Ignorato**

Il filtro **azione** consente di filtrare le azioni seguenti:

- **Creare** 
- **Aggiornamento**
- **Elimina**
- **Disabilitato**
- **Altri**

Oltre ai filtri della visualizzazione predefinita, è possibile impostare i filtri seguenti.

![Screenshot che mostra i campi che è possibile aggiungere come filtri.](./media/concept-provisioning-logs/add-filter.png "Selezionare un campo")

- **ID processo**: un ID processo univoco è associato a ogni applicazione per la quale è stato abilitato il provisioning.   

- **ID ciclo**: l'ID del ciclo identifica in modo univoco il ciclo di provisioning. È possibile condividere questo ID con il supporto tecnico per cercare il ciclo in cui si è verificato l'evento.

- **ID modifica**: l'ID di modifica è un identificatore univoco per l'evento di provisioning. È possibile condividere questo ID con il supporto tecnico per cercare l'evento di provisioning.   

- **Sistema di origine**: è possibile specificare la posizione da cui viene effettuato il provisioning dell'identità. Ad esempio, quando si esegue il provisioning di un oggetto da Azure AD a ServiceNow, il sistema di origine è Azure AD. 

- **Sistema di destinazione**: è possibile specificare la posizione in cui viene effettuato il provisioning dell'identità. Ad esempio, quando si esegue il provisioning di un oggetto da Azure AD a ServiceNow, il sistema di destinazione è ServiceNow. 

- **Applicazione**: è possibile visualizzare solo i record delle applicazioni con un nome visualizzato che contiene una stringa specifica.

## <a name="provisioning-details"></a>Dettagli del provisioning 

Quando si seleziona un elemento nella visualizzazione elenco di provisioning, si ottengono ulteriori dettagli su questo elemento. I dettagli sono raggruppati nelle schede seguenti.

![Screenshot che mostra quattro schede contenenti i dettagli del provisioning.](./media/concept-provisioning-logs/provisioning-tabs.png "Schede")

- **Passaggi**: delinea i passaggi necessari per eseguire il provisioning di un oggetto. Il provisioning di un oggetto può essere costituito da quattro passaggi:
  
  1. Importare l'oggetto.
  1. Determinare se l'oggetto è nell'ambito.
  1. Corrisponde all'oggetto tra origine e destinazione.
  1. Eseguire il provisioning dell'oggetto (create, Update, DELETE o Disable).

  ![Screenshot mostra i passaggi del provisioning nella scheda passaggi.](./media/concept-provisioning-logs/steps.png "Filtra")

- **Risoluzione dei problemi di & consigli**: fornisce il codice e il motivo dell'errore. Le informazioni sull'errore sono disponibili solo se si verifica un errore.

- **Proprietà modificate**: Mostra il valore precedente e il nuovo valore. Se non è presente alcun valore precedente, la colonna è vuota.

- **Riepilogo**: viene fornita una panoramica delle operazioni eseguite e degli identificatori per l'oggetto nei sistemi di origine e di destinazione.

## <a name="download-logs-as-csv-or-json"></a>Scaricare i log come CSV o JSON

È possibile scaricare i log di provisioning per usarli in un secondo momento passando ai log nel portale di Azure e selezionando **Scarica**. Il file verrà filtrato in base ai criteri di filtro selezionati. Rendere i filtri più specifici possibili per ridurre le dimensioni e l'ora del download. 

Il download di CSV include tre file:

* **ProvisioningLogs**: Scarica tutti i log, ad eccezione dei passaggi di provisioning e delle proprietà modificate.
* **ProvisioningLogs_ProvisioningSteps**: contiene i passaggi di provisioning e l'ID della modifica. È possibile usare l'ID modifica per unire in join l'evento con gli altri due file.
* **ProvisioningLogs_ModifiedProperties**: contiene gli attributi modificati e l'ID della modifica. È possibile usare l'ID modifica per unire in join l'evento con gli altri due file.

#### <a name="open-the-json-file"></a>Aprire il file JSON
Per aprire il file JSON, usare un editor di testo, ad esempio [Microsoft Visual Studio codice](https://aka.ms/vscode). Visual Studio Code semplifica la lettura del file fornendo l'evidenziazione della sintassi. È anche possibile aprire il file JSON usando i browser in un formato non modificabile, ad esempio [Microsoft Edge](https://aka.ms/msedge). 

#### <a name="prettify-the-json-file"></a>Edulcorare il file JSON
Il file JSON viene scaricato nel formato minimizzati per ridurre le dimensioni del download. Questo formato può rendere difficile la lettura del payload. Vedere due opzioni per edulcorare il file:

- Usare [Visual Studio Code per formattare il codice JSON](https://code.visualstudio.com/docs/languages/json#_formatting).

- Usare PowerShell per formattare il codice JSON. Questo script restituirà il codice JSON in un formato che include tabulazioni e spazi: 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>Analizzare il file JSON

Di seguito sono riportati alcuni comandi di esempio per lavorare con il file JSON usando PowerShell. È possibile usare qualsiasi linguaggio di programmazione con cui si ha familiarità.  

Per prima cosa, [leggere il file JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) eseguendo questo comando:

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

A questo punto è possibile analizzare i dati in base allo scenario. Ecco alcuni esempi: 

- Restituire tutti gli ID processo nel file JSON:

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Genera l'output di tutti gli ID modifiche per gli eventi in cui l'azione è stata creata:

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>Informazioni utili

Ecco alcuni suggerimenti e considerazioni per il provisioning dei report:

- Il portale di Azure archivia i dati di provisioning segnalati per 30 giorni se si dispone di un'edizione Premium e di 7 giorni se si dispone di un'edizione gratuita. È possibile pubblicare i log di provisioning in [log Analytics](../app-provisioning/application-provisioning-log-analytics.md) per la conservazione oltre i 30 giorni. 

- È possibile usare l'attributo Change ID come identificatore univoco. Questa operazione è utile quando si interagisce con il supporto del prodotto, ad esempio.

- È possibile che vengano visualizzati eventi ignorati per gli utenti che non sono inclusi nell'ambito. Questo comportamento è previsto, soprattutto quando l'ambito di sincronizzazione è impostato su tutti gli utenti e i gruppi. Il servizio valuterà tutti gli oggetti nel tenant, anche quelli che non rientrano nell'ambito. 

- I log di provisioning non sono attualmente disponibili nel cloud per enti pubblici. Se non è possibile accedere ai log di provisioning, usare i log di controllo come soluzione temporanea. 

- I log di provisioning non mostrano le importazioni di ruolo (si applica a AWS, Salesforce e Zendesk). È possibile trovare i log per le importazioni dei ruoli nei log di controllo. 

## <a name="error-codes"></a>Codici di errore

Usare la tabella seguente per comprendere meglio come risolvere gli errori che si trovano nei log di provisioning. Per i codici di errore mancanti, fornire commenti e suggerimenti usando il collegamento nella parte inferiore della pagina. 

|Codice di errore|Descrizione|
|---|---|
|Conflitto, EntryConflict|Correggere i valori di attributo in conflitto in Azure AD o nell'applicazione. In alternativa, rivedere la configurazione dell'attributo corrispondente se si suppone che l'account utente in conflitto sia stato trovato e sottomesso a riprenderlo. Per ulteriori informazioni sulla configurazione degli attributi corrispondenti, consultare la [documentazione](../app-provisioning/customize-application-attributes.md) .|
|TooManyRequests|L'app di destinazione ha rifiutato questo tentativo di aggiornare l'utente perché è sovraccarico e riceve troppe richieste. Non c'è niente da fare. Il tentativo verrà ritirato automaticamente. Microsoft ha inoltre ricevuto una notifica di questo problema.|
|InternalServerError |L'app di destinazione ha restituito un errore imprevisto. Un problema del servizio con l'applicazione di destinazione potrebbe impedire l'esecuzione di questa operazione. Questo tentativo verrà ritirato automaticamente tra 40 minuti.|
|InsufficientRights, MethodNotAllowed, NotPermitted, non autorizzato| Azure AD autenticato con l'applicazione di destinazione, ma non è stato autorizzato a eseguire l'aggiornamento. Esaminare le istruzioni fornite dall'applicazione di destinazione, insieme all' [esercitazione](../saas-apps/tutorial-list.md)relativa all'applicazione corrispondente.|
|UnprocessableEntity|L'applicazione di destinazione ha restituito una risposta imprevista. La configurazione dell'applicazione di destinazione potrebbe non essere corretta o un problema del servizio con l'applicazione di destinazione potrebbe impedire l'esecuzione di questa operazione.|
|WebExceptionProtocolError |Si è verificato un errore del protocollo HTTP durante la connessione all'applicazione di destinazione. Non è necessario eseguire alcuna operazione. Questo tentativo verrà ritirato automaticamente tra 40 minuti.|
|InvalidAnchor|Un utente creato in precedenza o corrispondente al servizio di provisioning non esiste più. Verificare che l'utente esista. Per forzare una nuova corrispondenza di tutti gli utenti, usare l'API Microsoft Graph per [riavviare il processo](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). <br><br>Il riavvio del provisioning attiverà un ciclo iniziale, operazione che può richiedere del tempo. Il riavvio del provisioning Elimina anche la cache utilizzata dal servizio di provisioning per il funzionamento. Ciò significa che tutti gli utenti e i gruppi nel tenant dovranno essere nuovamente valutati e alcuni eventi di provisioning potrebbero essere eliminati.|
|NotImplemented | L'app di destinazione ha restituito una risposta imprevista. La configurazione dell'app potrebbe non essere corretta o un problema del servizio con l'app di destinazione potrebbe impedire l'esecuzione di questa operazione. Esaminare le istruzioni fornite dall'applicazione di destinazione, insieme all' [esercitazione](../saas-apps/tutorial-list.md)relativa all'applicazione corrispondente. |
|MandatoryFieldsMissing, MissingValues |Impossibile creare l'utente perché mancano i valori obbligatori. Correggere i valori degli attributi mancanti nel record di origine oppure verificare la configurazione dell'attributo corrispondente per assicurarsi che i campi obbligatori non vengano omessi. [Altre](../app-provisioning/customize-application-attributes.md) informazioni sulla configurazione degli attributi corrispondenti.|
|SchemaAttributeNotFound |Non è stato possibile eseguire l'operazione perché è stato specificato un attributo che non esiste nell'applicazione di destinazione. Vedere la [documentazione](../app-provisioning/customize-application-attributes.md) sulla personalizzazione degli attributi e verificare che la configurazione sia corretta.|
|InternalError |Si è verificato un errore interno del servizio all'interno del servizio di provisioning Azure AD. Non è necessario eseguire alcuna operazione. Questo tentativo verrà ritentato automaticamente tra 40 minuti.|
|InvalidDomain |Non è stato possibile eseguire l'operazione perché un valore di attributo contiene un nome di dominio non valido. Aggiornare il nome di dominio dell'utente o aggiungerlo all'elenco delle applicazioni consentite nell'applicazione di destinazione. |
|Timeout |Non è stato possibile completare l'operazione perché l'applicazione di destinazione ha impiegato troppo tempo per rispondere. Non è necessario eseguire alcuna operazione. Questo tentativo verrà ritentato automaticamente tra 40 minuti.|
|LicenseLimitExceeded|Non è stato possibile creare l'utente nell'applicazione di destinazione perché non sono disponibili licenze per questo utente. Procurarsi altre licenze per l'applicazione di destinazione. In alternativa, esaminare le assegnazioni degli utenti e la configurazione del mapping degli attributi per assicurarsi che gli utenti corretti siano assegnati con gli attributi corretti.|
|DuplicateTargetEntries  |Non è stato possibile completare l'operazione perché è stato trovato più di un utente nell'applicazione di destinazione con gli attributi corrispondenti configurati. Rimuovere l'utente duplicato dall'applicazione di destinazione o [riconfigurare i mapping degli attributi](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | Non è stato possibile completare l'operazione perché è stato trovato più di un utente con gli attributi corrispondenti configurati. Rimuovere l'utente duplicato o [riconfigurare i mapping degli attributi](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | Quando ogni utente viene valutato, il sistema tenta di importare l'utente dal sistema di origine. Questo errore si verifica in genere quando all'utente che viene importato manca la proprietà corrispondente definita nei mapping degli attributi. Senza un valore presente nell'oggetto utente per l'attributo corrispondente, il sistema non può valutare le modifiche dell'ambito, della corrispondenza o dell'esportazione. Si noti che la presenza di questo errore non indica che l'utente è nell'ambito, perché non è ancora stata valutata la definizione dell'ambito per l'utente.|
|EntrySynchronizationSkipped | Il servizio di provisioning ha eseguito una query sul sistema di origine e ha identificato l'utente. Non è stata eseguita alcuna azione aggiuntiva per l'utente e i relativi elementi sono stati ignorati. L'utente potrebbe essere esterno all'ambito oppure è possibile che l'utente sia già presente nel sistema di destinazione senza che siano necessarie altre modifiche.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Una richiesta GET per recuperare un utente o un gruppo ha ricevuto più utenti o gruppi nella risposta. Il sistema prevede di ricevere solo un utente o un gruppo nella risposta. Se [ad esempio](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)si esegue una richiesta GET per recuperare un gruppo e si fornisce un filtro per escludere i membri e il sistema per l'endpoint di gestione delle identità tra domini (SCIM) restituisce i membri, viene restituito questo errore.|

## <a name="next-steps"></a>Passaggi successivi

* [Verificare lo stato del provisioning utenti](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Problemi di configurazione del provisioning utenti in un'applicazione della raccolta di Azure AD](../app-provisioning/application-provisioning-config-problem.md)
* [API Graph per il provisioning dei log](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)