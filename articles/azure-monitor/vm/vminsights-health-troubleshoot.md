---
title: Risolvere i problemi di integrità Guest di VM Insights (anteprima)
description: Descrive i passaggi di risoluzione dei problemi che è possibile eseguire quando si verificano problemi con l'integrità di VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 834c70e02ab25fa6dcadb5f6c997be09aaf5e353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932778"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>Risolvere i problemi di integrità Guest di VM Insights (anteprima)
Questo articolo descrive i passaggi di risoluzione dei problemi che è possibile eseguire quando si verificano problemi con l'integrità di VM Insights.


## <a name="upgrade-available-message-is-still-displayed-after-upgrading-guest-health"></a>Il messaggio aggiornamento disponibile viene comunque visualizzato dopo l'aggiornamento dell'integrità Guest 

- Verificare che la macchina virtuale sia in esecuzione in Azure globale. I server abilitati per Arc non sono ancora supportati.
- Verificare che l'area della macchina virtuale e la versione del sistema operativo siano supportate come descritto in [abilitare monitoraggio di Azure per le macchine virtuali integrità Guest (anteprima)](vminsights-health-enable.md).
- Verificare che l'estensione di integrità Guest sia stata installata correttamente con il codice di uscita 0.
- Verificare che l'estensione agente monitoraggio di Azure sia installata correttamente.
- Verificare che l'identità gestita assegnata dal sistema sia abilitata per la macchina virtuale.
- Verificare che non siano state specificate identità gestite assegnate dall'utente per la macchina virtuale.
- Verificare se le macchine virtuali Windows sono in *inglese (Stati Uniti*). La localizzazione non è attualmente supportata dall'agente di monitoraggio di Azure.
- Verificare che la macchina virtuale non usi il proxy di rete. L'agente di monitoraggio di Azure attualmente non supporta i proxy.
- Verificare che l'agente dell'estensione di integrità sia stato avviato senza errori. Se l'agente non può essere avviato, lo stato dell'agente potrebbe essere danneggiato. Eliminare il contenuto della cartella di stato dell'agente e riavviare l'agente.
  - Per Linux: daemon è *vmGuestHealthAgent*. La cartella di stato è */var/opt/vmGuestHealthAgent/**
  - Per Windows: il servizio è *Agente integrità Guest della macchina virtuale*. La cartella stato _è \\ * %ProgramData%\Microsoft\VMGuestHealthAgent_.
- Verificare che l'agente di monitoraggio di Azure disponga della connettività di rete. 
  - Dalla macchina virtuale, provare a eseguire il ping _<region> . handler.Control.monitor.Azure.com_. Ad esempio, per una macchina virtuale in westeurope, provare a eseguire il ping di _westeurope.handler.Control.monitor.Azure.com:443_.
- Verificare che la macchina virtuale disponga di un'associazione a una regola di raccolta dati nella stessa area dell'area di lavoro Log Analytics.
  -  Vedere **creare una regola di raccolta dati (DCR)** in [abilitare l'integrità Guest di monitoraggio di Azure per le macchine virtuali (anteprima)](vminsights-health-enable.md) per assicurarsi che la struttura di DCR sia corretta. Prestare particolare attenzione alla presenza della sezione dell'origine dati *PerformanceCounters* configurata per eseguire il campionamento di tre contatori e la presenza della sezione *inputDataSources* nella configurazione dell'estensione di integrità per inviare i contatori all'estensione.
-  Verificare la presenza di errori di estensione di integrità Guest nella macchina virtuale.
   -  Per Linux: controllare i log in _/var/log/Azure/Microsoft.Azure.monitor.VirtualMachines.GuestHealthLinuxAgent/*. log_.
   -  Per Windows: controllare i log in _C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.monitor.VirtualMachines.GuestHealthWindowsAgent \{ Extension Version} \* . log_.
-  Controllare la macchina virtuale per gli errori dell'agente di monitoraggio di Azure.
   -  Per Linux: controllare i log in _/var/log/MDSD. *_.
   -  Per Windows: controllare i log in _C:\WindowsAzure\Resources \* {vmName}. AMADataStore_.
 



## <a name="error-message-that-no-data-is-available"></a>Messaggio di errore che indica che non sono disponibili dati 

![Nessun dato](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Verificare che la macchina virtuale soddisfi i requisiti di configurazione

1. Verificare che la macchina virtuale sia una macchina virtuale di Azure. Azure Arc per server non è attualmente supportato.
2. Verificare che la macchina virtuale esegua un [sistema operativo supportato](vminsights-health-enable.md?current-limitations.md).
3. Verificare che la macchina virtuale sia installata in un'[area supportata](vminsights-health-enable.md?current-limitations.md).
4. Verificare che l'area di lavoro Log Analytics sia installata in un'[area supportata](vminsights-health-enable.md?current-limitations.md).

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Verificare che la macchina virtuale sia stata caricata correttamente
Verificare che l'estensione agente di monitoraggio di Azure e l'agente integrità macchina virtuale guest siano stati correttamente sottoposti a provisioning nella macchina virtuale. Selezionare **estensioni** dal menu della macchina virtuale nel portale di Azure e verificare che i due agenti siano elencati.

![Estensioni di VM](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Verificare che l'identità assegnata dal sistema sia abilitata nella macchina virtuale
Verificare che l'identità assegnata dal sistema sia abilitata nella macchina virtuale. Selezionare **Identity** dal menu della macchina virtuale nel portale di Azure. Se l'identità gestita dall'utente è abilitata, indipendentemente dallo stato dell'identità gestita dal sistema, l'agente di monitoraggio di Azure non sarà in grado di comunicare con il servizio di configurazione e l'estensione per l'integrità Guest non funzionerà.

![Identità assegnata dal sistema](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Verificare la regola di raccolta dati
Verificare che la regola di raccolta dati che specifica l'estensione di integrità come origine dati sia associata alla macchina virtuale.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Messaggio di errore per una richiesta non valida a causa di autorizzazioni insufficienti
Questo errore indica che il provider di risorse **Microsoft. WorkloadMonitor** non è stato registrato nella sottoscrizione. Per informazioni dettagliate sulla registrazione del provider di risorse, vedere [provider e tipi di risorse di Azure](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) . 

![Richiesta non valida](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="health-shows-as-unknown-after-guest-health-is-enabled"></a>Lo stato di integrità viene visualizzato come "sconosciuto" dopo l'abilitazione dell'integrità Guest.

### <a name="verify-that-performance-counters-on-windows-nodes-are-working-correctly"></a>Verificare che i contatori delle prestazioni nei nodi Windows funzionino correttamente 
L'integrità Guest si basa sull'agente che è in grado di raccogliere i contatori delle prestazioni dal nodo. il set di base delle librerie dei contatori delle prestazioni potrebbe essere danneggiato e potrebbe essere necessario ricompilarlo. Per ricompilare i contatori delle prestazioni, seguire le istruzioni riportate in [ricompilare manualmente i valori delle librerie dei contatori](/troubleshoot/windows-server/performance/rebuild-performance-counter-library-values)





## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una panoramica della funzionalità di integrità Guest di VM Insights](vminsights-health-overview.md)