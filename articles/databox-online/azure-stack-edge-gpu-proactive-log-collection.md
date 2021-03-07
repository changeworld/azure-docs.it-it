---
title: Informazioni sulla raccolta di log proattivi nel dispositivo Azure Stack Edge Pro
description: Viene descritto il modo in cui la raccolta dei log proattivi viene eseguita in un dispositivo Azure Stack Edge Pro e come disabilitarlo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: alkohli
ms.openlocfilehash: bdd44bf81e34b60ee648b71c6dc3bde5a96d3deb
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443013"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Raccolta di log proattiva sul dispositivo Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

La raccolta di log proattivo raccoglie gli indicatori di integrità del sistema sul dispositivo Azure Stack Edge per facilitare la risoluzione dei problemi relativi ai dispositivi. La raccolta dei log proattivi è abilitata per impostazione predefinita. Questo articolo descrive gli elementi registrati, il modo in cui Microsoft gestisce i dati e come disabilitare o abilitare la raccolta di log proattiva. 

Le informazioni contenute in questo articolo sono valide per Azure Stack GPU Pro Edge, Azure Stack Edge Pro R e i dispositivi Mini R Azure Stack Edge.

## <a name="about-proactive-log-collection"></a>Informazioni sulla raccolta di log proattivi

I team di supporto tecnico clienti Microsoft usano i registri di sistema del dispositivo Azure Stack Edge per identificare e correggere in modo efficiente i problemi che potrebbero verificarsi durante il funzionamento. La raccolta di log proattivo è un metodo che avvisa Microsoft che un problema o un evento è stato rilevato dal dispositivo Azure Stack Edge del cliente. Vedere [indicatori di raccolta di log proattivi](#proactive-log-collection-indicators) per gli eventi rilevati. I log di supporto relativi al problema vengono caricati automaticamente in un account di archiviazione di Azure gestito e controllato da Microsoft. Supporto tecnico Microsoft e i tecnici Microsoft esaminano i registri di supporto per determinare la migliore linea di azione per risolvere il problema con il cliente.

> [!NOTE]
> Questi log vengono utilizzati solo a scopo di debug e per fornire supporto ai clienti in caso di problemi.


## <a name="enabling-proactive-log-collection"></a>Abilitazione della raccolta di log proattiva

La raccolta dei log proattivi è abilitata per impostazione predefinita. È possibile disabilitare la raccolta dei log proattivi quando si tenta di attivare il dispositivo tramite l'interfaccia utente locale. 

1. Nell'interfaccia utente Web locale del dispositivo andare alla pagina attività **iniziali** .

2. Nel riquadro **Attivazione** selezionare **Attiva**. 

    ![Pagina "Dettagli cloud" dell'interfaccia utente Web locale - 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)

3. Nel riquadro **Attiva**:

   1. Immettere la **chiave di attivazione** ottenuta in [Ottenere la chiave di attivazione per Azure Stack Edge Pro](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

      Una volta attivata, la raccolta dei log proattivi è abilitata per impostazione predefinita e consente a Microsoft di raccogliere i log in base allo stato di integrità del dispositivo. Questi log vengono caricati in un account di archiviazione di Azure. 

      È possibile disabilitare la raccolta proattiva dei log per arrestare la raccolta di log da Microsoft.

   1. Se si vuole disabilitare la raccolta dei log proattivi nel dispositivo, selezionare **Disabilita**.

   1. Selezionare **Attiva**.

   ![Interfaccia utente Web locale: "Dettagli cloud" - Pagina 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)

## <a name="proactive-log-collection-indicators"></a>Indicatori di raccolta log proattivi

Dopo aver abilitato la raccolta di log proattiva, i log vengono caricati automaticamente quando nel dispositivo viene rilevato uno degli eventi seguenti:  


|Avviso/errore/condizione  |Descrizione  |
|---------|---------|
|AcsUnhealthyCondition     |I servizi coerenti di Azure non sono integri.         |
|IOTEdgeAgentNotRunningCondition      |IoT Edge Agent non è in esecuzione.         |
|UpdateInstallFailedEvent | Non è stato possibile installare l'aggiornamento.        |

 
Microsoft continuerà ad aggiungere nuovi eventi all'elenco precedente. Non è necessario alcun consenso aggiuntivo per i nuovi eventi. Fare riferimento a questa pagina per informazioni sugli indicatori di raccolta proattiva di log più aggiornati.    
 

## <a name="other-log-collection-methods"></a>Altri metodi di raccolta di log

Oltre alla raccolta dei log proattiva, che raccoglie log specifici relativi a un problema specifico rilevato, altre raccolte di log possono fornire una conoscenza più approfondita dell'integrità del sistema e del comportamento. In genere, questi altri registri possono essere raccolti durante una richiesta di supporto o attivati da Microsoft in base ai dati di telemetria del dispositivo.

## <a name="handling-data"></a>Gestione dei dati

Quando la raccolta dei log proattivi è abilitata, il cliente acconsente a Microsoft raccogliendo i log dal dispositivo Azure Stack Edge, come descritto nel presente documento. Il cliente inoltre riconosce e acconsente al caricamento e alla conservazione dei log in un account di archiviazione di Azure gestito e controllato da Microsoft.

Microsoft utilizza i dati per la risoluzione dei problemi relativi all'integrità del sistema e solo ai problemi. I dati non vengono utilizzati per il marketing, la pubblicità o altri scopi commerciali senza il consenso del cliente. 

I dati raccolti da Microsoft vengono gestiti in base alle procedure di privacy standard. Se un cliente decide di revocare il consenso per la raccolta dei log proattivi, i dati raccolti con il consenso prima della revoca non saranno interessati.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [raccogliere un pacchetto per il supporto](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).