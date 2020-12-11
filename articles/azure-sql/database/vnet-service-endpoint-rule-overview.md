---
title: Endpoint e regole della rete virtuale per i database nel database SQL di Azure
description: Contrassegnare una subnet come endpoint del servizio di rete virtuale. Aggiungere quindi l'endpoint come una regola della rete virtuale all'ACL per il database. Il database accetta quindi la comunicazione da tutte le macchine virtuali e da altri nodi della subnet.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: d480239c0eb99ed48c13ec2fdb5b052574acc318
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092500"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Usare gli endpoint del servizio rete virtuale e le regole per i server nel database SQL di Azure

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*Le regole della rete virtuale* sono una funzionalità di sicurezza del firewall che controlla se il server per i database e i pool elastici nel [database SQL di Azure](sql-database-paas-overview.md) o per i database in [Azure sinapsi Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) accetta le comunicazioni inviate da determinate subnet nelle reti virtuali. Questo articolo illustra il motivo per cui le regole della rete virtuale sono a volte la scelta migliore per consentire in modo sicuro la comunicazione con il database nel database SQL e nell'analisi delle sinapsi di Azure.

> [!NOTE]
> Questo articolo si applica sia al database SQL che ad Azure sinapsi Analytics. Per semplicità, il termine *database* fa riferimento a entrambi i database nel database SQL e in Azure sinapsi Analytics. Analogamente, tutti i riferimenti al *Server* si riferiscono al [server logico SQL](logical-servers.md) che ospita il database SQL e Azure sinapsi Analytics.

Per creare una regola di rete virtuale, deve essere disponibile un [endpoint servizio di rete virtuale][vm-virtual-network-service-endpoints-overview-649d] al quale la regola possa fare riferimento.

## <a name="create-a-virtual-network-rule"></a>Creare una regola della rete virtuale

