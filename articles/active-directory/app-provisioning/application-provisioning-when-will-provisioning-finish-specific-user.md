---
title: Scoprire quando un utente specifico sarà in grado di accedere a un'app
description: Come identificare se un utente fondamentale è in grado di accedere a un'applicazione configurata per il provisioning utenti con Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 9835ba2b6db2d71d0ff5825f2eb1996133e75537
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530815"
---
# <a name="check-the-status-of-user-provisioning"></a>Controllare lo stato del provisioning utenti

Il Azure AD di provisioning iniziale esegue un ciclo di provisioning iniziale nel sistema di origine e nel sistema di destinazione, seguito da cicli incrementali periodici. Quando si configura il provisioning per un'app, è possibile controllare lo stato corrente del servizio di provisioning e verificare quando un utente sarà in grado di accedere a un'app.

## <a name="view-the-provisioning-progress-bar"></a>Visualizzare l'indicatore di stato del provisioning

 Nella pagina **Provisioning** per un'app è possibile visualizzare lo stato del Azure AD provisioning. La **sezione Stato** corrente nella parte inferiore della pagina indica se un ciclo di provisioning ha avviato il provisioning degli account utente. È possibile osservare lo stato del ciclo, vedere quanti utenti e gruppi sono stati provisioning e quanti ruoli sono stati creati.

