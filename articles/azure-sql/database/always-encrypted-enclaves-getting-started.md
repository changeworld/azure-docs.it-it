---
title: 'Esercitazione: Introduzione a Always Encrypted con enclave sicure nel database SQL di Azure'
description: Questa esercitazione illustra come creare un ambiente di base per Always Encrypted con le enclave sicure nel database SQL di Azure e come crittografare i dati sul posto ed eseguire query riservate complesse sulle colonne crittografate usando SQL Server Management Studio (SSMS).
keywords: Crittografa dati, crittografia SQL, crittografia del database, dati sensibili, Always Encrypted, enclavi sicure, SGX, attestazione
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 94923b13181290a290f13339da5b05f6fdddff38
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252166"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>Esercitazione: Introduzione a Always Encrypted con enclave sicure nel database SQL di Azure

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted con enclave sicure per il database SQL di Azure è attualmente disponibile in **anteprima pubblica**.

Questa esercitazione illustra come iniziare a usare [Always Encrypted con le enclave sicure](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves) nel database SQL di Azure. L'esercitazione spiega:

> [!div class="checklist"]
> - Come creare un ambiente per il test e la valutazione di Always Encrypted con enclave sicure.
> - Come crittografare i dati sul posto ed eseguire query riservate complesse sulle colonne crittografate usando SQL Server Management Studio (SSMS).

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione richiede Azure PowerShell e [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="powershell-requirements"></a>Requisiti di PowerShell

Per informazioni su come installare ed eseguire Azure PowerShell, vedere [Panoramica di Azure PowerShell](https://docs.microsoft.com/powershell/azure). 

Versione minima dei moduli Az necessaria per supportare le operazioni di attestazione:

- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

Eseguire il comando seguente per verificare la versione installata di tutti i moduli AZ:

```powershell
Get-InstalledModule
```

Se le versioni non corrispondono al requisito minimo, eseguire il `Update-Module` comando.

Il PowerShell Gallery ha deprecato Transport Layer Security (TLS) versioni 1,0 e 1,1. È consigliabile usare TLS 1.2 o versione successiva. Se si usa una versione di TLS precedente alla 1,2, è possibile che vengano visualizzati gli errori seguenti:

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

Per continuare a interagire con PowerShell Gallery, eseguire il comando seguente prima dei comandi Install-Module

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

### <a name="ssms-requirements"></a>Requisiti di SSMS

Per informazioni su come scaricare SSMS, vedere [scaricare SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) .

La versione minima richiesta di SSMS è 18,8.


## <a name="step-1-create-a-server-and-a-dc-series-database"></a>Passaggio 1: creare un server e un database della serie DC

 In questo passaggio si creerà un nuovo server logico di database SQL di Azure e un nuovo database usando la configurazione hardware della serie DC. Always Encrypted con enclavi sicure nel database SQL di Azure usa le enclave Intel SGX, che sono supportate nella configurazione hardware della serie DC. Per ulteriori informazioni, vedere [serie DC](service-tiers-vcore.md#dc-series).

1. Aprire una console di PowerShell e accedere ad Azure. Se necessario, [passare alla sottoscrizione](https://docs.microsoft.com/powershell/azure/manage-subscriptions-azureps) usata per questa esercitazione.

  ```PowerShell
  Connect-AzAccount
  $subscriptionId = <your subscription ID>
  Set-AzContext -Subscription $serverSubscriptionId
  ```

2. Creare un gruppo di risorse che contenga il server di database. 

  ```powershell
  $serverResourceGroupName = "<server resource group name>"
  $serverLocation = "<Azure region that supports DC-series in SQL Database>"
  New-AzResourceGroup -Name $serverResourceGroupName -Location $serverLocation 
  ```

  > [!IMPORTANT]
  > È necessario creare il gruppo di risorse in un'area che supporta la configurazione hardware della serie DC. Per l'elenco delle aree attualmente supportate, vedere [disponibilità della serie DC](service-tiers-vcore.md#dc-series-1).

3. Creare un server di database. Quando richiesto, immettere il nome dell'amministratore del server e una password.

  ```powershell
  $serverName = "<server name>" 
  New-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -Location $serverLocation
  ```

4. Creare una regola del firewall del server che consenta l'accesso dall'intervallo di indirizzi IP specificato
  
  ```powershell
  # The ip address range that you want to allow to access your server
  $startIp = "<start of IP range>"
  $endIp = "<end of IP range>"
  $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
  ```

5. Assegnare un'identità del sistema gestito al server. Sarà necessario in un secondo momento per concedere al server l'accesso a Microsoft Azure attestazione.

  ```powershell
  Set-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -AssignIdentity 
  ```

6. Recuperare un ID oggetto dell'identità assegnata al server. Salvare l'ID oggetto risultante. L'ID sarà necessario in una sezione successiva.

  > [!NOTE]
  > Potrebbero essere necessari alcuni secondi per la propagazione dell'identità del sistema gestito appena assegnata in Azure Active Directory. Se lo script seguente restituisce un risultato vuoto, riprovare.

  ```PowerShell
  $server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 
  $serverObjectId = $server.Identity.PrincipalId
  $serverObjectId
  ```

7. Creare un database della serie DC.

  ```powershell
  $databaseName = "ContosoHR"
  $edition = "GeneralPurpose"
  $vCore = 2
  $generation = "DC"
  New-AzSqlDatabase -ResourceGroupName $serverResourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $edition -Vcore $vCore -ComputeGeneration $generation
  ```

## <a name="step-2-configure-an-attestation-provider"></a>Passaggio 2: configurare un provider di attestazione

In questo passaggio verrà creato e configurato un provider di attestazione in Microsoft Azure attestazione. Questa operazione è necessaria per attestare l'enclave sicura nel server di database.

1. Copiare i criteri di attestazione seguenti e salvarli in un file di testo (txt). Per informazioni sui criteri seguenti, vedere [creare e configurare un provider di attestazione](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider).

  ```output
  version= 1.0;
  authorizationrules 
  {
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
  };
  ```

2. Importare le versioni richieste di `Az.Accounts` e `Az.Attestation` .  

  ```powershell
  Import-Module "Az.Accounts" -MinimumVersion "1.9.2"
  Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
  ```

3. Creare un gruppo di risorse per il provider di attestazioni.

  ```powershell
  $attestationLocation = $serverLocation
  $attestationResourceGroupName = "<attestation provider resource group name>"
  New-AzResourceGroup -Name $attestationResourceGroupName -Location $location  
  ```

4. Creare un provider di attestazione. 

  ```powershell
  $attestationProviderName = "<attestation provider name>" 
  New-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Location $attestationLocation
  ```

5. Configurare i criteri di attestazione.
  
  ```powershell
  $policyFile = "<the pathname of the file from step 1 in this section"
  $teeType = "SgxEnclave"
  $policyFormat = "Text"
  $policy=Get-Content -path $policyFile -Raw
  Set-AzAttestationPolicy -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Tee $teeType -Policy $policy -PolicyFormat  $policyFormat
  ```

6. Concedere al server logico SQL di Azure l'accesso al provider di attestazione. In questo passaggio viene usato l'ID oggetto dell'identità del servizio gestito assegnata in precedenza al server.

  ```powershell
  New-AzRoleAssignment -ObjectId $serverObjectId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName  
  ```

7. Recuperare l'URL di attestazione.

  ```powershell
  $attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
  $attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
  Write-Host "Your attestation URL is: " $attestationUrl 
  ```

8.  Salvare l'URL di attestazione risultante che punta a un criterio di attestazione configurato per l'enclave SGX. Sarà necessario più avanti. L'URL di attestazione dovrebbe essere simile al seguente: `https://contososqlattestation.uks.attest.azure.net/attest/SgxEnclave`

## <a name="step-3-populate-your-database"></a>Passaggio 3: popolare il database

In questo passaggio verrà creata una tabella che verrà popolata con alcuni dati che verranno successivamente crittografati ed eseguiranno una query.

1. Aprire SSMS e connettersi al database **ContosoHR** nel server logico di Azure SQL creato **senza** always Encrypted abilitato nella connessione al database.
    1. Nella finestra di dialogo **Connetti al server** specificare il nome del server (ad esempio, *myserver123.database.Windows.NET*) e immettere il nome utente e la password configurati in precedenza.
    2. Fare clic su **opzioni >>** e selezionare la scheda **Proprietà connessione** . Assicurarsi di selezionare il database **ContosoHR** (non il database master predefinito). 
    3. Selezionare la scheda **Always Encrypted**.
    4. Verificare che la casella di controllo **Abilita Always Encrypted (crittografia colonna)** **non** sia selezionata.

        ![Connetti senza Always Encrypted](media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png)

    5. Fare clic su **Connect** (Connetti).

2. Creare una nuova tabella con nome **Employees**.

    ```sql
    CREATE SCHEMA [HR];
    GO

    CREATE TABLE [HR].[Employees]
    (
        [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [Salary] [money] NOT NULL
    ) ON [PRIMARY];
    GO
    ```

3. Aggiungere alcuni record dei dipendenti alla tabella **Employees**.

    ```sql
    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('795-73-9838'
            , N'Catherine'
            , N'Abel'
            , $31692);

    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('990-00-6818'
            , N'Kim'
            , N'Abercrombie'
            , $55415);
    ```


## <a name="step-4-provision-enclave-enabled-keys"></a>Passaggio 4: effettuare il provisioning di chiavi abilitate per Enclav

In questo passaggio verranno create una chiave master della colonna e una chiave di crittografia della colonna che consentono i calcoli dell'enclave.

1. Usando l'istanza di SSMS nel passaggio precedente, in **Esplora oggetti** espandere il database e passare a **Sicurezza** > **Chiavi Always Encrypted**.
1. Effettuare il provisioning di una nuova chiave master della colonna abilitata per l'enclave:
    1. Fare clic con il pulsante destro del mouse su **Chiavi Always Encrypted** e scegliere **Nuova chiave master della colonna**.
    2. Selezionare il nome della chiave master della colonna: **CMK1**.
    3. Assicurarsi di selezionare **Archivio certificati Windows (Utente corrente o Computer locale)** o **Azure Key Vault**.
    4. Selezionare **Consenti calcoli enclave**.
    5. Se è stata selezionata l'opzione Azure Key Vault, accedere ad Azure e selezionare l'insieme di credenziali delle chiavi. Per altre informazioni su come creare un insieme di credenziali delle chiavi per Always Encrypted, vedere [Manage your key vaults from Azure portal](/archive/blogs/kv/manage-your-key-vaults-from-new-azure-portal) (Gestire gli insiemi di credenziali delle chiavi dal portale di Azure).
    6. Selezionare il certificato o la chiave di Azure Key Vault se esiste già oppure fare clic sul pulsante **Genera certificato** per crearne uno nuovo.
    7. Selezionare **OK**.

        ![Consenti calcoli enclave](media/always-encrypted-enclaves/allow-enclave-computations.png)

1. Creare una nuova chiave di crittografia di colonna abilitata per l'enclave:

    1. Fare clic con il pulsante destro del mouse su **Chiavi Always Encrypted** e scegliere **Nuova chiave di crittografia della colonna**.
    2. Immettere un nome per la nuova chiave di crittografia della colonna: **CEK1**.
    3. Nell'elenco a discesa **Chiave master della colonna** selezionare la chiave master della colonna creata nei passaggi precedenti.
    4. Selezionare **OK**.

## <a name="step-5-encrypt-some-columns-in-place"></a>Passaggio 5: crittografare alcune colonne sul posto

In questo passaggio verranno crittografati i dati archiviati nelle colonne **SSN** e **Salary** nell'enclave del lato server e quindi verrà testato un query SELECT sui dati.

1. Aprire una nuova istanza di SSMS e connettersi al database **con** always Encrypted abilitata per la connessione al database.
    1. Avviare una nuova istanza di SSMS.
    2. Nella finestra di dialogo **Connetti al server** specificare il nome del server, selezionare un metodo di autenticazione e specificare le credenziali.
    3. Fare clic su **opzioni >>** e selezionare la scheda **Proprietà connessione** . Assicurarsi di selezionare il database **ContosoHR** (non il database master predefinito). 
    4. Selezionare la scheda **Always Encrypted**.
    5. Verificare che la casella di controllo **abilita always Encrypted (crittografia di colonna)** sia selezionata.
    6. Specificare l'URL di attestazione dell'enclave ottenuto seguendo i passaggi descritti in [passaggio 2: configurare un provider di attestazione](#step-2-configure-an-attestation-provider). Vedere la schermata seguente.

        ![Connetti con attestazione](media/always-encrypted-enclaves/connect-to-server-configure-attestation.png)

    7. Selezionare **Connetti**.
    8. Se viene richiesto di abilitare la parametrizzazione per Always Encrypted query, selezionare **Abilita**.



1. Usando la stessa istanza di SSMS (con Always Encrypted abilitato), aprire una nuova finestra query e crittografare le colonne **SSN** e **Salary** eseguendo le istruzioni seguenti.

    ```sql
    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [SSN] [char] (11) COLLATE Latin1_General_BIN2
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [Salary] [money]
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
    ```

    > [!NOTE]
    > Si noti l'istruzione ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE per cancellare la cache del piano di query per il database nello script precedente. Dopo aver modificato la tabella, è necessario cancellare i piani per tutti i batch e le stored procedure che accedono alla tabella per aggiornare le informazioni di crittografia dei parametri. 

1. Per verificare che le colonne **SSN** e **Salary** ora sono crittografate, aprire una nuova finestra di query nell'istanza di SSMS **senza** Always Encrypted abilitato per la connessione di database ed eseguire l'istruzione seguente. La finestra di query restituirà valori crittografati nelle colonne **SSN** e **Salary**. Se si esegue la stessa query usando l'istanza di SSMS con Always Encrypted abilitato, verranno visualizzati i dati decrittografati.

    ```sql
    SELECT * FROM [HR].[Employees];
    ```

## <a name="step-6-run-rich-queries-against-encrypted-columns"></a>Passaggio 6: eseguire query complete sulle colonne crittografate

È possibile eseguire query complete sulle colonne crittografate. Vengono eseguite alcune operazioni di elaborazione query nell'enclave lato server. 

1. Nell'istanza di SSMS **con** Always Encrypted abilitato verificare che la parametrizzazione per Always Encrypted sia abilitata.
    1. Selezionare **Strumenti** dal menu principale di SSMS.
    2. Selezionare **Opzioni**.
    3. Passare a **Esecuzione query** > **SQL Server** > **Avanzata**.
    4. Assicurarsi che l'opzione **Abilita parametrizzazione per Always Encrypted** sia selezionata.
    5. Selezionare **OK**.
2. Aprire una nuova finestra di query, incollare la query seguente ed eseguire. La query restituisce valori di testo non crittografato e righe che soddisfano i criteri di ricerca specificati.

    ```sql
    DECLARE @SSNPattern [char](11) = '%6818';
    DECLARE @MinSalary [money] = $1000;
    SELECT * FROM [HR].[Employees]
    WHERE SSN LIKE @SSNPattern AND [Salary] >= @MinSalary;
    ```

3. Provare a ripetere la stessa query nell'istanza di SSMS che non ha Always Encrypted abilitata. Si verificherà un errore.
 
## <a name="next-steps"></a>Passaggi successivi

Al termine dell'esercitazione, è possibile passare a una delle esercitazioni seguenti:
- [Esercitazione: Sviluppare un'applicazione .NET usando Always Encrypted con enclave sicure](https://docs.microsoft.com/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)
- [Esercitazione: Sviluppare un'applicazione .NET Framework usando Always Encrypted con enclave sicuri](https://docs.microsoft.com/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [Esercitazione: Creazione e uso di indici sulle colonne abilitate per l'enclave tramite la crittografia casuale](https://docs.microsoft.com/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>Vedere anche

- [Configurare e usare Always Encrypted con enclave sicuri](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)