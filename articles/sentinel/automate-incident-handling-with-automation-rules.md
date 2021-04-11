---
title: Automatizzare la gestione degli eventi imprevisti in Sentinel di Azure | Microsoft Docs
description: Questo articolo illustra come usare le regole di automazione per automatizzare la gestione degli eventi imprevisti, allo scopo di massimizzare l'efficienza e l'efficacia del SOC in risposta alle minacce per la sicurezza.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 1ff9fbbb6cd4b8827555a6cb1b222ed4eb0a5299
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609810"
---
# <a name="automate-incident-handling-in-azure-sentinel-with-automation-rules"></a>Automatizzare la gestione degli eventi imprevisti in Sentinel di Azure con le regole di automazione

Questo articolo illustra le regole di automazione di Sentinel di Azure e come usarle per implementare le operazioni di orchestrazione e risposta di sicurezza, l'automazione e la risposta (SOAR), aumentando l'efficacia del SOC e risparmiando tempo e risorse.

> [!IMPORTANT]
>
> - La funzionalità **regole di automazione** è attualmente disponibile in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

## <a name="what-are-automation-rules"></a>Che cosa sono le regole di automazione?

Le regole di automazione sono un nuovo concetto in Sentinel di Azure. Questa funzionalità consente agli utenti di gestire centralmente l'automazione della gestione degli eventi imprevisti. Oltre a consentire l'assegnazione di PlayBook a eventi imprevisti (non solo agli avvisi come prima), le regole di automazione consentono anche di automatizzare le risposte per più regole di analisi in una sola volta, contrassegnare, assegnare o chiudere automaticamente gli eventi imprevisti senza la necessità di PlayBook e controllare l'ordine delle azioni eseguite. Le regole di automazione semplificano l'uso dell'automazione in Sentinel di Azure e consentono di semplificare i flussi di lavoro complessi per i processi di orchestrazione degli eventi imprevisti.

## <a name="components"></a>Componenti

Le regole di automazione sono costituite da diversi componenti:

### <a name="trigger"></a>Trigger

Le regole di automazione vengono attivate dalla creazione di un evento imprevisto. 

Per eseguire la revisione, gli eventi imprevisti vengono creati dagli avvisi per le regole di analisi, di cui sono disponibili quattro tipi, come illustrato nell'esercitazione [rilevare le minacce con le regole di analisi predefinite in Sentinel di Azure](tutorial-detect-threats-built-in.md).

### <a name="conditions"></a>Condizioni

È possibile definire set complessi di condizioni per stabilire quando eseguire le azioni (vedere di seguito). Queste condizioni si basano in genere sugli Stati o sui valori degli attributi degli eventi imprevisti e delle rispettive entità e possono includere `AND` / `OR` / `NOT` / `CONTAINS` operatori.

### <a name="actions"></a>Azioni

È possibile definire le azioni da eseguire quando vengono soddisfatte le condizioni (vedere sopra). È possibile definire molte azioni in una regola ed è possibile scegliere l'ordine in cui verranno eseguite (vedere di seguito). È possibile definire le azioni seguenti usando le regole di automazione, senza la necessità [di una funzionalità avanzata di un PlayBook](automate-responses-with-playbooks.md):