Quando si configura il provisioning automatico per la prima volta, la sezione **Stato** corrente nella parte inferiore della pagina mostra lo stato del ciclo di provisioning iniziale. Questa sezione viene aggiornata ogni volta che viene eseguito un ciclo incrementale. Vengono visualizzati i dettagli seguenti:
- Tipo di ciclo di provisioning (iniziale o incrementale) attualmente in esecuzione o completato per l'ultima volta.
- Un **indicatore di stato** che mostra la percentuale di completamento del ciclo di provisioning. La percentuale riflette il numero di pagine di cui è stato effettuato il provisioning. Si noti che ogni pagina può contenere più utenti o gruppi, quindi la percentuale non è direttamente correlata al numero di utenti, gruppi o ruoli di cui è stato effettuato il provisioning.
- Pulsante **Aggiorna** che è possibile usare per mantenere aggiornata la visualizzazione.
- Numero di utenti **e gruppi** **nell'archivio** dati del connettore. Il conteggio aumenta ogni volta che un oggetto viene aggiunto all'ambito del provisioning. Il conteggio non scenderà se un utente viene eliminato in modo soft o hard perché non rimuove l'oggetto dall'archivio dati del connettore. Il conteggio verrà ricalcolato alla prima sincronizzazione dopo la reimpostazione [di](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true) CDS 
- Collegamento **Visualizza log di** controllo, che apre i log di provisioning Azure AD per informazioni dettagliate su tutte le operazioni eseguite dal servizio di provisioning utenti, incluso lo stato del provisioning per i singoli utenti (vedere la sezione Usare i log di [provisioning](#use-provisioning-logs-to-check-a-users-provisioning-status) più avanti).

Al termine di un  ciclo di provisioning, la sezione Statistiche fino alla data mostra il numero cumulativo di utenti e gruppi di cui è stato effettuato il provisioning fino a oggi, insieme alla data di completamento e alla durata dell'ultimo ciclo. **L'ID attività** identifica in modo univoco il ciclo di provisioning più recente. **L'ID processo** è un identificatore univoco per il processo di provisioning ed è specifico dell'app nel tenant.

Lo stato di avanzamento del provisioning può essere visualizzato nella portale di Azure, nella scheda provisioning **&gt; &gt; \[ Azure Active Directory'applicazione \] &gt;** App aziendali.

![Indicatore di stato della pagina di provisioning](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Usare i log di provisioning per controllare lo stato del provisioning di un utente

Per visualizzare lo stato del provisioning per un utente selezionato, vedere i log [di provisioning (anteprima)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) in Azure AD. Tutte le operazioni eseguite dal servizio di provisioning utenti vengono registrate nei log Azure AD di provisioning degli utenti. Sono incluse tutte le operazioni di lettura e scrittura eseguite nei sistemi di origine e di destinazione e i dati utente letti o scritti durante ogni operazione.

È possibile accedere ai log di provisioning  nel portale di Azure selezionando Azure Active Directory log di provisioning delle app &gt; **aziendali** &gt; **(anteprima)** nella **sezione** Attività. È possibile cercare i dati di provisioning in base al nome dell'utente o all'identificatore nel sistema di origine o nel sistema di destinazione. Per informazioni dettagliate, vedere [Log di provisioning (anteprima).](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 

I log di provisioning registrano tutte le operazioni eseguite dal servizio di provisioning, tra cui:

* Esecuzione in Azure AD di query sugli utenti assegnati che rientrano nell'ambito del provisioning
* Esecuzione nell'app di destinazione di query sull'esistenza di tali utenti
* Confronto degli oggetti utente tra i sistemi
* Aggiunta, aggiornamento o disabilitazione dell'account utente nel sistema di destinazione in base al confronto

Per altre informazioni su come leggere i log di provisioning nel portale di Azure, vedere la guida [alla creazione di report sul provisioning.](check-status-user-account-provisioning.md)

## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo sarà necessario per eseguire il provisioning degli utenti?
Quando si usa il provisioning utenti automatico con un'applicazione, Azure AD effettua [](../manage-apps/assign-user-or-group-access-portal.md) automaticamente il provisioning e l'aggiornamento degli account utente in un'app in base a elementi come l'assegnazione di utenti e gruppi a intervalli di tempo pianificati regolarmente, in genere ogni 40 minuti.

Il tempo necessario per il provisioning di un determinato utente dipende principalmente dal fatto che il processo di provisioning esenda un ciclo iniziale o incrementale.

- Per **il ciclo iniziale**, il tempo processo dipende da molti fattori, tra cui il numero di utenti e gruppi nell'ambito per il provisioning e il numero totale di utenti e gruppi nel sistema di origine. La prima sincronizzazione tra Azure AD e un'app può richiedere da 20 minuti a diverse ore, a seconda delle dimensioni della directory di Azure AD e del numero di utenti inclusi nell'ambito del provisioning. Un elenco completo dei fattori che influiscono sulle prestazioni del ciclo iniziale è riepilogato più avanti in questa sezione.

- Per i cicli **incrementali** dopo il ciclo iniziale, i tempi dei processi tendono a essere più veloci (ad esempio entro 10 minuti), perché il servizio di provisioning archivia i limiti che rappresentano lo stato di entrambi i sistemi dopo il ciclo iniziale, migliorando le prestazioni delle sincronizzazioni successive. Il tempo del processo dipende dal numero di modifiche rilevate nel ciclo di provisioning. Se sono presenti meno di 5.000 modifiche all'appartenenza a utenti o gruppi, il processo può terminare entro un singolo ciclo di provisioning incrementale. 

La tabella seguente riepiloga i tempi di sincronizzazione per gli scenari di provisioning più comuni. In questi scenari, il sistema di origine è Azure AD e il sistema di destinazione è un'applicazione SaaS. I tempi di sincronizzazione derivano da un'analisi statistica dei processi di sincronizzazione per le applicazioni SaaS ServiceNow, Workplace, Salesforce e G Suite.


| Configurazione dell'ambito | Utenti, gruppi e membri nell'ambito | Tempo ciclo iniziale | Tempo ciclo incrementale |
| -------- | -------- | -------- | -------- |
| Sincronizza solo utenti e gruppi assegnati |  < 1.000 |  < 30 minuti | < 30 minuti |
| Sincronizza solo utenti e gruppi assegnati |  1.000 - 10.000 | 142 - 708 minuti | < 30 minuti |
| Sincronizza solo utenti e gruppi assegnati |   10.000 - 100.000 | 1.170 - 2.340 minuti | < 30 minuti |
| Sincronizza tutti gli utenti e i gruppi in Azure AD |  < 1.000 | < 30 minuti  | < 30 minuti |
| Sincronizza tutti gli utenti e i gruppi in Azure AD |  1.000 - 10.000 | < 30 - 120 minuti | < 30 minuti |
| Sincronizza tutti gli utenti e i gruppi in Azure AD |  10.000 - 100.000  | 713 - 1.425 minuti | < 30 minuti |
| Sincronizza tutti gli utenti in Azure AD|  < 1.000  | < 30 minuti | < 30 minuti |
| Sincronizza tutti gli utenti in Azure AD | 1.000 - 10.000  | 43 - 86 minuti | < 30 minuti |

Per la configurazione **Sincronizzare solo** utenti e gruppi assegnati, è possibile usare le formule seguenti per determinare i tempi approssimativi del ciclo iniziale minimo e **massimo** previsti:

- Minuti minimi = 0,01 x [Numero di utenti, gruppi e membri del gruppo assegnati]
- Numero massimo di minuti = 0,08 x [Numero di utenti, gruppi e membri del gruppo assegnati]

Riepilogo dei fattori che influenzano il tempo necessario per completare un **ciclo iniziale:**

- Numero totale di utenti e gruppi nell'ambito del provisioning.

- Numero totale di utenti, gruppi e membri del gruppo presenti nel sistema di origine (Azure AD).

- Se gli utenti nell'ambito per il provisioning vengono abbinati agli utenti esistenti nell'applicazione di destinazione o devono essere creati per la prima volta. I processi di sincronizzazione per cui vengono  creati tutti gli utenti per la prima volta durano circa il doppio dei processi di sincronizzazione per cui tutti gli utenti corrispondono agli utenti esistenti.

- Numero di errori nei [log di provisioning.](check-status-user-account-provisioning.md) Le prestazioni risultano ridotte se sono presenti troppi errori e se il servizio di provisioning è in stato di quarantena. 

- Limitazioni relative al numero e alla frequenza delle richieste implementate dal sistema di destinazione. Alcuni sistemi di destinazione implementano limiti di frequenza delle richieste e limitazione, che possono influire sulle prestazioni durante operazioni di sincronizzazione di grandi dimensioni. In queste condizioni, un'applicazione che riceve un numero eccessivo di richieste potrebbe ridurre la propria velocità di risposta o interrompere la connessione. Per migliorare le prestazioni, il connettore deve essere regolato in modo da non inviare le richieste di app più velocemente di quanto l'app possa elaborarle. I connettori di provisioning creati da Microsoft sono in grado di eseguire questa regolazione. 

- Numero e dimensione dei gruppi assegnati. La sincronizzazione dei gruppi assegnati richiede più tempo rispetto alla sincronizzazione degli utenti. Il numero e la dimensione dei gruppi assegnati incidono sulle prestazioni. Se in un'applicazione è [abilitato il mapping per la sincronizzazione dell'oggetto del gruppo](customize-application-attributes.md#editing-group-attribute-mappings), in aggiunta agli utenti vengono sincronizzate le proprietà del gruppo, come i nomi e le appartenenze del gruppo. Queste sincronizzazioni aggiuntive richiederanno più tempo rispetto alla sincronizzazione dei soli oggetti utente.

- Se le prestazioni diventano un problema e si sta tentando di effettuare il provisioning della maggior parte degli utenti e dei gruppi nel tenant, usare i filtri di ambito. I filtri di ambito consentono di ottimizzare i dati che il servizio di provisioning estrae da Azure AD, filtrando gli utenti in base a valori di attributo specifici. Per altre informazioni sui filtri di ambito, vedere [Provisioning dell'applicazione basato su attributi con filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Passaggi successivi
[Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](user-provisioning.md)