Se si vuole creare solo una regola della rete virtuale, è possibile passare alla procedura e alla spiegazione [più avanti in questo articolo](#anchor-how-to-by-using-firewall-portal-59j).

## <a name="details-about-virtual-network-rules"></a>Dettagli sulle regole di rete virtuale

Questa sezione illustra alcuni dettagli sulle regole di rete virtuale.

### <a name="only-one-geographic-region"></a>Una sola area geografica

Ogni endpoint di servizio della rete virtuale si applica a una sola area di Azure. L'endpoint non consente ad altre aree di accettare le comunicazioni dalla subnet.

Ogni regola di rete virtuale è limitata all'area a cui si applica il relativo endpoint sottostante.

### <a name="server-level-not-database-level"></a>A livello di server, non a livello di database

Ogni regola della rete virtuale si applica all'intero server, non solo a un database specifico sul server. In altre parole, le regole della rete virtuale si applicano a livello di server, non a livello di database.

Al contrario, le regole IP possono essere applicate a qualsiasi livello.

### <a name="security-administration-roles"></a>Ruoli di amministrazione di sicurezza

Esiste una separazione dei ruoli di sicurezza nell'amministrazione degli endpoint di servizio della rete virtuale. Ogni ruolo indicato di seguito deve svolgere determinate azioni:

- **Amministratore di rete (ruolo [collaboratore rete](../../role-based-access-control/built-in-roles.md#network-contributor) ):** &nbsp; Attivare l'endpoint.
- **Amministratore di database (ruolo [collaboratore SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) ):** &nbsp; Aggiornare l'elenco di controllo di accesso (ACL) per aggiungere la subnet specificata al server.

#### <a name="azure-rbac-alternative"></a>Alternative RBAC di Azure

I ruoli di amministratore di rete e amministratore di database hanno più funzionalità di quelle necessarie a gestire le regole di rete virtuale. È necessario solo un subset delle relative funzionalità.

È possibile scegliere di usare il [controllo degli accessi in base al ruolo (RBAC)][rbac-what-is-813s] in Azure per creare un singolo ruolo personalizzato che contiene solo il subset necessario di funzionalità. Il ruolo personalizzato può essere utilizzato invece di coinvolgere l'amministratore di rete o l'amministratore del database. La superficie di attacco dell'esposizione alla sicurezza è inferiore se si aggiunge un utente a un ruolo personalizzato anziché aggiungere l'utente agli altri due ruoli di amministratore principali.

> [!NOTE]
> In alcuni casi, il database nel database SQL e la subnet della rete virtuale si trovano in sottoscrizioni diverse. In questi casi, è necessario verificare le configurazioni seguenti:
>
> - Entrambe le sottoscrizioni devono trovarsi nello stesso tenant di Azure Active Directory (Azure AD).
> - L'utente dispone delle autorizzazioni necessarie per avviare le operazioni, ad esempio l'abilitazione degli endpoint di servizio e l'aggiunta di una subnet della rete virtuale al server specificato.
> - Entrambe le sottoscrizioni devono avere registrato il provider di Microsoft.Sql.

## <a name="limitations"></a>Limitazioni

Per il database SQL, la funzionalità delle regole della rete virtuale presenta le limitazioni seguenti:

- Nel firewall per il database nel database SQL, ogni regola della rete virtuale fa riferimento a una subnet. Tutte le subnet a cui viene fatto riferimento devono essere ospitate nella stessa area geografica che ospita il database.
- Ogni server può disporre di un massimo di 128 voci ACL per qualsiasi rete virtuale.
- Le regole della rete virtuale si applicano solo alle reti Azure Resource Manager virtuali e non alle reti di [modelli di distribuzione classica][arm-deployment-model-568f] .
- L'attivazione degli endpoint di servizio della rete virtuale nel database SQL Abilita anche gli endpoint per database di Azure per MySQL e database di Azure per PostgreSQL. Con gli endpoint impostati **su on**, i tentativi di connessione dagli endpoint alle istanze di database di Azure per MySQL o database di Azure per PostgreSQL potrebbero non riuscire.
  - Il motivo principale è che database di Azure per MySQL e database di Azure per PostgreSQL probabilmente non hanno una regola della rete virtuale configurata. È necessario configurare una regola della rete virtuale per database di Azure per MySQL e database di Azure per PostgreSQL e la connessione avrà esito positivo.
  - Per definire le regole del firewall della rete virtuale in un server logico SQL già configurato con endpoint privati, impostare **Nega accesso alla rete pubblica** su **No**.
- Sul firewall, gli intervalli di indirizzi IP si applicano agli elementi di rete seguenti, ma non alle regole della rete virtuale:
  - [Rete privata virtuale (VPN) da sito a sito (S2S)][vpn-gateway-indexmd-608y]
  - Locale tramite [Azure ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-you-use-service-endpoints"></a>Considerazioni sull'utilizzo degli endpoint di servizio

Quando si usano gli endpoint di servizio per il database SQL, esaminare le considerazioni seguenti:

- **Gli indirizzi IP pubblici in uscita per il database SQL di Azure sono obbligatori.** I gruppi di sicurezza di rete (gruppi) devono essere aperti agli indirizzi IP del database SQL per consentire la connettività. A tale scopo, è possibile usare i [tag del servizio](../../virtual-network/network-security-groups-overview.md#service-tags) NSG per il database SQL.

### <a name="expressroute"></a>ExpressRoute

Se si usa [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) dalla propria sede, per il peering pubblico o il peering Microsoft, è necessario identificare gli indirizzi IP NAT usati. Per il peering pubblico, ogni circuito ExpressRoute usa per impostazione predefinita due indirizzi IP NAT applicati al traffico del servizio di Azure quando il traffico entra nel backbone della rete di Microsoft Azure. Per il peering Microsoft, gli indirizzi IP NAT usati vengono forniti dal cliente o dal provider di servizi. Per consentire l'accesso alle risorse del servizio è necessario autorizzare questi indirizzi IP pubblici nell'impostazione del firewall IP per le risorse. Per trovare gli indirizzi IP del circuito ExpressRoute per il peering pubblico, [aprire un ticket di supporto in ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) tramite il portale di Azure. Per altre informazioni su NAT per il peering pubblico e Microsoft ExpressRoute, vedere [requisiti NAT per il peering pubblico di Azure](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

Per consentire la comunicazione dal circuito al database SQL, è necessario creare regole di rete IP per gli indirizzi IP pubblici del NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Conseguenze dell'uso di endpoint del servizio di rete virtuale con archiviazione di Azure

Archiviazione di Azure ha implementato la stessa funzionalità che consente di limitare la connettività all'account di archiviazione di Azure. Se si sceglie di usare questa funzionalità con un account di archiviazione di Azure usato dal database SQL, è possibile riscontrare problemi. Di seguito è descritto un elenco e una discussione sulle funzionalità del database SQL e di Azure sinapsi Analytics interessate da questo problema.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Dichiarazione di base e copia di Azure sinapsi Analytics

Per caricare i dati in Azure sinapsi Analytics dagli account di archiviazione di Azure per l'inserimento dei dati con velocità effettiva elevata, viene comunemente usata la polibase e l'istruzione COPY. Se l'account di archiviazione di Azure da cui si caricano i dati limita gli accessi solo a un set di subnet della rete virtuale, la connettività quando si usa la funzione di base e l'istruzione COPY per l'account di archiviazione si interrompe. Per abilitare gli scenari di importazione ed esportazione usando la copia e la polibase con analisi sinapsi di Azure che si connette ad archiviazione di Azure protetta a una rete virtuale, seguire la procedura descritta in questa sezione.

#### <a name="prerequisites"></a>Prerequisiti

- Installare Azure PowerShell usando [questa guida](/powershell/azure/install-az-ps).
- Se si dispone di un account per utilizzo generico V1 o di archiviazione BLOB di Azure, è necessario eseguire prima l'aggiornamento a utilizzo generico V2 attenendosi alla procedura descritta in [eseguire l'aggiornamento a un account di archiviazione per utilizzo generico V2](../../storage/common/storage-account-upgrade.md).
- È necessario **consentire ai servizi Microsoft attendibili di accedere a questo account di archiviazione** attivato nel menu impostazioni del firewall dell'account di archiviazione **di Azure e delle reti virtuali** . L'abilitazione di questa configurazione consentirà a polibase e all'istruzione COPY di connettersi all'account di archiviazione usando l'autenticazione avanzata in cui il traffico di rete rimane nel backbone di Azure. Per ulteriori informazioni, vedere [la guida](../../storage/common/storage-network-security.md#exceptions).

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Il modulo AzureRM continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Passaggi

1. Se si dispone di un pool SQL dedicato autonomo, registrare SQL Server con Azure AD usando PowerShell:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Questo passaggio non è necessario per i pool SQL dedicati in un'area di lavoro di Azure sinapsi Analytics.

1. Se si dispone di un'area di lavoro di Azure sinapsi Analytics, registrare l'identità gestita dal sistema dell'area di lavoro:

   1. Passare all'area di lavoro di Azure sinapsi Analytics nell'portale di Azure.
   2. Passare al riquadro **identità gestite** .
   3. Verificare che l'opzione **Consenti pipeline** sia abilitata.
   
1. Creare un **account di archiviazione per utilizzo generico V2** attenendosi alla procedura illustrata in [creare un account di archiviazione](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Se si dispone di un account di archiviazione BLOB o V1 per utilizzo generico, è necessario *prima eseguire l'aggiornamento alla versione V2* attenendosi alla procedura descritta in [eseguire l'aggiornamento a un account di archiviazione per utilizzo generico V2](../../storage/common/storage-account-upgrade.md).
   > - Per problemi noti relativi a Azure Data Lake Storage Gen2, vedere [problemi noti relativi a Azure Data Lake storage Gen2](../../storage/blobs/data-lake-storage-known-issues.md).

1. Nell'account di archiviazione passare a **controllo di accesso (IAM)** e selezionare **Aggiungi assegnazione ruolo**. Assegnare il ruolo Azure **Storage Data Contributor collaboratore** al server o all'area di lavoro che ospita il pool SQL dedicato, registrato con Azure ad.

   > [!NOTE]
   > Solo i membri con privilegi di proprietario per l'account di archiviazione possono eseguire questo passaggio. Per i vari ruoli predefiniti di Azure, vedere [ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md).
  
1. Per abilitare la connettività di base per l'account di archiviazione di Azure:

   1. Creare una [chiave master](/sql/t-sql/statements/create-master-key-transact-sql) del database, se non ne è stata creata una in precedenza.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Creare una credenziale con ambito database con **Identity = "identità del servizio gestita"**.

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Non è necessario specificare il segreto con una chiave di accesso di archiviazione di Azure perché questo meccanismo usa l' [identità gestita](../../active-directory/managed-identities-azure-resources/overview.md) dietro le quinte.
       > - Il nome dell'identità deve essere **' identità del servizio gestità** per la connettività di base per l'uso di un account di archiviazione di Azure protetto in una rete virtuale.

   1. Creare un'origine dati esterna con lo `abfss://` schema per la connessione all'account di archiviazione per utilizzo generico v2 tramite la funzione polibase.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Se sono già presenti tabelle esterne associate a un account di archiviazione BLOB o V1 per utilizzo generico, è necessario innanzitutto eliminare le tabelle esterne. Eliminare quindi l'origine dati esterna corrispondente. Successivamente, creare un'origine dati esterna con lo `abfss://` schema che si connette a un account di archiviazione per utilizzo generico V2, come illustrato in precedenza. Quindi ricreare tutte le tabelle esterne usando la nuova origine dati esterna. È possibile utilizzare la [procedura guidata genera e pubblica script](/sql/ssms/scripting/generate-and-publish-scripts-wizard) per generare agevolmente gli script di creazione per tutte le tabelle esterne.
       > - Per altre informazioni sullo `abfss://` schema, vedere [usare l'URI del Azure Data Lake storage Gen2](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Per ulteriori informazioni sulla creazione di un'origine dati esterna, vedere [questa guida](/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Eseguire la query normalmente utilizzando le [tabelle esterne](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="sql-database-blob-auditing"></a>Controllo BLOB del database SQL

Il controllo BLOB esegue il push dei log di controllo nell'account di archiviazione. Se questo account di archiviazione usa la funzionalità degli endpoint del servizio rete virtuale, la connettività dal database SQL all'account di archiviazione verrà interrotta.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Aggiungere una regola del firewall di rete virtuale al server

Molto tempo fa, prima che questa funzionalità venisse migliorata, era necessario attivare gli endpoint di servizio della rete virtuale prima di poter implementare una regola della rete virtuale in tempo reale nel firewall. Gli endpoint correlano una determinata subnet della rete virtuale a un database nel database SQL. A partire dal 2018 gennaio, è possibile aggirare questo requisito impostando il flag **IgnoreMissingVNetServiceEndpoint** . A questo punto, è possibile aggiungere una regola del firewall di rete virtuale al server senza attivare gli endpoint di servizio della rete virtuale.

La semplice impostazione di una regola del firewall non consente di proteggere il server. Per rendere effettive le sicurezza, è inoltre necessario attivare gli endpoint di servizio della rete virtuale. Quando si attivano gli endpoint di servizio, la subnet della rete virtuale presenta tempi di inattività fino a quando non viene completata la transizione da disattivato a attivato. Questo periodo di inattività è particolarmente vero nel contesto di reti virtuali di grandi dimensioni. È possibile usare il flag **IgnoreMissingVNetServiceEndpoint** per ridurre o eliminare il tempo di inattività durante la transizione.

È possibile impostare il flag **IgnoreMissingVNetServiceEndpoint** usando PowerShell. Per altre informazioni, vedere [PowerShell per creare un endpoint del servizio di rete virtuale e una regola per il database SQL][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Errori 40914 e 40615

L'errore di connessione 40914 si riferisce alle *regole della rete virtuale*, come specificato nel riquadro **Firewall** del portale di Azure. L'errore 40615 è simile, ma si riferisce alle *regole degli indirizzi IP* sul firewall.

### <a name="error-40914"></a>Errore 40914

**Testo del messaggio:** "Impossibile aprire il server '*[nome-server]*' richiesto dall'account di accesso. Al client non è consentito accedere al server. "

**Descrizione dell'errore:** il client si trova in una subnet che include endpoint server di rete virtuale. Tuttavia, il server non dispone di alcuna regola di rete virtuale che conceda alla subnet il diritto di comunicare con il database.

**Risoluzione degli errori:** Nel riquadro **Firewall** del portale di Azure usare il controllo regole di rete virtuale per [aggiungere una regola della rete virtuale](#anchor-how-to-by-using-firewall-portal-59j) per la subnet.

### <a name="error-40615"></a>Errore 40615

**Testo del messaggio:** "Impossibile aprire il server ' {0} ' richiesto dall'account di accesso. Al client con indirizzo IP ' {1} ' non è consentito accedere al server. "

**Descrizione errore:** Il client sta tentando di connettersi da un indirizzo IP che non è autorizzato a connettersi al server. Il firewall del server non dispone di una regola di indirizzi IP che consente a un client di comunicare dall'indirizzo IP specificato al database.

**Risoluzione dell'errore:** immettere l'indirizzo IP del client come regola IP. Usare il riquadro **Firewall** nel portale di Azure per eseguire questo passaggio.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>Usare il portale per creare una regola della rete virtuale

In questa sezione viene illustrato come è possibile utilizzare il [portale di Azure][http-azure-portal-link-ref-477t] per creare una *regola della rete virtuale* nel database nel database SQL. La regola indica al database di accettare la comunicazione da una particolare subnet contrassegnata come *endpoint del servizio di rete virtuale*.

> [!NOTE]
> Se si intende aggiungere un endpoint di servizio alle regole del firewall della rete virtuale del server, verificare innanzitutto che gli endpoint di servizio siano attivati per la subnet.
>
> Se gli endpoint di servizio non sono attivati per la subnet, il portale richiede di abilitarli. Selezionare il pulsante **Abilita** nello stesso riquadro in cui si aggiunge la regola.

## <a name="powershell-alternative"></a>Alternativa PowerShell

Uno script può anche creare regole di rete virtuale usando il cmdlet di PowerShell **New-AzSqlServerVirtualNetworkRule** o [AZ Network VNET create](/cli/azure/network/vnet#az-network-vnet-create). Se si è interessati, vedere [PowerShell per creare un endpoint del servizio di rete virtuale e una regola per il database SQL][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Alternativa API REST

Internamente, i cmdlet di PowerShell per le azioni di rete virtuale SQL chiamano le API REST. È possibile chiamare direttamente le API REST.

- [Regole della rete virtuale: operazioni][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Prerequisiti

È necessario avere già una subnet contrassegnata con il *nome del tipo* di endpoint del servizio di rete virtuale specifico pertinente per il database SQL.

- Il nome del tipo di endpoint pertinente è **Microsoft.Sql**.
- Se la subnet non è contrassegnata con il nome del tipo, vedere [Verificare che la subnet sia un endpoint][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Procedure del portale di Azure

1. Accedere al [portale di Azure][http-azure-portal-link-ref-477t].

1. Cercare e selezionare **SQL Server**, quindi selezionare il server. In **sicurezza** selezionare **firewall e reti virtuali**.

1. Impostare **Consenti accesso ai servizi di Azure** su **disattivato**.

    > [!IMPORTANT]
    > Se si lascia il controllo impostato **su on**, il server accetta la comunicazione da qualsiasi subnet all'interno del limite di Azure. Ovvero la comunicazione originata da uno degli indirizzi IP riconosciuti come quelli compresi negli intervalli definiti per i Data Center di Azure. Lasciare il controllo impostato **su on** potrebbe essere un accesso eccessivo da un punto di vista della sicurezza. La funzionalità di endpoint del servizio Rete virtuale di Microsoft Azure in coordinamento con la funzionalità regole della rete virtuale del database SQL può ridurre la superficie di attacco della sicurezza.

1. Selezionare **+ Aggiungi esistente** nella sezione **reti virtuali** .

    ![Screenshot che Mostra come selezionare + Aggiungi esistente (endpoint subnet, come regola SQL).][image-portal-firewall-vnet-add-existing-10-png]

1. Nel nuovo riquadro **Crea/aggiorna** compilare le caselle con i nomi delle risorse di Azure.

    > [!TIP]
    > È necessario includere il prefisso dell'indirizzo corretto per la subnet. Il valore del **prefisso dell'indirizzo** è reperibile nel portale. Passare a **tutte le risorse** &gt; **tutti i tipi** &gt; **reti virtuali**. Il filtro visualizza le reti virtuali. Selezionare la rete virtuale e quindi fare clic su **subnet**. Il prefisso dell'indirizzo della colonna **intervallo di indirizzi** è necessario.

    ![Screenshot che mostra le caselle di riempimento per la nuova regola.][image-portal-firewall-create-update-vnet-rule-20-png]

1. Selezionare il pulsante **OK** nella parte inferiore del riquadro.

1. Vedere la regola della rete virtuale risultante nel riquadro del **Firewall** .

    ![Screenshot che mostra la nuova regola nel riquadro del firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Le regole presentano gli stati seguenti:
>
> - **Ready**: indica che l'operazione avviata è riuscita.
> - **Failed**: indica che l'operazione avviata non è riuscita.
> - **Deleted**: si applica solo all'operazione DELETE e indica che la regola è stata eliminata e non è più applicabile.
> - **InProgress**: indica che l'operazione è in corso. Mentre l'operazione è in questo stato, viene applicata la regola precedente.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Articoli correlati

- [Endpoint servizio di rete virtuale di Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Regole del firewall a livello di server e di database][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Passaggi successivi

- [Usare PowerShell per creare un endpoint del servizio di rete virtuale e quindi una regola della rete virtuale per il database SQL][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regole della rete virtuale: operazioni][rest-api-virtual-network-rules-operations-862r] con le API REST

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