- Modifica dello stato di un evento imprevisto, mantenimento aggiornato del flusso di lavoro.

  - Quando si passa a "closed", specificando il [motivo di chiusura](tutorial-investigate-cases.md#closing-an-incident) e aggiungendo un commento. Questo consente di tenere traccia delle prestazioni e dell'efficacia e di ottimizzare la riduzione dei falsi positivi.

- Modifica della gravità di un evento imprevisto: è possibile rivalutare e riassegnare la priorità in base alla presenza, all'assenza, ai valori o agli attributi delle entità incluse nell'evento imprevisto.

- Assegnazione di un evento imprevisto a un proprietario: consente di indirizzare i tipi di eventi imprevisti al personale più adatto per gestirli o per il personale più disponibile.

- Aggiunta di un tag a un evento imprevisto: questa operazione è utile per classificare gli eventi imprevisti in base al soggetto, all'autore dell'attacco o a qualsiasi altro denominatore comune.

Inoltre, è possibile definire un'azione per eseguire un [PlayBook](tutorial-respond-threats-playbook.md), in modo da eseguire azioni di risposta più complesse, incluse quelle che coinvolgono sistemi esterni. Solo i PlayBook attivati dal [trigger di evento imprevisto](automate-responses-with-playbooks.md#azure-logic-apps-basic-concepts) sono disponibili per essere usati nelle regole di automazione. È possibile definire un'azione per includere più PlayBook o combinazioni di PlayBook e altre azioni e l'ordine in cui verranno eseguite.

### <a name="expiration-date"></a>Expiration date

È possibile definire una data di scadenza per una regola di automazione. La regola verrà disabilitata dopo tale data. Questa operazione è utile per la gestione, ovvero la chiusura, degli eventi imprevisti "rumore" causati da attività pianificate e limitate al tempo, ad esempio i test di penetrazione.

### <a name="order"></a>JSON

È possibile definire l'ordine in cui le regole di automazione vengono eseguite. Le regole di automazione successive valuteranno le condizioni dell'evento imprevisto in base al relativo stato dopo che è stato agito dalle regole di automazione precedenti.

Se, ad esempio, la "prima regola di automazione" ha modificato la gravità di un evento imprevisto da medio a basso e la "seconda regola di automazione" viene definita per essere eseguita solo su eventi imprevisti con gravità media o superiore, non verrà eseguita in tale evento imprevisto.

## <a name="common-use-cases-and-scenarios"></a>Casi d'uso comuni e scenari

### <a name="incident-triggered-automation"></a>Automazione attivata dagli eventi imprevisti

Fino ad ora, solo gli avvisi potevano attivare una risposta automatica, tramite l'uso di PlayBook. Con le regole di automazione, gli eventi imprevisti possono ora attivare catene di risposta automatizzate, che possono includere nuovi PlayBook attivati dagli eventi imprevisti ([sono necessarie autorizzazioni speciali](#permissions-for-automation-rules-to-run-playbooks)) quando viene creato un evento imprevisto. 

### <a name="trigger-playbooks-for-microsoft-providers"></a>Attivare PlayBook per i provider Microsoft

Le regole di automazione consentono di automatizzare la gestione degli avvisi di sicurezza Microsoft applicando tali regole agli eventi imprevisti creati dagli avvisi. Le regole di automazione possono chiamare PlayBook ([sono necessarie autorizzazioni speciali](#permissions-for-automation-rules-to-run-playbooks)) e passare gli eventi imprevisti con tutti i relativi dettagli, inclusi avvisi ed entità. In generale, le procedure consigliate di Azure sentinella stabiliscono l'uso della coda degli eventi imprevisti come punto focale delle operazioni di sicurezza.

Gli avvisi di sicurezza Microsoft includono quanto segue:

- Microsoft Cloud App Security (MCAS)
- Azure AD Identity Protection
- Azure Defender (ASC)
- Defender for Internet (in precedenza ASC per l'it)
- Microsoft Defender for Office 365 (in precedenza Office 365 ATP)
- Microsoft Defender for Endpoint (in precedenza Microsoft Defender Advanced Threat Protection)
- Microsoft Defender for Identity (in precedenza Azure ATP)

### <a name="multiple-sequenced-playbooksactions-in-a-single-rule"></a>Più PlayBook/azioni sequenziate in una singola regola

È ora possibile avere un controllo quasi completo sull'ordine di esecuzione di azioni e PlayBook in un'unica regola di automazione. È anche possibile controllare l'ordine di esecuzione delle regole di automazione. In questo modo è possibile semplificare notevolmente i PlayBook, ridurli a un'unica attività o una semplice sequenza di attività e combinare questi piccoli PlayBook in combinazioni diverse in regole di automazione diverse.

### <a name="assign-one-playbook-to-multiple-analytics-rules-at-once"></a>Assegnare un PlayBook a più regole di analisi contemporaneamente

Se si ha un'attività che si vuole automatizzare su tutte le regole di analisi, ad esempio la creazione di un ticket di supporto in un sistema di emissione di ticket esterno, è possibile applicare un singolo PlayBook a una o a tutte le regole di analisi, incluse eventuali regole future, in un unico colpo. In questo modo, le attività di manutenzione e manutenzione semplici ma ripetitive sono molto meno gravose.

### <a name="automatic-assignment-of-incidents"></a>Assegnazione automatica degli eventi imprevisti

È possibile assegnare automaticamente gli eventi imprevisti al proprietario appropriato. Se il SOC dispone di un analista specializzato in una determinata piattaforma, eventuali eventi imprevisti relativi a tale piattaforma possono essere assegnati automaticamente a tale analista.

### <a name="incident-suppression"></a>Eliminazione evento imprevisto

È possibile utilizzare le regole per risolvere automaticamente gli eventi imprevisti che sono noti come positivi falsi/benigni senza utilizzare i PlayBook. Ad esempio, durante l'esecuzione di test di penetrazione, la manutenzione o gli aggiornamenti pianificati o il test delle procedure di automazione, è possibile creare molti eventi imprevisti falsi positivi che il SOC vuole ignorare. Una regola di automazione limitata al tempo può chiudere automaticamente questi eventi imprevisti durante la creazione e contrassegnarli con un descrittore della relativa generazione.

### <a name="time-limited-automation"></a>Automazione a tempo limitato

È possibile aggiungere date di scadenza per le regole di automazione. Potrebbero essere presenti casi diversi dall'eliminazione degli eventi imprevisti che garantiscano un'automazione limitata al tempo. È possibile assegnare un particolare tipo di evento imprevisto a un determinato utente (ad indicare, una stagista o un consulente) per un intervallo di tempo specifico. Se il periodo di tempo è noto in anticipo, è possibile fare in modo che la regola venga disabilitata alla fine della pertinenza, senza doversi preoccupare di tale operazione.

### <a name="automatically-tag-incidents"></a>Contrassegna automaticamente gli eventi imprevisti

È possibile aggiungere automaticamente tag di testo libero agli eventi imprevisti per raggrupparli o classificarli in base a qualsiasi criterio a scelta.

## <a name="automation-rules-execution"></a>Esecuzione delle regole di automazione

Le regole di automazione vengono eseguite in sequenza, in base all'ordine determinato. Ogni regola di automazione viene eseguita dopo il completamento dell'esecuzione di quella precedente. All'interno di una regola di automazione, tutte le azioni vengono eseguite in sequenza nell'ordine in cui sono definite.

Per le azioni PlayBook, si verifica un ritardo di due minuti tra l'inizio dell'azione PlayBook e l'azione successiva nell'elenco.

### <a name="permissions-for-automation-rules-to-run-playbooks"></a>Autorizzazioni per le regole di automazione per l'esecuzione di PlayBook

Quando una regola di automazione di Sentinel di Azure esegue un PlayBook, usa uno speciale account del servizio sentinella di Azure appositamente autorizzato per questa azione. L'uso di questo account, anziché dell'account utente, aumenta il livello di sicurezza del servizio.

Per consentire a una regola di automazione di eseguire un PlayBook, è necessario concedere a questo account le autorizzazioni esplicite per il gruppo di risorse in cui risiede il PlayBook. A questo punto, tutte le regole di automazione saranno in grado di eseguire qualsiasi PlayBook in tale gruppo di risorse.

Quando si configura una regola di automazione e si aggiunge un'azione **Esegui PlayBook** , viene visualizzato un elenco a discesa di PlayBook. I PlayBook a cui Azure Sentinel non ha le autorizzazioni vengono visualizzati come non disponibili ("disabilitato"). È possibile concedere l'autorizzazione sentinella di Azure ai gruppi di risorse dei PlayBook in loco selezionando il collegamento **Gestisci autorizzazioni PlayBook** .

> [!NOTE]
> **Autorizzazioni in un'architettura multi-tenant**
>
> Le regole di automazione supportano completamente le distribuzioni tra aree di lavoro e multi-tenant (nel caso di multi-tenant con [Azure Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)).
>
> Se, ad esempio, la distribuzione di Azure Sentinel usa un'architettura multi-tenant (ad esempio, se si è un MSSP), è possibile avere una regola di automazione in un tenant che esegue un PlayBook che risiede in un tenant diverso, ma le autorizzazioni per Sentinel per eseguire i PlayBook devono essere definite nel tenant in cui risiedono i PlayBook, non nel tenant in cui sono definite le regole di

## <a name="creating-and-managing-automation-rules"></a>Creazione e gestione delle regole di automazione

È possibile creare e gestire regole di automazione da punti diversi nell'esperienza Azure Sentinel, a seconda delle esigenze specifiche e del caso d'uso.

- **Pannello automazione**

    Le regole di automazione possono essere gestite centralmente nel pannello nuovo **automazione** (che sostituisce il pannello **PlayBook** ), nella scheda **regole di automazione** . è anche possibile gestire i PlayBook in questo pannello, nella scheda **PlayBook** . Da qui è possibile creare nuove regole di automazione e modificare quelle esistenti. È anche possibile trascinare le regole di automazione per modificare l'ordine di esecuzione e abilitarle o disabilitarle.

    Nel pannello **automazione** vengono visualizzate tutte le regole definite nell'area di lavoro, insieme al relativo stato (abilitato/disabilitato) e alle regole di analisi a cui vengono applicati.

    Quando è necessaria una regola di automazione che verrà applicata a numerose regole di analisi, crearla direttamente nel pannello **automazione** . Dal menu in alto fare clic su **Crea** e **Aggiungi nuova regola** per aprire il pannello **Crea nuova regola di automazione** . Da qui è disponibile la massima flessibilità per la configurazione della regola: è possibile applicarla a qualsiasi regola di analisi (incluse quelle future) e definire la più ampia gamma di condizioni e azioni.

- **Creazione guidata regola di analisi**

    Nella scheda **risposta automatica** della creazione guidata regola di analisi è possibile visualizzare, gestire e creare regole di automazione applicabili alla particolare regola di analisi creata o modificata nella procedura guidata.

    Quando si fa clic su **Crea** e uno dei tipi di regola (regola di **query pianificata** o **regola di creazione degli eventi imprevisti Microsoft**) dal menu in alto nel pannello **analisi** oppure se si seleziona una regola di analisi esistente e si fa clic su **modifica**, si aprirà la creazione guidata regola. Quando si seleziona la scheda **risposta automatica** , verrà visualizzata una sezione denominata **automazione evento imprevisto**, in cui verranno visualizzate le regole di automazione attualmente applicabili a questa regola. È possibile selezionare una regola di automazione esistente da modificare oppure fare clic su **Aggiungi nuovo** per crearne una nuova.

    Si noterà che quando si crea la regola di automazione da qui, il pannello **Crea nuova regola di automazione** Mostra la condizione della **regola di analisi** come non disponibile, perché questa regola è già impostata per essere applicata solo alla regola di analisi che si sta modificando nella procedura guidata. Tutte le altre opzioni di configurazione sono ancora disponibili.

- **Pannello eventi imprevisti**

    È anche possibile creare una regola di automazione dal pannello **eventi imprevisti** , in modo da rispondere a un singolo evento imprevisto ricorrente. Questa operazione è utile quando si crea una [regola di eliminazione](#incident-suppression) per la chiusura automatica degli eventi imprevisti "rumorosi". Selezionare un evento imprevisto dalla coda e fare clic su **Crea regola di automazione** dal menu in alto.

    Si noterà che il pannello **Crea nuova regola di automazione** ha popolato tutti i campi con i valori dell'evento imprevisto. Denomina la regola con lo stesso nome dell'evento imprevisto, lo applica alla regola Analytics che ha generato l'evento imprevisto e usa tutte le entità disponibili nell'evento imprevisto come condizioni della regola. Suggerisce anche un'azione di eliminazione (chiusura) per impostazione predefinita e suggerisce una data di scadenza per la regola. È possibile aggiungere o rimuovere condizioni e azioni e modificare la data di scadenza nel modo desiderato.

## <a name="auditing-automation-rule-activity"></a>Controllo dell'attività regola di automazione

Si potrebbe essere interessati a sapere cosa è successo a un determinato evento imprevisto e a quale regola di automazione potrebbe o meno essere eseguita. Si dispone di un record completo delle cronache degli eventi imprevisti disponibili nella tabella *SecurityIncident* nel pannello **logs** . Usare la query seguente per visualizzare tutte le attività della regola di automazione:

```kusto
SecurityIncident
| where ModifiedBy contains "Automation"
```

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare le regole di automazione per gestire la coda degli eventi imprevisti di Sentinel di Azure e implementare alcune automazione di gestione degli eventi imprevisti di base.

- Per altre informazioni sulle opzioni di automazione avanzate, vedere [automatizzare la risposta alle minacce con PlayBook in Sentinel di Azure](automate-responses-with-playbooks.md).
- Per informazioni sull'implementazione di regole e playbook di automazione, vedere [esercitazione: usare PlayBook per automatizzare le risposte alle minacce in Sentinel di Azure](tutorial-respond-threats-playbook.md).
