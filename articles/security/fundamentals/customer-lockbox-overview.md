---
title: Customer Lockbox per Microsoft Azure
description: Panoramica tecnica di Customer Lockbox per Microsoft Azure, che consente di controllare l'accesso del provider di servizi cloud quando Microsoft potrebbe dover accedere ai dati dei clienti.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 02/19/2021
ms.openlocfilehash: 0146e4fcaf70d37975dc587a266c47bf4b3f4601
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461675"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Customer Lockbox per Microsoft Azure

> [!NOTE]
> Per usare questa funzionalità, è necessario che l'organizzazione disponga di un [piano di supporto di Azure](https://azure.microsoft.com/support/plans/) con un livello minimo di **sviluppatore**.

Customer Lockbox per Microsoft Azure fornisce un'interfaccia che consente ai clienti di esaminare e approvare oppure rifiutare le richieste di accesso ai loro dati. Viene usato nei casi in cui un tecnico Microsoft deve accedere ai dati dei clienti durante una richiesta di supporto.

Questo articolo illustra come abilitare Customer Lockbox e come vengono avviate, rilevate e archiviate le richieste dell'archivio degli archivi per verifiche e controlli successivi.

<a name='supported-services-and-scenarios-in-general-availability'></a><a name='supported-services-and-scenarios-in-preview'></a>
## <a name="supported-services-and-scenarios-general-availability"></a>Servizi e scenari supportati (disponibilità generale)

I servizi seguenti sono ora disponibili a livello generale per Customer Lockbox:

- Gestione API di Azure
- Servizio app di Azure
- Servizi cognitivi di Azure
- Registro Azure Container
- Database di Azure per MySQL
- Azure Databricks
- Azure Data Box
- Esplora dati di Azure
- Azure Data Factory
- Database di Azure per PostgreSQL
- Funzioni di Azure
- Azure HDInsight
- Servizio Azure Kubernetes
- Monitoraggio di Azure
- Archiviazione di Azure
- Database SQL di Azure
- Trasferimenti di sottoscrizioni di Azure
- Azure Synapse Analytics
- Macchine virtuali in Azure (che riguardano l'accesso desktop remoto, l'accesso ai dump della memoria e i dischi gestiti)

## <a name="enable-customer-lockbox"></a>Abilita Customer Lockbox

