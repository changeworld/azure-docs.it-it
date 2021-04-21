---
title: Gruppi di failover automatico
titleSuffix: Azure SQL Database & SQL Managed Instance
description: I gruppi di failover automatico consentono di gestire la replica e il failover automatico/coordinato di un gruppo di database in un server o in tutti i database in un'istanza gestita.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/26/2021
ms.openlocfilehash: f3bc1dfcfeeb6dda110f71ed7a1c53909153cf00
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762156"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Usare i gruppi di failover automatico per consentire il failover trasparente e coordinato di più database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

La funzionalità gruppi di failover automatico consente di gestire la replica e il failover di un gruppo di database in un server o di tutti i database in un'istanza gestita in un'altra area. Si tratta di un'astrazione dichiarativa sulla funzionalità di replica [geografica](active-geo-replication-overview.md) attiva esistente, progettata per semplificare la distribuzione e la gestione dei database con replica geografica su larga scala. È possibile avviare il failover manualmente oppure delegarlo al servizio di Azure in base a criteri definiti dall'utente. Quest'ultima opzione consente di ripristinare automaticamente più database correlati in un'area secondaria dopo un errore irreversibile o un altro evento non pianificato che comporta una perdita completa o parziale della disponibilità del database SQL o di SQL Istanza gestita nell'area primaria. Un gruppo di failover può includere uno o più database, in genere usati dalla stessa applicazione. È inoltre possibile usare i database secondari leggibili per l'offload dei carichi di lavoro delle query di sola lettura. Poiché i gruppi di failover automatico coinvolgono più database, questi ultimi devono essere configurati nel server primario. I gruppi di failover automatico supportano la replica di tutti i database nel gruppo in un solo server secondario o in un'istanza in un'area diversa.

> [!NOTE]
> Se si vogliono più database SQL di Azure secondarie nella stessa area o in aree diverse, usare [la replica geografica attiva.](active-geo-replication-overview.md)

Se si usano gruppi di failover automatico con criteri di failover automatico, eventuali interruzioni che influiscono su uno o più database nel gruppo determinano un failover automatico. In genere si tratta di eventi imprevisti che non possono essere attenuati automaticamente dalle operazioni di disponibilità elevata automatiche incorporate. Gli esempi di trigger di failover includono un evento imprevisto causato da un anello del tenant del database SQL o da un anello di controllo in stato di incompleto a causa di una perdita di memoria del kernel del sistema operativo in diversi nodi di calcolo o di un evento imprevisto causato dall'insodre di uno o più anelli del tenant perché un cavo di rete errato è stato tagliato durante la rimozione di routine dell'hardware.  Per altre informazioni, vedere Disponibilità elevata [del database SQL](high-availability-sla.md).

I gruppi di failover automatico forniscono anche endpoint di listener di sola lettura e di sola scrittura che rimangono invariati durante i failover. Se si usa l'attivazione di failover manuale o automatica, il failover trasforma tutti i database secondari nel gruppo in database primari. Dopo aver completato il failover del database, il record DNS viene automaticamente aggiornato per reindirizzare gli endpoint alla nuova area. Per i dati RPO e RTO specifici consultare la [panoramica della continuità aziendale](business-continuity-high-availability-disaster-recover-hadr-overview.md).

Quando si usano gruppi di failover automatico con criteri di failover automatico, qualsiasi interruzione che influisce sui database in un server o in un'istanza gestita comporta il failover automatico. È possibile gestire il gruppo di failover automatico mediante:

- [Portale di Azure](geo-distributed-application-configure-tutorial.md)
- [Interfaccia della riga di comando di Azure: Gruppo di failover](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: gruppo di failover](scripts/add-database-to-failover-group-powershell.md)
- [API REST: gruppo di failover](/rest/api/sql/failovergroups)

Dopo il failover, verificare che i requisiti di autenticazione per il database e il server o l'istanza siano configurati nel nuovo database primario. Per tutti i dettagli, vedere l'articolo sulla [sicurezza del database SQL di Azure dopo il ripristino di emergenza](active-geo-replication-security-configure.md).

