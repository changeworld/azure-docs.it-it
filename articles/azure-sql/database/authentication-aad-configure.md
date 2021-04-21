---
title: Configurare l'autenticazione di Azure Active Directory
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Informazioni su come connettersi al database SQL, Istanza gestita SQL Azure Synapse Analytics usando l'autenticazione Azure Active Directory, dopo aver configurato Azure AD.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 08/17/2020
ms.openlocfilehash: c75364f2565611b6738996c082610229db0cb2a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762228"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>Configurare e gestire l'autenticazione di Azure AD con Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Questo articolo illustra come creare e popolare un'istanza di Azure Active Directory (Azure AD) e quindi usare Azure AD con [database SQL di Azure](sql-database-paas-overview.md), [Istanza gestita di SQL di Azure](../managed-instance/sql-managed-instance-paas-overview.md)e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Per una panoramica, vedere Autenticazione [Azure Active Directory.](authentication-aad-overview.md)

## <a name="azure-ad-authentication-methods"></a>Metodi di autenticazione di Azure AD

Azure AD'autenticazione supporta i metodi di autenticazione seguenti:

- Azure AD solo cloud
- Azure AD identità ibride che supportano:
  - Autenticazione cloud con due opzioni abbinate all'accesso Single Sign-On facile (SSO)
    - Azure AD dell'hash delle password
    - Azure AD autenticazione pass-through
  - Autenticazione federata

Per altre informazioni sui Azure AD di autenticazione e su quale scegliere, vedere Scegliere il metodo di autenticazione appropriato per la soluzione ibrida di gestione delle identità [Azure Active Directory.](../../active-directory/hybrid/choose-ad-authn.md)

Per altre informazioni su Azure AD identità ibride, la configurazione e la sincronizzazione, vedere:

