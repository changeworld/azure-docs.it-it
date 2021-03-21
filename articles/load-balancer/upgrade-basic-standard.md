---
title: Eseguire l'aggiornamento dal pubblico di base al Azure Load Balancer pubblico standard
description: Questo articolo illustra come aggiornare il Load Balancer pubblico di Azure dallo SKU Basic allo SKU standard
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 125d4a02d06e2792f9a2a4e646c3788dcf223318
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612831"
---
# <a name="upgrade-azure-public-load-balancer"></a>Aggiornare Load Balancer pubblico di Azure
[Azure Load Balancer standard](load-balancer-overview.md) offre un set completo di funzionalità e disponibilità elevata tramite la ridondanza della zona. Per altre informazioni su Load Balancer SKU, vedere [tabella di confronto](./skus.md#skus).

L'aggiornamento prevede due fasi:

1. Modificare il metodo di allocazione IP da dinamico a statico.
2. Eseguire lo script di PowerShell per completare l'aggiornamento e la migrazione del traffico.

> [!IMPORTANT]
> Lo script è attualmente in fase di manutenzione. È possibile fare riferimento alle istruzioni [qui](../virtual-network/virtual-network-public-ip-address-upgrade.md) per aggiornare gli indirizzi IP pubblici dallo SKU Basic e dallo SKU standard.

## <a name="upgrade-overview"></a>Panoramica dell'aggiornamento

È disponibile uno script Azure PowerShell che esegue le operazioni seguenti:

* Crea uno SKU standard Load Balancer con la posizione specificata nello stesso gruppo di risorse della Load Balancer Standard di base.
* Aggiorna l'indirizzo IP pubblico dallo SKU Basic allo SKU standard sul posto.
* Copia agevolmente le configurazioni dello SKU Basic Load Balancer al Load Balancer Standard appena creato.
* Crea una regola in uscita predefinita che consente la connettività in uscita.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Lo script supporta solo l'aggiornamento di Load Balancer pubblici. Per l'aggiornamento interno Load Balancer di base, fare riferimento a [Questa pagina](./upgrade-basicinternal-standard.md) per istruzioni.
* Il metodo di allocazione dell'indirizzo IP pubblico deve essere impostato su "static" prima di eseguire lo script. 
* Se il Load Balancer non dispone di alcuna configurazione IP front-end o di un pool back-end, probabilmente si verifica un errore durante l'esecuzione dello script. Verificare che non siano vuoti.

### <a name="change-allocation-method-of-the-public-ip-address-to-static"></a>Modificare il metodo di allocazione dell'indirizzo IP pubblico in static

* * * Ecco le procedure consigliate:

    1. Per eseguire le attività di questo avvio rapido, accedere al [portale di Azure](https://portal.azure.com).
 
    1. Selezionare **tutte le risorse** nel menu a sinistra, quindi selezionare l' **indirizzo IP pubblico di base associato a Load Balancer Basic** dall'elenco di risorse.
   
    1. In **Impostazioni** selezionare **configurazioni**.
   
    1. In **Assegnazione** selezionare **Statico**.
    1. Selezionare **Salva**.
    >[!NOTE]
    >Per le macchine virtuali con indirizzi IP pubblici, è necessario creare prima di tutto indirizzi IP standard in cui lo stesso indirizzo IP non è garantito. Dissociare le VM dagli indirizzi IP di base e associarle agli indirizzi IP standard appena creati. Sarà quindi possibile seguire le istruzioni per aggiungere macchine virtuali nel pool back-end di Load Balancer Standard. 

* **Creazione di nuove macchine virtuali da aggiungere ai pool back-end del Load Balancer pubblico standard appena creato**.
    * Altre istruzioni su come creare una macchina virtuale e associarla a Load Balancer Standard sono disponibili [qui](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines).


## <a name="download-the-script"></a>Scaricare lo script

Scaricare lo script di migrazione dalla  [PowerShell Gallery](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/4.0).
## <a name="use-the-script"></a>USA lo script

Sono disponibili due opzioni a seconda della configurazione e delle preferenze dell'ambiente di PowerShell locale:

* Se non si dispone di Azure AZ Modules installato o non si vuole disinstallare i moduli AZ di Azure, l'opzione migliore consiste nell'usare l' `Install-Script` opzione per eseguire lo script.
* Se è necessario proteggere i moduli di Azure AZ, la scommessa migliore consiste nel scaricare lo script ed eseguirlo direttamente.

Per determinare se Azure AZ Modules è installato, eseguire `Get-InstalledModule -Name az` . Se non vengono visualizzati i moduli AZ installati, è possibile usare il `Install-Script` metodo.

### <a name="install-using-the-install-script-method"></a>Eseguire l'installazione usando il metodo Install-Script

Per usare questa opzione, non è necessario che nel computer siano installati i moduli AZ di Azure. Se sono installati, il comando seguente visualizza un errore. È possibile disinstallare i moduli di Azure AZ oppure usare l'altra opzione per scaricare lo script manualmente ed eseguirlo.
  
Eseguire lo script con il comando seguente:

`Install-Script -Name AzurePublicLBUpgrade`

Questo comando installa anche i moduli AZ richiesti.  

### <a name="install-using-the-script-directly"></a>Installare usando direttamente lo script

Se si dispone di alcuni moduli AZ di Azure installati e non è possibile disinstallarli (o non si vuole disinstallarli), è possibile scaricare manualmente lo script usando la scheda **download manuale** nel collegamento per il download dello script. Lo script viene scaricato come file nupkg non elaborato. Per installare lo script da questo file nupkg, vedere [download manuale del pacchetto](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Per eseguire lo script:

1. Usare `Connect-AzAccount` per connettersi ad Azure.

1. Usare `Import-Module Az` per importare i moduli AZ.

1. Esaminare i parametri obbligatori:

   * **oldRgName: [String]: required** : questo è il gruppo di risorse per la Load Balancer di base esistente che si vuole aggiornare. Per trovare questo valore stringa, passare a portale di Azure, selezionare l'origine Load Balancer di base e fare clic sulla **Panoramica** per il servizio di bilanciamento del carico. Il gruppo di risorse si trova in questa pagina.
   * **oldLBName: [String]: required** . si tratta del nome del servizio di bilanciamento di base esistente che si vuole aggiornare. 
   * **newLBName: [String]: required** : nome del Load Balancer standard da creare.
1. Eseguire lo script usando i parametri appropriati. Il completamento può richiedere da cinque a sette minuti.

    **Esempio**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newLbName "LBForUpgrade"
   ```

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Creare una regola in uscita per la connessione in uscita

Seguire le [istruzioni](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) per creare una regola in uscita, in modo che sia possibile
* Definire la NAT in uscita da zero.
* Ridimensionare e ottimizzare il comportamento del NAT in uscita esistente.

## <a name="common-questions"></a>Domande frequenti

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Esistono limitazioni allo script Azure PowerShell per eseguire la migrazione della configurazione dalla versione V1 alla versione V2?

Sì. Vedere [avvertenze/limitazioni](#caveatslimitations).

### <a name="how-long-does-the-upgrade-take"></a>Quanto tempo è necessario per l'aggiornamento?

Il completamento dello script richiede in genere circa 5-10 minuti e potrebbe richiedere più tempo a seconda della complessità della configurazione del Load Balancer. Pertanto, tenere presente il tempo di inattività e pianificare il failover, se necessario.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Lo script di Azure PowerShell passa anche il traffico dal Load Balancer di base al Load Balancer Standard appena creato?

Sì. Lo script di Azure PowerShell non solo Aggiorna l'indirizzo IP pubblico, copia la configurazione da base a Load Balancer Standard, ma esegue anche la migrazione della macchina virtuale a dietro il Load Balancer pubblico standard appena creato. 

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sulle Load Balancer Standard](load-balancer-overview.md)