Per ottenere una reale continuità aziendale, l'aggiunta di ridondanza dei database tra i data center rappresenta solo una parte della soluzione. Per ripristinare un'applicazione (servizio) end-to-end dopo un problema grave, è necessario effettuare il ripristino di tutti i componenti del servizio e gli eventuali servizi dipendenti. Esempi di questi componenti includono il software client (ad esempio, un browser con JavaScript personalizzato), front-end Web, spazio di archiviazione e DNS. È fondamentale che tutti i componenti siano resilienti agli stessi problemi e diventino disponibili entro I'obiettivo del tempo di ripristino (RTO) dell'applicazione. È perciò necessario identificare tutti i servizi dipendenti e comprendere quali garanzie e funzionalità vengono fornite. È quindi necessario intraprendere le azioni appropriate per assicurare il funzionamento del servizio durante il failover dei servizi da cui dipende. Per altre informazioni sulla progettazione di soluzioni per il ripristino di emergenza, vedere [Progettazione di applicazioni per il ripristino di emergenza cloud con la replica geografica attiva in database SQL](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="terminology-and-capabilities"></a>Terminologia e funzionalità

- **Gruppo di failover (BIE)**

  Un gruppo di failover è un gruppo denominato di database gestiti da un singolo server o all'interno di un'istanza gestita che può eseguire il failover come unità in un'altra area nel caso in cui tutti o alcuni database primari diventino non disponibili a causa di un'interruzione nell'area primaria. Quando viene creato per SQL Istanza gestita, un gruppo di failover contiene tutti i database utente nell'istanza e pertanto è possibile configurare un solo gruppo di failover in un'istanza.
  
  > [!IMPORTANT]
  > Il nome del gruppo di failover deve essere univoco a livello globale all'interno del `.database.windows.net` dominio.

- **Server**

     Con i server, alcuni o tutti i database utente in un server possono essere inseriti in un gruppo di failover. Inoltre, un server supporta più gruppi di failover in un singolo server.

- **Server/istanza primaria**

  Server o istanza gestita che ospita i database primari nel gruppo di failover.

- **Server/istanza secondaria**

  Server o istanza gestita che ospita i database secondari nel gruppo di failover. Il server o l'istanza secondaria non può trovarsi nella stessa area del server o dell'istanza primaria.

- **Aggiunta di database singoli al gruppo di failover**

  È possibile inserire più database singoli nello stesso server nello stesso gruppo di failover. Se si aggiunge un database singolo al gruppo di failover, viene creato automaticamente un database secondario usando la stessa edizione e le stesse dimensioni di calcolo nel server secondario.  Tale server è stato specificato quando è stato creato il gruppo di failover. Se si aggiunge un database che ha già un database secondario nel server secondario, tale collegamento della replica geografica viene ereditato dal gruppo. Quando si aggiunge un database che dispone già di un database secondario in un server che non fa parte del gruppo di failover, viene creato un nuovo database secondario nel server secondario.

  > [!IMPORTANT]
  > Assicurarsi che il server secondario non abbia un database con lo stesso nome, a meno che non sia un database secondario esistente. Nei gruppi di failover per SQL Istanza gestita vengono replicati tutti i database utente. Non è possibile selezionare un subset di database utente per la replica nel gruppo di failover.

- **Aggiunta di database nel pool elastico al gruppo di failover**

  È possibile inserire tutti o alcuni database all'interno di un pool elastico nello stesso gruppo di failover. Se il database primario si trova in un pool elastico, il database secondario viene creato automaticamente nel pool elastico con lo stesso nome (pool secondario). È necessario assicurarsi che il server secondario contenga un pool elastico con lo stesso identico nome e capacità sufficiente per ospitare i database secondari che verranno creati dal gruppo di failover. Se si aggiunge un database nel pool che ha già un database secondario nel pool secondario, tale collegamento della replica geografica viene ereditato dal gruppo. Quando si aggiunge un database che ha già un database secondario in un server che non fa parte del gruppo di failover, viene creato un nuovo database secondario nel pool secondario.
  
- **Seeding iniziale**

  Quando si aggiungono database, pool elastici o istanze gestite a un gruppo di failover, è presente una fase di seeding iniziale prima dell'avvio della replica dei dati. La fase di seeding iniziale è l'operazione più lunga e più costosa. Al termine del seeding iniziale, i dati vengono sincronizzati e vengono replicate solo le modifiche successive ai dati. Il tempo necessario per il completamento del seeding iniziale dipende dalle dimensioni dei dati, dal numero di database replicati e dalla velocità del collegamento tra le entità nel gruppo di failover. In circostanze normali, la possibile velocità di seeding è fino a 500 GB all'ora per il database SQL e fino a 360 GB all'ora per SQL Istanza gestita. Il seeding viene eseguito per tutti i database in parallelo.

  Per sql Istanza gestita, prendere in considerazione la velocità del collegamento Express Route tra le due istanze durante la stima dell'ora della fase di seeding iniziale. Se la velocità del collegamento tra le due istanze è più lenta rispetto a quanto necessario, è probabile che il tempo di seed è notevolmente intasato. È possibile usare la velocità di seeding indicata, il numero di database, le dimensioni totali dei dati e la velocità di collegamento per stimare il tempo necessario per la fase di seeding iniziale prima dell'avvio della replica dei dati. Ad esempio, per un singolo database da 100 GB, la fase di inizializzazione iniziale potrebbe richiedere circa 1,2 ore se il collegamento è in grado di eseguire il push di 84 GB all'ora e se non sono presenti altri database di cui viene creato il seeded. Se il collegamento può trasferire solo 10 GB all'ora, il seeding di un database da 100 GB potrebbe richiedere circa 10 ore. Se sono presenti più database da replicare, il seeding verrà eseguito in parallelo e, se combinato con una velocità di collegamento lenta, la fase di seeding iniziale potrebbe richiedere molto più tempo, soprattutto se il seeding parallelo dei dati di tutti i database supera la larghezza di banda del collegamento disponibile. Se la larghezza di banda di rete tra due istanze è limitata e si aggiungono più istanze gestite a un gruppo di failover, è consigliabile aggiungere più istanze gestite al gruppo di failover in sequenza, una alla volta. Dato uno SKU gateway di dimensioni appropriate tra le due istanze gestite e se la larghezza di banda della rete aziendale lo consente, è possibile ottenere velocità fino a 360 GB all'ora.  

- **Zona DNS**

  ID univoco generato automaticamente quando viene creato un nuovo Istanza gestita SQL. Viene effettuato il provisioning di un certificato multi-dominio (SAN) per questa istanza per autenticare le connessioni client a qualsiasi istanza nella stessa zona DNS. Le due istanze gestite nello stesso gruppo di failover devono condividere la zona DNS.
  
  > [!NOTE]
  > Non è necessario un ID zona DNS per i gruppi di failover creati per il database SQL.

- **Listener di lettura/scrittura del gruppo di failover**

  Record CNAME DNS che punta all'URL del database primario corrente. Viene creato automaticamente quando viene creato il gruppo di failover e consente al carico di lavoro di lettura/scrittura di riconnettersi in modo trasparente al database primario quando il database primario viene modificato dopo il failover. Quando il gruppo di failover viene creato in un server, il record CNAME DNS per l'URL del listener viene formato come `<fog-name>.database.windows.net` . Quando il gruppo di failover viene creato in un Istanza gestita SQL, il record CNAME DNS per l'URL del listener viene formato come `<fog-name>.<zone_id>.database.windows.net` .

- **Listener di sola lettura del gruppo di failover**

  Un record CNAME DNS che punta al listener di sola lettura che punta all'URL del database secondario. Viene creato automaticamente quando viene creato il gruppo di failover e consente al carico di lavoro SQL di sola lettura di connettersi in modo trasparente al database secondario usando le regole di bilanciamento del carico specificate. Quando il gruppo di failover viene creato in un server, il record CNAME DNS per l'URL del listener viene formato come `<fog-name>.secondary.database.windows.net` . Quando il gruppo di failover viene creato in un Istanza gestita SQL, il record CNAME DNS per l'URL del listener viene formato come `<fog-name>.secondary.<zone_id>.database.windows.net` .

- **Criteri di failover automatico**

  Per impostazione predefinita un gruppo di failover viene configurato con criteri di failover automatico. Azure attiva il failover dopo che l'errore è stato rilevato e il periodo di tolleranza è scaduto. Il sistema deve verificare che l'interruzione non [](high-availability-sla.md) possa essere mitigata dall'infrastruttura a disponibilità elevata incorporata a causa della portata dell'impatto. Se si desidera controllare il flusso di lavoro di failover dall'applicazione o manualmente, è possibile disattivare il failover automatico.
  
  > [!NOTE]
  > Poiché la verifica della scala dell'interruzione e della velocità con cui può essere mitigata comporta azioni umane da parte del team operativo, il periodo di tolleranza non può essere impostato al di sotto di un'ora. Questa limitazione si applica a tutti i database nel gruppo di failover indipendentemente dal relativo stato di sincronizzazione dei dati.

- **Criteri di failover di sola lettura**

  Per impostazione predefinita, il failover del listener di sola lettura è disabilitato. Verificare che le prestazioni del database primario non siano interessate quando il database secondario è offline. Ciò significa anche che le sessioni di sola lettura non sono in grado di connettersi fino a quando il database secondario non viene recuperato. Se non è possibile tollerare tempi di inattività per le sessioni di sola lettura e si può usare il database primario per il traffico di sola lettura e di lettura/scrittura a scapito della potenziale riduzione delle prestazioni del database primario, è possibile abilitare il failover per il listener di sola lettura configurando la `AllowReadOnlyFailoverToPrimary` proprietà . In tal caso, il traffico di sola lettura verrà reindirizzato automaticamente al database primario se il database secondario non è disponibile.

  > [!NOTE]
  > La proprietà ha effetto solo se i criteri di failover automatico sono abilitati e un `AllowReadOnlyFailoverToPrimary` failover automatico è stato attivato da Azure. In tal caso, se la proprietà è impostata su True, il nuovo database primario verrà utilizzato sia per le sessioni di lettura/scrittura che per le sessioni di sola lettura.

- **Failover pianificato**

   Il failover pianificato esegue la sincronizzazione completa tra il database primario e il database secondario prima che il database secondario passi al ruolo primario. In questo modo si esclude la perdita di dati. Il failover pianificato viene usato negli scenari seguenti:

  - Eseguire esercitazioni per il ripristino di emergenza in produzione quando la perdita di dati non è accettabile
  - Rilocare i database in un'area diversa
  - Ripristinare i database nell'area primaria dopo la mitigazione dell'interruzione (failback)

- **Failover non pianificato**

   Con il failover non pianificato o forzato il database secondario passa immediatamente al ruolo primario senza alcuna sincronizzazione. Questa operazione comporta la perdita di dati. Un failover non pianificato viene usato come metodo di ripristino durante le interruzioni quando non è accessibile il database primario. Quando il database primario originale torna online, si riconnette automaticamente senza sincronizzazione e diventa un nuovo database secondario.

- **Failover manuale**

  È possibile avviare manualmente il failover in qualsiasi momento, indipendentemente dalla configurazione del failover automatico. Se non sono configurati criteri di failover automatico, è necessario eseguire il failover manuale per ripristinare i database del gruppo di failover nell'area secondaria. È possibile avviare il failover forzato o semplice (con sincronizzazione completa dei dati). Quest'ultimo può essere usato per rilocare il database primario nell'area secondaria. Al termine del failover, i record DNS vengono aggiornati automaticamente per garantire la connettività al nuovo database primario.

- **Periodo di tolleranza con perdita di dati**

  Poiché i database primario e secondario vengono sincronizzati tramite la replica asincrona, il failover può comportare la perdita di dati. È possibile personalizzare i criteri di failover automatico per riflettere la tolleranza dell'applicazione verso la perdita dei dati. Configurando , è possibile controllare il tempo di attesa del sistema prima di avviare il failover che può causare `GracePeriodWithDataLossHours` la perdita di dati.

- **Più gruppi di failover**

  È possibile configurare più gruppi di failover per la stessa coppia di server per controllare l'ambito dei failover. Ogni gruppo esegue il failover in modo indipendente. Se l'applicazione multi-tenant fa uso di pool elastici, è possibile usare questa funzionalità per combinare i database primari e secondari in ogni pool. In questo modo è possibile ridurre l'impatto di un'interruzione a solo la metà dei tenant.

  > [!NOTE]
  > SQL Istanza gestita non supporta più gruppi di failover.
  
## <a name="permissions"></a>Autorizzazioni

Le autorizzazioni per un gruppo di failover vengono gestite tramite [il controllo degli accessi in](../../role-based-access-control/overview.md)base al ruolo di Azure. Il [SQL Server collaboratore](../../role-based-access-control/built-in-roles.md#sql-server-contributor) ha tutte le autorizzazioni necessarie per gestire i gruppi di failover.

### <a name="create-failover-group"></a>Create failover group

Per creare un gruppo di failover, è necessario l'accesso in scrittura del controllo degli accessi in base al ruolo di Azure ai server primario e secondario e a tutti i database nel gruppo di failover. Per un Istanza gestita SQL, è necessario l'accesso in scrittura del controllo degli accessi in base al ruolo di Azure al Istanza gestita SQL primario e secondario, ma le autorizzazioni per i singoli database non sono rilevanti, perché i singoli database SQL Istanza gestita non possono essere aggiunti o rimossi da un gruppo di failover.

### <a name="update-a-failover-group"></a>Aggiornare un gruppo di failover

Per aggiornare un gruppo di failover, è necessario l'accesso in scrittura del controllo degli accessi in base al ruolo di Azure al gruppo di failover e a tutti i database nel server primario o nell'istanza gestita corrente.  

### <a name="fail-over-a-failover-group"></a>Eseguire il failover di un gruppo di failover

Per eseguire il failover di un gruppo di failover, è necessario l'accesso in scrittura del controllo degli accessi in base al ruolo di Azure al gruppo di failover nel nuovo server primario o istanza gestita.

## <a name="best-practices-for-sql-database"></a>Procedure consigliate per il database SQL

Il gruppo di failover automatico deve essere configurato nel server primario e lo connetterà al server secondario in un'area di Azure diversa. I gruppi possono includere alcuni o tutti i database in questi server. Il diagramma seguente illustra una configurazione tipica di un'applicazione cloud con ridondanza geografica con più database e un gruppo di failover automatico.

![Il diagramma mostra una configurazione tipica di un'applicazione cloud con ridondanza geografica che usa più database e un gruppo di failover automatico.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> Per [un'esercitazione dettagliata sull'aggiunta](failover-group-add-single-database-tutorial.md) di un database nel database SQL a un gruppo di failover, vedere Aggiungere un database SQL a un gruppo di failover.

Quando si progetta un servizio facendo particolare attenzione alla continuità aziendale, seguire queste linee guida generali:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Uso di uno o più gruppi di failover per gestire il failover di più database

È possibile creare uno o più gruppi di failover tra due server in aree diverse (server primario e secondario). Ogni gruppo può includere uno o più database che vengono ripristinati come unità nel caso in cui tutti o alcuni database primari diventino non disponibili a causa di un'interruzione nell'area primaria. Il gruppo di failover crea un database di replica geografica secondaria con lo stesso obiettivo di servizio di quello primario. Se si aggiunge una relazione di replica geografica esistente al gruppo di failover, verificare che il database di replica geografica secondario sia configurato con lo stesso livello di servizio e le stesse dimensioni di calcolo del database primario.
  
> [!IMPORTANT]
> La creazione di gruppi di failover tra due server in sottoscrizioni diverse non è attualmente supportata per database SQL di Azure. Se si sposta il server primario o secondario in una sottoscrizione diversa dopo la creazione del gruppo di failover, potrebbero verificarsi errori delle richieste di failover e di altre operazioni.

### <a name="using-read-write-listener-for-oltp-workload"></a>Uso del listener di lettura/scrittura per il carico di lavoro OLTP

Quando si eseguono operazioni OLTP, usare `<fog-name>.database.windows.net` come URL del server per indirizzare automaticamente le connessioni al database primario. Questo URL non cambia dopo il failover. Si noti che il failover comporta l'aggiornamento del record DNS in modo che le connessioni client vengano reindirizzate al nuovo database primario solo dopo l'aggiornamento della cache DNS del client.

### <a name="using-read-only-listener-for-read-only-workload"></a>Uso del listener di sola lettura per il carico di lavoro di sola lettura

Se è presente un carico di lavoro di sola lettura isolato logicamente che tollera un certo grado di obsolescenza dei dati, è possibile usare il database secondario nell'applicazione. Per sessioni di sola lettura usare `<fog-name>.secondary.database.windows.net` come URL del server per indirizzare automaticamente le connessioni al server secondario. È anche consigliabile indicare la finalità di lettura nella stringa di connessione usando `ApplicationIntent=ReadOnly` .

> [!NOTE]
> Nei livelli di servizio Premium, business critical e Hyperscale, il database SQL supporta l'uso di [repliche](read-scale-out.md) di sola lettura per l'offload dei carichi di lavoro di query di sola lettura, usando il parametro nella stringa di `ApplicationIntent=ReadOnly` connessione. Dopo aver configurato un database secondario con replica geografica, sarà possibile usare questa funzionalità per connettersi a una replica di sola lettura nella posizione primaria o nella posizione con replica geografica.
>
> - Per connettersi a una replica di sola lettura nel percorso primario, usare `ApplicationIntent=ReadOnly` e `<fog-name>.database.windows.net` .
> - Per connettersi a una replica di sola lettura nel percorso secondario, usare `ApplicationIntent=ReadOnly` e `<fog-name>.secondary.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Preparazione per la riduzione delle prestazioni

Una tipica applicazione Azure usa più servizi di Azure ed è costituita da più componenti. Il failover automatico del gruppo di failover viene attivato in base allo stato dei Azure SQL componenti. Altri servizi di Azure nell'area primaria potrebbero non essere interessati dall'interruzione del servizio e i relativi componenti potrebbero essere ancora disponibili in tale area. Quando i database primari passano all'area di ripristino di emergenza, la latenza tra i componenti dipendenti può aumentare. Per evitare l'impatto di una latenza maggiore sulle prestazioni dell'applicazione, verificare la ridondanza di tutti i componenti dell'applicazione nell'area di ripristino di emergenza e seguire queste linee guida per la [sicurezza di rete.](#failover-groups-and-network-security)

### <a name="preparing-for-data-loss"></a>Preparazione per la perdita di dati

Se viene rilevata un'interruzione, Azure attende il periodo specificato da `GracePeriodWithDataLossHours` . Il valore predefinito è 1 ora. Se non è possibile permettersi la perdita di dati, assicurarsi di impostare su un numero `GracePeriodWithDataLossHours` sufficientemente elevato, ad esempio 24 ore. Usare il failover manuale dei gruppi per eseguire il failback dal server secondario a quello primario.

> [!IMPORTANT]
> I pool elastici con 800 o meno DTU e più di 250 database con replica geografica possono riscontrare problemi quali failover pianificati più lunghi e un peggioramento delle prestazioni.  Questi problemi si verificano con maggiore probabilità nella scrittura di carichi di lavoro con utilizzo intensivo, quando gli endpoint con replica geografica sono ampiamente separati per area geografica o quando vengono utilizzati più endpoint secondari per ogni database.  I sintomi di questi problemi emergono quando il ritardo della replica geografica aumenta nel tempo.  Questo ritardo può essere monitorato mediante [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Se si verificano questi problemi, per prevenirli si può aumentare il numero di DTU nel pool o ridurre il numero di database replicati geograficamente nello stesso pool.

### <a name="changing-secondary-region-of-the-failover-group"></a>Modifica dell'area secondaria del gruppo di failover

Per illustrare la sequenza di modifiche, si presuppone che il server A sia il server primario, il server B sia il server secondario esistente e il server C sia il nuovo server secondario nella terza area.  Per eseguire la transizione, seguire questa procedura:

1. Creare database secondari aggiuntivi di ogni database nel server A al server C usando [la replica geografica attiva.](active-geo-replication-overview.md) Ogni database nel server A avrà due database secondari, uno sul server B e uno sul server C. Ciò garantisce che i database primari rimangano protetti durante la transizione.
2. Eliminare il gruppo di failover. A questo punto gli account di accesso avranno esito negativo. Questo perché gli alias SQL per i listener del gruppo di failover sono stati eliminati e il gateway non riconoscerà il nome del gruppo di failover.
3. Ri-creare il gruppo di failover con lo stesso nome tra i server A e C. A questo punto gli account di accesso smetteranno di riuscire.
4. Aggiungere tutti i database primari nel server A al nuovo gruppo di failover.
5. Eliminare il server B. Tutti i database in B verranno eliminati automaticamente.

### <a name="changing-primary-region-of-the-failover-group"></a>Modifica dell'area primaria del gruppo di failover

Per illustrare la sequenza di modifiche, si presuppone che il server A sia il server primario, il server B sia il server secondario esistente e il server C sia il nuovo server primario nella terza area.  Per eseguire la transizione, seguire questa procedura:

1. Eseguire un failover pianificato per passare il server primario a B. Il server A diventerà il nuovo server secondario. Il failover può comportare alcuni minuti di inattività. Il tempo effettivo dipenderà dalle dimensioni del gruppo di failover.
2. Creare repliche secondarie aggiuntive di ogni database nel server B al server C usando [la replica geografica attiva.](active-geo-replication-overview.md) Ogni database nel server B avrà due database secondari, uno nel server A e uno sul server C. Ciò garantisce che i database primari rimangano protetti durante la transizione.
3. Eliminare il gruppo di failover. A questo punto gli account di accesso avranno esito negativo. Questo perché gli alias SQL per i listener del gruppo di failover sono stati eliminati e il gateway non riconoscerà il nome del gruppo di failover.
4. Creare nuovamente il gruppo di failover con lo stesso nome tra i server B e C. A questo punto gli account di accesso smetteranno di riuscire.
5. Aggiungere tutti i database primari in B al nuovo gruppo di failover.
6. Eseguire un failover pianificato del gruppo di failover per cambiare B e C. A questo punto il server C diventerà il server primario e B, il database secondario. Tutti i database secondari nel server A verranno collegati automaticamente alle repliche primarie in C. Come nel passaggio 1, il failover può comportare diversi minuti di inattività.
7. Eliminare il server A. Tutti i database in A verranno eliminati automaticamente.

> [!IMPORTANT]
> Quando il gruppo di failover viene eliminato, vengono eliminati anche i record DNS per gli endpoint del listener. A questo punto, esiste una probabilità diverso da zero che qualcun altro crei un gruppo di failover o un alias del server con lo stesso nome, impedendo di usarlo nuovamente. Per ridurre al minimo il rischio, non usare nomi di gruppi di failover generici.

## <a name="best-practices-for-sql-managed-instance"></a>Procedure consigliate per SQL Istanza gestita

Il gruppo di failover automatico deve essere configurato nell'istanza primaria e la connetterà all'istanza secondaria in un'altra area di Azure.  Tutti i database nell'istanza verranno replicati nell'istanza secondaria.

Il diagramma seguente illustra una configurazione tipica di un'applicazione cloud con ridondanza geografica con un'istanza gestita un gruppo di failover automatico.

![diagramma di failover automatico](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> Vedere [Aggiungere un'istanza gestita a un](../managed-instance/failover-group-add-instance-tutorial.md) gruppo di failover per un'esercitazione dettagliata sull'aggiunta di un'istanza Istanza gestita SQL per l'uso del gruppo di failover.

Se l'applicazione usa SQL Istanza gestita come livello dati, seguire queste linee guida generali durante la progettazione per la continuità aziendale:

### <a name="creating-the-secondary-instance"></a>Creazione dell'istanza secondaria

Per garantire la connettività non interrotta all'istanza di SQL Istanza gestita dopo il failover, entrambe le istanze primaria e secondaria devono essere nella stessa zona DNS. Garantisce che lo stesso certificato multi-dominio (SAN) possa essere usato per autenticare le connessioni client a una delle due istanze nel gruppo di failover. Quando l'applicazione è pronta per la distribuzione di produzione, creare un Istanza gestita SQL secondario in un'area diversa e assicurarsi che condivia la zona DNS con il server SQL Istanza gestita. È possibile eseguire questa operazione specificando il parametro facoltativo durante la creazione. Se si usa PowerShell o l'API REST, il nome del parametro facoltativo è e il nome del campo facoltativo corrispondente nel portale di Azure `DNS Zone Partner` è Primary Istanza gestita.

> [!IMPORTANT]
> La prima istanza gestita creata nella subnet determina la zona DNS per tutte le istanze successive nella stessa subnet. Ciò significa che due istanze della stessa subnet non possono appartenere a zone DNS diverse.

Per altre informazioni sulla creazione del database SQL secondario Istanza gestita nella stessa zona DNS dell'istanza primaria, vedere [Creare un'istanza gestita secondaria](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance).

### <a name="using-geo-paired-regions"></a>Uso di aree geograficamente abbinate

Distribuire entrambe le istanze gestite in [aree associate](../../best-practices-availability-paired-regions.md) per motivi di prestazioni. Le istanze gestite che si trovano in aree geografiche associate hanno prestazioni di gran lunga migliori di quelle che si trovano in aree non associate. 

### <a name="enabling-replication-traffic-between-two-instances"></a>Abilitazione del traffico di replica tra due istanze

Poiché ogni istanza è isolata nella propria rete virtuale, è necessario consentire il traffico bidirezionale tra queste reti virtuali. Vedere [Gateway VPN di Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Creazione di un gruppo di failover tra istanze gestite in sottoscrizioni diverse

È possibile creare un gruppo di failover tra istanze gestite di SQL in due sottoscrizioni diverse, purché le sottoscrizioni siano associate allo stesso [tenant Azure Active Directory tenant](../../active-directory/fundamentals/active-directory-whatis.md#terminology). Quando si usa l'API di PowerShell, è possibile farlo specificando il parametro per l'istanza `PartnerSubscriptionId` di SQL Istanza gestita. Quando si usa l'API REST, ogni ID istanza incluso nel `properties.managedInstancePairs` parametro può avere un proprio id sottoscrizione.
  
> [!IMPORTANT]
> portale di Azure non supporta la creazione di gruppi di failover tra sottoscrizioni diverse. Inoltre, per i gruppi di failover esistenti tra sottoscrizioni e/o gruppi di risorse diversi, il failover non può essere avviato manualmente tramite il portale dal database SQL Istanza gestita. È quindi necessario avviarlo dall'istanza geografica secondaria.

### <a name="managing-failover-to-secondary-instance"></a>Gestione del failover nell'istanza secondaria

Il gruppo di failover gestisce il failover di tutti i database in Istanza gestita di SQL. Quando viene creato un gruppo, ogni database nell'istanza viene automaticamente sottoposto a replica geografica nell'istanza secondaria di Istanza gestita di SQL. Non è possibile usare gruppi di failover per avviare un failover parziale di un subset dei database.

> [!IMPORTANT]
> Se un database viene rimosso dal database SQL Istanza gestita, verrà eliminato automaticamente anche nel database SQL secondario geografico Istanza gestita.

### <a name="using-read-write-listener-for-oltp-workload"></a>Uso del listener di lettura/scrittura per il carico di lavoro OLTP

Quando si eseguono operazioni OLTP, usare `<fog-name>.zone_id.database.windows.net` come URL del server per indirizzare automaticamente le connessioni al database primario. Questo URL non cambia dopo il failover. Il failover comporta l'aggiornamento del record DNS in modo che le connessioni client vengano reindirizzate al nuovo database primario solo dopo l'aggiornamento della cache DNS del client. Poiché l'istanza secondaria condivide la zona DNS con la zona primaria, l'applicazione client sarà in grado di riconnettersi usando lo stesso certificato SAN.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Uso del listener di sola lettura per connettersi all'istanza secondaria

Se è presente un carico di lavoro di sola lettura isolato logicamente che tollera un certo grado di obsolescenza dei dati, è possibile usare il database secondario nell'applicazione. Per connettersi direttamente al database secondario con replica geografica, usare `<fog-name>.secondary.<zone_id>.database.windows.net` come URL del server.

> [!NOTE]
> Nel business critical, SQL Istanza gestita supporta l'uso di [repliche](read-scale-out.md) di sola lettura per l'offload dei carichi di lavoro di query di sola lettura, usando il parametro nella stringa `ApplicationIntent=ReadOnly` di connessione. Dopo aver configurato un database secondario con replica geografica, sarà possibile usare questa funzionalità per connettersi a una replica di sola lettura nella posizione primaria o nella posizione con replica geografica.
>
> - Per connettersi a una replica di sola lettura nel percorso primario, usare `ApplicationIntent=ReadOnly` e `<fog-name>.<zone_id>.database.windows.net` .
> - Per connettersi a una replica di sola lettura nella posizione secondaria, usare `ApplicationIntent=ReadOnly` e `<fog-name>.secondary.<zone_id>.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Preparazione per la riduzione delle prestazioni

Una tipica applicazione Azure usa più servizi di Azure ed è costituita da più componenti. Il failover automatico del gruppo di failover viene attivato in base allo stato dei Azure SQL componenti. Altri servizi di Azure nell'area primaria potrebbero non essere interessati dall'interruzione del servizio e i relativi componenti potrebbero essere ancora disponibili in tale area. Quando i database primari passano all'area secondaria, la latenza tra i componenti dipendenti può aumentare. Per evitare l'impatto di una latenza maggiore sulle prestazioni dell'applicazione, verificare la ridondanza di tutti i componenti dell'applicazione nell'area secondaria ed eseguire il failover dei componenti dell'applicazione insieme al database. In fase di configurazione, seguire [le linee guida sulla sicurezza di](#failover-groups-and-network-security) rete per garantire la connettività al database nell'area secondaria.

### <a name="preparing-for-data-loss"></a>Preparazione per la perdita di dati

Se viene rilevata un'interruzione, viene attivato un failover di lettura/scrittura in caso di perdita di dati pari a zero, al meglio delle informazioni. In caso contrario, il failover viene posticipato per il periodo specificato utilizzando `GracePeriodWithDataLossHours` . Se è stato specificato `GracePeriodWithDataLossHours`, potrebbe verificarsi una perdita di dati. Durante le interruzioni del servizio, generalmente Azure predilige la disponibilità. Se non è possibile permettersi la perdita di dati, assicurarsi di impostare GracePeriodWithDataLossHours su un numero sufficientemente elevato, ad esempio 24 ore, o disabilitare il failover automatico.

Subito dopo l'avvio del failover si verificherà l'aggiornamento DNS del listener di lettura/scrittura. Questa operazione non comporta la perdita di dati. Tuttavia, il processo di scambio dei ruoli del database può richiedere fino a 5 minuti in condizioni normali. Durante il processo alcuni database nella nuova istanza primaria rimarranno di sola lettura. Se viene avviato un failover tramite PowerShell, l'operazione per cambiare il ruolo di replica primaria è sincrona. Se viene avviato usando il portale di Azure, l'interfaccia utente indicherà lo stato di completamento. Se viene avviato mediante l'API REST, usare il meccanismo di polling standard di Azure Resource Manager per il monitoraggio del completamento.

> [!IMPORTANT]
> Usare il failover manuale dei gruppi per ripristinare i database primari nella posizione originale. Quando viene risolta l'interruzione del servizio che ha determinato il failover, sarà possibile spostare i database primari nel percorso originale. A tale scopo è necessario avviare il failover manuale del gruppo.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Modifica dell'area secondaria del gruppo di failover

Si supponga che l'istanza A sia l'istanza primaria, che l'istanza B sia l'istanza secondaria esistente e che l'istanza C sia la nuova istanza secondaria nella terza area.  Per eseguire la transizione, seguire questa procedura:

1. Creare l'istanza C con le stesse dimensioni di A e nella stessa zona DNS.
2. Eliminare il gruppo di failover tra le istanze A e B. A questo punto gli account di accesso avranno esito negativo perché gli alias SQL per i listener del gruppo di failover sono stati eliminati e il gateway non riconoscerà il nome del gruppo di failover. I database secondari verranno disconnessi dai database primari e diventeranno database di lettura/scrittura.
3. Creare un gruppo di failover con lo stesso nome tra l'istanza A e C. Seguire le istruzioni nel gruppo di failover con [SQL Istanza gestita tutorial](../managed-instance/failover-group-add-instance-tutorial.md). Si tratta di un'operazione di dimensione dei dati che verrà completata quando tutti i database dell'istanza A vengono inizializzati e sincronizzati.
4. Eliminare l'istanza B se non è necessario per evitare addebiti non necessari.

> [!NOTE]
> Dopo il passaggio 2 e fino al completamento del passaggio 3, i database nell'istanza A rimarranno non protetti da un errore irreversibile dell'istanza A.

### <a name="changing-primary-region-of-the-failover-group"></a>Modifica dell'area primaria del gruppo di failover

Si supponga che l'istanza A sia l'istanza primaria, che l'istanza B sia l'istanza secondaria esistente e che l'istanza C sia la nuova istanza primaria nella terza area.  Per eseguire la transizione, seguire questa procedura:

1. Creare l'istanza C con le stesse dimensioni di B e nella stessa zona DNS.
2. Connettersi all'istanza B e eseguire manualmente il failover per passare l'istanza primaria a B. L'istanza A diventerà automaticamente la nuova istanza secondaria.
3. Eliminare il gruppo di failover tra le istanze A e B. A questo punto gli account di accesso avranno esito negativo perché gli alias SQL per i listener del gruppo di failover sono stati eliminati e il gateway non riconoscerà il nome del gruppo di failover. I database secondari verranno disconnessi dai database primari e diventeranno database di lettura/scrittura.
4. Creare un gruppo di failover con lo stesso nome tra l'istanza A e C. Seguire le istruzioni riportate nell'esercitazione sul gruppo [di failover con istanza gestita](../managed-instance/failover-group-add-instance-tutorial.md). Si tratta di un'operazione di dimensione dei dati che verrà completata quando tutti i database dell'istanza A vengono inizializzati e sincronizzati.
5. Eliminare l'istanza A se non è necessario per evitare addebiti non necessari.

> [!CAUTION]
> Dopo il passaggio 3 e fino al completamento del passaggio 4, i database nell'istanza A rimarranno non protetti da un errore irreversibile dell'istanza A.

> [!IMPORTANT]
> Quando il gruppo di failover viene eliminato, vengono eliminati anche i record DNS per gli endpoint del listener. A questo punto, esiste una probabilità diverso da zero che un altro utente crei un gruppo di failover o un alias del server con lo stesso nome, impedendo di usarlo nuovamente. Per ridurre al minimo il rischio, non usare nomi di gruppi di failover generici.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>Abilitare scenari dipendenti da oggetti dai database di sistema
I database di sistema non vengono replicati nell'istanza secondaria in un gruppo di failover. Per abilitare scenari che dipendono da oggetti dei database di sistema, nell'istanza secondaria assicurarsi di creare gli stessi oggetti nel database secondario. Ad esempio, se si prevede di usare gli stessi account di accesso nell'istanza secondaria, assicurarsi di crearli con lo stesso SID. 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 


## <a name="failover-groups-and-network-security"></a>Gruppi di failover e sicurezza di rete

Per alcune applicazioni le regole di sicurezza richiedono che l'accesso di rete al livello dati sia limitato a uno o più componenti specifici, ad esempio una macchina virtuale, un servizio Web e così via. Questo requisito presenta alcune problematiche per la progettazione della continuità aziendale e l'uso dei gruppi di failover. Quando si implementa tale accesso con restrizioni, considerare le opzioni seguenti.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Uso dei gruppi di failover e delle regole di rete virtuale

Se si [](vnet-service-endpoint-rule-overview.md) usano endpoint servizio di rete virtuale e regole per limitare l'accesso al database nel database SQL o in SQL Istanza gestita, tenere presente che ogni endpoint servizio di rete virtuale si applica a una sola area di Azure. L'endpoint non consente ad altre aree di accettare le comunicazioni dalla subnet. Pertanto, solo le applicazioni client distribuite nella stessa area possono connettersi al database primario. Poiché il failover comporta il reindirizzamento delle sessioni client del database SQL a un server in un'area diversa (secondaria), queste sessioni avranno esito negativo se originate da un client esterno a tale area. Per questo motivo, i criteri di failover automatico non possono essere abilitati se i server o le istanze partecipanti sono inclusi nelle regole di rete virtuale. Per supportare il failover manuale, seguire questa procedura:

1. Effettuare il provisioning delle copie ridondanti dei componenti front-end dell'applicazione (servizio Web, macchine virtuali e così via) nell'area secondaria
2. Configurare le [regole di rete virtuale](vnet-service-endpoint-rule-overview.md) singolarmente per il server primario e per quello secondario
3. Abilitare il [failover front-end usando una configurazione di Gestione traffico](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Avviare il failover manuale quando viene rilevata l'interruzione. Questa opzione è ottimizzata per le applicazioni che richiedono la latenza coerente tra il front-end e il livello dati e supporta il ripristino quando il front-end, il livello dati o entrambi sono interessati dall'interruzione del servizio.

> [!NOTE]
> Se si usa il **listener di sola lettura** per il bilanciamento di un carico di lavoro di sola lettura, assicurarsi che il carico di lavoro venga eseguito in una macchina virtuale o in un'altra risorsa nell'area secondaria in modo da consentire la connessione al database secondario.

### <a name="use-failover-groups-and-firewall-rules"></a>Usare i gruppi di failover e le regole del firewall

Se il piano di continuità aziendale richiede il failover usando gruppi con failover automatico, è possibile limitare l'accesso al database nel database SQL usando le regole del firewall tradizionali. Per supportare il failover automatico, seguire questa procedura:

1. [Creare un indirizzo IP pubblico](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address).
2. [Creare un servizio di bilanciamento del carico pubblico](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) e assegnare l'indirizzo IP pubblico a tale servizio.
3. [Creare una rete virtuale e le macchine virtuali](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) per i componenti front-end.
4. [Creare un gruppo di sicurezza di rete](../../virtual-network/network-security-groups-overview.md) e configurare le connessioni in ingresso.
5. Assicurarsi che le connessioni in uscita siano aperte database SQL di Azure usando il tag di [servizio "Sql".](../../virtual-network/network-security-groups-overview.md#service-tags)
6. Creare una [regola del firewall del database SQL](firewall-configure.md) per consentire il traffico in ingresso dall'indirizzo IP pubblico creato nel passaggio 1.

Per altre informazioni su come configurare l'accesso in uscita e sull'indirizzo IP da usare nelle regole del firewall, vedere Connessioni in uscita [del servizio di bilanciamento del carico](../../load-balancer/load-balancer-outbound-connections.md).

La configurazione appena illustrata garantisce che il failover automatico non blocchi le connessioni tra i componenti front-end e presuppone che l'applicazione riesca a tollerare una maggiore latenza tra il front-end e il livello dati.

> [!IMPORTANT]
> Per garantire la continuità aziendale nel caso di interruzioni del servizio a livello di area è necessario verificare la ridondanza geografica sia per i componenti front-end che per i database.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Abilitazione della replica geografica tra le istanze gestite e le loro reti virtuali

Quando si configura un gruppo di failover tra istanze gestite sql primarie e secondarie in due aree diverse, ogni istanza viene isolata usando una rete virtuale indipendente. Per consentire il traffico di replica tra queste reti virtuali, assicurarsi che siano soddisfatti questi prerequisiti:

- Le due istanze di SQL Istanza gestita devono essere in aree di Azure diverse.
- Le due istanze di SQL Istanza gestita devono essere dello stesso livello di servizio e avere le stesse dimensioni di archiviazione.
- L'istanza secondaria di SQL Istanza gestita deve essere vuota (nessun database utente).
- Le reti virtuali usate dalle istanze di SQL Istanza gestita devono essere connesse tramite un [gateway VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) o [Express Route.](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) Quando due reti virtuali si connettono tramite una rete locale, assicurarsi che non sia presente una regola del firewall che blocca le porte 5022 e 11000-11999. Il Peering reti virtuali globale è supportato con la limitazione descritta nella nota riportata sotto.

   > [!IMPORTANT]
   > [Il 22/09/2020 Microsoft ha annunciato il Peering reti virtuali globale per i cluster virtuali appena creati](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Ciò significa che il Peering reti virtuali globale è supportato per le Istanze gestite di SQL create nelle subnet vuote dopo la data di annuncio, nonché per tutte le istanze gestite successive create in tali subnet. Per tutte le altre istanze gestite di SQL il supporto del peering è limitato alle reti nella stessa area a causa dei vincoli del peering di [rete virtuale globale.](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Per altri dettagli, vedere anche la sezione pertinente dell'articolo Domande frequenti su Reti virtuali di [Azure.](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 

- Le due reti Istanza gestita SQL non possono avere indirizzi IP sovrapposti.
- È necessario configurare i gruppi di sicurezza di rete (NSG) in modo che le porte 5022 e l'intervallo 11000~12000 siano aperti in ingresso e in uscita per le connessioni dalla subnet dell'altra istanza gestita, per consentire il traffico di replica tra le istanze.

   > [!IMPORTANT]
   > Regole di sicurezza dei gruppi di sicurezza di rete non configurate correttamente determinano il blocco delle operazioni di copia del database.

- L'istanza di SQL Istanza gestita è configurata con l'ID zona DNS corretto. La zona DNS è una proprietà di un Istanza gestita SQL e di un cluster virtuale sottostante e il relativo ID è incluso nell'indirizzo del nome host. L'ID zona viene generato come stringa casuale quando viene creato il primo Istanza gestita SQL in ogni rete virtuale e lo stesso ID viene assegnato a tutte le altre istanze nella stessa subnet. Dopo l'assegnazione, la zona DNS non può essere modificata. Le istanze gestite di SQL incluse nello stesso gruppo di failover devono condividere la zona DNS. A tale scopo, passare l'ID zona dell'istanza primaria come valore del parametro DnsZonePartner durante la creazione dell'istanza secondaria.

   > [!NOTE]
   > Per un'esercitazione dettagliata sulla configurazione dei gruppi di failover con SQL Istanza gestita, vedere Aggiungere un Istanza gestita [SQL a un gruppo di failover.](../managed-instance/failover-group-add-instance-tutorial.md)

## <a name="upgrading-or-downgrading-a-primary-database"></a>Aggiornamento o downgrade di un database primario

È possibile eseguire l'aggiornamento o il downgrade di un database primario a dimensioni di calcolo diverse (entro lo stesso livello di servizio e non tra il livello per utilizzo generico e business critical) senza disconnettere eventuali database secondari. Quando si esegue l'aggiornamento, è consigliabile aggiornare prima tutti i database secondari e quindi il database primario. Durante il downgrade, invertire l'ordine: eseguire prima il downgrade del database primario e quindi eseguire il downgrade di tutti i database secondari. Quando si aggiorna o si effettua il downgrade del database a un livello di servizio diverso, viene applicata questa raccomandazione.

Questa sequenza è consigliata specificamente per evitare il problema in cui l'elemento secondario nello SKU di una versione precedente è in rapporto di overload e richiede un nuovo processo di seeding durante la procedura di aggiornamento o downgrade. È possibile evitare il problema anche rendendo la replica primaria di sola lettura, a scapito di tutti i carichi di lavoro di lettura/scrittura sul componente primario.

> [!NOTE]
> Se è stato creato un database secondario come parte della configurazione del gruppo di failover, non è consigliabile eseguire il downgrade del database secondario. In questo modo si garantisce che il livello dei dati abbia una capacità sufficiente per elaborare il carico di lavoro normale dopo che il failover viene attivato.

## <a name="preventing-the-loss-of-critical-data"></a>Evitare la perdita di dati critici

A causa della latenza elevata delle reti WAN, per la copia continua viene usato un meccanismo di replica asincrona. La replica asincrona rende inevitabile una perdita parziale dei dati nel caso si verifichi un problema. Tuttavia, alcune applicazione potrebbero non essere soggette alla perdita dei dati. Per proteggere questi aggiornamenti critici, uno sviluppatore di applicazioni può chiamare la procedura di sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) subito dopo il commit della transazione. La chiamata di blocca il thread chiamante fino a quando l'ultima transazione di cui è stato eseguito il commit `sp_wait_for_database_copy_sync` non è stata trasmessa al database secondario. Tuttavia, non attende che le transazioni trasmesse vengano riprodotta e che ne venga eseguito il commit nel database secondario. `sp_wait_for_database_copy_sync` ha come ambito un collegamento di copia continua specifico. La procedura può essere chiamata da qualsiasi utente che abbia diritti di connessione al database primario.

> [!NOTE]
> `sp_wait_for_database_copy_sync` impedisce la perdita di dati dopo il failover, ma non garantisce la sincronizzazione completa per l'accesso in lettura. Il ritardo causato da una chiamata di routine può essere significativo e dipende dalle dimensioni del log delle `sp_wait_for_database_copy_sync` transazioni al momento della chiamata.

## <a name="failover-groups-and-point-in-time-restore"></a>Gruppi di failover e ripristino temporizzato

Per informazioni sull'uso del ripristino temporizzato con gruppi di failover, vedere [Point in Time Recovery (PITR)](recovery-using-backups.md#point-in-time-restore) (Recupero temporizzato).

## <a name="limitations-of-failover-groups"></a>Limitazioni dei gruppi di failover

Tenere presente le limitazioni seguenti:

- Non è possibile creare gruppi di failover tra due server o istanze nelle stesse aree di Azure.
- I gruppi di failover non possono essere rinominati. Sarà necessario eliminare il gruppo e crearlo di nuovo con un nome diverso.
- La ridenominazione del database non è supportata per le istanze nel gruppo di failover. Sarà necessario eliminare temporaneamente un gruppo di failover per poter rinominare un database.
- I database di sistema non vengono replicati nell'istanza secondaria in un gruppo di failover. Pertanto, gli scenari che dipendono da oggetti dei database di sistema saranno impossibili nell'istanza secondaria, a meno che gli oggetti non vengano creati manualmente nel database secondario.

## <a name="programmatically-managing-failover-groups"></a>Gestione di gruppi di failover a livello di codice

Come indicato in precedenza, i gruppi di failover automatico e la replica geografica attiva possono essere gestiti a livello di codice usando Azure PowerShell e l'API REST. Le tabelle seguenti descrivono il set di comandi disponibili. La replica geografica attiva include un set di API di Azure Resource Manager per la gestione, compresa l'[API REST del Database SQL di Azure](/rest/api/sql/) e i [cmdlet di Azure PowerShell](/powershell/azure/). Queste API richiedono l'uso di gruppi di risorse e supportano il controllo degli accessi in base al ruolo di Azure. Per altre informazioni su come implementare i ruoli di accesso, vedere Controllo degli accessi in base al ruolo di [Azure.](../../role-based-access-control/overview.md)

### <a name="manage-sql-database-failover"></a>Gestire il failover del database SQL

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Descrizione |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Questo comando crea un gruppo di failover e lo registra nei server primario e secondario|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Rimuove un gruppo di failover dal server |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Recupera la configurazione di un gruppo di failover |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modifica la configurazione di un gruppo di failover |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Attiva il failover di un gruppo di failover nel server secondario |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Aggiunge uno o più database a un gruppo di failover|

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

| Comando | Descrizione |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az_sql_failover_group_create) |Questo comando crea un gruppo di failover e lo registra nei server primario e secondario|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | Rimuove un gruppo di failover dal server |
| [az sql failover-group show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | Recupera una configurazione del gruppo di failover |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |Modifica la configurazione di un gruppo di failover e/o aggiunge uno o più database a un gruppo di failover|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | Attiva il failover di un gruppo di failover nel server secondario |

# <a name="rest-api"></a>[API REST](#tab/rest-api)

| API | Descrizione |
| --- | --- |
| [Create or Update Failover Group](/rest/api/sql/failovergroups/createorupdate) | Crea o aggiorna un gruppo di failover. |
| [Delete Failover Group](/rest/api/sql/failovergroups/delete) | Rimuove un gruppo di failover dal server |
| [Failover (Planned)](/rest/api/sql/failovergroups/failover) | Attiva il failover dal server primario corrente al server secondario con la sincronizzazione completa dei dati.|
| [Force Failover Allow Data Loss](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Attiva il failover dal server primario corrente al server secondario senza sincronizzare i dati. Questa operazione può comportare la perdita di dati. |
| [Get Failover Group](/rest/api/sql/failovergroups/get) | Recupera la configurazione di un gruppo di failover. |
| [List Failover Groups By Server](/rest/api/sql/failovergroups/listbyserver) | Elenca i gruppi di failover in un server. |
| [Update Failover Group](/rest/api/sql/failovergroups/update) | Aggiorna la configurazione di un gruppo di failover. |

---

### <a name="manage-sql-managed-instance-failover"></a>Gestire il failover Istanza gestita SQL


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Descrizione |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Questo comando crea un gruppo di failover e lo registra nelle istanze primarie e secondarie|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modifica la configurazione di un gruppo di failover|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Recupera la configurazione di un gruppo di failover|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Attiva il failover di un gruppo di failover nell'istanza secondaria|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Rimuove un gruppo di failover|


# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

| Comando | Descrizione |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az_sql_failover_group_create) |Questo comando crea un gruppo di failover e lo registra nei server primario e secondario|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | Rimuove un gruppo di failover dal server |
| [az sql failover-group show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | Recupera una configurazione del gruppo di failover |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |Modifica la configurazione di un gruppo di failover e/o aggiunge uno o più database a un gruppo di failover|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | Attiva il failover di un gruppo di failover nel server secondario |

# <a name="rest-api"></a>[API REST](#tab/rest-api)

| API | Descrizione |
| --- | --- |
| [Create or Update Failover Group](/rest/api/sql/instancefailovergroups/createorupdate) | Crea o aggiorna la configurazione di un gruppo di failover |
| [Delete Failover Group](/rest/api/sql/instancefailovergroups/delete) | Rimuove un gruppo di failover dall'istanza |
| [Failover (Planned)](/rest/api/sql/instancefailovergroups/failover) | Attiva il failover dall'istanza primaria corrente a questa istanza con la sincronizzazione completa dei dati. |
| [Force Failover Allow Data Loss](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Attiva il failover dall'istanza primaria corrente all'istanza secondaria senza sincronizzare i dati. Questa operazione può causare la perdita di dati. |
| [Get Failover Group](/rest/api/sql/instancefailovergroups/get) | recupera la configurazione di un gruppo di failover. |
| [List Failover Groups - List By Location](/rest/api/sql/instancefailovergroups/listbylocation) | Elenca i gruppi di failover in una posizione. |

---

## <a name="next-steps"></a>Passaggi successivi

- Per esercitazioni dettagliate, vedere
  - [Aggiungere un database SQL a un gruppo di failover](failover-group-add-single-database-tutorial.md)
  - [Aggiungere un pool elastico a un gruppo di failover](failover-group-add-elastic-pool-tutorial.md)
  - [Aggiungere un'istanza Istanza gestita SQL a un gruppo di failover](../managed-instance/failover-group-add-instance-tutorial.md)
- Per script di esempio, vedere:
  - [Usare PowerShell per configurare la replica geografica attiva per database SQL di Azure](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Usare PowerShell per configurare la replica geografica attiva per un database in pool nel database SQL di Azure](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [Usare PowerShell per aggiungere un database SQL di Azure a un gruppo di failover](scripts/add-database-to-failover-group-powershell.md)
- Per una panoramica e scenari di continuità aziendale, vedere [Panoramica della continuità aziendale](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](automated-backups-overview.md).
- Per informazioni sull'uso dei backup automatizzati per il ripristino, vedere Ripristinare un [database dai backup](recovery-using-backups.md)avviati dal servizio .
- Per ulteriori informazioni sui requisiti di autenticazione per un nuovo database e server primario, vedere l'articolo sulla [sicurezza del database SQL di Azure dopo il ripristino di emergenza](active-geo-replication-security-configure.md).
