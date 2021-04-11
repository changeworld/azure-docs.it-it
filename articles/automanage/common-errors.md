---
title: Risolvere gli errori di onboarding comuni di Azure automanage
description: Errori comuni di onboarding degli errori di caricamento e come risolverli
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 3c9f1b76bb707a296da00ac503482efe6a22385b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278338"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Risolvere gli errori di onboarding comuni per la gestione del caricamento
La gestione automatica potrebbe non riuscire a caricare un computer nel servizio. Questo documento illustra come risolvere gli errori di distribuzione, condivide alcuni motivi comuni per cui le distribuzioni possono avere esito negativo e descrive i potenziali passaggi successivi per la mitigazione.

## <a name="troubleshooting-deployment-failures"></a>Risoluzione degli errori di distribuzione
L'onboarding di un computer per la gestione automatica comporterà la creazione di una distribuzione Azure Resource Manager. Per altre informazioni, vedere la distribuzione per ulteriori dettagli sul motivo per cui non è riuscita. Sono disponibili collegamenti alle distribuzioni nel riquadro a comparsa Dettagli errore, illustrato di seguito.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Riquadro a comparsa Dettagli errore automanage.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Controllare le distribuzioni per il gruppo di risorse che contiene la macchina virtuale non riuscita
Il riquadro a comparsa errore conterrà un collegamento alle distribuzioni nel gruppo di risorse che contiene il computer che non ha eseguito l'onboarding. Il riquadro a comparsa conterrà anche un nome di prefisso che è possibile usare per filtrare le distribuzioni con. Facendo clic sul collegamento di distribuzione si passerà al pannello distribuzioni, in cui è possibile filtrare le distribuzioni per visualizzare le distribuzioni di gestione automatica nel computer. Se si esegue la distribuzione in più aree, assicurarsi di fare clic sulla distribuzione nell'area corretta.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Controllare le distribuzioni della sottoscrizione che contiene la macchina virtuale non riuscita
Se non vengono visualizzati errori nella distribuzione del gruppo di risorse, il passaggio successivo consiste nell'esaminare le distribuzioni nella sottoscrizione che contengono la macchina virtuale che ha avuto esito negativo. Fare clic sul collegamento **distribuzioni per la sottoscrizione** nel riquadro a comparsa errore e filtrare le distribuzioni usando il filtro **automanage-DefaultResourceGroup** . Usare il nome del gruppo di risorse nel pannello errore per filtrare le distribuzioni. Il nome della distribuzione verrà suffisso con un nome di area. Se si esegue la distribuzione in più aree, assicurarsi di fare clic sulla distribuzione nell'area corretta.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Controllare le distribuzioni in una sottoscrizione collegata a un'area di lavoro Log Analytics
Se non vengono visualizzate le distribuzioni non riuscite nel gruppo di risorse o nella sottoscrizione che contiene la macchina virtuale non riuscita e se la macchina virtuale non riuscita è connessa a un'area di lavoro di Log Analytics in una sottoscrizione diversa, passare alla sottoscrizione collegata all'area di lavoro di Log Analytics e verificare la presenza di distribuzioni non riuscite.

## <a name="common-deployment-errors"></a>Errori di distribuzione comuni

Errore |  Strategia di riduzione del rischio
:-----|:-------------|
Errore di autorizzazioni insufficienti per l'account di gestione | Questo errore può verificarsi se è stata spostata di recente una sottoscrizione contenente un nuovo account di gestione di automanage in un nuovo tenant. I passaggi per risolvere l'errore sono disponibili [qui](./repair-automanage-account.md).
Area dell'area di lavoro requisiti di mapping area non corrispondenti | La gestione automatica non è riuscita a caricare il computer perché l'area di lavoro Log Analytics a cui il computer è attualmente collegato non è mappata a un'area di automazione supportata. Assicurarsi che l'area di lavoro Log Analytics esistente e l'account di automazione si trovino in un [mapping di area supportato](../automation/how-to/region-mappings.md).
"Accesso negato a causa dell'assegnazione Deny con il nome ' assegnazione di rifiuto di sistema creata dall'applicazione gestità" | È stata creata una [denyAssignment](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) sulla risorsa, che ha impedito l'accesso alla risorsa da automanage. È possibile che questo denyAssignment sia stato creato da un [progetto](https://docs.microsoft.com/azure/governance/blueprints/concepts/resource-locking) o da un' [applicazione gestita](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/overview).
"Informazioni sistema operativo: nome =' (null)', ver =' (null)', stato agente =' non pronto '". | Verificare che sia in esecuzione una [versione minima dell'agente supportata](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version), che l'agente sia in esecuzione ([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) e [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) e che l'agente sia aggiornato ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) e [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)).
"Impossibile determinare il sistema operativo per il nome del sistema operativo della macchina virtuale:, ver. Verificare che l'agente di macchine virtuali sia in esecuzione, che lo stato corrente sia pronto. " | Verificare che sia in esecuzione una [versione minima dell'agente supportata](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version), che l'agente sia in esecuzione ([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) e [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) e che l'agente sia aggiornato ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) e [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)).

"La macchina virtuale ha segnalato un errore durante l'elaborazione dell'estensione ' IaaSAntimalware '" | Assicurarsi di non avere un'altra offerta Antimalware/Antivirus già installata nella macchina virtuale. In caso di errore, contattare il supporto tecnico.
Area di lavoro ASC: la gestione di automanage attualmente non supporta il servizio Log Analytics nel _percorso_. | Verificare che la macchina virtuale si trovi in un' [area supportata](./automanage-virtual-machines.md#supported-regions).
La distribuzione del modello non è riuscita a causa di una violazione dei criteri. Per ulteriori informazioni, vedere i dettagli. | Si verifica un criterio che impedisce la gestione di automanage dall'onboarding della macchina virtuale. Verificare i criteri applicati alla sottoscrizione o al gruppo di risorse che contiene la macchina virtuale da caricare in automanage.
"L'assegnazione non è riuscita. non sono disponibili informazioni aggiuntive "| Aprire un caso con supporto Microsoft Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su gestione autogestita di Azure](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Abilitare la gestione automatici per le macchine virtuali nella portale di Azure](quick-create-virtual-machines-portal.md)