- Autenticazione dell'hash delle password : [implementare la sincronizzazione dell'hash](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) delle password Azure AD Connect sincronizzazione
- Autenticazione pass-through : Azure Active Directory [autenticazione pass-through](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Autenticazione federata: [distribuzione di Active Directory Federation Services in Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) e Azure AD Connect e [federazione](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

## <a name="create-and-populate-an-azure-ad-instance"></a>Creare e popolare un'istanza Azure AD dati

Creare un Azure AD istanza di e popolarla con utenti e gruppi. Azure AD può essere il dominio gestito di Azure AD iniziale. Azure AD può anche essere un set di servizi di dominio Active Directory locali federato con Azure AD.

Per altre informazioni, consultare [Integrazione delle identità locali con Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [Aggiungere un nome di dominio personalizzato ad Azure AD](../../active-directory/fundamentals/add-custom-domain.md), [Windows Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Nuovo supporto per la federazione con Active Directory di Windows Server in Windows Azure), [Amministrazione della directory di Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) e [Gestire Azure AD con Windows PowerShell](/powershell/azure/) e [Porte e protocolli necessari per la soluzione ibrida di gestione delle identità](../../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associare o aggiungere una sottoscrizione di Azure ad Azure Active Directory

1. Associare la sottoscrizione di Azure ad Azure Active Directory impostando la directory come directory attendibile per la sottoscrizione di Azure che ospita il database. Per informazioni dettagliate, [vedere Associare o aggiungere una sottoscrizione di Azure al tenant Azure Active Directory.](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

2. Usare il controllo per cambiare directory nel portale di Azure per passare alla sottoscrizione associata al dominio.

   > [!IMPORTANT]
   > Ogni sottoscrizione di Azure ha una relazione di trust con un'istanza di Azure AD. Ciò significa che considera attendibile quella directory per l'autenticazione di utenti, servizi e dispositivi. Più sottoscrizioni possono considerare attendibile la stessa directory, ma una sottoscrizione considera attendibile una sola directory. Questa relazione di trust tra la sottoscrizione e la directory è diversa dalla relazione tra la sottoscrizione e tutte le altre risorse in Azure, ad esempio siti Web, database e così via, le quali sono da considerarsi più come risorse figlio di una sottoscrizione. Se la sottoscrizione scade, non sarà più possibile accedere a tutte queste altre risorse associate alla sottoscrizione anche se la directory rimane in Azure, quindi sarà possibile associarvi un'altra sottoscrizione e continuare a gestire gli utenti della directory. Per altre informazioni sulle risorse, vedere [Informazioni sull'accesso alle risorse di Azure](../../active-directory/external-identities/add-users-administrator.md). Per altre informazioni su questa relazione attendibile, vedere [Associare o aggiungere una sottoscrizione di Azure ad Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>Azure AD amministratore con un server nel database SQL

Ogni [server](logical-servers.md) in Azure (che ospita il database SQL o Azure Synapse) inizia con un singolo account amministratore del server che è l'amministratore dell'intero server. Creare un secondo account amministratore come account Azure AD servizio. Questa entità viene creata come utente di database indipendente nel database master del server. Gli account amministratore sono membri del **ruolo db_owner** in ogni database utente e immettono ogni database utente come **utente dbo.** Per altre informazioni sugli account amministratore, vedere [Gestione di database e account di accesso](logins-create-manage.md).

Quando si usa Azure Active Directory con la replica geografica, l'amministratore di Azure Active Directory deve essere configurato sia per i server primari che per quelli secondari. Se un server non dispone di un amministratore Azure Active Directory, gli account Azure Active Directory e gli utenti ricevono un errore `Cannot connect` a server.

> [!NOTE]
> Gli utenti che non sono basati su un account Azure AD (incluso l'account amministratore del server) non possono creare utenti basati su Azure AD, perché non hanno l'autorizzazione per convalidare gli utenti del database proposti con il Azure AD.

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>Eseguire il provisioning Azure AD amministratore (SQL Istanza gestita)

> [!IMPORTANT]
> Seguire questa procedura solo se si sta provisioning di un Istanza gestita di SQL di Azure. Questa operazione può essere eseguita solo dall'amministratore globale o da un amministratore dei ruoli con privilegi in Azure AD.
>
> **Nell'anteprima pubblica** è possibile assegnare il **ruolo Lettori** directory a un gruppo in Azure AD. I proprietari del gruppo possono quindi aggiungere l'identità dell'istanza gestita come membro di questo gruppo, che consente di effettuare il provisioning di un amministratore Azure AD per l'istanza di SQL Istanza gestita. Per altre informazioni su questa funzionalità, vedere [Ruolo con autorizzazioni di lettura nella directory in Azure Active Directory per Azure SQL](authentication-aad-directory-readers-role.md).

L'Istanza gestita SQL deve avere le autorizzazioni per Azure AD per eseguire correttamente attività quali l'autenticazione degli utenti tramite l'appartenenza a gruppi di sicurezza o la creazione di nuovi utenti. Per il funzionamento, è necessario concedere all'istanza di SQL Istanza gestita l'autorizzazione per la lettura Azure AD. È possibile eseguire questa operazione usando il portale di Azure o PowerShell.

### <a name="azure-portal"></a>Portale di Azure

Per concedere l'autorizzazione di lettura Istanza gestita Azure AD SQL portale di Azure, accedere come amministratore globale in Azure AD e seguire questa procedura:

1. [Nell'portale di Azure](https://portal.azure.com), nell'angolo superiore destro, selezionare la connessione da un elenco a discesa di possibili Active Directory.

2. Scegliere la directory corretta come directory predefinita di Azure AD.

   Questo passaggio collega la sottoscrizione associata ad Active Directory a SQL Istanza gestita, assicurando che la stessa sottoscrizione sia usata sia per l'istanza di Azure AD che per l'istanza di SQL Istanza gestita.

3. Passare all'istanza di SQL Istanza gestita che si vuole usare per l Azure AD integra.

   ![Screenshot dell'portale di Azure che mostra la pagina amministratore di Active Directory aperta per l'istanza gestita di SQL selezionata.](./media/authentication-aad-configure/aad.png)

4. Selezionare il banner nella parte superiore della pagina di amministrazione di Active Directory e concedere l'autorizzazione all'utente corrente.

    ![Screenshot della finestra di dialogo per la concessione delle autorizzazioni a un'istanza gestita di SQL per l'accesso ad Active Directory. Il pulsante Concedi autorizzazioni è selezionato.](./media/authentication-aad-configure/grant-permissions.png)

5. Al termine dell'operazione, nell'angolo in alto a destra verrà visualizzata la notifica seguente:

    ![Screenshot di una notifica che conferma che le autorizzazioni di lettura di Active Directory sono state aggiornate correttamente per l'istanza gestita.](./media/authentication-aad-configure/success.png)

6. È ora possibile scegliere l'amministratore Azure AD per l'istanza di SQL Istanza gestita. Nella pagina Amministratore di Active Directory selezionare il comando **Imposta amministratore**.

    ![Screenshot che mostra il comando Imposta amministratore evidenziato nella pagina Amministratore di Active Directory per l'istanza gestita di SQL selezionata.](./media/authentication-aad-configure/set-admin.png)

7. Nella pagina Azure AD amministratore cercare un utente, selezionare l'utente o il gruppo da impostare come amministratore e quindi **selezionare Seleziona.**

   La pagina Amministratore di Active Directory mostra tutti i membri e i gruppi di Active Directory. Gli utenti e i gruppi non disponibili (in grigio) non possono essere selezionati, perché non sono supportati come amministratori di Azure AD. Vedere l'elenco degli amministratori supportati in [Funzionalità e limitazioni di Azure AD](authentication-aad-overview.md#azure-ad-features-and-limitations). Il controllo degli accessi in base al ruolo di Azure si applica solo al portale di Azure e non viene propagato al database SQL, al Istanza gestita SQL o Azure Synapse.

    ![Aggiungere Azure Active Directory amministratore](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. Nella parte superiore della pagina Amministratore di Active Directory selezionare **Salva**.

    ![Screenshot della pagina Amministratore di Active Directory con il pulsante Salva nella riga superiore accanto ai pulsanti Imposta amministratore e Rimuovi amministratore.](./media/authentication-aad-configure/save.png)

    Il processo di modifica dell'amministratore può richiedere alcuni minuti. Al termine del processo, nella casella Amministratore di Active Directory verrà visualizzato il nome del nuovo amministratore.

Dopo aver provisioning di Azure AD amministratore di sql Istanza gestita, è possibile iniziare Azure AD creare entità server (account di accesso) con la sintassi [CREATE LOGIN.](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) Per altre informazioni, vedere [Panoramica di SQL Istanza gestita .](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration)

> [!TIP]
> Per rimuovere un amministratore in un secondo momento, nella parte superiore della pagina Amministratore di Active Directory scegliere **Rimuovi amministratore** e quindi **Salva**.

### <a name="powershell"></a>PowerShell

Per concedere all'Istanza gestita Azure AD SQL l'autorizzazione di lettura tramite PowerShell, eseguire questo script:

```powershell
# Gives Azure Active Directory read permission to a Service Principal representing the SQL Managed Instance.
# Can be executed only by a "Global Administrator" or "Privileged Role Administrator" type of user.

$aadTenant = "<YourTenantId>" # Enter your tenant ID
$managedInstanceName = "MyManagedInstance"

# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}

# Get service principal for your SQL Managed Instance
$roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
    exit
}
if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}

# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
    Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

    #Write-Output "Dumping service principal '$($managedInstanceName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
}
else {
    Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
}
```

### <a name="powershell-for-sql-managed-instance"></a>PowerShell per SQL Istanza gestita

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eseguire i cmdlet di PowerShell, è necessario che Azure PowerShell sia installato e in esecuzione. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/).

> [!IMPORTANT]
> Il modulo powershell Azure Resource Manager (RM) è ancora supportato da Istanza gestita di SQL di Azure, ma tutto lo sviluppo futuro è per il modulo Az.Sql. Il modulo AzureRM continuerà a ricevere correzioni di bug almeno fino a dicembre 2020.  Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](/powershell/azure/new-azureps-module-az).

Per eseguire il provisioning di un amministratore di Azure AD, eseguire i comandi di Azure PowerShell seguenti:

- Connect-AzAccount
- Select-AzSubscription

I cmdlet usati per effettuare il provisioning e la gestione Azure AD amministratore di SQL Istanza gestita sono elencati nella tabella seguente:

| Nome del cmdlet | Descrizione |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Effettua il provisioning Azure AD amministratore del Istanza gestita SQL nella sottoscrizione corrente. (Deve essere della sottoscrizione corrente)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Rimuove un Azure AD per l'istanza Istanza gestita SQL nella sottoscrizione corrente. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Restituisce informazioni su un amministratore Azure AD per il Istanza gestita SQL nella sottoscrizione corrente.|

Il comando seguente ottiene informazioni su un amministratore Azure AD per un Istanza gestita SQL denominato ManagedInstance01 associato a un gruppo di risorse denominato ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Il comando seguente effettua il provisioning Azure AD un gruppo di amministratori di database denominato DBAs per il Istanza gestita SQL denominato ManagedInstance01. Questo server è associato al gruppo di risorse ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Il comando seguente rimuove l'Azure AD per l'istanza di SQL Istanza gestita denominata ManagedInstanceName01 associata al gruppo di risorse ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È anche possibile effettuare il provisioning di un amministratore Azure AD per sql Istanza gestita chiamando i comandi dell'interfaccia della riga di comando seguenti:

| Comando | Descrizione |
| --- | --- |
|[az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_create) | Effettua il provisioning Azure Active Directory amministratore del database SQL Istanza gestita (deve essere della sottoscrizione corrente). |
|[az sql mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_delete) | Rimuove un amministratore Azure Active Directory per l'istanza di SQL Istanza gestita. |
|[az sql mi ad-admin list](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_list) | Restituisce informazioni su un amministratore Azure Active Directory attualmente configurato per l'istanza di SQL Istanza gestita. |
|[az sql mi ad-admin update](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_update) | Aggiorna l'amministratore di Active Directory per sql Istanza gestita. |

Per altre informazioni sui comandi dell'interfaccia della riga di comando, [vedere az sql mi](/cli/azure/sql/mi).

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>Effettuare il Azure AD amministratore (database SQL)

> [!IMPORTANT]
> Seguire questa procedura solo se si sta provisioning di un [server per](logical-servers.md) il database SQL o Azure Synapse.

Le due procedure seguenti illustrano come effettuare il provisioning di un amministratore Azure Active Directory per il server nel portale di Azure e tramite PowerShell.

### <a name="azure-portal"></a>Portale di Azure

1. Nell'angolo in alto a destra del [portale di Azure](https://portal.azure.com/) scegliere la connessione per visualizzare un elenco a discesa delle possibili directory di Active Directory. Scegliere la directory corretta come directory predefinita di Azure AD. Questo passaggio collega l'istanza di Active Directory associata alla sottoscrizione al server assicurando che la stessa sottoscrizione sia usata sia per Azure AD che per il server.

2. Cercare e selezionare **SQL Server.**

    ![Cercare e selezionare SQL Server](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > In questa pagina, prima di selezionare **SQL Server** è possibile selezionare la **stella** accanto al nome per *aggiungere ai preferiti* la categoria e aggiungere **SQL Server** alla barra di navigazione sinistra.

3. Nella pagina **SQL Server** selezionare **Amministratore di Active Directory.**

4. Nella pagina **Amministratore di Active Directory** selezionare **Imposta amministratore**.

    ![Impostazione dell'amministratore di Active Directory da parte dei server SQL](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. Nella pagina **Aggiungi amministratore** cercare un utente, selezionare l'utente o il gruppo da impostare come amministratore e quindi **selezionare Seleziona.** La pagina Amministratore di Active Directory mostra tutti i membri e i gruppi di Active Directory. Gli utenti e i gruppi non disponibili (in grigio) non possono essere selezionati, perché non sono supportati come amministratori di Azure AD. Vedere l'elenco degli amministratori supportati nella sezione Funzionalità e limitazioni di Azure AD in Usare l'autenticazione di **Azure Active Directory** per l'autenticazione con il database SQL [o Azure Synapse.](authentication-aad-overview.md) Il controllo degli accessi in base al ruolo di Azure si applica solo al portale e non viene propagato SQL Server.

    ![Selezionare Azure Active Directory amministratore](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. Nella parte superiore della pagina **Amministratore di Active Directory** selezionare **Salva**.

    ![Salvare l'impostazione dell'amministratore](./media/authentication-aad-configure/save-admin.png)

Il processo di modifica dell'amministratore può richiedere alcuni minuti. Il nuovo amministratore è quindi visualizzato nella casella **Amministratore di Active Directory** .

   > [!NOTE]
   > Quando si configura l'amministratore Azure AD, il nuovo nome amministratore (utente o gruppo) non può essere già presente nel database master virtuale come utente di autenticazione server. Se presente, l’impostazione dell’amministratore di Microsoft Azure avrà esito negativo; eseguire il rollback della creazione e indicare che tale (nome) di amministratore già esiste. Poiché tale utente di autenticazione server non fa parte del Azure AD, qualsiasi tentativo di connettersi al server tramite l'autenticazione Azure AD non riesce.

Per rimuovere un amministratore in un secondo momento, nella parte superiore della pagina **Amministratore di Active Directory** scegliere **Rimuovi amministratore** e quindi **Salva**.

### <a name="powershell-for-sql-database-and-azure-synapse"></a>PowerShell per database SQL e Azure Synapse

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eseguire i cmdlet di PowerShell, è necessario che Azure PowerShell sia installato e in esecuzione. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/). Per eseguire il provisioning di un amministratore di Azure AD, eseguire i comandi di Azure PowerShell seguenti:

- Connect-AzAccount
- Select-AzSubscription

Cmdlet usati per effettuare il provisioning e la gestione Azure AD amministratore per il database SQL e Azure Synapse:

| Nome del cmdlet | Descrizione |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Effettua il provisioning di Azure Active Directory amministratore di sistema per il server che ospita il database SQL o Azure Synapse. (Deve essere della sottoscrizione corrente) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Rimuove un amministratore Azure Active Directory per il server che ospita il database SQL o Azure Synapse.|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Restituisce informazioni su un amministratore Azure Active Directory attualmente configurato per il server che ospita il database SQL o Azure Synapse. |

Usare il comando get-help di PowerShell per visualizzare altre informazioni per ognuno di questi comandi. Ad esempio: `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Lo script seguente effettua il provisioning Azure AD un gruppo di amministratori denominato **DBA_Group** (ID oggetto ) per il server demo_server in un gruppo di risorse denominato `40b79501-b343-44ed-9ce7-da4c8cc7353f`  **Group-23:**

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

Il parametro di input **DisplayName** accetta il nome visualizzato di Azure AD o il nome dell'entità utente. Ad esempio, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Per i gruppi di Azure AD è supportato solo il nome visualizzato di Azure AD.

> [!NOTE]
> Il comando di Azure PowerShell ```Set-AzSqlServerActiveDirectoryAdministrator``` non impedisce di eseguire il provisioning degli amministratori di Azure AD per gli utenti non supportati. È possibile eseguire il provisioning di un utente non supportato il quale non può tuttavia connettersi a un database.

L'esempio seguente usa il valore **ObjectID** opzionale:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Il valore **ObjectID** di Azure AD è obbligatorio quando **DisplayName** non è univoco. Per recuperare i valori **ObjectID** e **DisplayName**, usare la sezione Active Directory del portale di Azure classico e visualizzare le proprietà di un utente o di un gruppo.

Nell'esempio seguente vengono restituite informazioni sull'Azure AD amministratore corrente per il server :

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

L'esempio seguente illustra come rimuovere un amministratore di Azure AD:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile effettuare il provisioning di un amministratore Azure AD chiamando i comandi dell'interfaccia della riga di comando seguenti:

| Comando | Descrizione |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) | Effettua il provisioning Azure Active Directory amministratore di sistema per il server che ospita il database SQL o Azure Synapse. (Deve essere della sottoscrizione corrente) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) | Rimuove un amministratore Azure Active Directory per il server che ospita il database SQL o Azure Synapse. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) | Restituisce informazioni su un amministratore Azure Active Directory attualmente configurato per il server che ospita il database SQL o Azure Synapse. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) | Aggiorna l'amministratore di Active Directory per il server che ospita il database SQL o Azure Synapse. |

Per altre informazioni sui comandi dell'interfaccia della riga di comando, [vedere az sql server](/cli/azure/sql/server).

* * *

> [!NOTE]
> È anche possibile effettuare il provisioning di un amministratore di Azure Active Directory usando le API REST. Per altre informazioni, vedere [Service Management REST API Reference and Operations for Azure SQL Database](/rest/api/sql/) (Informazioni di riferimento e operative sull'API REST di gestione dei servizi per le operazioni del database SQL di Azure)

## <a name="configure-your-client-computers"></a>Configurare i computer client

In tutti i computer client da cui le applicazioni o gli utenti si connettono al database SQL o Azure Synapse usando Azure AD identità, è necessario installare il software seguente:

- .NET Framework 4.6 o versione successiva da [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers) .
- Azure Active Directory Authentication Library per SQL Server (*ADAL.DLL*). Di seguito sono riportati i collegamenti di download per installare la versione più recente di SSMS, ODBC e il driver OLE DB che contiene la *libreriaADAL.DLL.*
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [ODBC Driver 17 for SQL Server](/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)
  - [OLE DB Driver 18 per SQL Server](/sql/connect/oledb/download-oledb-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)

È possibile soddisfare questi requisiti tramite:

- L'installazione della versione più recente [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) o [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) soddisfa il requisito .NET Framework 4.6.
  - SSMS installa la versione x86 *diADAL.DLL*.
  - SSDT installa la versione amd64 di *ADAL.DLL*.
  - La versione Visual Studio più recente [Visual Studio download](https://www.visualstudio.com/downloads/download-visual-studio-vs) soddisfa il requisito .NET Framework 4.6, ma non installa la versione amd64 richiesta *diADAL.DLL*.

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>Creare utenti indipendenti mappati a identità di Azure AD

Poiché SQL Istanza gestita supporta Azure AD entità server (account di accesso), l'uso di utenti di database indipendente non è obbligatorio. Le entità di sicurezza del server Azure AD (accessi) consentono di creare gli accessi dagli utenti, dai gruppi o dalle applicazioni di Azure AD. Ciò significa che è possibile eseguire l'autenticazione con il Istanza gestita SQL usando l'account di accesso Azure AD server anziché un utente del database indipendente. Per altre informazioni, vedere [Panoramica di SQL Istanza gestita .](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration) Per la sintassi sulla creazione di entità server (account di accesso) di Azure AD, vedere [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

Tuttavia, l'Azure Active Directory con il database SQL e Azure Synapse richiede l'uso di utenti di database indipendente basati su un'Azure AD identità. Un utente del database indipendente non dispone di un account di accesso nel database master ed esegue il mapping a un'identità Azure AD associata al database. L'identità di Azure AD può essere un singolo account utente o un gruppo. Per altre informazioni sugli utenti di database indipendente, vedere [Utenti di database indipendente: rendere portabile un database](/sql/relational-databases/security/contained-database-users-making-your-database-portable).

> [!NOTE]
> Gli utenti del database, tranne gli amministratori, non possono essere creati tramite il portale di Azure. I ruoli di Azure non vengono propagati al database nel database SQL, nel database SQL Istanza gestita o Azure Synapse. I ruoli di Azure vengono usati per la gestione delle risorse di Azure e non si applicano alle autorizzazioni del database. Ad esempio, il **ruolo SQL Server Collaboratore** non concede l'accesso per connettersi al database nel database SQL, nel Istanza gestita SQL o Azure Synapse. L'autorizzazione di accesso deve essere concessa direttamente nel database tramite istruzioni Transact-SQL.

> [!WARNING]
> I caratteri speciali, ad esempio due punti o e commerciale, se inclusi come nomi `:` utente in T-SQL e le istruzioni non sono `&` `CREATE LOGIN` `CREATE USER` supportati.

Per creare un utente di database indipendente basato su Azure AD, diverso dall'amministratore del server proprietario del database, connettersi al database con un'identità di Azure AD come utente con almeno l'autorizzazione **ALTER ANY USER** . Usare quindi la sintassi Transact-SQL seguente:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* può essere il nome dell'entità utente di un utente di Azure AD o il nome visualizzato di un gruppo di Azure AD.

**Esempi:** per creare un utente di database indipendente che rappresenta un utente di dominio gestito o federato di Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Per creare un utente di database indipendente che rappresenta un gruppo di dominio federato o di Azure AD, specificare il nome visualizzato di un gruppo di sicurezza:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Per creare un utente di database indipendente che rappresenta un'applicazione che si connette usando un token di Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Questo comando richiede che l'accesso a SQL Azure AD (il "provider esterno") per conto dell'utente connesso. In alcuni casi si verificano circostanze che Azure AD restituire un'eccezione a SQL. In questi casi, l'utente visualizza l'errore SQL 33134, che deve contenere Azure AD messaggio di errore specifico. Nella maggior parte dei casi, l'errore indica che l'accesso è negato o che l'utente deve registrarsi in MFA per accedere alla risorsa o che l'accesso tra applicazioni di prima parte deve essere gestito tramite preautenizzazione. Nei primi due casi, il problema è in genere causato dai criteri di accesso condizionale impostati nel tenant Azure AD dell'utente: impediscono all'utente di accedere al provider esterno. L'aggiornamento dei criteri di accesso condizionale per consentire l'accesso all'applicazione '000000002-0000-0000-c000-0000000000000' (ID applicazione del Azure AD API Graph) dovrebbe risolvere il problema. Se l'errore indica che l'accesso tra applicazioni di terze parti deve essere gestito tramite preautorizzazione, il problema è dovuto al fatto che l'utente ha eseguito l'accesso come entità servizio. Il comando dovrebbe avere esito positivo se viene invece eseguito da un utente.

> [!TIP]
> È possibile creare un utente direttamente da Azure Active Directory solo se si usa l'Azure Active Directory associata alla sottoscrizione di Azure. Tuttavia, i membri di altre directory di Active Directory che sono utenti importati nell'Active Directory associata (chiamati utenti esterni) possono essere aggiunti a un gruppo Active Directory nell'Active Directory tenant. Tramite la creazione di un utente del database indipendente per il gruppo AD, gli utenti dell'Active Directory esterna possono ottenere l'accesso al database SQL.

Per altre informazioni sulla creazione di utenti di database indipendente basati su identità di Azure Active Directory, vedere [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql).

> [!NOTE]
> La rimozione dell Azure Active Directory amministratore del server impedisce a qualsiasi utente Azure AD di autenticazione di connettersi al server. Se necessario, un amministratore del database SQL può eliminare manualmente gli utenti di Azure AD inutilizzabili.

> [!NOTE]
> Se si riceve un messaggio di **timeout della connessione scaduto**, potrebbe essere necessario impostare il parametro `TransparentNetworkIPResolution` della stringa di connessione su false. Per altre informazioni, vedere [Connection timeout issue with .NET Framework 4.6.1 - TransparentNetworkIPResolution](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution) (Problema di timeout della connessione con .NET Framework 4.6.1 - TransparentNetworkIPResolution).

Quando si crea un database utente, questo riceve l'autorizzazione **CONNECT** e può connettersi al database come membro del ruolo **PUBLIC**. Inizialmente le sole autorizzazioni disponibili per l'utente sono quelle concesse al ruolo **PUBLIC** o quelle concesse a qualsiasi gruppo di Azure AD di cui è membro. Dopo avere effettuato il provisioning di un utente di database indipendente basato su Azure AD, è possibile concedere all'utente altre autorizzazioni, esattamente come si concede un'autorizzazione a qualsiasi altro tipo di utente. In genere si concedono autorizzazioni ai ruoli del database e si aggiungono gli utenti ai ruoli. Per altre informazioni, vedere l'articolo relativo alle [nozioni di base delle autorizzazioni per il motore di database](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Per altre informazioni sui ruoli speciali del database SQL, vedere [Gestione di database e account di accesso nel database SQL di Azure](logins-create-manage.md).
Un account utente di dominio federato importato in un dominio gestito come utente esterno deve usare l'identità del dominio gestito.

> [!NOTE]
> Gli utenti di Azure AD sono contrassegnati nei metadati del database con il tipo E (EXTERNAL_USER). I gruppi sono contrassegnati con il tipo X (EXTERNAL_GROUPS). Per altre informazioni, vedere [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>Connettersi al database usando SSMS o SSDT  

Per verificare che l'amministratore di Azure AD sia configurato correttamente, connettersi al database **master** con l'account amministratore di Azure AD.
Per effettuare il provisioning di un utente di database indipendente basato su Azure AD, diverso dall'amministratore del server proprietario del database, connettersi al database con un'identità di Azure AD che abbia accesso al database.

> [!IMPORTANT]
> Il supporto per l'autenticazione di Azure Active Directory è disponibile con [SQL Server 2016 Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) in Visual Studio 2015. La versione di agosto 2016 di SQL Server Management Studio include anche il supporto per l'autenticazione universale di Active Directory, che consente agli amministratori di richiedere la Multi-Factor Authentication mediante chiamata telefonica, SMS, smart card con pin, o notifica dell'app per dispositivi mobili.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Uso di un'identità di Azure AD per la connessione con SSMS o SSDT

Le procedure seguenti illustrano come connettersi al database SQL con un'identità Azure AD usando SQL Server Management Studio o SQL Server Database Tools.

### <a name="active-directory-integrated-authentication"></a>Autenticazione integrata di Active Directory

Usare questo metodo se si è connessi a Windows usando le credenziali di Azure Active Directory da un dominio federato o un dominio gestito configurato per l'accesso Single Sign-On facile per l'autenticazione pass-through e dell'hash delle password. Per altre informazioni, vedere [Accesso Single Sign-On facile di Azure Active Directory](../../active-directory/hybrid/how-to-connect-sso.md).

1. Avviare Management Studio o Strumenti dati e nella finestra di dialogo Connetti al **server** (o Connetti **a motore di database**) selezionare Azure Active Directory **- Integrata**.  La password non è necessaria e non può essere immessa, perché per la connessione vengono presentate le credenziali esistenti.

   ![Selezionare Autenticazione integrata di Active Directory][11]

2. Scegliere il pulsante **Opzioni**, quindi nella pagina **Proprietà connessione** digitare il nome del database utente a cui si desidera connettersi nella casella **Connect to database** (Connetti al database). Per altre informazioni, vedere l'articolo [Multi-factor Azure AD auth](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter) sulle differenze tra le proprietà di connessione per SSMS 17.x e 18.x.

   ![Selezionare il nome del database][13]

### <a name="active-directory-password-authentication"></a>Autenticazione della password di Active Directory

Usare questo metodo per connettersi con il nome dell'entità di Azure AD tramite il dominio gestito di Azure AD. È anche possibile usarlo per gli account federati senza accesso al dominio, ad esempio quando si lavora in remoto.

Usare questo metodo per eseguire l'autenticazione al database nel database SQL o nel Istanza gestita SQL con gli utenti con identità solo cloud di Azure AD o con gli utenti che usano Azure AD identità ibride. Questo metodo supporta gli utenti che vogliono usare le credenziali di Windows, ma il computer locale non è aggiunto al dominio (ad esempio, usando l'accesso remoto). In questo caso, un utente di Windows può indicare l'account di dominio e la password e può eseguire l'autenticazione al database nel database SQL, nel database SQL Istanza gestita o Azure Synapse.

1. Avviare Management Studio o Strumenti dati e nella finestra di dialogo Connetti al **server** (o Connetti **a motore di database**) selezionare Azure Active Directory **- Password**. 

2. Nella casella **Nome utente** digitare il nome utente Azure Active Directory nel formato nome **utente \@ domain.com**. I nomi utente devono essere un account Azure Active Directory o un account di un dominio gestito o federato con Azure Active Directory.

3. Nella casella **Password** digitare la password utente per l'account Azure Active Directory o l'account di dominio gestito/federato.

    ![Selezionare Autenticazione della password di Active Directory][12]

4. Scegliere il pulsante **Opzioni**, quindi nella pagina **Proprietà connessione** digitare il nome del database utente a cui si desidera connettersi nella casella **Connect to database** (Connetti al database). Vedere il grafico nell'opzione precedente.

### <a name="active-directory-interactive-authentication"></a>Autenticazione interattiva di Active Directory

Usare questo metodo per l'autenticazione interattiva con o senza Multi-Factor Authentication (MFA), con password richiesta in modo interattivo. Questo metodo può essere usato per eseguire l'autenticazione al database nel database SQL, nel Istanza gestita SQL e nel Azure Synapse per gli utenti con identità solo cloud di Azure AD o per gli utenti che usano Azure AD identità ibride.

Per altre informazioni, vedere [Using multi-factor Azure AD authentication with SQL Database and Azure Synapse (Supporto di SSMS per MFA).](authentication-mfa-ssms-overview.md)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Utilizzo di un'identità di Azure Active Directory per connettersi da un'applicazione client

Le procedure seguenti illustrano come connettersi a un database SQL con un'Azure AD identità da un'applicazione client.

### <a name="active-directory-integrated-authentication"></a>Autenticazione integrata di Active Directory

Per usare il autenticazione di Windows integrato, Active Directory del dominio deve essere federato con Azure Active Directory o deve essere un dominio gestito configurato per l'accesso Single Sign-On facile per l'autenticazione pass-through o dell'hash delle password. Per altre informazioni, vedere [Accesso Single Sign-On facile di Azure Active Directory](../../active-directory/hybrid/how-to-connect-sso.md).

> [!NOTE]
> [MSAL.NET (Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) per l'autenticazione integrata di Windows non è supportato per l'accesso Single Sign-On facile per l'autenticazione pass-through e con hash delle password.

L'applicazione client (o un servizio) che si connette al database deve essere in esecuzione in un computer aggiunto a un dominio con le credenziali di dominio di un utente.

Per connettersi a un database usando l'autenticazione integrata e un'identità Azure AD, la parola chiave Authentication nella stringa di connessione del database deve essere impostata su `Active Directory Integrated` . L'esempio di codice C# seguente usa ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

La parola chiave `Integrated Security=True` della stringa di connessione non è supportata per la connessione al database SQL di Azure. Quando si esegue una connessione ODBC, è necessario rimuovere gli spazi e impostare l'autenticazione su 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autenticazione della password di Active Directory

Per connettersi a un database usando Azure AD account utente con identità solo cloud o gli utenti che usano identità ibride di Azure AD, la parola chiave Authentication deve essere impostata su `Active Directory Password` . La stringa di connessione deve contenere le parole chiave e i valori User ID/UID e Password/PWD. L'esempio di codice C# seguente usa ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Per altre informazioni sui metodi di autenticazione di Azure AD, usare gli esempi di codice dimostrativo disponibili in [Azure AD Authentication GitHub Demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)(Dimostrazione GitHub dell'autenticazione di Azure AD).

## <a name="azure-ad-token"></a>Token Azure AD

Questo metodo di autenticazione consente ai servizi di livello intermedio di ottenere token [JSON Web (JWT)](../../active-directory/develop/id-tokens.md) per connettersi al database nel database SQL, nel Istanza gestita SQL o nel Azure Synapse ottenendo un token da Azure AD. Questo metodo consente vari scenari applicativi, tra cui identità del servizio, entità servizio e applicazioni che usano l'autenticazione basata su certificati. Per usare l'autenticazione tramite token di Azure AD, è necessario completare quattro passaggi fondamentali:

1. Registrare l'applicazione con Azure Active Directory e ottenere l'ID client per il codice.
2. Creare un utente del database che rappresenta l'applicazione. (Completato in precedenza al passaggio 6).
3. Creare un certificato nel computer client che esegue l'applicazione.
4. Aggiungere il certificato come chiave per l'applicazione.

Stringa di connessione di esempio:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Per altre informazioni, vedere [SQL Server Security Blog](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth)(Blog sulla sicurezza del server SQL). Per informazioni sull'aggiunta di un certificato, vedere [Introduzione all'autenticazione basata su certificati di Azure Active Directory](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Le istruzioni seguenti eseguono la connessione usando la versione 13.1 di sqlcmd disponibile in [Download Center](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd` con il `-G` comando non funziona con le identità di sistema e richiede un account di accesso dell'entità utente.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Risolvere i problemi Azure AD autenticazione

Le indicazioni sulla risoluzione dei problemi Azure AD'autenticazione sono disponibili nel blog seguente: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di account di accesso, utenti, ruoli del database e autorizzazioni nel database SQL, vedere Account di [accesso, utenti,](logins-create-manage.md)ruoli del database e account utente.
- Per altre informazioni sulle entità di database, vedere [Entità](/sql/relational-databases/security/authentication-access/principals-database-engine).
- Per altre informazioni sui ruoli del database, vedere [Ruoli a livello di database](/sql/relational-databases/security/authentication-access/database-level-roles).
- Per informazioni generali sulle regole del firewall, vedere l'articolo relativo alle [regole del firewall per il database SQL](firewall-configure.md).
- Per informazioni su come impostare un utente guest Azure AD come amministratore di Azure AD, vedere Creare utenti [guest](authentication-aad-guest-users.md)Azure AD e impostare come amministratore Azure AD .
- Per informazioni su come usare le entità servizio con Azure SQL, vedere Creare Azure AD [utenti usando Azure AD applicazioni](authentication-aad-service-principal-tutorial.md)

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png
