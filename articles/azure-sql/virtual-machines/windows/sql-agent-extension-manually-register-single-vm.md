---
title: Eseguire la registrazione con l'estensione SQL IaaS Agent
description: Registrare la macchina virtuale Azure SQL Server con l'estensione SQL IaaS Agent per abilitare le funzionalità per le macchine virtuali SQL Server distribuite all'esterno di Azure Marketplace, nonché la conformità e una migliore gestibilità.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: e34876c76259b8274e0b0ef9059659802eb55cf1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765450"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>Registrare SQL Server virtuale con l'estensione SQL IaaS Agent
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Registrazione della macchina SQL Server virtuale con l'estensione [SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) per ottenere numerosi vantaggi in termini di funzionalità per SQL Server in una macchina virtuale di Azure. 

Questo articolo illustra come registrare una singola macchina SQL Server virtuale con l'estensione SQL IaaS Agent. In alternativa, è possibile registrare tutte le SQL Server [macchine](sql-agent-extension-automatic-registration-all-vms.md) virtuali automaticamente o più macchine virtuali con script [in blocco.](sql-agent-extension-manually-register-vms-bulk.md)


## <a name="overview"></a>Panoramica

La registrazione con l SQL Server interno [dell'agente IaaS](sql-server-iaas-agent-extension-automate-management.md) crea la risorsa  macchina virtuale **SQL**  all'interno della sottoscrizione, che è una risorsa separata dalla risorsa macchina virtuale. L'annullamento della SQL Server virtuale virtuale dall'estensione rimuoverà la risorsa macchina virtuale **SQL,**  ma non rimuoverà la macchina virtuale effettiva.

La distribuzione di SQL Server'immagine Azure Marketplace macchina virtuale tramite il portale di Azure registra automaticamente la macchina SQL Server virtuale con l'estensione. Tuttavia, se si sceglie di eseguire l'installazione automatica di SQL Server in una macchina virtuale di Azure o di effettuare il provisioning di una macchina virtuale di Azure da un disco rigido virtuale personalizzato, è necessario registrare la macchina virtuale SQL Server con l'estensione SQL IaaS Agent in per ottenere tutti i vantaggi e la gestibilità delle funzionalità. 

Per usare l'estensione SQL IaaS Agent, è innanzitutto necessario registrare la sottoscrizione con il [provider **Microsoft.SqlVirtualMachine**](#register-subscription-with-resource-provider), che offre all'estensione SQL IaaS la possibilità di creare risorse all'interno di tale sottoscrizione specifica.

> [!IMPORTANT]
> L'estensione SQL IaaS Agent raccoglie i dati allo scopo di offrire ai clienti vantaggi facoltativi quando SQL Server all'interno di macchine virtuali di Azure. Microsoft non userà questi dati per i controlli delle licenze senza il consenso anticipato del cliente. Per altre [informazioni, SQL Server supplemento alla privacy.](/sql/sql-server/sql-server-privacy#non-personal-data)

## <a name="prerequisites"></a>Prerequisiti

Per registrare la SQL Server virtuale con l'estensione, è necessario: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Una macchina virtuale [Windows Server 2008 (o](../../../virtual-machines/windows/quick-create-portal.md) versione successiva) del modello di risorse di Azure con [SQL Server 2008 (o](https://www.microsoft.com/sql-server/sql-server-downloads) versione successiva) distribuita nel cloud pubblico o Azure per enti pubblici locale. 
- La versione più recente [dell'interfaccia](/cli/azure/install-azure-cli) della riga di comando di Azure [Azure PowerShell (almeno 5.0).](/powershell/azure/install-az-ps) 


## <a name="register-subscription-with-resource-provider"></a>Registrare la sottoscrizione con il provider di risorse

Per registrare la SQL Server virtuale con l'estensione SQL IaaS Agent, è necessario prima registrare la sottoscrizione con il provider di risorse **Microsoft.SqlVirtualMachine.** In questo modo l'estensione sql IaaS Agent può creare risorse all'interno della sottoscrizione.  A tale scopo, è possibile usare il portale di Azure, l'interfaccia della riga di comando di Azure o Azure PowerShell.

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al portale di Azure e andare a **Tutti i servizi**. 
1. Andare a **Sottoscrizioni** e selezionare la sottoscrizione di interesse.  
1. Nella pagina **Sottoscrizioni** passare alle **estensioni**. 
1. Immettere **sql** nel filtro per visualizzare le estensioni correlate a SQL. 
1. Selezionare uno tra **Registra**, **Ripeti registrazione**, o **Annulla registrazione** per il provider **Microsoft.SqlVirtualMachine** in base all'azione desiderata. 

   ![Modificare il provider](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)


### <a name="command-line"></a>Riga di comando

Registrare la sottoscrizione di Azure con il provider **Microsoft.SqlVirtualMachine** usando l'interfaccia della riga di comando di Azure o Azure PowerShell. 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/bash)

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-extension"></a>Eseguire la registrazione con l'estensione

Esistono tre modalità di gestione per [l'SQL Server dell'agente IaaS.](sql-server-iaas-agent-extension-automate-management.md#management-modes) 

La registrazione dell'estensione in modalità di gestione completa riavvia il servizio SQL Server quindi è consigliabile registrare prima l'estensione in modalità leggera e quindi eseguire l'aggiornamento a [full](#upgrade-to-full) durante una finestra di manutenzione. 

### <a name="lightweight-management-mode"></a>Modalità di gestione semplificata

Usare l'interfaccia della riga di comando di Azure Azure PowerShell per registrare la macchina virtuale SQL Server con l'estensione in modalità leggera. In questo modo non verrà riavviato SQL Server servizio. È quindi possibile eseguire l'aggiornamento alla modalità completa in qualsiasi momento, ma in questo modo verrà riavviato il servizio SQL Server. Pertanto, è consigliabile attendere una finestra di manutenzione pianificata. 

Specificare il tipo di licenza SQL Server come pagamento in base al consumo ( ) per il pagamento in base all'utilizzo, Vantaggio Azure Hybrid ( ) per usare la propria licenza o ripristino di emergenza ( ) per attivare la licenza di replica di ripristino di emergenza `PAYG` `AHUB` `DR` [gratuita.](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)

Le istanze del cluster di failover e le distribuzioni a istanze multiple possono essere registrate solo con l'estensione SQL IaaS Agent in modalità leggera. 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/bash)

Registrare una macchina SQL Server in modalità leggera con l'interfaccia della riga di comando di Azure: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registrare una SQL Server virtuale in modalità leggera con Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Modalità di gestione completa

La registrazione della SQL Server virtuale in modalità completa riavvierà il SQL Server servizio. Procedere con cautela. 

Per registrare la macchina SQL Server virtuale direttamente in modalità completa (ed eventualmente riavviare il servizio SQL Server), usare il comando Azure PowerShell seguente: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Modalità di gestione NoAgent 

SQL Server 2008 e 2008 R2 installati in Windows Server 2008 ( non _R2_) possono essere registrati con l'estensione SQL IaaS Agent in modalità [NoAgent](sql-server-iaas-agent-extension-automate-management.md#management-modes). Questa opzione assicura la conformità e consente il monitoraggio della macchina virtuale SQL Server nel portale di Azure con funzionalità limitate.


Per il **tipo di** licenza , specificare: , `AHUB` o `PAYG` `DR` . Per **l'offerta di immagine,** specificare `SQL2008-WS2008` o `SQL2008R2-WS2008`

Per registrare l'SQL Server 2008 ( ) o 2008 R2 ( ) nell'istanza di `SQL2008-WS2008` Windows Server 2008, usare il frammento di codice seguente dell'interfaccia della riga di `SQL2008R2-WS2008` comando Azure PowerShell Azure: 


# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/bash)

Registrare la macchina SQL Server virtuale in modalità NoAgent con l'interfaccia della riga di comando di Azure: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type <license type>  --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer <image offer> 
 ```
 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)


Registrare SQL Server macchina virtuale in modalità NoAgent con Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
  ```

---

## <a name="verify-mode"></a>Modalità di verifica

È possibile visualizzare la modalità corrente dell'agente SQL Server IaaS usando Azure PowerShell: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Eseguire l'aggiornamento alla versione completa  

SQL Server macchine virtuali che hanno registrato l'estensione *in* modalità leggera possono eseguire l'aggiornamento alla modalità completa usando il portale di Azure, l'interfaccia della riga di comando di Azure o Azure PowerShell.  Le macchine virtuali SQL Server in modalità _NoAgent_ possono eseguire l'aggiornamento alla modalità _completa_ dopo l'aggiornamento del sistema operativo a Windows 2008 R2 e versioni successive. Non è possibile effettuare il downgrade. A [](#unregister-from-extension) tale scopo, sarà necessario annullare la registrazione della macchina virtuale SQL Server dall'estensione SQL IaaS Agent. Questa operazione rimuoverà la **risorsa** _macchina virtuale di SQL Server_ ma non eliminerà la macchina virtuale in sé. 

> [!NOTE]
> Quando si aggiorna la modalità di gestione per l'estensione SQL IaaS alla modalità completa, verrà riavviato SQL Server servizio. In alcuni casi, il riavvio può causare la modifica dei nomi dell'entità servizio (SPN) associati al servizio SQL Server all'account utente errato. Se si verificano problemi di connettività dopo l'aggiornamento della modalità di gestione alla modalità completa, annullare [la registrazione e registrare nuovamente i nomi SPN.](/sql/database-engine/configure-windows/register-a-service-principal-name-for-kerberos-connections)


### <a name="azure-portal"></a>Portale di Azure

Per aggiornare l'estensione alla modalità completa usando il portale di Azure, seguire questa procedura: 

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Andare alla risorsa [macchine virtuali SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). 
1. Selezionare la SQL Server virtuale e selezionare **Panoramica.** 
1. Per le macchine virtuali di SQL Server in modalità IaaS NoAgent o semplificata, selezionare il messaggio **Solo il tipo di licenza e gli aggiornamenti dell'edizione sono disponibili con l'estensione IaaS di SQL**.

   ![Selezioni per modificare la modalità dal portale](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. Selezionare la casella di controllo **Accetto di riavviare il servizio SQL Server nella macchina virtuale**, quindi **Conferma** per eseguire l'aggiornamento alla modalità IaaS completa. 

    ![Selezionare la casella per accettare di riavviare il servizio SQL Server nella macchina virtuale](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>Riga di comando

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/bash)

Per aggiornare l'estensione alla modalità completa, eseguire il frammento di codice dell'interfaccia della riga di comando di Azure seguente:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Per aggiornare l'estensione alla modalità completa, eseguire il frammento di Azure PowerShell seguente:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Verificare lo stato di registrazione
È possibile verificare se la macchina virtuale SQL Server è già stata registrata con l'estensione SQL IaaS Agent usando il portale di Azure, l'interfaccia della riga di comando di Azure o Azure PowerShell. 

### <a name="azure-portal"></a>Portale di Azure 

Per verificare lo stato della registrazione usando il portale di Azure, seguire questa procedura: 

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alle macchine [SQL Server virtuali .](manage-sql-vm-portal.md)
1. Selezionare la macchina virtuale di SQL Server dall'elenco. Se la SQL Server vm non è elencata qui, è probabile che non sia stata registrata con l'estensione SQL IaaS Agent. 
1. Visualizzare il valore in **Stato**. Se **Status** è **Succeeded**, la macchina SQL Server è stata registrata correttamente con l'estensione SQL IaaS Agent. 

   ![Verificare lo stato con la registrazione del provider di risorse SQL](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

### <a name="command-line"></a>Riga di comando

Verificare lo stato SQL Server registrazione della macchina virtuale usando l'interfaccia della riga di comando di Azure o Azure PowerShell. `ProvisioningState` mostrerà `Succeeded` se la registrazione è riuscita. 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/bash)

Per verificare lo stato della registrazione usando l'interfaccia della riga di comando di Azure, eseguire il frammento di codice seguente:  

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Per verificare lo stato della registrazione usando il Azure PowerShell, eseguire il frammento di codice seguente:

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Un errore indica che la macchina SQL Server macchina virtuale non è stata registrata con l'estensione. 


## <a name="unregister-from-extension"></a>Annullare la registrazione dall'estensione

Per annullare la registrazione della macchina virtuale SQL Server con l'estensione SQL  IaaS Agent, eliminare la risorsa macchina virtuale SQL usando l'interfaccia della portale di Azure o l'interfaccia della riga di comando di Azure. L'eliminazione della risorsa macchina *virtuale* SQL non elimina la macchina SQL Server macchina virtuale. Tuttavia, prestare attenzione e seguire attentamente i passaggi perché è possibile eliminare inavvertitamente la macchina virtuale quando si tenta di rimuovere la *risorsa*. 

L'annullamento della registrazione della macchina virtuale SQL con l'estensione SQL IaaS Agent è necessario per eseguire il downgrade completo della modalità di gestione. 

### <a name="azure-portal"></a>Portale di Azure

Per annullare la registrazione SQL Server macchina virtuale dall'estensione usando il portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare alla risorsa macchina virtuale SQL. 
  
   ![Risorsa di macchine virtuali SQL](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. Selezionare **Elimina**. 

   ![Selezionare Elimina nel riquadro di spostamento superiore](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. Digitare il nome della macchina virtuale SQL e **deselezionare la casella di controllo accanto alla macchina virtuale**.

   ![Deselezionare la macchina virtuale per impedire l'eliminazione della macchina virtuale effettiva e quindi selezionare Elimina per procedere con l'eliminazione della risorsa macchina virtuale SQL](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Se non si deseleziona la casella di controllo accanto al nome della macchina virtuale, la macchina virtuale verrà *eliminata* completamente. Deselezionare la casella di controllo per annullare la registrazione SQL Server macchina virtuale dall'estensione, *ma non eliminare la macchina virtuale effettiva.* 

1. Selezionare **Elimina** per confermare l'eliminazione della risorsa *macchina* virtuale SQL e non della SQL Server virtuale. 

### <a name="command-line"></a>Riga di comando

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per annullare la registrazione della SQL Server virtuale dall'estensione con l'interfaccia della riga di comando di Azure, usare [il comando az sql vm delete.](/cli/azure/sql/vm#az_sql_vm_delete) Questa operazione rimuoverà la SQL Server *macchina virtuale,* ma non eliminerà la macchina virtuale. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per annullare la registrazione della SQL Server virtuale dall'estensione con Azure PowerShell, usare il [comando Remove-AzSqlVM.](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm) Questa operazione rimuoverà la SQL Server *macchina virtuale,* ma non eliminerà la macchina virtuale. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Domande frequenti su SQL Server in una VM di Windows](frequently-asked-questions-faq.md)  
* [Informazioni sui prezzi di SQL Server in una VM di Windows](pricing-guidance.md)
* [Note sulla versione di SQL Server in una VM di Windows](../../database/doc-changes-updates-release-notes.md)
