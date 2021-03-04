---
title: Che cos'è la SQL Server estensione dell'agente IaaS?
description: Questo articolo descrive il modo in cui l'estensione SQL Server agente IaaS consente di automatizzare le attività amministrative specifiche di gestione delle SQL Server nelle VM di Azure. Sono incluse funzionalità come backup automatizzato, applicazione automatica di patch, integrazione Azure Key Vault, gestione delle licenze, configurazione dell'archiviazione e gestione centrale di tutte le istanze di VM SQL Server.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: cab5ac5e6a8fd900a41ff3690763746033b6200e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034813"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>Automatizzare la gestione con l'estensione SQL Server agente IaaS
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


L'estensione SQL Server IaaS Agent (SqlIaasExtension) viene eseguita su SQL Server in macchine virtuali di Azure per automatizzare le attività di gestione e amministrazione. 

Questo articolo fornisce una panoramica dell'estensione. Per installare l'estensione IaaS SQL Server per SQL Server in macchine virtuali di Azure, vedere gli articoli relativi all' [installazione automatica](sql-agent-extension-automatic-registration-all-vms.md), alle [macchine virtuali singole](sql-agent-extension-manually-register-single-vm.md)o alle [VM in blocco](sql-agent-extension-manually-register-vms-bulk.md). 

## <a name="overview"></a>Panoramica

Il SQL Server estensione dell'agente IaaS consente l'integrazione con l'portale di Azure e, a seconda della modalità di gestione, sblocca diversi vantaggi di funzionalità per SQL Server in macchine virtuali di Azure: 

