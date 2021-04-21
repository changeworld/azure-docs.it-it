---
title: Endpoint di rete virtuale e regole per i database in database SQL di Azure
description: Contrassegnare una subnet come endpoint servizio di rete virtuale. Aggiungere quindi l'endpoint come regola di rete virtuale all'elenco di controllo di accesso per il database. Il database accetta quindi la comunicazione da tutte le macchine virtuali e da altri nodi nella subnet.
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
ms.openlocfilehash: 67e807e948caf1fec014457814c1b7f105630f9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784426"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Usare gli endpoint servizio di rete virtuale e le regole per il database SQL di Azure

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

 Le regole di rete virtuale sono una funzionalità di sicurezza del firewall che controlla se il server per i database e i pool elastici [in database SQL di Azure](sql-database-paas-overview.md) o per i database del pool SQL dedicato (in precedenza SQL DW) in [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) accetta le comunicazioni inviate da subnet specifiche nelle reti virtuali. Questo articolo spiega perché le regole di rete virtuale sono talvolta l'opzione migliore per consentire in modo sicuro la comunicazione con il database nel database SQL e Azure Synapse Analytics.

> [!NOTE]
> Questo articolo si applica sia al database SQL che Azure Synapse Analytics. Per semplicità, il termine *database* si riferisce sia ai database nel database SQL che Azure Synapse Analytics. Analogamente, tutti i riferimenti al *server fanno* riferimento al server [SQL logico](logical-servers.md) che ospita il database SQL Azure Synapse Analytics.

Per creare una regola di rete virtuale, deve essere disponibile un [endpoint servizio di rete virtuale][vm-virtual-network-service-endpoints-overview-649d] al quale la regola possa fare riferimento.

## <a name="create-a-virtual-network-rule"></a>Creare una regola di rete virtuale

