---
title: Panoramica dei log di provisioning nel portale di Azure (anteprima) | Microsoft Docs
description: Introduzione ai report di log di provisioning Azure Active Directory tramite il portale di Azure.
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
ms.openlocfilehash: 468e885bab6aab4becb5aaaec7b4d52ce5ef5e07
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535994"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Panoramica dei log di provisioning nel portale di Azure (anteprima)

L'architettura di report in Azure Active Directory (Azure AD) include i componenti seguenti:

- Attività: 
    - **Accesso: informazioni sull'utilizzo** di applicazioni gestite e attività di accesso utente.
    - [Log di controllo:](concept-audit-logs.md)informazioni sulle attività di sistema relative alla gestione di utenti e gruppi, applicazioni gestite e attività della directory.
    - **Log di provisioning:** attività di sistema relative a utenti, gruppi e ruoli di cui viene effettuato il provisioning dal Azure AD di provisioning. 

- Sicurezza: 
    - **Accesso rischioso:** un [](../identity-protection/overview-identity-protection.md) accesso rischioso è un indicatore per un tentativo di accesso che potrebbe essere stato eseguito da un utente che non è il legittimo proprietario di un account utente.
    - **Utenti contrassegnati per il rischio:** un utente a [rischio](../identity-protection/overview-identity-protection.md) è un indicatore per un account utente che potrebbe essere stato compromesso.

Questo argomento offre una panoramica dei log di provisioning. I log forniscono risposte a domande quali: 

* Quali gruppi sono stati creati correttamente in ServiceNow?
* Quali utenti sono stati rimossi correttamente da Adobe?
* Quali utenti di Workday sono stati creati correttamente in Active Directory? 

## <a name="prerequisites"></a>Prerequisiti

Questi utenti possono accedere ai dati nei log di provisioning:

