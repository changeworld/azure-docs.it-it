---
title: Protezione della proprietà intellettuale MSSPs (Managed Security Service Provider) in Sentinel di Azure | Microsoft Docs
description: Informazioni sul modo in cui i provider di servizi di sicurezza gestiti (MSSPs) possono proteggere la proprietà intellettuale creata in Sentinel di Azure.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2021
ms.author: bagol
ms.openlocfilehash: 1c15c341d85fae667ee23883043350340ad866b8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315536"
---
# <a name="protecting-mssp-intellectual-property-in-azure-sentinel"></a>Protezione della proprietà intellettuale MSSP in Sentinel di Azure

Questo articolo descrive i metodi che possono essere usati dai provider di servizi di sicurezza gestiti (MSSPs) per proteggere la proprietà intellettuale sviluppata in Sentinel di Azure, ad esempio le regole di analisi di Azure Sentinel, le query di ricerca, i PlayBook e le cartelle di lavoro.

Il metodo scelto dipenderà dal modo in cui ognuno dei clienti acquista Azure; sia che si agisca come [provider di soluzioni cloud (CSP)](#cloud-solutions-providers-csp)o che il cliente disponga di un account [contratto Enterprise (EA)/Pay-As-You-Go (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg) . Le sezioni seguenti descrivono ognuno di questi metodi separatamente.

## <a name="cloud-solutions-providers-csp"></a>Provider di soluzioni cloud (CSP)

Se si sta rivendendo Azure come provider di soluzioni cloud (CSP), si sta gestendo la sottoscrizione di Azure del cliente. Grazie all' [amministratore per conto di (Aobo)](/partner-center/azure-plan-manage), agli utenti del gruppo di agenti di amministrazione del tenant MSSP viene concesso il proprietario dell'accesso alla sottoscrizione di Azure del cliente e per impostazione predefinita il cliente non ha accesso.

Se altri utenti del tenant MSSP, al di fuori del gruppo di agenti di amministrazione, devono accedere all'ambiente del cliente, è consigliabile usare [Azure Lighthouse](multiple-tenants-service-providers.md). Azure Lighthouse consente di concedere a utenti o gruppi l'accesso a un ambito specifico, ad esempio un gruppo di risorse o una sottoscrizione, usando uno dei ruoli predefiniti.

Se è necessario fornire agli utenti del cliente l'accesso all'ambiente Azure, è consigliabile concedere loro l'accesso a livello di *gruppo di risorse* , anziché l'intera sottoscrizione, in modo che sia possibile visualizzare/nascondere parti dell'ambiente in base alle esigenze.

Ad esempio:

- È possibile concedere al cliente l'accesso a diversi gruppi di risorse in cui si trovano le applicazioni, mantenendo comunque l'area di lavoro di Azure Sentinel in un gruppo di risorse separato, in cui il cliente non ha accesso.

- Usare questo metodo per consentire ai clienti di visualizzare le cartelle di lavoro e i PlayBook selezionati, che sono risorse separate che possono risiedere nel proprio gruppo di risorse.

Anche con la concessione dell'accesso a livello di gruppo di risorse, i clienti potranno comunque accedere ai dati di log per le risorse a cui possono accedere, ad esempio i log da una macchina virtuale, anche senza accesso a Sentinel di Azure. Per altre informazioni, vedere [Manage Access to Azure Sentinel data by Resource](resource-context-rbac.md).

> [!TIP]
> Se è necessario fornire ai clienti l'accesso all'intera sottoscrizione, è possibile consultare le linee guida per i [contratti Enterprise Agreement (EA)/con pagamento in base al consumo (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg).
>

### <a name="sample-azure-sentinel-csp-architecture"></a>Esempio di architettura CSP di Azure Sentinel

Nell'immagine seguente viene descritto in che modo le autorizzazioni descritte nella [sezione precedente](#cloud-solutions-providers-csp) potrebbero funzionare quando si fornisce l'accesso ai clienti CSP:

:::image type="content" source="media/mssp-protect-intellectual-property/csp-customers.png" alt-text="Proteggi la tua proprietà intellettuale di Azure Sentinel con i clienti CSP.":::

In questa immagine:

- Gli utenti autorizzati con accesso **proprietario** alla sottoscrizione CSP sono gli utenti del gruppo agenti di amministrazione nel tenant di MSSP Azure ad.
- Altri gruppi di MSSP ottengono l'accesso all'ambiente del cliente tramite Azure Lighthouse.
- L'accesso del cliente alle risorse di Azure viene gestito da RBAC di Azure a livello di gruppo di risorse.

    Ciò consente a MSSPs di nascondere i componenti di Sentinel di Azure in base alle esigenze, come le regole di analisi e le query di caccia

Per altre informazioni, vedere anche la [documentazione di Azure Lighthouse](/azure/lighthouse/concepts/cloud-solution-provider).

## <a name="enterprise-agreements-ea--pay-as-you-go-payg"></a>Enterprise Agreement (EA)/con pagamento in base al consumo (PAYG)

Se il cliente acquista direttamente da Microsoft, il cliente dispone già dell'accesso completo all'ambiente Azure e non è possibile nascondere qualsiasi elemento presente nella sottoscrizione di Azure del cliente.

Proteggere invece la proprietà intellettuale sviluppata in Sentinel di Azure come indicato di seguito, a seconda del tipo di risorsa che è necessario proteggere:

### <a name="analytics-rules-and-hunting-queries"></a>Regole di analisi e query di caccia

Le regole di analisi e le query di ricerca sono entrambe contenute all'interno di Sentinel di Azure e pertanto non possono essere separate dall'area di lavoro di Azure Sentinel.

Anche se un utente dispone solo delle autorizzazioni di lettura di Azure Sentinel, sarà comunque in grado di visualizzare la query. In questo caso, è consigliabile ospitare le regole di analisi e le query di ricerca nel tenant di MSSP, invece che nel tenant del cliente.

A tale scopo, è necessaria un'area di lavoro nel tenant con Azure Sentinel abilitato e sarà anche necessario visualizzare l'area di lavoro Customer tramite [Azure Lighthouse](multiple-tenants-service-providers.md).

Per creare una regola analitica o una query di ricerca nel tenant di MSSP che fa riferimento ai dati nel tenant del cliente, è necessario usare l' `workspace` istruzione come indicato di seguito:

```kql
workspace('<customer-workspace>').SecurityEvent
| where EventID == ‘4625’
```

Quando si aggiunge un' `workspace` istruzione alle regole di analisi, tenere presente quanto segue:

- **Nessun avviso nell'area di lavoro del cliente**. Le regole create in questo modo non creeranno avvisi o eventi imprevisti nell'area di lavoro del cliente. Gli avvisi e gli eventi imprevisti possono esistere solo nell'area di lavoro MSSP.

- **Creare avvisi distinti per ogni cliente**. Quando si utilizza questo metodo, è inoltre consigliabile utilizzare regole di avviso separate per ogni cliente e rilevamento, in quanto l'istruzione dell'area di lavoro sarà diversa in ogni caso.

    È possibile aggiungere il nome del cliente al nome della regola di avviso per identificare facilmente il cliente in cui viene attivato l'avviso. Gli avvisi separati possono comportare un numero elevato di regole, che può essere utile gestire tramite script o [Azure Sentinel come codice](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928).

    Ad esempio:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-per-customer.png" alt-text="Creare regole separate nell'area di lavoro MSSP per ogni cliente.":::

- **Creare aree di lavoro MSSP separate per ogni cliente**. La creazione di regole separate per ogni cliente e rilevamento può causare il raggiungimento del numero massimo di regole di analisi per l'area di lavoro (512). Se si dispone di molti clienti e si prevede di raggiungere questo limite, potrebbe essere necessario creare un'area di lavoro MSSP separata per ogni cliente.

    Ad esempio:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-and-workspace-per-customer.png" alt-text="Creare un'area di lavoro e le regole nel tenant di MSSP per ogni cliente.":::

> [!IMPORTANT]
> La chiave per l'uso corretto di questo metodo è l'uso dell'automazione per gestire un ampio set di regole tra le aree di lavoro.
>
> Per altre informazioni, vedere [regole di analisi tra aree di lavoro](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cross-workspace-analytics-rules/ba-p/1664211)
>

### <a name="workbooks"></a>Workbooks

Se è stata sviluppata una cartella di lavoro di Azure Sentinel che non si vuole copiare dal cliente, ospitare la cartella di lavoro nel tenant di MSSP. Assicurarsi di avere accesso alle aree di lavoro del cliente tramite Azure Lighthouse, quindi assicurarsi di modificare la cartella di lavoro per usare le aree di lavoro del cliente.

Ad esempio:

:::image type="content" source="media/mssp-protect-intellectual-property/cross-workspace-workbook.png" alt-text="Cartelle di lavoro tra aree di lavoro":::

Per altre informazioni, vedere [cartelle](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks)di lavoro tra più aree di lavoro.

Se si desidera che il cliente sia in grado di visualizzare le visualizzazioni della cartella di lavoro, mantenendo comunque il segreto del codice, è consigliabile esportare la cartella di lavoro in Power BI.

Esportazione della cartella di lavoro in Power BI:

- **Semplifica la condivisione delle visualizzazioni della cartella di lavoro**. È possibile inviare al cliente un collegamento al dashboard Power BI, in cui è possibile visualizzare i dati segnalati, senza richiedere le autorizzazioni di accesso di Azure.
- **Consente la pianificazione**. Configurare Power BI per l'invio periodico di messaggi di posta elettronica che contengono uno snapshot del dashboard per quel periodo di tempo.

Per altre informazioni, vedere [importare i dati di log di monitoraggio di Azure in Power bi](/azure/azure-monitor/visualize/powerbi).

### <a name="playbooks"></a>Playbook

È possibile proteggere i PlayBook come segue, a seconda del punto in cui sono state create le regole analitiche che attivano il PlayBook:

- **Regole di analisi create nell'area di lavoro MSSP**.  Assicurarsi di creare i PlayBook nel tenant di MSSP e di ottenere tutti i dati relativi agli eventi imprevisti e agli avvisi dall'area di lavoro MSSP. È possibile aggiungere i PlayBook ogni volta che si crea una nuova regola nell'area di lavoro.

    Ad esempio:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-mssp-workspace.png" alt-text="Regole create nell'area di lavoro MSSP.":::

- **Regole di analisi create nell'area di lavoro del cliente**. Usare Azure Lighthouse per aggiungere regole di analisi dall'area di lavoro del cliente a un PlayBook ospitato nell'area di lavoro di MSSP. In questo caso, il PlayBook riceve i dati di avviso e evento imprevisto e qualsiasi altra informazione del cliente dall'area di lavoro del cliente.

    Ad esempio:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-customer-workspace.png" alt-text="Regole create nell'area di lavoro del cliente.":::

In entrambi i casi, se il PlayBook deve accedere all'ambiente Azure del cliente, usare un utente o un'entità servizio con tale accesso tramite Lighthouse.

Tuttavia, se il PlayBook deve accedere a risorse non di Azure nel tenant del cliente, ad esempio Azure AD, Office 365 o Microsoft 365 Defender, sarà necessario creare un'entità servizio con le autorizzazioni appropriate nel tenant del cliente, quindi aggiungere tale identità nel PlayBook.

> [!NOTE]
> Se si usano regole di automazione insieme ai PlayBook, è necessario impostare le autorizzazioni per le regole di automazione per il gruppo di risorse in cui si trovano i PlayBook.
> Per altre informazioni, vedere [autorizzazioni per le regole di automazione per l'esecuzione di PlayBook](automate-incident-handling-with-automation-rules.md#permissions-for-automation-rules-to-run-playbooks).
>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

- [PlayBook tecnico di Azure Sentinel per MSSPs](https://cloudpartners.transform.microsoft.com/download?assetname=assets/Azure-Sentinel-Technical-Playbook-for-MSSPs.pdf&download=1)
- [Gestire più tenant in Sentinel di Azure come MSSP](multiple-tenants-service-providers.md)
- [Estendere Azure Sentinel tra più aree di lavoro e tenant](extend-sentinel-across-workspaces-tenants.md)
- [Esercitazione: Visualizzare e monitorare i dati](tutorial-monitor-your-data.md)
- [Esercitazione: Configurare le risposte automatiche alle minacce in Azure Sentinel](tutorial-respond-threats-playbook.md)
