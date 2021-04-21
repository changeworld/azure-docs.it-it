---
title: Entità servizio di Azure Active Directory con Azure SQL
description: Azure AD applicazioni (entità servizio) supportano Azure AD creazione di utenti in database SQL di Azure, Istanza gestita di SQL di Azure e Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 17e846c7435e2f1cc77c5915f7e0b308c3706f96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775422"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Entità servizio di Azure Active Directory con Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Il supporto per Azure Active Directory (Azure AD) di utenti in database SQL di Azure (database SQL) e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) per conto di applicazioni Azure AD (entità servizio) è attualmente disponibile in **anteprima pubblica.**

> [!NOTE]
> Questa funzionalità è già supportata per SQL Istanza gestita.

## <a name="service-principal-azure-ad-applications-support"></a>Supporto delle entità servizio (Azure AD)

Questo articolo si applica alle applicazioni integrate con Azure AD e fanno parte della Azure AD registrazione. Queste applicazioni spesso necessitano dell'accesso di autenticazione e autorizzazione Azure SQL eseguire varie attività. Questa funzionalità in **anteprima pubblica** consente ora alle entità servizio di creare Azure AD utenti nel database SQL e Azure Synapse. Esisteva una limitazione che impediva Azure AD creazione di oggetti per conto Azure AD applicazioni rimosse.

Quando un'Azure AD viene registrata usando il portale di Azure o un comando di PowerShell, vengono creati due oggetti nel tenant Azure AD:

- Un oggetto applicazione
- Un oggetto entità servizio

Per altre informazioni sulle applicazioni Azure AD, vedere Oggetti applicazione e oggetti entità servizio [in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) e Creare un'entità servizio di [Azure con Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Il database SQL, Azure Synapse e SQL Istanza gestita supportano gli oggetti Azure AD seguenti:

- Azure AD utenti (gestiti, federati e guest)
- Azure AD gruppi (gestiti e federati)
-  Applicazioni Azure AD 

Il comando T-SQL `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` per conto di un'Azure AD è ora supportato per il database SQL e Azure Synapse.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Funzionalità della creazione Azure AD utenti tramite entità servizio

Il supporto di questa funzionalità è utile Azure AD processi di automazione delle applicazioni in cui gli oggetti Azure AD vengono creati e gestiti nel database SQL e Azure Synapse senza interazione umana. Le entità servizio possono essere un amministratore Azure AD per il server logico SQL, come parte di un gruppo o di un singolo utente. L'applicazione può automatizzare Azure AD creazione di oggetti nel database SQL e Azure Synapse quando viene eseguita come amministratore di sistema e non richiede privilegi SQL aggiuntivi. Ciò consente un'automazione completa della creazione di un utente del database. Questa funzionalità è supportata anche per l'identità gestita assegnata dal sistema e l'identità gestita assegnata dall'utente. Per altre informazioni, vedere [Che cosa sono le identità gestite per le risorse di Azure?](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Abilitare le entità servizio per creare Azure AD utenti

Per abilitare la creazione Azure AD di oggetti nel database SQL e Azure Synapse per conto di un'applicazione Azure AD, sono necessarie le impostazioni seguenti:

1. Assegnare l'identità del server. L'identità del server assegnata rappresenta l'identità del servizio gestito. Attualmente, l'identità del server Azure SQL non supporta l'identità gestita dall'utente (UMI).
    - Per un nuovo Azure SQL logico, eseguire il comando di PowerShell seguente:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Per altre informazioni, vedere il [comando New-AzSqlServer.](/powershell/module/az.sql/new-azsqlserver)

    - Per i Azure SQL logici esistenti, eseguire il comando seguente:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Per altre informazioni, vedere il comando [Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver).

    - Per verificare se l'identità del server è assegnata al server, eseguire il Get-AzSqlServer comando .

    > [!NOTE]
    > L'identità del server può essere assegnata anche tramite i comandi dell'interfaccia della riga di comando. Per altre informazioni, vedere [az sql server create](/cli/azure/sql/server#az_sql_server_create) e [az sql server update](/cli/azure/sql/server#az_sql_server_update).

2. Concedere l'Azure AD [**Autorizzazioni per i lettori di directory**](../../active-directory/roles/permissions-reference.md#directory-readers) all'identità del server creata o assegnata al server.
    - Per concedere questa autorizzazione, seguire la descrizione usata per SQL Istanza gestita disponibile nell'articolo seguente: [Provisioning Azure AD admin (SQL Istanza gestita)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - L Azure AD utente che concede questa autorizzazione deve far parte del Azure AD **amministratore** globale o amministratore **dei ruoli con** privilegi.

> [!IMPORTANT]
> I passaggi 1 e 2 devono essere eseguiti nell'ordine precedente. Per prima cosa, creare o assegnare l'identità del server, quindi concedere [**l'autorizzazione Lettori**](../../active-directory/roles/permissions-reference.md#directory-readers) directory. L'omissione di uno di questi passaggi o di entrambi causerà un errore di esecuzione durante la creazione di un oggetto Azure AD in Azure SQL per conto di un'Azure AD applicazione.
>
> Se si usa l'entità servizio per impostare o annullare l'impostazione dell'amministratore di Azure AD, l'applicazione deve avere anche l'autorizzazione dell'API [Directory.Read.All](/graph/permissions-reference#application-permissions-18) per l'Azure AD. Per altre informazioni sulle autorizzazioni necessarie per impostare un amministratore di [Azure AD](authentication-aad-service-principal-tutorial.md#permissions-required-to-set-or-unset-the-azure-ad-admin)e istruzioni dettagliate per creare un utente Azure AD per conto di un'applicazione Azure AD, vedere [Esercitazione:](authentication-aad-service-principal-tutorial.md)Creare utenti Azure AD usando applicazioni Azure AD .
>
> **Nell'anteprima pubblica** è possibile assegnare il **ruolo con** autorizzazioni di lettura nella directory a un gruppo in Azure AD. I proprietari del gruppo possono quindi aggiungere l'identità gestita come membro  di  questo gruppo, in modo da evitare che un amministratore globale o un amministratore dei ruoli con privilegi concedi il ruolo con autorizzazioni di lettura nella **directory.** Per altre informazioni su questa funzionalità, vedere [Ruolo con autorizzazioni di lettura nella directory in Azure Active Directory per Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="troubleshooting-and-limitations-for-public-preview"></a>Risoluzione dei problemi e limitazioni per l'anteprima pubblica

- Quando si creano oggetti Azure AD in Azure SQL per conto di un'applicazione Azure AD senza  abilitare l'identità del server e concedere l'autorizzazione con autorizzazioni di lettura nella directory, l'operazione avrà esito negativo con i possibili errori seguenti. L'errore di esempio seguente si verifica per l'esecuzione di un comando di PowerShell per creare un utente del database SQL nell'articolo Esercitazione: Creare Azure AD utenti usando Azure AD `myapp` [applicazioni](authentication-aad-service-principal-tutorial.md).
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - Per l'errore precedente, seguire la procedura descritta in Assegnare un'identità al server logico [Azure SQL](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) e [Assegnare](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity)autorizzazioni di lettura directory all'identità del server logico SQL .
    > [!NOTE]
    > I messaggi di errore indicati in precedenza verranno modificati prima della funzionalità disponibile a livello generale per identificare chiaramente il requisito di configurazione mancante per il supporto Azure AD'applicazione.
- L'impostazione dell'applicazione Azure AD come amministratore di Azure AD per SQL Istanza gestita è supportata solo tramite il comando dell'interfaccia della riga di comando e il comando di PowerShell [con Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) o versione successiva. Per altre informazioni, vedere i [comandi az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_create) e [Set-AzSqlInstanceActiveDirectoryAdministrator.](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) 
    - Se si vuole usare il portale di Azure per SQL Istanza gestita per impostare l'amministratore di Azure AD, una possibile soluzione alternativa consiste nel creare un gruppo Azure AD database. Aggiungere quindi l'entità servizio (Azure AD) a questo gruppo e impostare questo gruppo come amministratore Azure AD per l'istanza di SQL Istanza gestita.
    - L'impostazione dell'entità servizio (Azure AD) come amministratore di Azure AD per il database SQL e Azure Synapse è supportata tramite i comandi portale di Azure, [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)e l'interfaccia della riga [di](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) comando.
- L'uso Azure AD'applicazione con entità servizio da un altro tenant Azure AD avrà esito negativo quando si accede al database SQL o Istanza gestita SQL creato in un tenant diverso. Un'entità servizio assegnata a questa applicazione deve essere dello stesso tenant del server logico SQL o Istanza gestita.
- Un modulo [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) o versioni successive se si usa PowerShell per configurare una singola applicazione di Azure AD come amministratore Azure AD per Azure SQL. Assicurarsi di aver effettuato l'aggiornamento al modulo più recente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Creare utenti Azure AD con applicazioni di Azure AD](authentication-aad-service-principal-tutorial.md)
