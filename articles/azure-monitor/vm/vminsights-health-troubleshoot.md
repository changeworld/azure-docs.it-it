---
title: Risolvere i problemi di integrità Guest di VM Insights (anteprima)
description: Descrive i passaggi di risoluzione dei problemi che è possibile eseguire quando si verificano problemi con l'integrità di VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: d8b37569ebaa8e75be601a1efd65a23a61aeaa75
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051940"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>Risolvere i problemi di integrità Guest di VM Insights (anteprima)
Questo articolo descrive i passaggi di risoluzione dei problemi che è possibile eseguire quando si verificano problemi con l'integrità di VM Insights.

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

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una panoramica della funzionalità di integrità Guest di VM Insights](vminsights-health-overview.md)