* Proprietari dell'applicazione (log per le proprie applicazioni)
* Utenti con i ruoli Amministratore sicurezza, Lettore sicurezza, Lettore di report, Operatore di sicurezza, Amministratore applicazioni e Amministratore applicazioni cloud
* Utenti in un ruolo personalizzato con [l'autorizzazione provisioningLogs](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)
* Amministratori globali


Per visualizzare il report dell'attività di provisioning, al tenant deve essere associata Azure AD Premium licenza. Per aggiornare l'Azure AD, vedere [Introduzione a Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md). 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>Modalità di interazione con i log di provisioning 
I clienti possono interagire con i log di provisioning in quattro modi:

- Accesso ai log dal portale di Azure, come descritto nella sezione successiva.
- Streaming dei log di provisioning [in Monitoraggio di Azure](../app-provisioning/application-provisioning-log-analytics.md). Questo metodo consente la conservazione dei dati estesa e la creazione di dashboard, avvisi e query personalizzati.
- Esecuzione di query [sull Microsoft Graph API](/graph/api/resources/provisioningobjectsummary) per i log di provisioning.
- Download dei log di provisioning come file CSV o JSON.

## <a name="access-the-logs-from-the-azure-portal"></a>Accedere ai log dal portale di Azure
È possibile accedere ai log di  provisioning selezionando **Log di provisioning** nella sezione Monitoraggio del riquadro Azure Active Directory **nella** [portale di Azure](https://portal.azure.com). La visualizzazione di alcuni record di provisioning nel portale può richiedere fino a due ore.

![Screenshot che mostra le selezioni per l'accesso ai log di provisioning.](./media/concept-provisioning-logs/access-provisioning-logs.png "Log di provisioning")


Un log di provisioning ha una visualizzazione elenco predefinita che mostra:

- Identità
- Azione
- Sistema di origine
- Sistema di destinazione
- Stato
- Data


![Screenshot che mostra le colonne predefinite in un log di provisioning.](./media/concept-provisioning-logs/default-columns.png "Colonne predefinite")

È possibile personalizzare la visualizzazione elenco selezionando **Colonne sulla barra** degli strumenti.

![Screenshot che mostra il pulsante per la personalizzazione delle colonne.](./media/concept-provisioning-logs/column-chooser.png "Selezione colonne")

Questa area consente di visualizzare campi aggiuntivi o rimuovere i campi già visualizzati.

![Screenshot che mostra le colonne disponibili con alcune colonne selezionate.](./media/concept-provisioning-logs/available-columns.png "Colonne disponibili")

Selezionare un elemento nella visualizzazione elenco per ottenere maggiori informazioni dettagliate.

![Screenshot che mostra informazioni dettagliate.](./media/concept-provisioning-logs/steps.png "Filtra")


## <a name="filter-provisioning-activities"></a>Filtrare le attività di provisioning

È possibile filtrare i dati di provisioning. Alcuni valori di filtro vengono popolati dinamicamente in base al tenant. Se, ad esempio, non sono presenti eventi di "creazione" nel tenant, non sarà disponibile **un'opzione di** filtro Crea.

Nella visualizzazione predefinita è possibile selezionare i filtri seguenti:

- **Identità**
- **Data**
- **Status**
- **Azione**


![Screenshot che mostra i valori di filtro.](./media/concept-provisioning-logs/default-filter.png "Filtra")

Il **filtro** Identità consente di specificare il nome o l'identità a cui si è a cuore. Questa identità può essere un utente, un gruppo, un ruolo o un altro oggetto. 

È possibile eseguire la ricerca in base al nome o all'ID dell'oggetto. L'ID varia in base allo scenario. Ad esempio, quando si esegue il provisioning di un oggetto da Azure AD a Salesforce, l'ID di origine è l'ID oggetto dell'utente in Azure AD. L'ID di destinazione è l'ID dell'utente in Salesforce. Quando si esegue il provisioning da Workday ad Active Directory, l'ID di origine è l'ID dipendente del ruolo di lavoro di Workday. 

> [!NOTE]
> Il nome dell'utente potrebbe non essere sempre presente nella **colonna** Identity. Sarà sempre presente un ID. 


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
- **Operazioni non riuscite**
- **Ignorato**

Il **filtro** Azione consente di filtrare queste azioni:

- **Creare** 
- **Aggiornamento**
- **Elimina**
- **Disabilitato**
- **Altri**

Oltre ai filtri della visualizzazione predefinita, è possibile impostare i filtri seguenti.

![Screenshot che mostra i campi che è possibile aggiungere come filtri.](./media/concept-provisioning-logs/add-filter.png "Selezionare un campo")

- **ID processo:** un ID processo univoco è associato a ogni applicazione per cui è stato abilitato il provisioning.   

- **ID ciclo:** l'ID del ciclo identifica in modo univoco il ciclo di provisioning. È possibile condividere questo ID con il supporto tecnico per cercare il ciclo in cui si è verificato l'evento.

- **ID modifica:** l'ID modifica è un identificatore univoco per l'evento di provisioning. È possibile condividere questo ID con il supporto tecnico per cercare l'evento di provisioning.   

- **Sistema di origine:** è possibile specificare la posizione da cui viene effettuato il provisioning dell'identità. Ad esempio, quando si esegue il provisioning di un oggetto da Azure AD a ServiceNow, il sistema di origine viene Azure AD. 

- **Sistema di destinazione:** è possibile specificare dove viene effettuato il provisioning dell'identità. Ad esempio, quando si esegue il provisioning di un oggetto da Azure AD a ServiceNow, il sistema di destinazione è ServiceNow. 

- **Applicazione:** è possibile visualizzare solo i record delle applicazioni con un nome visualizzato che contiene una stringa specifica.

## <a name="provisioning-details"></a>Dettagli del provisioning 

Quando si seleziona un elemento nella visualizzazione elenco di provisioning, si ottengono altri dettagli su questo elemento. I dettagli sono raggruppati nelle schede seguenti.

![Screenshot che mostra quattro schede che contengono i dettagli del provisioning.](./media/concept-provisioning-logs/provisioning-tabs.png "Schede")

- **Passaggi:** descrive i passaggi necessari per effettuare il provisioning di un oggetto. Il provisioning di un oggetto può essere costituito da quattro passaggi:
  
  1. Importare l'oggetto .
  1. Determinare se l'oggetto è nell'ambito.
  1. Trovare la corrispondenza tra l'oggetto tra l'origine e la destinazione.
  1. Effettuare il provisioning dell'oggetto (creazione, aggiornamento, eliminazione o disabilitazione).

  ![Screenshot che mostra i passaggi di provisioning nella scheda Passaggi.](./media/concept-provisioning-logs/steps.png "Filtra")

- **Risoluzione & raccomandazioni:** fornisce il codice di errore e il motivo. Le informazioni sull'errore sono disponibili solo se si verifica un errore.

- **Proprietà modificate**: mostra il valore precedente e il nuovo valore. Se non è presente alcun valore precedente, la colonna è vuota.

- **Riepilogo:** offre una panoramica dell'evento e degli identificatori per l'oggetto nei sistemi di origine e di destinazione.

## <a name="download-logs-as-csv-or-json"></a>Scaricare i log come CSV o JSON

È possibile scaricare i log di provisioning per usarli in un secondo momento accedendo ai log nella portale di Azure e selezionando **Scarica**. Il file verrà filtrato in base ai criteri di filtro selezionati. Rendere i filtri il più specifici possibile per ridurre le dimensioni e il tempo del download. 

Il download CSV include tre file:

* **ProvisioningLogs:** scarica tutti i log, ad eccezione dei passaggi di provisioning e delle proprietà modificate.
* **ProvisioningLogs_ProvisioningSteps**: contiene i passaggi di provisioning e l'ID modifica. È possibile usare l'ID modifica per aggiungere l'evento agli altri due file.
* **ProvisioningLogs_ModifiedProperties**: contiene gli attributi modificati e l'ID modifica. È possibile usare l'ID modifica per aggiungere l'evento agli altri due file.

#### <a name="open-the-json-file"></a>Aprire il file JSON
Per aprire il file JSON, usare un editor di testo, ad esempio [Microsoft Visual Studio Code](https://aka.ms/vscode). Visual Studio Code semplifica la lettura del file fornendo l'evidenziazione della sintassi. È anche possibile aprire il file JSON usando i browser in un formato non modificabile, ad esempio [Microsoft Edge](https://aka.ms/msedge). 

#### <a name="prettify-the-json-file"></a>Prettificare il file JSON
Il file JSON viene scaricato in formato minimificato per ridurre le dimensioni del download. Questo formato può rendere difficile la lettura del payload. Per prefiggere il file, vedere due opzioni:

- Usare [Visual Studio Code per formattare l'oggetto JSON.](https://code.visualstudio.com/docs/languages/json#_formatting)

- Usare PowerShell per formattare il codice JSON. Questo script restituisce il codice JSON in un formato che include tabulazioni e spazi: 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>Analizzare il file JSON

Ecco alcuni comandi di esempio da usare con il file JSON con PowerShell. È possibile usare qualsiasi linguaggio di programmazione con cui si ha familiarità.  

Per prima [cosa, leggere il file JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) eseguendo questo comando:

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

È ora possibile analizzare i dati in base allo scenario. Ecco alcuni esempi: 

- Output di tutti gli ID processo nel file JSON:

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Output di tutti gli ID di modifica per gli eventi in cui l'azione è stata "create":

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>Informazioni utili

Ecco alcuni suggerimenti e considerazioni per il provisioning dei report:

- Il portale di Azure archivia i dati di provisioning segnalati per 30 giorni se si ha un'edizione Premium e 7 giorni se si ha un'edizione gratuita. È possibile pubblicare i log di provisioning in [Log Analytics per](../app-provisioning/application-provisioning-log-analytics.md) la conservazione oltre 30 giorni. 

- È possibile usare l'attributo CHANGE ID come identificatore univoco. Ciò è utile, ad esempio, quando si interagisce con il supporto tecnico.

- È possibile che gli eventi ignorati siano visualizzati per gli utenti che non sono nell'ambito. Questo comportamento è previsto, soprattutto quando l'ambito di sincronizzazione è impostato su tutti gli utenti e i gruppi. Il servizio valuterà tutti gli oggetti nel tenant, anche quelli che non sono nell'ambito. 

- I log di provisioning non sono attualmente disponibili nel cloud per enti pubblici. Se non è possibile accedere ai log di provisioning, usare i log di controllo come soluzione temporanea. 

- I log di provisioning non mostrano le importazioni di ruoli (si applica ad AWS, Salesforce e Zendesk). È possibile trovare i log per le importazioni di ruoli nei log di controllo. 

## <a name="error-codes"></a>Codici di errore

Usare la tabella seguente per comprendere meglio come risolvere gli errori rilevati nei log di provisioning. Per eventuali codici di errore mancanti, inviare commenti e suggerimenti usando il collegamento nella parte inferiore di questa pagina. 

|Codice di errore|Descrizione|
|---|---|
|Conflict, EntryConflict|Correggere i valori di attributo in conflitto nell'Azure AD o nell'applicazione. In caso contrario, esaminare la configurazione dell'attributo corrispondente se l'account utente in conflitto deve essere abbinato e assunto. Per altre [informazioni sulla](../app-provisioning/customize-application-attributes.md) configurazione degli attributi corrispondenti, vedere la documentazione.|
|TooManyRequests|L'app di destinazione ha rifiutato questo tentativo di aggiornare l'utente perché è sovraccarico e riceve troppe richieste. Non è necessario eseguire alcuna operazione. Questo tentativo verrà ritirato automaticamente. Microsoft ha anche notificato questo problema.|
|InternalServerError |L'app di destinazione ha restituito un errore imprevisto. Un problema del servizio con l'applicazione di destinazione potrebbe impedire il funzionamento. Questo tentativo verrà ritirato automaticamente tra 40 minuti.|
|InsufficientRights, MethodNotAllowed, NotPermitted, Unauthorized| Azure AD autenticato con l'applicazione di destinazione, ma non è stato autorizzato a eseguire l'aggiornamento. Esaminare le istruzioni fornite dall'applicazione di destinazione, insieme alla rispettiva esercitazione [sull'applicazione](../saas-apps/tutorial-list.md).|
|UnprocessableEntity|L'applicazione di destinazione ha restituito una risposta imprevista. La configurazione dell'applicazione di destinazione potrebbe non essere corretta o un problema del servizio con l'applicazione di destinazione potrebbe impedire il funzionamento.|
|WebExceptionProtocolError |Si è verificato un errore del protocollo HTTP durante la connessione all'applicazione di destinazione. Non c'è nulla da fare. Questo tentativo verrà ritirato automaticamente tra 40 minuti.|
|InvalidAnchor|Un utente creato in precedenza o corrispondente al servizio di provisioning non esiste più. Assicurarsi che l'utente esista. Per forzare una nuova corrispondenza di tutti gli utenti, usare l'API Microsoft Graph per [riavviare il processo](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). <br><br>Il riavvio del provisioning attiverà un ciclo iniziale, che può richiedere tempo per il completamento. Il riavvio del provisioning elimina anche la cache utilizzata dal servizio di provisioning per operare. Ciò significa che tutti gli utenti e i gruppi nel tenant devono essere nuovamente valutati e alcuni eventi di provisioning potrebbero essere eliminati.|
|NotImplemented | L'app di destinazione ha restituito una risposta imprevista. La configurazione dell'app potrebbe non essere corretta o un problema del servizio con l'app di destinazione potrebbe impedire il funzionamento. Esaminare le istruzioni fornite dall'applicazione di destinazione, insieme alla rispettiva esercitazione [sull'applicazione](../saas-apps/tutorial-list.md). |
|MandatoryFieldsMissing, MissingValues |Impossibile creare l'utente perché mancano i valori obbligatori. Correggere i valori degli attributi mancanti nel record di origine oppure esaminare la configurazione dell'attributo corrispondente per assicurarsi che i campi obbligatori non siano omessi. [Altre informazioni sulla](../app-provisioning/customize-application-attributes.md) configurazione degli attributi corrispondenti.|
|SchemaAttributeNotFound |Impossibile eseguire l'operazione perché è stato specificato un attributo che non esiste nell'applicazione di destinazione. Vedere la [documentazione sulla](../app-provisioning/customize-application-attributes.md) personalizzazione degli attributi e verificare che la configurazione sia corretta.|
|InternalError |Si è verificato un errore interno del servizio all'interno Azure AD di provisioning. Non c'è nulla da fare. Questo tentativo verrà ritentato automaticamente tra 40 minuti.|
|InvalidDomain |Impossibile eseguire l'operazione perché un valore di attributo contiene un nome di dominio non valido. Aggiornare il nome di dominio dell'utente o aggiungerlo all'elenco consentito nell'applicazione di destinazione. |
|Timeout |Impossibile completare l'operazione perché l'applicazione di destinazione ha impiegato troppo tempo per rispondere. Non c'è nulla da fare. Questo tentativo verrà ritentato automaticamente tra 40 minuti.|
|LicenseLimitExceeded|Non è stato possibile creare l'utente nell'applicazione di destinazione perché non sono disponibili licenze per l'utente. Acquistare altre licenze per l'applicazione di destinazione. In caso contrario, esaminare le assegnazioni utente e la configurazione del mapping degli attributi per assicurarsi che gli utenti corretti siano assegnati con gli attributi corretti.|
|DuplicateTargetEntries  |Impossibile completare l'operazione perché sono stati trovati più utenti nell'applicazione di destinazione con gli attributi corrispondenti configurati. Rimuovere l'utente duplicato dall'applicazione di destinazione o [riconfigurare i mapping degli attributi](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | Impossibile completare l'operazione perché sono stati trovati più utenti con gli attributi corrispondenti configurati. Rimuovere l'utente duplicato o [riconfigurare i mapping degli attributi.](../app-provisioning/customize-application-attributes.md)|
|ImportSkipped | Quando ogni utente viene valutato, il sistema tenta di importare l'utente dal sistema di origine. Questo errore si verifica in genere quando all'utente importato manca la proprietà corrispondente definita nei mapping degli attributi. Senza un valore presente nell'oggetto utente per l'attributo corrispondente, il sistema non può valutare le modifiche all'ambito, alla corrispondenza o all'esportazione. Si noti che la presenza di questo errore non indica che l'utente è nell'ambito, perché non è ancora stato valutato l'ambito per l'utente.|
|EntrySynchronizationSkipped | Il servizio di provisioning ha eseguito correttamente una query sul sistema di origine e ha identificato l'utente. Non sono state intraprese altre azioni sull'utente e sono state ignorate. È possibile che l'utente sia fuori ambito o che l'utente sia già presente nel sistema di destinazione senza ulteriori modifiche necessarie.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Una richiesta GET per recuperare un utente o un gruppo ha ricevuto più utenti o gruppi nella risposta. Il sistema prevede di ricevere un solo utente o gruppo nella risposta. Ad [esempio,](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)se si fa una richiesta GET per recuperare un gruppo e si fornisce un filtro per escludere i membri e l'endpoint SYSTEM for Cross-Domain Identity Management (SCIM) restituisce i membri, si otterrà questo errore.|

## <a name="next-steps"></a>Passaggi successivi

* [Controllare lo stato del provisioning utenti](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Problemi di configurazione del provisioning utenti in un'applicazione della raccolta di Azure AD](../app-provisioning/application-provisioning-config-problem.md)
* [API Graph per i log di provisioning](/graph/api/resources/provisioningobjectsummary)