- **Vantaggi della funzionalità**: l'estensione consente di sbloccare diversi vantaggi della funzionalità di automazione, ad esempio la gestione del portale, la flessibilità delle licenze, il backup automatico, l'applicazione automatica di patch e altro ancora. Per informazioni dettagliate, vedere [vantaggi della funzionalità](#feature-benefits) più avanti in questo articolo. 

- **Conformità**: l'estensione offre un metodo semplificato per soddisfare la richiesta di notifica a Microsoft che la vantaggio Azure Hybrid è stata abilitata come indicato nelle condizioni del prodotto. Questo processo rimuove la necessità di gestire i moduli di registrazione delle licenze per ogni risorsa.  

- **Gratuito**: l'estensione in tutte e tre le modalità di gestibilità è completamente gratuita. Non vi sono costi aggiuntivi associati all'estensione o con modalità di gestione mutevoli. 

- **Gestione semplificata delle licenze**: l'estensione semplifica SQL Server la gestione delle licenze e consente di identificare rapidamente SQL Server VM con la vantaggio Azure Hybrid abilitata usando il [portale di Azure](manage-sql-vm-portal.md), PowerShell o l'interfaccia della riga di comando di Azure: 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---


> [!IMPORTANT]
> L'estensione SQL IaaS Agent raccoglie i dati per lo scopo esplicito di fornire ai clienti vantaggi facoltativi quando si usa SQL Server all'interno di macchine virtuali di Azure. Microsoft non utilizzerà questi dati per i controlli delle licenze senza il consenso anticipato del cliente. Per ulteriori informazioni, vedere la [SQL Server supplemento sulla privacy](/sql/sql-server/sql-server-privacy#non-personal-data) .


## <a name="feature-benefits"></a>Vantaggi delle funzionalità 

Il SQL Server estensione dell'agente IaaS sblocca diversi vantaggi di funzionalità per la gestione della SQL Server VM. 

La tabella seguente illustra i vantaggi seguenti: 


| Funzionalità | Descrizione |
| --- | --- |
| **Gestione nel portale** | Sblocca [la gestione nel portale](manage-sql-vm-portal.md), in modo che sia possibile visualizzare tutte le macchine virtuali SQL Server in un'unica posizione, in modo che sia possibile abilitare e disabilitare funzionalità specifiche di SQL direttamente dal portale. <br/> Modalità di gestione: Lightweight & completo|  
| **Backup automatico** |Consente di automatizzare la pianificazione delle operazioni di backup per tutti i database correlati all'istanza predefinita o a un'istanza denominata [correttamente installata](frequently-asked-questions-faq.md#administration) di SQL Server nella macchina virtuale. Per altre informazioni, vedere l'articolo [Backup automatico per SQL Server in macchine virtuali di Azure (Resource Manager)](automated-backup-sql-2014.md). <br/> Modalità di gestione: completa|
| **Applicazione automatica delle patch** |Configura una finestra di manutenzione durante la quale è possibile eseguire aggiornamenti importanti di Windows e SQL Server della sicurezza per la macchina virtuale, in modo da evitare gli aggiornamenti durante i periodi di picco del carico di lavoro. Per altre informazioni, vedere l'articolo [Applicazione automatica delle patch per SQL Server in macchine virtuali di Azure (Resource Manager)](automated-patching.md). <br/> Modalità di gestione: completa|
| **Integrazione di Azure Key Vault** |Consente di installare e configurare automaticamente l'insieme di credenziali delle chiavi di Azure nella VM di SQL Server. Per altre informazioni, vedere l'articolo [Configurare l'integrazione di Azure Key Vault per SQL Server in macchine virtuali di Azure (Resource Manager)](azure-key-vault-integration-configure.md). <br/> Modalità di gestione: completa|
| **Visualizzare l'utilizzo del disco nel portale** | Consente di visualizzare una rappresentazione grafica dell'utilizzo del disco dei file di dati SQL nel portale di Azure.  <br/> Modalità di gestione: completa | 
| **Licenze flessibili** | Risparmia sui costi grazie alla [transizione senza interruzioni](licensing-model-azure-hybrid-benefit-ahb-change.md) dalla tua licenza Bring your own License (nota anche come vantaggio Azure Hybrid) al modello di licenza con pagamento in base al consumo e viceversa. <br/> Modalità di gestione: Lightweight & completo| 
| **Versione flessibile/edizione** | Se si decide di modificare la [versione](change-sql-server-version.md) o l' [edizione](change-sql-server-edition.md) di SQL Server, è possibile aggiornare i metadati all'interno dell'portale di Azure senza dover ridistribuire l'intera macchina virtuale SQL Server.  <br/> Modalità di gestione: Lightweight & completo| 


## <a name="management-modes"></a>Modalità di gestione

È possibile scegliere di registrare l'estensione SQL IaaS in tre modalità di gestione: 

- La modalità **Lightweight** copia i file binari dell'estensione nella macchina virtuale ma non installa l'agente e non riavvia il servizio SQL Server. La modalità Lightweight supporta solo la modifica del tipo di licenza e dell'edizione di SQL Server e fornisce una gestione del portale limitata. Usare questa opzione per SQL Server VM con più istanze o quelle che partecipano a un'istanza del cluster di failover. La modalità Lightweight è la modalità di gestione predefinita quando si utilizza la funzionalità di [registrazione automatica](sql-agent-extension-automatic-registration-all-vms.md) oppure quando un tipo di gestione non viene specificato durante la registrazione manuale. Non vi è alcun impatto sulla memoria o sulla CPU quando si usa la modalità semplificata e non vi sono costi associati. Si consiglia di eseguire prima la registrazione della macchina virtuale di SQL Server in modalità semplificata, quindi di eseguire l'aggiornamento alla modalità completa durante una finestra di manutenzione pianificata. 

- La modalità **completa** consente di installare SQL IaaS Agent nella macchina virtuale per offrire tutte le funzionalità, ma richiede il riavvio delle autorizzazioni di amministratore di sistema e del servizio SQL Server. Usarla per gestire una macchina virtuale di SQL Server con una singola istanza. La modalità completa installa due servizi Windows che hanno un effetto minimo sulla memoria e sulla CPU, monitorabili tramite Gestione attività. Non è previsto alcun costo associato all'uso della modalità di gestione completa. 

- La modalità **NoAgent** è dedicata a SQL Server 2008 e SQL Server 2008 R2 installati in Windows Server 2008. Non vi è alcun effetto sulla memoria o sulla CPU quando si usa la modalità NoAgent. Non è associato alcun costo all'uso della modalità di gestibilità di noagent, il SQL Server non viene riavviato e un agente non è installato nella macchina virtuale. 

È possibile visualizzare la modalità corrente dell'agente di SQL Server IaaS usando Azure PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```


## <a name="installation"></a>Installazione

Registrare la macchina virtuale SQL Server con l'estensione dell'agente IaaS SQL Server per creare la _risorsa_ della **macchina virtuale SQL** nella sottoscrizione, che è una risorsa _separata_ dalla risorsa della macchina virtuale. Se si annulla la registrazione della VM SQL Server dall'estensione, la _risorsa_ della **macchina virtuale SQL** viene rimossa, ma non viene eliminata la macchina virtuale effettiva.

La distribuzione di un'immagine SQL Server VM di Azure Marketplace tramite il portale di Azure registra automaticamente la macchina virtuale SQL Server con l'estensione. Tuttavia, se si sceglie di installare autonomamente SQL Server in una macchina virtuale di Azure o di effettuare il provisioning di una macchina virtuale di Azure da un disco rigido virtuale personalizzato, è necessario registrare la VM SQL Server con l'estensione SQL IaaS per sbloccare i vantaggi della funzionalità. 

Se si registra l'estensione in modalità Lightweight, i file binari vengono copiati ma non installati nella macchina virtuale. L'agente viene installato nella macchina virtuale quando l'estensione viene aggiornata alla modalità di gestione completa. 

Esistono tre modi per eseguire la registrazione con l'estensione: 
- [Automaticamente per tutte le macchine virtuali correnti e future in una sottoscrizione](sql-agent-extension-automatic-registration-all-vms.md)
- [Manualmente per una singola macchina virtuale](sql-agent-extension-manually-register-single-vm.md)
- [Manualmente per più macchine virtuali in blocco](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>Supporto per istanze denominate

Il SQL Server estensione dell'agente IaaS funziona con un'istanza denominata di SQL Server se è l'unica istanza di SQL Server disponibile nella macchina virtuale. Non è possibile installare l'estensione in macchine virtuali con più istanze denominate SQL Server se non è presente alcuna istanza predefinita nella macchina virtuale. 

Per usare un'istanza denominata di SQL Server, distribuire una macchina virtuale di Azure, installare una singola istanza di SQL Server denominata e quindi registrarla con l' [estensione SQL IaaS](sql-agent-extension-manually-register-single-vm.md).

In alternativa, per usare un'istanza denominata con un'immagine del SQL Server di Azure Marketplace, seguire questa procedura: 

   1. Distribuire una macchina virtuale di SQL Server da Azure Marketplace. 
   1. [Annullare la registrazione](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) della macchina virtuale SQL Server dall'estensione SQL IaaS Agent. 
   1. Disinstallare completamente SQL Server nella macchina virtuale di SQL Server.
   1. Installare SQL Server con un'istanza denominata nella macchina virtuale di SQL Server. 
   1. [Registrare la macchina virtuale con l'estensione SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md#register-with-extension). 

## <a name="verify-status-of-extension"></a>Verificare lo stato dell'estensione

Usare il portale di Azure o Azure PowerShell per verificare lo stato dell'estensione. 

### <a name="azure-portal"></a>Portale di Azure

Verificare che l'estensione sia installata nell'portale di Azure. 

Selezionare **tutte le impostazioni** nel riquadro macchina virtuale e quindi fare clic su **estensioni**. L'estensione **SQLIaaSExtension** dovrebbe essere visualizzata nell'elenco.

![Stato del SQL Server estensione dell'agente IaaS nella portale di Azure](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

È anche possibile usare il cmdlet **Get-AzVMSqlServerExtension** di Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Il comando precedente verifica se l'agente è installato e visualizza informazioni generali sullo stato. È anche possibile ottenere informazioni specifiche sullo stato del backup e dell'applicazione di patch in modalità automatica usando i comandi seguenti:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>Limitazioni

L'estensione SQL IaaS Agent supporta solo: 

- Macchine virtuali di SQL Server distribuite tramite Azure Resource Manager. Le macchine virtuali di SQL Server distribuite tramite il modello classico non sono supportate. 
- Macchine virtuali di SQL Server distribuite nel cloud pubblico o Azure per enti pubblici. Le distribuzioni in altri cloud privati o governativi non sono supportate. 



## <a name="next-steps"></a>Passaggi successivi

Per installare l'estensione IaaS SQL Server per SQL Server in macchine virtuali di Azure, vedere gli articoli relativi all' [installazione automatica](sql-agent-extension-automatic-registration-all-vms.md), alle [macchine virtuali singole](sql-agent-extension-manually-register-single-vm.md)o alle [VM in blocco](sql-agent-extension-manually-register-vms-bulk.md).

Per altre informazioni sull'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [Cos'è SQL Server in Macchine virtuali di Azure?](sql-server-on-azure-vm-iaas-what-is-overview.md).

Per altre informazioni, vedere [domande frequenti](frequently-asked-questions-faq.md). 
