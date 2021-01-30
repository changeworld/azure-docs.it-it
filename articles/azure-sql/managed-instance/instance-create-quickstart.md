---
title: 'Guida introduttiva: creare un Istanza gestita SQL di Azure (portale)'
description: Questa guida di avvio rapido descrive come creare un'istanza gestita, un ambiente di rete e una macchina virtuale client per l'accesso usando il portale di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 1/29/2021
ms.openlocfilehash: 95b721f12e4818a77f18d01c99a5c6d60b881169
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089704"
---
# <a name="quickstart-create-an-azure-sql-managed-instance"></a>Avvio rapido: Creare un'istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questa guida di avvio rapido illustra come creare un'[istanza gestita di SQL di Azure](sql-managed-instance-paas-overview.md) nel portale di Azure.

> [!IMPORTANT]
> Per le limitazioni, vedere [Aree supportate](resource-limits.md#supported-regions) e [Tipi di sottoscrizione supportati](resource-limits.md#supported-subscription-types).

## <a name="create-an-azure-sql-managed-instance"></a>Creare un'istanza gestita di SQL di Azure

Per creare un Istanza gestita SQL, seguire questa procedura: 

### <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/).

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare **Azure SQL** nel menu a sinistra del portale di Azure. Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi immettere **Azure SQL** nella casella di ricerca.
1. Selezionare **+Aggiungi** per aprire la pagina **Selezionare l'opzione di distribuzione SQL**. È possibile visualizzare informazioni aggiuntive su un'istanza gestita di SQL di Azure selezionando **Mostra dettagli** nel riquadro **Istanze gestite di SQL**.
1. Selezionare **Crea**.

   ![Creare un'istanza gestita](./media/instance-create-quickstart/create-azure-sql-managed-instance.png)

4. Usare le schede nel modulo di provisioning **Crea Istanza gestita di SQL di Azure** per aggiungere le informazioni obbligatorie e quelle facoltative. Le sezioni seguenti descrivono queste schede.

### <a name="basics-tab"></a>Scheda Informazioni di base

- Compilare le informazioni nella scheda **Informazioni di base**. Contiene un set minimo di informazioni necessarie per effettuare il provisioning di un'istanza gestita di SQL.

   ![Scheda "Informazioni di base" per la creazione di un'istanza gestita di SQL](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-basics.png)

   Usare la tabella seguente come riferimento per le informazioni necessarie in questa scheda.

   | Impostazione| Valore consigliato | Descrizione |
   | ------ | --------------- | ----------- |
   | **Sottoscrizione** | Sottoscrizione in uso. | Una sottoscrizione che fornisce l'autorizzazione per creare nuove risorse. |
   | **Gruppo di risorse** | Gruppo di risorse nuovo o esistente.|Per i nomi di gruppi di risorse validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/resource-naming).|
   | **Nome istanza gestita** | Qualsiasi nome valido.|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/resource-naming).|
   | **Area** |Area in cui si vuole creare l'istanza gestita.|Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/).|
   | **Account di accesso amministratore istanza gestita** | Qualsiasi nome utente valido. | Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/resource-naming). Non usare "serveradmin" perché è un ruolo a livello di server riservato.|
   | **Password** | Qualsiasi password valida.| La password deve contenere almeno 16 caratteri e soddisfare i [requisiti di complessità definiti](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Selezionare **Configura istanza gestita** per definire le dimensioni delle risorse di calcolo e di archiviazione ed esaminare i piani tariffari. Usare i dispositivi di scorrimento o le caselle di testo per specificare la quantità di spazio di archiviazione e il numero di core virtuali. Al termine, selezionare **Applica** per salvare la selezione. 

   ![Modulo dell'istanza gestita](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-configure-performance.png)

| Impostazione| Valore consigliato | Descrizione |
| ------ | --------------- | ----------- |
| **Livello di servizio** | Selezionare una delle opzioni disponibili. | A seconda dello scenario, selezionare una delle opzioni seguenti: </br> <ul><li>**Per utilizzo generico**: per la maggior parte dei carichi di lavoro di produzione e l'opzione predefinita.</li><li>**Business critical**: progettato per carichi di lavoro a bassa latenza con resilienza elevata a errori e failover rapidi.</li></ul><BR>Per altre informazioni, vedere i [livelli di servizio del database SQL di Azure e di Azure sql istanza gestita](../../azure-sql/database/service-tiers-general-purpose-business-critical.md) e rivedere [i limiti delle risorse di istanza gestita SQL di Azure](../../azure-sql/managed-instance/resource-limits.md).|
| **Generazione hardware** | Selezionare una delle opzioni disponibili. | La generazione hardware definisce in genere i limiti di calcolo e memoria e altre caratteristiche che influiscano sulle prestazioni del carico di lavoro. **Quinta generazione** è il valore predefinito.|
| **modello di calcolo vCore** | Selezionare un'opzione. | VCore rappresentano la quantità esatta di risorse di calcolo di cui è sempre stato effettuato il provisioning per il carico di lavoro. Il valore predefinito è **otto Vcore** .|
| **Archiviazione in GB** | Selezionare un'opzione. | Dimensioni di archiviazione in GB, selezionare in base alle dimensioni dei dati previsti. Se si esegue la migrazione dei dati esistenti dall'ambiente locale o da varie piattaforme cloud, vedere [Panoramica della migrazione: SQL Server a SQL istanza gestita](../../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-overview.md).|
| **Vantaggio Azure Hybrid** | Selezionare l'opzione se applicabile. | Per sfruttare una licenza esistente per Azure. Per altre informazioni, vedere [vantaggio Azure Hybrid-database SQL di Azure & istanza gestita SQL](../../azure-sql/azure-hybrid-benefit.md). |
| **Ridondanza dell'archiviazione di backup** | Selezionare **archiviazione di backup con ridondanza geografica**. | Ridondanza di archiviazione in Azure per l'archiviazione di backup. Si noti che questo valore non può essere modificato in un secondo momento. L'archiviazione di backup con ridondanza geografica è predefinita e consigliata, anche se la zona e la ridondanza locale consentono una maggiore flessibilità e una residenza dei dati a area singola. Per altre informazioni, vedere [ridondanza dell'archiviazione di backup](../database/automated-backups-overview.md?tabs=managed-instance#backup-storage-redundancy).|


- Per rivedere le scelte effettuate prima di creare un'istanza gestita di SQL, è possibile selezionare **Rivedi e crea**. In alternativa, configurare le opzioni di rete selezionando **Avanti: Rete**.

### <a name="networking-tab"></a>Scheda Rete

- Compilare le informazioni facoltative nella scheda **Rete**. Se si omettono queste informazioni, il portale applicherà le impostazioni predefinite.

   ![Scheda "Rete" per la creazione di un'istanza gestita](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-networking.png)

   Usare la tabella seguente come riferimento per le informazioni necessarie in questa scheda.

   | Impostazione| Valore consigliato | Descrizione |
   | ------ | --------------- | ----------- |
   | **Rete virtuale** | Selezionare **Crea nuova rete virtuale** o una rete virtuale valida e una subnet.| Se una rete o una subnet non è disponibile, prima di selezionarla come destinazione per la nuova istanza gestita, è necessario [modificarla per soddisfare i requisiti di rete](vnet-existing-add-subnet.md). Per informazioni sui requisiti per la configurazione dell'ambiente di rete per Istanza gestita di SQL, vedere [Configurare una rete virtuale per Istanza gestita di SQL](connectivity-architecture-overview.md). |
   | **Tipo di connessione** | Scegliere tra un tipo di connessione proxy e uno con reindirizzamento.|Per altre informazioni sui tipi di connessione, vedere [Tipo di connessione dell'istanza gestita di SQL di Azure](../database/connectivity-architecture.md#connection-policy).|
   | **Endpoint pubblico**  | Selezionare **Disabilita**. | Affinché l'istanza gestita sia accessibile tramite l'endpoint dati pubblico, è necessario abilitare questa opzione. | 
   | **Consenti l'accesso da** (se è abilitata l'opzione **Endpoint pubblico**) | Seleziona **Nessun accesso**  |L'esperienza del portale consente di configurare un gruppo di sicurezza con un endpoint pubblico. </br> </br> A seconda dello scenario, selezionare una delle opzioni seguenti: </br> <ul> <li>**Servizi di Azure**: questa opzione è consigliabile in caso di connessione da Power BI o un altro servizio multi-tenant. </li> <li> **Internet**: usare questa opzione a scopo di test quando si vuole creare rapidamente un'istanza gestita. Non è consigliabile per ambienti di produzione. </li> <li> **Nessun accesso**: questa opzione crea una regola di sicurezza **Nega**. Modificare questa regola per rendere un'istanza gestita accessibile tramite un endpoint pubblico. </li> </ul> </br> Per altre informazioni sulla sicurezza dell'endpoint pubblico, vedere [Uso sicuro dell'istanza gestita di SQL di Azure con un endpoint pubblico](public-endpoint-overview.md).|

- Selezionare **Rivedi e crea** per rivedere le scelte effettuate prima di creare un'istanza gestita. In alternativa, configurare altre impostazioni personalizzate selezionando **Avanti: Impostazioni aggiuntive**.


### <a name="additional-settings"></a>Impostazioni aggiuntive

- Compilare le informazioni facoltative nella scheda **Impostazioni aggiuntive**. Se si omettono queste informazioni, il portale applicherà le impostazioni predefinite.

   ![Scheda "Impostazioni aggiuntive" per la creazione di un'istanza gestita](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-additional-settings.png)

   Usare la tabella seguente come riferimento per le informazioni necessarie in questa scheda.

   | Impostazione| Valore consigliato | Descrizione |
   | ------ | --------------- | ----------- |
   | **Regole di confronto** | Scegliere le regole di confronto da usare per l'istanza gestita. In caso di migrazione di database da SQL Server, controllare le regole di confronto di origine con `SELECT SERVERPROPERTY(N'Collation')` e usare tale valore.| Per informazioni sulle regole di confronto, vedere [Impostare o modificare le regole di confronto del server](/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Fuso orario** | Selezionare il fuso orario che verrà osservato dall'istanza gestita.|Per altre informazioni, vedere [Fusi orari](timezones-overview.md).|
   | **Usa come failover secondario** | Selezionare **Sì**. | Abilitare questa opzione per usare l'istanza gestita come istanza secondaria del gruppo di failover.|
   | **Istanza gestita di SQL primaria** (se l'opzione **Usa come failover secondario** è impostata su **Sì**) | Scegliere un'istanza gestita primaria esistente che verrà aggiunta alla stessa zona DNS dell'istanza gestita che si sta creando. | Questo passaggio consentirà la configurazione del gruppo di failover dopo la creazione. Per altre informazioni, vedere [Esercitazione: Aggiungere l'istanza gestita a un gruppo di failover](failover-group-add-instance-tutorial.md).|

- Selezionare **Rivedi e crea** per rivedere le scelte effettuate prima di creare un'istanza gestita. In alternativa, configurare i tag di Azure selezionando **Avanti: Tag** (scelta consigliata).

### <a name="tags"></a>Tag

- Aggiungere tag alle risorse nel modello di Azure Resource Manager (modello ARM). I [tag](/azure/azure-resource-manager/management/tag-resources) consentono di organizzare le risorse in modo logico. I valori dei tag vengono visualizzati nei report sui costi e consentono altre attività di gestione in base al tag. 

- Si consiglia di contrassegnare almeno la nuova Istanza gestita SQL con il tag Owner per identificare chi ha creato e il tag di ambiente per identificare se il sistema è di produzione, sviluppo e così via. Per altre informazioni, vedere [sviluppare la strategia di denominazione e assegnazione di tag per le risorse di Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
 
- Selezionare **Verifica + crea** per continuare.

## <a name="review--create"></a>Rivedi e crea

1. Selezionare la scheda **Rivedi e crea** per rivedere le scelte effettuate prima di creare un'istanza gestita.

   ![Scheda per rivedere e creare un'istanza gestita](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-review-create.png)

1. Selezionare **Crea** per avviare il provisioning dell'istanza gestita.

> [!IMPORTANT]
> La distribuzione di un'istanza gestita è un'operazione di lunga durata. La distribuzione della prima istanza nella subnet richiede in genere molto più tempo rispetto alla distribuzione in una subnet contenente istanze gestite esistenti. Per i tempi di provisioning medi, vedere [Panoramica delle operazioni di gestione di istanza gestita SQL di Azure](management-operations-overview.md#duration).

## <a name="monitor-deployment-progress"></a>Monitorare lo stato di avanzamento della distribuzione

1. Selezionare l'icona **Notifiche** per visualizzare lo stato della distribuzione.

   ![Stato di avanzamento della distribuzione di un'istanza gestita di SQL](./media/instance-create-quickstart/azure-sql-managed-instance-create-deployment-in-progress.png)

1. Selezionare **Distribuzione in corso** nella notifica per aprire la finestra dell'istanza gestita di SQL e monitorare ulteriormente lo stato di avanzamento della distribuzione. 

> [!TIP]
> - Se il Web browser è stato chiuso o è stato rimosso dalla schermata di stato della distribuzione, è possibile monitorare l'operazione di provisioning tramite la pagina **Panoramica** dell'istanza gestita oppure tramite PowerShell o l'interfaccia della riga di comando di Azure. Per ulteriori informazioni, vedere [monitoraggio delle operazioni](management-operations-monitor.md#monitor-operations). 
> - È possibile annullare il processo di provisioning tramite portale di Azure o tramite PowerShell o l'interfaccia della riga di comando di Azure o altri strumenti usando l'API REST. Vedere [annullamento delle operazioni di gestione di istanza gestita SQL di Azure](management-operations-cancel.md).

> [!IMPORTANT]
> - L'avvio della creazione di Istanza gestita di SQL potrebbe essere ritardato a causa di altre operazioni a esecuzione prolungata già in corso, ad esempio operazioni di ripristino o dimensionamento di altre istanze gestite nella stessa subnet. Per altre informazioni, vedere [Effetti incrociati sulle operazioni di gestione](management-operations-overview.md#management-operations-cross-impact).
> - Per ottenere lo stato della creazione dell'istanza gestita, è necessario avere **autorizzazioni di lettura** per il gruppo di risorse. Se non si ha questa autorizzazione o la si revoca mentre è in corso la creazione dell'istanza gestita, è possibile che l'istanza gestita di SQL non sia visibile nell'elenco delle distribuzioni del gruppo di risorse.
>

## <a name="view-resources-created"></a>Visualizzare le risorse create

Al completamento della distribuzione dell'istanza gestita, per visualizzare le risorse create:

1. Aprire il gruppo di risorse per l'istanza gestita. 

   ![Risorse dell'istanza gestita di SQL](./media/instance-create-quickstart/azure-sql-managed-instance-resources.png)

## <a name="view-and-fine-tune-network-settings"></a>Visualizzare e ottimizzare le impostazioni di rete

Per ottimizzare facoltativamente le impostazioni di rete, controllare quanto segue:

1. Nell'elenco delle risorse selezionare la tabella di route per esaminare l'oggetto tabella di Route definito dall'utente (UDR) che è stato creato.

2. Nella tabella di route esaminare le voci per indirizzare il traffico dalla rete virtuale dell'istanza gestita di SQL e all'interno di tale rete. Se si crea o si configura manualmente la tabella di route, creare queste voci nella tabella di route SQL Istanza gestita.

   ![Voce per una subnet dell'istanza gestita di SQL in locale](./media/instance-create-quickstart/azure-sql-managed-instance-route-table-user-defined-route.png)

    Per modificare o aggiungere route, aprire le **Route** nelle impostazioni della tabella di route.

3. Tornare al gruppo di risorse e selezionare l'oggetto gruppo di sicurezza di rete (NSG) che è stato creato.

4. Esaminare le regole di sicurezza in ingresso e in uscita. 

   ![Regole di sicurezza](./media/instance-create-quickstart/azure-sql-managed-instance-security-rules.png)

    Per modificare o aggiungere regole, aprire le regole di sicurezza in **ingresso** e le **regole di sicurezza in uscita** nelle impostazioni del gruppo di sicurezza di rete.

> [!IMPORTANT]
> Se è stato configurato un endpoint pubblico per Istanza gestita di SQL, è necessario aprire le porte per autorizzare il traffico di rete che consente le connessioni all'istanza gestita di SQL dalla rete Internet pubblica. Per altre informazioni, vedere [configurare un endpoint pubblico per SQL istanza gestita](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).
>

## <a name="retrieve-connection-details-to-sql-managed-instance"></a>Recuperare i dettagli della connessione all'istanza gestita di SQL

Per connettersi all'istanza gestita di SQL, seguire questa procedura per recuperare il nome host e il nome di dominio completo (FQDN):

1. Tornare al gruppo di risorse e selezionare l'oggetto istanza gestita di SQL creato.

2. Nella scheda **Panoramica** individuare la proprietà **Host**. Copiare il nome host negli Appunti per l'istanza gestita da usare nella Guida introduttiva successiva facendo clic sul pulsante **copia negli Appunti** .

   ![Nome host](./media/instance-create-quickstart/azure-sql-managed-instance-host-name.png)

   Il valore copiato rappresenta un nome di dominio completo (FQDN) che può essere usato per connettersi all'istanza gestita di SQL. È simile all'esempio di indirizzo seguente: *your_host_name.a1b2c3d4e5f6.database.windows.net*.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come connettersi a Istanza gestita di SQL:
- Per una panoramica delle opzioni di connessione per le applicazioni, vedere [Connettere le applicazioni a Istanza gestita di SQL](connect-application-instance.md).
- Per una guida di avvio rapido che illustra come connettersi a Istanza gestita di SQL tramite una macchina virtuale di Azure, vedere [Configurare una connessione tramite macchina virtuale di Azure](connect-vm-instance-configure.md).
- Per una guida di avvio rapido che illustra come connettersi a Istanza gestita di SQL tramite un computer client locale usando una connessione da punto a sito, vedere [Configurare una connessione da punto a sito](point-to-site-p2s-configure.md).

Per ripristinare un database di SQL Server esistente dall'ambiente locale a Istanza gestita di SQL: 
- Usare il [Servizio Migrazione del database di Azure per la migrazione](../../dms/tutorial-sql-server-to-managed-instance.md) per eseguire il ripristino da un file di backup del database. 
- Usare il [comando T-SQL RESTORE](restore-sample-database-quickstart.md) per eseguire il ripristino da un file di backup del database.

Per informazioni sul monitoraggio avanzato delle prestazioni del database dell'istanza gestita di SQL con intelligence predefinita per la risoluzione dei problemi, vedere [Monitorare l'istanza gestita di SQL di Azure usando Analisi SQL di Azure](../../azure-monitor/insights/azure-sql.md).
