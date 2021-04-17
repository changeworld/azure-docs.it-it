---
title: Report delle attività di accesso nel portale di Azure Active Directory | Microsoft Docs
description: Introduzione ai report delle attività di accesso nel portale di Azure Active Directory
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
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99f1f27cb087dc83295dddade4c0fca551a0d9c9
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589687"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Report delle attività di accesso nel portale di Azure Active Directory

Il Azure Active Directory portale consente di accedere a tre log attività:

- **Accesso: informazioni sugli** account di accesso e sul modo in cui le risorse vengono usate dagli utenti.
- **[Controllo:](concept-audit-logs.md)** informazioni sulle modifiche applicate al tenant, ad esempio la gestione di utenti e gruppi o gli aggiornamenti applicati alle risorse del tenant.
- **[Provisioning:](concept-provisioning-logs.md)** attività eseguite dal servizio di provisioning, ad esempio la creazione di un gruppo in ServiceNow o un utente importato da Workday.

Questo articolo offre una panoramica del report di accesso.

## <a name="prerequisites"></a>Prerequisiti

### <a name="who-can-access-the-data"></a>Chi può accedere ai dati?

* Utenti con i ruoli Amministratore sicurezza, Lettore sicurezza, Lettore globale e Lettore di report
* Amministratori globali
* Qualsiasi utente (non amministratore) può visualizzare i propri accessi 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Quale licenza di Azure AD è necessaria per visualizzare le attività di accesso?

Il report attività di accesso è disponibile in tutte [le edizioni](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) di Azure AD ed è accessibile anche tramite l'API Microsoft Graph.

## <a name="sign-ins-report"></a>Report sugli accessi

Il report relativo agli accessi utente fornisce le risposte alle domande seguenti:

* Qual è il modello di accesso di un utente?
* Quanti utenti hanno effettuato l'accesso nell'arco di una settimana?
* Qual è lo stato di questi accessi?