È ora possibile abilitare Customer Lockbox dal [modulo di amministrazione](https://aka.ms/customerlockbox/administration) nel pannello Customer Lockbox.  

> [!NOTE]
> Per abilitare Customer Lockbox, l'account utente deve avere il [ruolo di amministratore globale assegnato](../../active-directory/roles/manage-roles-portal.md).

## <a name="workflow"></a>Flusso di lavoro

La procedura seguente illustra un flusso di lavoro tipico per una richiesta di Customer Lockbox.

1. Un utente di un'organizzazione presenta un problema con il carico di lavoro di Azure.

2. Quando questo utente risolve il problema, ma non può correggerlo, apre un ticket di supporto dal [portale di Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). Il ticket viene assegnato a un tecnico del supporto tecnico di Azure.

3. Un tecnico del supporto di Azure esamina la richiesta di servizio e determina i passaggi successivi per risolvere il problema.

4. Se il tecnico del supporto non riesce a risolvere il problema usando gli strumenti e la telemetria standard, il passaggio successivo consiste nel richiedere autorizzazioni elevate tramite un servizio di accesso JIT (just-in-Time). Questa richiesta può provenire dal tecnico del supporto originale o da un altro tecnico perché il problema viene inoltrato al team di Azure DevOps.

5. Una volta inviata la richiesta di accesso da parte del tecnico di Azure, il servizio JIT valuta la richiesta tenendo conto dei fattori seguenti:
    - Ambito della risorsa
    - Indica se il richiedente è un'identità isolata o usa l'autenticazione a più fattori
    - Livelli di autorizzazione

    In base alla regola JIT, questa richiesta può includere anche un'approvazione dei responsabili approvazione interni di Microsoft. Ad esempio, l'approvatore potrebbe essere il responsabile del supporto tecnico o il responsabile del DevOps.

6. Quando la richiesta richiede l'accesso diretto ai dati dei clienti, viene avviata una richiesta Customer Lockbox. Ad esempio, accesso desktop remoto alla macchina virtuale di un cliente.

    La richiesta è ora in uno stato di **notifica del cliente** , in attesa dell'approvazione del cliente prima di concedere l'accesso.

7. Presso l'organizzazione del cliente, l'utente che ha il [ruolo di proprietario](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) per la sottoscrizione di Azure riceve un messaggio di posta elettronica da Microsoft per notificare la richiesta di accesso in sospeso. Per Customer Lockbox richieste, questa persona è il responsabile approvazione designato.

    Posta elettronica di esempio:

    ![Azure Customer Lockbox-notifica tramite posta elettronica](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. La notifica tramite posta elettronica contiene un collegamento al pannello **Customer Lockbox** nel modulo di amministrazione. Con questo collegamento, il responsabile approvazione designato accede al portale di Azure per visualizzare le richieste in sospeso che la propria organizzazione ha per Customer Lockbox:

    ![Azure Customer Lockbox-pagina di destinazione](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   La richiesta rimane nella coda del cliente per quattro giorni. Al termine di questo periodo, la richiesta di accesso scadrà automaticamente e non verrà concesso alcun accesso ai tecnici Microsoft.

9. Per ottenere i dettagli della richiesta in sospeso, il responsabile approvazione designato può selezionare la richiesta di archivio protetto dalle **richieste in sospeso**:

    ![Azure Customer Lockbox-visualizzare la richiesta in sospeso](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. Il responsabile approvazione designato può anche selezionare l' **ID richiesta di servizio** per visualizzare la richiesta di ticket di supporto creata dall'utente originale. Queste informazioni forniscono il contesto per i motivi per cui supporto tecnico Microsoft è impegnato e la cronologia del problema segnalato. Ad esempio:

    ![Azure Customer Lockbox-visualizzare la richiesta di ticket di supporto](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Dopo aver esaminato la richiesta, l'approvatore designato seleziona **approva** o **Nega**:

    ![Azure Customer Lockbox-selezionare approva o nega](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    In seguito alla selezione:
    - **Approva**: l'accesso viene concesso al tecnico Microsoft. L'accesso viene concesso per un periodo predefinito di otto ore.
    - **Deny**: la richiesta di accesso con privilegi elevati da parte del tecnico Microsoft è stata rifiutata e non vengono eseguite altre azioni.

Ai fini del controllo, le azioni eseguite in questo flusso di lavoro vengono registrate [Customer Lockbox log delle richieste](#auditing-logs).

## <a name="auditing-logs"></a>Log di controllo

I log di Customer Lockbox vengono archiviati nei log attività. Nella portale di Azure selezionare **log attività** per visualizzare le informazioni di controllo relative alle richieste di Customer Lockbox. È possibile filtrare in base ad azioni specifiche, ad esempio:
- **Nega richiesta di Lockbox**
- **Crea richiesta di Lockbox**
- **Approva richiesta di Lockbox**
- **Scadenza richiesta archivio protetto**

Ad esempio:

![Azure Customer Lockbox-log attività](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>Integrazione di Customer Lockbox con il benchmark di sicurezza di Azure

È stato introdotto un nuovo controllo Baseline ([3,13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios)) nel benchmark di sicurezza di Azure che copre Customer Lockbox applicabilità. I clienti possono ora sfruttare il benchmark per esaminare Customer Lockbox applicabilità per un servizio.

## <a name="exclusions"></a>Esclusioni

Customer Lockbox richieste non vengono attivate negli scenari di supporto tecnico seguenti:

- Un tecnico Microsoft deve eseguire un'attività che esula dalle procedure operative standard. Ad esempio, per recuperare o ripristinare i servizi in scenari imprevisti o imprevedibili.
- Un tecnico Microsoft accede alla piattaforma Azure nell'ambito della risoluzione dei problemi e accede inavvertitamente ai dati dei clienti. Ad esempio, il team di rete di Azure esegue la risoluzione dei problemi che genera un'acquisizione di pacchetti in un dispositivo di rete. In questo scenario, se il cliente crittografa i dati mentre è in transito, il tecnico non potrà leggere i dati.

## <a name="next-steps"></a>Passaggi successivi

Customer Lockbox è disponibile per tutti i clienti che dispongono di un [piano di supporto di Azure](https://azure.microsoft.com/support/plans/) con un livello minimo di **sviluppatore**. È possibile abilitare Customer Lockbox dal [modulo di amministrazione](https://aka.ms/customerlockbox/administration) nel pannello Customer Lockbox.

Customer Lockbox richieste vengono avviate da un tecnico Microsoft se questa azione è necessaria per l'avanzamento di un caso di supporto.
