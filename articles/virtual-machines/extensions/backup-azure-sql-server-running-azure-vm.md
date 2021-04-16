---
title: Backup di Azure per SQL Server in esecuzione in una macchina virtuale di Azure
description: Questo articolo illustra come registrare le Backup di Azure in SQL Server in esecuzione in una macchina virtuale di Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: v-amallick
ms.author: v-amallick
ms.collection: windows
ms.date: 07/05/2019
ms.openlocfilehash: c10be941206dd60887c9d82025506d1ea15c51a2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517250"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Backup di Azure per SQL Server in esecuzione in una macchina virtuale di Azure

Backup di Azure, tra le altre offerte, offre il supporto per il backup dei carichi di lavoro, ad esempio SQL Server in esecuzione nelle macchine virtuali di Azure. Poiché l'applicazione SQL è in esecuzione all'interno di una macchina virtuale di Azure, il servizio di backup deve disporre dell'autorizzazione per accedere all'applicazione e recuperare i dettagli necessari.
A tale scopo, Backup di Azure installa l'estensione **AzureBackupWindowsWorkload** nella macchina virtuale in cui è in esecuzione il SQL Server, durante il processo di registrazione attivato dall'utente.

## <a name="prerequisites"></a>Prerequisiti

Per l'elenco degli scenari supportati, vedere la matrice [di supporto](../../backup/sql-support-matrix.md#scenario-support) supportata da Backup di Azure.

## <a name="network-connectivity"></a>Connettività di rete

Backup di Azure supporta i tag del gruppo di sicurezza di rete, la distribuzione di un server proxy o di intervalli IP elencati; Per informazioni dettagliate su ognuno dei metodi, vedere questo [articolo.](../../backup/backup-sql-server-database-azure-vms.md#establish-network-connectivity)

## <a name="extension-schema"></a>Schema dell'estensione

Lo schema di estensione e i valori delle proprietà sono i valori di configurazione (impostazioni di runtime) che il servizio sta passando all'API CRP. Questi valori di configurazione vengono usati durante la registrazione e l'aggiornamento. **Anche l'estensione AzureBackupWindowsWorkload** usa questo schema. Lo schema è pre-impostato. è possibile aggiungere un nuovo parametro nel campo objectStr

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

Il codice JSON seguente mostra lo schema per l'estensione WorkloadBackup.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Valori delle proprietà

Nome | Valore/esempio | Tipo di dati
 --- | --- | ---
locale | it-it  |  string
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | string
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwMyMGVjNjVhIiwiU3Vic2JaxB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | string
commandStartTimeUTCTicks | "636967192566036845"  | string
vmType  | "microsoft.compute/virtualmachines"  | string
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwMyMGVjNjVhIiwiU3Vic2JaxB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | string
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | string
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | string

## <a name="template-deployment"></a>Distribuzione del modello

È consigliabile aggiungere l'estensione AzureBackupWindowsWorkload a una macchina virtuale abilitando SQL Server backup nella macchina virtuale. Questa operazione può essere ottenuta tramite il [modello Resource Manager progettato](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) per l'automazione del backup in una macchina SQL Server macchina virtuale.

## <a name="powershell-deployment"></a>Distribuzione PowerShell

È necessario "registrare" la macchina virtuale di Azure che contiene l'applicazione SQL con un insieme di credenziali dei servizi di ripristino. Durante la registrazione, l'estensione AzureBackupWindowsWorkload viene installata nella macchina virtuale. Usare il cmdlet [Register-AzRecoveryServicesBackupContainerPS](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) per registrare la macchina virtuale.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Il comando restituirà un **contenitore di backup** di questa risorsa e lo stato verrà **registrato.**

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sulle linee](../../backup/backup-sql-server-azure-troubleshoot.md) guida per la risoluzione dei Azure SQL di backup di macchine virtuali server
- [Domande comuni](../../backup/faq-backup-sql-server.yml) sul backup di SQL Server database che vengono eseguiti in macchine virtuali di Azure e che usano il Backup di Azure servizio.