Nel menu [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** oppure cercare e selezionare Azure Active Directory **da** qualsiasi pagina.

![Selezionare Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

In **Monitoraggio** selezionare **Sign-ins (Accedi)** per aprire il [report Sign-ins (Accedi).](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)

![Screenshot che mostra gli accesso selezionati dal menu Monitoraggio.](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Attività di accesso")

La visualizzazione di alcuni record di accesso nel portale può richiedere fino a due ore.

> [!IMPORTANT]
> Il report degli accessi mostra solo gli accessi **interattivi**, ovvero gli accessi eseguiti manualmente da un utente usando nome utente e password. Gli accessi non interattivi, ad esempio l'autenticazione da servizio a servizio, non vengono visualizzati nel report degli accessi. 

Un log di accesso ha una visualizzazione elenco predefinita che include:

- Data di accesso
- Utente correlato
- Applicazione a cui l'utente ha eseguito l'accesso
- Stato dell'accesso
- Stato di rilevamento rischi
- Stato del requisito di autenticazione a più fattori (MFA)

![Screenshot che mostra gli accesso a Office 365 SharePoint Online.](./media/concept-sign-ins/sign-in-activity.png "Attività di accesso")

È possibile personalizzare la visualizzazione elenco facendo clic **su Colonne** sulla barra degli strumenti.

![Screenshot che mostra l'opzione Colonne nella pagina Accedi.](./media/concept-sign-ins/19.png "Attività di accesso")

La **finestra** di dialogo Colonne consente di accedere agli attributi selezionabili. In un report di accesso non è possibile avere campi con più di un valore per una determinata richiesta di accesso come colonna. Questo vale, ad esempio, per i dettagli dell'autenticazione, i dati di accesso condizionale e il percorso di rete.   

![Screenshot che mostra la finestra di dialogo Colonne in cui è possibile selezionare gli attributi.](./media/concept-sign-ins/columns.png "Attività di accesso")

Selezionare un elemento nella visualizzazione elenco per ottenere maggiori informazioni dettagliate.

![Screenshot che mostra una visualizzazione dettagliata delle informazioni.](./media/concept-sign-ins/basic-sign-in.png "Attività di accesso")

> [!NOTE]
> I clienti possono ora risolvere i problemi relativi ai criteri di accesso condizionale tramite tutti i report di accesso. Facendo clic sulla scheda **Accesso** condizionale per un record di accesso, i clienti possono esaminare lo stato di accesso condizionale e approfondire i dettagli dei criteri applicati all'accesso e il risultato per ogni criterio.
> Per altre informazioni, vedere [Domande frequenti sulle informazioni di CA in tutti gli accessi](reports-faq.md#conditional-access).


## <a name="sign-in-error-code"></a>Codice dell'errore di accesso

Se un accesso non è riuscito, è possibile ottenere altre informazioni sul motivo nella sezione **Informazioni di base** dell'elemento di log correlato. 

![codice di errore di accesso](./media/concept-all-sign-ins/error-code.png)
 
Anche se l'elemento di log fornisce un motivo di errore, in alcuni casi è possibile ottenere altre informazioni usando lo strumento di ricerca degli [errori di accesso](https://login.microsoftonline.com/error). Ad esempio, se disponibile, questo strumento fornisce i passaggi di correzione.  

![Strumento di ricerca del codice di errore](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities&quot;></a>Filtrare le attività di accesso

In primo luogo, restringere i dati segnalati a un livello che funzioni per l'utente. Filtrare quindi i dati di accesso usando il campo data come filtro predefinito. Azure AD offre un'ampia gamma di filtri aggiuntivi che è possibile impostare:

![Screenshot che mostra l'opzione Aggiungi filtri.](./media/concept-sign-ins/04.png &quot;Attività di accesso")

**ID richiesta:** ID della richiesta che interessa.

**Utente:** il nome o il nome dell'entità utente (UPN) dell'utente a cui si è a cui si è a cui si è in cura.

**Applicazione:** nome dell'applicazione di destinazione.
 
**Stato:** lo stato di accesso a cui si è a cui si è attenzione:

- Operazione completata

- Operazioni non riuscite

- Interrotto


**Indirizzo IP:** l'indirizzo IP del dispositivo usato per connettersi al tenant.

**Percorso:** la posizione da cui è stata avviata la connessione:

- City

- Stato/Provincia

- Paese/Area geografica


**Risorsa:** nome del servizio usato per l'accesso.


**ID risorsa:** ID del servizio usato per l'accesso.


**App client:** tipo di app client usata per connettersi al tenant:

![Filtro dell'app client](./media/concept-sign-ins/client-app-filter.png)


|Nome|Autenticazione moderna|Descrizione|
|---|:-:|---|
|SMTP autenticato| |Usato dai client POP e IMAP per inviare messaggi di posta elettronica.|
|Autodiscover| |Usato dai client Outlook ed EAS per trovare e connettersi alle cassette postali in Exchange Online.|
|Exchange ActiveSync| |Questo filtro mostra tutti i tentativi di accesso in cui è stato tentato il protocollo EAS.|
|Browser|![Segno di spunta blu.](./media/concept-sign-ins/check.png)|Mostra tutti i tentativi di accesso degli utenti tramite Web browser|
|Exchange ActiveSync| | Mostra tutti i tentativi di accesso degli utenti con app client che usano Exchange ActiveSync per connettersi a Exchange Online|
|PowerShell per Exchange Online| |Usato per connettersi a Exchange Online con PowerShell remoto. Se si blocca l'autenticazione di base per Exchange Online PowerShell, è necessario usare il modulo PowerShell di Exchange Online per connettersi. Per istruzioni, vedere [Connettersi a Exchange Online PowerShell con l'autenticazione a più fattori](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Servizi Web Exchange| |Interfaccia di programmazione usata da Outlook, Outlook per Mac e app di terze parti.|
|IMAP4| |Un client di posta elettronica legacy che usa IMAP per recuperare la posta elettronica.|
|MAPI su HTTP| |Usato da Outlook 2010 e versioni successive.|
|App per dispositivi mobili e client desktop|![Segno di spunta blu.](./media/concept-sign-ins/check.png)|Mostra tutti i tentativi di accesso degli utenti che usano app per dispositivi mobili e client desktop.|
|Offline Rubrica| |Copia delle raccolte di elenchi di indirizzi scaricate e usate da Outlook.|
|Outlook via Internet (RPC su HTTP)| |Usato da Outlook 2016 e versioni precedenti.|
|Servizio Outlook| |Usato dall'app Posta e calendario per Windows 10.|
|POP3| |Un client di posta elettronica legacy che usa POP3 per recuperare la posta elettronica.|
|Reporting Web Services| |Usato per recuperare i dati del report in Exchange Online.|
|Altri client| |Mostra tutti i tentativi di accesso degli utenti in cui l'app client non è inclusa o sconosciuta.|



**Sistema operativo:** il sistema operativo in esecuzione nel dispositivo ha usato l'accesso al tenant. 


**Browser di dispositivi:** se la connessione è stata avviata da un browser, questo campo consente di filtrare in base al nome del browser.


**ID correlazione:** ID di correlazione dell'attività.




**Accesso condizionale:** stato delle regole di accesso condizionale applicate

- **Non applicato:** nessun criterio applicato all'utente e all'applicazione durante l'accesso.

- **Operazione** riuscita: uno o più criteri di accesso condizionale applicati all'utente e all'applicazione (ma non necessariamente alle altre condizioni) durante l'accesso. 

- **Errore:** l'accesso ha soddisfatto la condizione dell'utente e dell'applicazione di almeno un criterio di accesso condizionale e i controlli di concessione non sono soddisfatti o impostati per bloccare l'accesso.









## <a name="download-sign-in-activities"></a>Scaricare le attività di accesso

Fare clic **sull'opzione** Download per creare un file CSV o JSON dei 250.000 record più recenti. Per [iniziare, scaricare i dati di accesso](quickstart-download-sign-in-report.md) se si vuole usare i dati all'esterno del portale di Azure.  

![Scaricare](./media/concept-sign-ins/71.png "Scarica")

> [!IMPORTANT]
> Il numero di record che è possibile scaricare è limitato dai [criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Tasti di scelta rapida per i dati degli accessi

Azure AD e il portale di Azure forniscono entrambi punti di ingresso aggiuntivi per i dati di accesso:

- Panoramica Identity Security e Protection
- Utenti
- Gruppi
- Applicazioni aziendali

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Dati degli accessi degli utenti in Identity Security e Protection

Il grafico di accesso utente nella pagina di panoramica **della protezione della** sicurezza delle identità mostra le aggregazioni settimanali degli account di accesso. Il valore predefinito per il periodo di tempo è 30 giorni.

![Screenshot che mostra un grafico degli accesso in un mese.](./media/concept-sign-ins/06.png "Attività di accesso")

Quando si fa clic su un giorno nel grafico degli accessi, si ottiene una panoramica delle attività di accesso per tale giorno.

Ogni riga dell'elenco delle attività di accesso mostra:

* Chi ha effettuato l'accesso?
* Qual era l'applicazione di destinazione dell'accesso?
* Qual è lo stato dell'accesso?
* Qual è lo stato MFA dell'accesso?

Facendo clic su un elemento, si ottengono altri dettagli sull'operazione di accesso:

- ID utente
- Utente
- Username
- ID applicazione
- Applicazione
- Client
- Località
- indirizzo IP
- Data
- Autenticazione a più fattori obbligatoria
- Stato accesso

> [!NOTE]
> gli indirizzi IP vengono rilasciati in modo che non esista una connessione certa tra un IP e la posizione in cui si trova fisicamente il computer con tale indirizzo. Il mapping degli indirizzi IP è complicato dal fatto che provider di telefonia mobile e VPN possono rilasciare indirizzi IP da pool centrali spesso molto distanti dal luogo in cui viene effettivamente usato il dispositivo client. Attualmente nei report di Azure AD la conversione di un indirizzo IP in una posizione fisica è un'approssimazione basata su tracce, dati del Registro di sistema, ricerche inverse e altre informazioni.

Nella pagina **Utenti** è possibile accedere a una panoramica completa di tutti i accessi degli utenti facendo clic su **Accessi** nella sezione **Attività**.

![Screenshot che mostra la sezione Attività in cui è possibile selezionare Gli accesso.](./media/concept-sign-ins/08.png "Attività di accesso")

## <a name="usage-of-managed-applications"></a>Utilizzo di applicazioni gestite

Con una visualizzazione dei dati di accesso basata sulle applicazioni, è possibile rispondere a domande come:

* Chi sta usando le applicazioni?
* Quali sono le prime tre applicazioni dell'organizzazione?
* Come funziona l'applicazione più recente?

Il punto di ingresso a questi dati è le prime tre applicazioni dell'organizzazione. I dati sono contenuti nel report degli ultimi 30 giorni nella sezione **Panoramica** in **Applicazioni aziendali**.

![Screenshot che mostra dove è possibile selezionare Panoramica.](./media/concept-sign-ins/10.png "Attività di accesso")

L'utilizzo delle app rappresenta le aggregazioni settimanali di accesso per le prime tre applicazioni in un determinato periodo di tempo. Il periodo di tempo predefinito è di 30 giorni.

![Screenshot che mostra l'utilizzo dell'app per un periodo di un mese.](./media/concept-sign-ins/graph-chart.png "Attività di accesso")

Se si preferisce, è possibile mettere in evidenza un'applicazione specifica.

![Creazione di report](./media/concept-sign-ins/single-app-usage-graph.png "Creazione di report")

Quando si fa clic su un giorno nel grafico dell'utilizzo dell'app, si ottiene un elenco dettagliato delle attività di accesso.

L'opzione **Accessi** offre una panoramica completa di tutti gli eventi di accesso nell'applicazione.

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 log attività

È possibile visualizzare Microsoft 365 log attività dal [interfaccia di amministrazione di Microsoft 365](/office365/admin/admin-overview/about-the-admin-center). Si consideri il punto che, Microsoft 365 attività e Azure AD log attività condividono un numero significativo di risorse della directory. Solo il interfaccia di amministrazione di Microsoft 365 fornisce una visualizzazione completa dei log attività Microsoft 365 attività. 

È anche possibile accedere ai log Microsoft 365 attività a livello di codice usando le API di gestione [di Office 365](/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Passaggi successivi

* [Codici di errore del report delle attività di accesso](reference-sign-ins-error-codes.md)
* [Criteri di conservazione dei report di Azure AD](reference-reports-data-retention.md)
* [Latenze dei report di Azure AD](reference-reports-latencies.md)