Se si vuole creare solo una regola di rete virtuale, è possibile passare ai passaggi e alla spiegazione [più avanti in questo articolo.](#anchor-how-to-by-using-firewall-portal-59j)

## <a name="details-about-virtual-network-rules"></a>Dettagli sulle regole di rete virtuale

Questa sezione illustra alcuni dettagli sulle regole di rete virtuale.

### <a name="only-one-geographic-region"></a>Una sola area geografica

Ogni endpoint servizio di rete virtuale si applica a una sola area di Azure. L'endpoint non consente ad altre aree di accettare la comunicazione dalla subnet.

Ogni regola di rete virtuale è limitata all'area a cui si applica il relativo endpoint sottostante.

### <a name="server-level-not-database-level"></a>A livello di server, non di database

Ogni regola di rete virtuale si applica all'intero server, non solo a un database specifico nel server. In altre parole, le regole di rete virtuale si applicano a livello di server, non a livello di database.

Al contrario, le regole IP possono essere applicate a qualsiasi livello.

### <a name="security-administration-roles"></a>Ruoli di amministrazione di sicurezza

Esiste una separazione dei ruoli di sicurezza nell'amministrazione degli endpoint servizio di rete virtuale. Ogni ruolo indicato di seguito deve svolgere determinate azioni:

- **Amministratore di rete [(ruolo Collaboratore](../../role-based-access-control/built-in-roles.md#network-contributor) Rete):** &nbsp; Attivare l'endpoint.
- **Amministratore del [database (SQL Server collaboratore):](../../role-based-access-control/built-in-roles.md#sql-server-contributor)** &nbsp; Aggiornare l'elenco di controllo di accesso (ACL) per aggiungere la subnet specificata al server.

#### <a name="azure-rbac-alternative"></a>Alternativa al controllo degli accessi in base al ruolo di Azure

I ruoli di amministratore di rete e amministratore di database hanno più funzionalità di quelle necessarie a gestire le regole di rete virtuale. È necessario solo un subset delle relative funzionalità.

È possibile scegliere di usare il [controllo degli accessi in base al ruolo (RBAC)][rbac-what-is-813s] in Azure per creare un singolo ruolo personalizzato che contiene solo il subset necessario di funzionalità. Il ruolo personalizzato può essere usato invece di coinvolgere l'amministratore di rete o l'amministratore del database. La superficie di attacco dell'esposizione alla sicurezza è inferiore se si aggiunge un utente a un ruolo personalizzato anziché si aggiunge l'utente agli altri due ruoli di amministratore principali.

> [!NOTE]
> In alcuni casi, il database nel database SQL e la subnet della rete virtuale sono in sottoscrizioni diverse. In questi casi, è necessario verificare le configurazioni seguenti:
>
> - Entrambe le sottoscrizioni devono essere nello stesso tenant Azure Active Directory (Azure AD).
> - L'utente dispone delle autorizzazioni necessarie per avviare operazioni, ad esempio l'abilitazione degli endpoint di servizio e l'aggiunta di una subnet di rete virtuale al server specificato.
> - Entrambe le sottoscrizioni devono avere registrato il provider di Microsoft.Sql.

## <a name="limitations"></a>Limitazioni

Per il database SQL, la funzionalità regole di rete virtuale presenta le limitazioni seguenti:

- Nel firewall per il database nel database SQL ogni regola di rete virtuale fa riferimento a una subnet. Tutte queste subnet a cui si fa riferimento devono essere ospitate nella stessa area geografica che ospita il database.
- Ogni server può avere fino a 128 voci ACL per qualsiasi rete virtuale.
- Le regole di rete virtuale si applicano solo Azure Resource Manager reti virtuali e non alle reti del modello [di distribuzione][arm-deployment-model-568f] classica.
- L'attivazione degli endpoint del servizio di rete virtuale nel database SQL abilita anche gli endpoint per Database di Azure per MySQL e Database di Azure per PostgreSQL. Con gli endpoint impostati su **ON,** i tentativi di connessione dagli endpoint alle istanze di Database di Azure per MySQL o Database di Azure per PostgreSQL potrebbero non riuscire.
  - Il motivo sottostante è che database di Azure per MySQL e Database di Azure per PostgreSQL probabilmente non hanno una regola di rete virtuale configurata. È necessario configurare una regola di rete virtuale per Database di Azure per MySQL e Database di Azure per PostgreSQL e la connessione avrà esito positivo.
  - Per definire le regole del firewall di rete virtuale in un server logico SQL già configurato con endpoint privati, impostare Nega accesso **alla** rete pubblica su **No.**
- Nel firewall gli intervalli di indirizzi IP si applicano agli elementi di rete seguenti, ma non alle regole di rete virtuale:
  - [Rete privata virtuale (VPN) da sito a sito][vpn-gateway-indexmd-608y]
  - In locale tramite [Azure ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-you-use-service-endpoints"></a>Considerazioni sull'uso degli endpoint di servizio

Quando si usano gli endpoint di servizio per il database SQL, esaminare le considerazioni seguenti:

- **È necessario database SQL di Azure indirizzi IP pubblici in uscita.** I gruppi di sicurezza di rete (NSG) devono essere aperti agli ip del database SQL per consentire la connettività. A tale scopo, è possibile usare i tag del [servizio](../../virtual-network/network-security-groups-overview.md#service-tags) NSG per il database SQL.

### <a name="expressroute"></a>ExpressRoute

Se si [usa ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) dall'ambiente locale, per il peering pubblico o il peering Microsoft, è necessario identificare gli indirizzi IP NAT usati. Per il peering pubblico, ogni circuito ExpressRoute usa per impostazione predefinita due indirizzi IP NAT applicati al traffico del servizio di Azure quando il traffico entra nel backbone della rete di Microsoft Azure. Per il peering Microsoft, gli indirizzi IP NAT usati vengono forniti dal cliente o dal provider di servizi. Per consentire l'accesso alle risorse del servizio è necessario autorizzare questi indirizzi IP pubblici nell'impostazione del firewall IP per le risorse. Per trovare gli indirizzi IP del circuito ExpressRoute per il peering pubblico, [aprire un ticket di supporto in ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) tramite il portale di Azure. Per altre informazioni su NAT per il peering pubblico e Microsoft ExpressRoute, vedere [Requisiti NAT per il peering pubblico di Azure.](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)

Per consentire la comunicazione dal circuito al database SQL, è necessario creare regole di rete IP per gli indirizzi IP pubblici di NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Impatto dell'uso di endpoint servizio di rete virtuale con Archiviazione di Azure

Archiviazione di Azure ha implementato la stessa funzionalità che consente di limitare la connettività all'account di archiviazione di Azure. Se si sceglie di usare questa funzionalità con un account Archiviazione di Azure che usa il database SQL, è possibile che si verificano problemi. Di seguito è riportato un elenco e una descrizione delle funzionalità Azure Synapse Analytics e del database SQL interessate.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure Synapse Analytics PolyBase e COPY

PolyBase e l'istruzione COPY vengono comunemente usati per caricare dati in Azure Synapse Analytics da Archiviazione di Azure per l'inserimento di dati con velocità effettiva elevata. Se l'account Archiviazione di Azure da cui si caricano i dati limita l'accesso solo a un set di subnet di rete virtuale, la connettività quando si usa PolyBase e l'istruzione COPY all'account di archiviazione si interromperanno. Per abilitare scenari di importazione ed esportazione usando COPY e PolyBase con Azure Synapse Analytics che si connettono a Archiviazione di Azure protetto a una rete virtuale, seguire la procedura descritta in questa sezione.

#### <a name="prerequisites"></a>Prerequisiti

- Installare Azure PowerShell usando [questa guida.](/powershell/azure/install-az-ps)
- Se si dispone di un account per utilizzo generico v1 o Archiviazione BLOB di Azure, è necessario eseguire prima l'aggiornamento a per utilizzo generico v2 seguendo la procedura descritta in Eseguire l'aggiornamento a un account di archiviazione per utilizzo generico [v2](../../storage/common/storage-account-upgrade.md).
- È necessario avere **l'opzione Consenti servizi Microsoft** attendibili per accedere a questo account di archiviazione attivata nel menu Archiviazione di Azure **impostazioni** Firewall e reti virtuali dell'account. L'abilitazione di questa configurazione consentirà a PolyBase e all'istruzione COPY di connettersi all'account di archiviazione usando l'autenticazione avanzata in cui il traffico di rete rimane sul backbone di Azure. Per altre informazioni, vedere [questa guida.](../../storage/common/storage-network-security.md#exceptions)

> [!IMPORTANT]
> Il modulo Azure Resource Manager PowerShell è ancora supportato dal database SQL, ma tutto lo sviluppo futuro è per il modulo Az.Sql. Il modulo AzureRM continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Passaggi

1. Se si dispone di un pool SQL autonomo dedicato, registrare il server SQL Azure AD usando PowerShell:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Questo passaggio non è necessario per i pool SQL dedicati all'interno di un'area Azure Synapse Analytics lavoro.

1. Se si dispone di un'Azure Synapse Analytics, registrare l'identità gestita dal sistema dell'area di lavoro:

   1. Passare all'area Azure Synapse Analytics di lavoro nel portale di Azure.
   2. Passare al **riquadro Identità** gestite.
   3. Assicurarsi che **l'opzione Consenti pipeline** sia abilitata.
   
1. Creare un **account di archiviazione per utilizzo generico v2** seguendo la procedura descritta in Creare un account di [archiviazione](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Se si dispone di un account di archiviazione BLOB o v1 per utilizzo generico, è prima necessario eseguire l'aggiornamento alla *versione 2* seguendo la procedura descritta in Eseguire l'aggiornamento a un account di archiviazione per utilizzo [generico v2](../../storage/common/storage-account-upgrade.md).
   > - Per i problemi noti con Azure Data Lake Storage Gen2, vedere [Problemi noti con Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-known-issues.md).

1. Nell'account di archiviazione passare a **Controllo di accesso (IAM)** e selezionare **Aggiungi assegnazione di ruolo**. Assegnare **il ruolo collaboratore ai** dati dei BLOB di archiviazione di Azure al server o all'area di lavoro che ospita il pool SQL dedicato, registrato con Azure AD.

   > [!NOTE]
   > Solo i membri con privilegio Proprietario nell'account di archiviazione possono eseguire questo passaggio. Per i vari ruoli predefiniti di Azure, vedere [Ruoli predefiniti di Azure.](../../role-based-access-control/built-in-roles.md)
  
1. Per abilitare la connettività PolyBase all'account Archiviazione di Azure:

   1. Creare una chiave [master del](/sql/t-sql/statements/create-master-key-transact-sql) database se non è stata creata in precedenza.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Creare una credenziale con ambito database con **IDENTITY = 'Managed Service Identity'.**

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Non è necessario specificare SECRET con una chiave Archiviazione di Azure di accesso perché questo meccanismo usa l'identità [gestita.](../../active-directory/managed-identities-azure-resources/overview.md)
       > - Il nome IDENTITY deve essere **"Identità del servizio gestita"** perché la connettività PolyBase funzioni con un account Archiviazione di Azure protetto in una rete virtuale.

   1. Creare un'origine dati esterna con lo schema per la connessione all'account di archiviazione per utilizzo generico `abfss://` v2 usando PolyBase.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Se sono già presenti tabelle esterne associate a un account di archiviazione BLOB o per utilizzo generico v1, è prima necessario eliminare tali tabelle esterne. Eliminare quindi l'origine dati esterna corrispondente. Creare quindi un'origine dati esterna con lo schema che si connette a un account di archiviazione per utilizzo generico `abfss://` v2, come illustrato in precedenza. Quindi, creare nuovamente tutte le tabelle esterne usando questa nuova origine dati esterna. È possibile usare la [procedura guidata Genera e pubblica script](/sql/ssms/scripting/generate-and-publish-scripts-wizard) per generare facilmente script di creazione per tutte le tabelle esterne.
       > - Per altre informazioni sullo `abfss://` schema, vedere [Usare l'URI Azure Data Lake Storage Gen2.](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md)
       > - Per altre informazioni su CREATE EXTERNAL DATA SOURCE, vedere [questa guida.](/sql/t-sql/statements/create-external-data-source-transact-sql)

   1. Eseguire query come di consueto usando [tabelle esterne](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="sql-database-blob-auditing"></a>Controllo BLOB del database SQL

Il controllo BLOB esegue il push dei log di controllo nell'account di archiviazione. Se questo account di archiviazione usa la funzionalità degli endpoint servizio di rete virtuale, la connettività dal database SQL all'account di archiviazione si interromperà.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Aggiungere una regola del firewall di rete virtuale al server

Molto tempo fa, prima che questa funzionalità fosse migliorata, era necessario attivare gli endpoint del servizio di rete virtuale prima di poter implementare una regola di rete virtuale attiva nel firewall. Gli endpoint hanno correlato una determinata subnet di rete virtuale a un database nel database SQL. A partire da gennaio 2018, è possibile aggirare questo requisito impostando il flag **IgnoreMissingVNetServiceEndpoint.** A questo punto, è possibile aggiungere una regola del firewall di rete virtuale al server senza attivare gli endpoint del servizio di rete virtuale.

La semplice impostazione di una regola del firewall non consente di proteggere il server. È anche necessario attivare gli endpoint del servizio di rete virtuale per l'applicazione della sicurezza. Quando si attivano gli endpoint di servizio, la subnet della rete virtuale si verifica un tempo di inattività fino a quando non viene completata la transizione da disattivato a attivato. Questo periodo di inattività è particolarmente vero nel contesto di reti virtuali di grandi dimensioni. È possibile usare il flag **IgnoreMissingVNetServiceEndpoint** per ridurre o eliminare il tempo di inattività durante la transizione.

È possibile impostare il flag **IgnoreMissingVNetServiceEndpoint** usando PowerShell. Per altre informazioni, vedere PowerShell per creare un endpoint del servizio di rete [virtuale e una regola per il database SQL.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]

## <a name="errors-40914-and-40615"></a>Errori 40914 e 40615

L'errore di connessione 40914 si riferisce alle regole di rete *virtuale,* come specificato nel riquadro **Firewall** della portale di Azure. L'errore 40615 è simile, ma si riferisce alle regole *degli indirizzi IP* nel firewall.

### <a name="error-40914"></a>Errore 40914

**Testo del messaggio:** "Impossibile aprire il server '*[server-name]*' richiesto dall'account di accesso. Il client non è autorizzato ad accedere al server."

**Descrizione dell'errore:** il client si trova in una subnet che include endpoint server di rete virtuale. Tuttavia, il server non dispone di una regola di rete virtuale che concede alla subnet il diritto di comunicare con il database.

**Risoluzione degli errori:** Nel riquadro **Firewall** della portale di Azure usare il controllo regole di rete virtuale per aggiungere [una regola di rete virtuale](#anchor-how-to-by-using-firewall-portal-59j) per la subnet.

### <a name="error-40615"></a>Errore 40615

**Testo del messaggio:** "Impossibile aprire il server ' {0} ' richiesto dall'account di accesso. Il client con indirizzo IP {1} ' ' non è autorizzato ad accedere al server."

**Descrizione dell'errore:** Il client sta tentando di connettersi da un indirizzo IP non autorizzato a connettersi al server. Il firewall del server non dispone di una regola dell'indirizzo IP che consenta a un client di comunicare dall'indirizzo IP specificato al database.

**Risoluzione dell'errore:** immettere l'indirizzo IP del client come regola IP. Usare il **riquadro** Firewall nella portale di Azure per eseguire questo passaggio.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>Usare il portale per creare una regola di rete virtuale

In questa sezione viene illustrato come usare il portale di Azure [per][http-azure-portal-link-ref-477t] creare una regola *di rete* virtuale nel database nel database SQL. La regola indica al database di accettare la comunicazione da una determinata subnet contrassegnata come *endpoint servizio di rete virtuale.*

> [!NOTE]
> Se si intende aggiungere un endpoint di servizio alle regole del firewall della rete virtuale del server, assicurarsi prima di tutto che gli endpoint di servizio siano attivati per la subnet.
>
> Se gli endpoint di servizio non sono attivati per la subnet, il portale chiede di abilitarli. Selezionare il **pulsante** Abilita nello stesso riquadro in cui si aggiunge la regola.

## <a name="powershell-alternative"></a>Alternativa PowerShell

Uno script può anche creare regole di rete virtuale usando il cmdlet di PowerShell **New-AzSqlServerVirtualNetworkRule** o [az network vnet create.](/cli/azure/network/vnet#az_network_vnet_create) Se si è interessati, vedere PowerShell per creare un endpoint servizio di rete virtuale e [una regola per il database SQL.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]

## <a name="rest-api-alternative"></a>Alternativa API REST

Internamente, i cmdlet di PowerShell per le azioni della rete virtuale SQL chiamano le API REST. È possibile chiamare direttamente le API REST.

- [Regole di rete virtuale: Operazioni][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Prerequisiti

È necessario avere già una subnet contrassegnata con il nome del tipo di *endpoint* servizio di rete virtuale specifico pertinente per il database SQL.

- Il nome del tipo di endpoint pertinente è **Microsoft.Sql**.
- Se la subnet non è contrassegnata con il nome del tipo, vedere [Verificare che la subnet sia un endpoint][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Procedure del portale di Azure

1. Accedere al [portale di Azure][http-azure-portal-link-ref-477t].

1. Cercare e selezionare **SQL Server** e quindi selezionare il server. In **Sicurezza** selezionare **Firewall e reti virtuali.**

1. Impostare **Consenti l'accesso ai servizi di Azure** su **OFF.**

    > [!IMPORTANT]
    > Se si lascia il controllo impostato su **ON,** il server accetta la comunicazione da qualsiasi subnet all'interno del limite di Azure. Si tratta di una comunicazione che ha origine da uno degli indirizzi IP riconosciuti come quelli all'interno degli intervalli definiti per i data center di Azure. Lasciare il controllo impostato su **ON potrebbe** essere un accesso eccessivo dal punto di vista della sicurezza. La Rete virtuale di Microsoft Azure endpoint di servizio in coordinamento con la funzionalità regole di rete virtuale del database SQL può ridurre la superficie di attacco della sicurezza.

1. Selezionare **+ Aggiungi esistente** nella sezione **Reti** virtuali.

    ![Screenshot che mostra la selezione di + Aggiungi esistente (endpoint subnet, come regola SQL).][image-portal-firewall-vnet-add-existing-10-png]

1. Nel nuovo **riquadro Crea/Aggiorna** compilare le caselle con i nomi delle risorse di Azure.

    > [!TIP]
    > È necessario includere il prefisso dell'indirizzo corretto per la subnet. È possibile trovare il **valore Prefisso** indirizzo nel portale. Passare a **Tutte le risorse** Tutti i &gt; **tipi** &gt; **Reti virtuali**. Il filtro visualizza le reti virtuali. Selezionare la rete virtuale e quindi **selezionare Subnet**. La **colonna INTERVALLO INDIRIZZI** include il prefisso di indirizzo necessario.

    ![Screenshot che mostra la compilazione di caselle per la nuova regola.][image-portal-firewall-create-update-vnet-rule-20-png]

1. Selezionare il **pulsante OK** nella parte inferiore del riquadro.

1. Vedere la regola di rete virtuale risultante nel **riquadro Firewall.**

    ![Screenshot che mostra la nuova regola nel riquadro Firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Le regole presentano gli stati seguenti:
>
> - **Pronto:** indica che l'operazione avviata è stata completata.
> - **Failed**: indica che l'operazione avviata non è riuscita.
> - **Deleted**: si applica solo all'operazione Delete e indica che la regola è stata eliminata e non è più applicabile.
> - **InProgress:** indica che l'operazione è in corso. Mentre l'operazione è in questo stato, viene applicata la regola precedente.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Articoli correlati

- [Endpoint servizio di rete virtuale di Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Regole del firewall a livello di server e di database][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Passaggi successivi

- [Usare PowerShell per creare un endpoint del servizio di rete virtuale e quindi una regola di rete virtuale per il database SQL][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regole di rete virtuale: operazioni][rest-api-virtual-network-rules-operations-862r] con le API REST

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
