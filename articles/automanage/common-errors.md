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
ms.openlocfilehash: 18165ce5f39b32fe1c5af28bc88e8e1bd0e9cb62
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955551"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Risolvere gli errori di onboarding comuni per la gestione del caricamento
La gestione automatica potrebbe non riuscire a caricare un computer nel servizio. Questo documento illustra come risolvere gli errori di distribuzione, condivide alcuni motivi comuni per cui le distribuzioni possono avere esito negativo e descrive i potenziali passaggi successivi per la mitigazione.

## <a name="troubleshooting-deployment-failures"></a>Risoluzione degli errori di distribuzione
L'onboarding di un computer per la gestione automatica comporterà la creazione di una distribuzione Azure Resource Manager. Se il caricamento ha esito negativo, può essere utile consultare la distribuzione per ulteriori dettagli sul motivo per cui non è riuscito. Sono disponibili collegamenti alle distribuzioni nel riquadro a comparsa Dettagli errore, illustrato di seguito.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Riquadro a comparsa Dettagli errore automanage.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Controllare le distribuzioni per il gruppo di risorse che contiene la macchina virtuale non riuscita
Il riquadro a comparsa errore conterrà un collegamento alle distribuzioni all'interno del gruppo di risorse che contiene il computer che ha avuto esito negativo e un nome di prefisso che è possibile usare per filtrare le distribuzioni con. Facendo clic sul collegamento si passerà al pannello distribuzioni, in cui è possibile filtrare le distribuzioni per visualizzare le distribuzioni autogestite nel computer. Se si esegue la distribuzione in più aree, assicurarsi di fare clic sulla distribuzione nell'area corretta.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Controllare le distribuzioni della sottoscrizione che contiene la macchina virtuale non riuscita
Se non vengono visualizzati errori nella distribuzione del gruppo di risorse, il passaggio successivo consiste nell'esaminare le distribuzioni nella sottoscrizione che contengono la macchina virtuale che ha avuto esito negativo. Fare clic sul collegamento **distribuzioni per la sottoscrizione** nel riquadro a comparsa errore e filtrare le distribuzioni usando il filtro **automanage-DefaultResourceGroup** . Usare il nome del gruppo di risorse nel pannello errore per filtrare le distribuzioni. Il nome della distribuzione verrà suffisso con un nome di area. Se si esegue la distribuzione in più aree, assicurarsi di fare clic sulla distribuzione nell'area corretta.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Controllare le distribuzioni in una sottoscrizione collegata a un'area di lavoro Log Analytics
Se non vengono visualizzate le distribuzioni non riuscite nel gruppo di risorse o nella sottoscrizione che contiene la macchina virtuale non riuscita e se la macchina virtuale non riuscita è connessa a un'area di lavoro di Log Analytics in una sottoscrizione diversa, passare alla sottoscrizione collegata all'area di lavoro di Log Analytics e verificare la presenza di distribuzioni non riuscite.

## <a name="common-deployment-errors"></a>Errori di distribuzione comuni

Errore |  Strategia di riduzione del rischio
:-----|:-------------|
Errore di autorizzazioni insufficienti per l'account di gestione | Questo problema può verificarsi se di recente è stata spostata una sottoscrizione contenente un nuovo account di gestione autogestita in un nuovo tenant. I passaggi per risolvere il problema sono disponibili [qui](./repair-automanage-account.md).
Area dell'area di lavoro requisiti di mapping area non corrispondenti | La gestione automatica non è riuscita a caricare il computer, ma l'area di lavoro Log Analytics a cui il computer è attualmente collegato non è mappata a un'area di automazione supportata. Assicurarsi che l'area di lavoro Log Analytics esistente e l'account di automazione si trovino in un [mapping di area supportato](../automation/how-to/region-mappings.md).
"Accesso negato a causa dell'assegnazione Deny con il nome ' assegnazione di rifiuto di sistema creata dall'applicazione gestità" | Nella risorsa è stato creato un [denyAssignment](../role-based-access-control/deny-assignments.md) che ha impedito l'accesso alla risorsa da automanage. Il problema potrebbe essere stato causato da un [progetto](../governance/blueprints/concepts/resource-locking.md) o da un' [applicazione gestita](../azure-resource-manager/managed-applications/overview.md).
"Informazioni sistema operativo: nome =' (null)', ver =' (null)', stato agente =' non pronto '". | Verificare che sia in esecuzione una [versione minima dell'agente supportata](/troubleshoot/azure/virtual-machines/support-extensions-agent-version), che l'agente sia in esecuzione ([Linux](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) e [Windows](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) e che l'agente sia aggiornato ([Linux](../virtual-machines/extensions/update-linux-agent.md) e [Windows](../virtual-machines/extensions/agent-windows.md)).
"La macchina virtuale ha segnalato un errore durante l'elaborazione dell'estensione ' IaaSAntimalware '" | Assicurarsi di non avere un'altra offerta Antimalware/Antivirus già installata nella macchina virtuale. In caso di errore, contattare il supporto tecnico.
Area di lavoro ASC: la gestione di automanage attualmente non supporta il servizio Log Analytics nel _percorso_. | Verificare che la macchina virtuale si trovi in un' [area supportata](./automanage-virtual-machines.md#supported-regions).
La distribuzione del modello non è riuscita a causa di una violazione dei criteri. Per ulteriori informazioni, vedere i dettagli. | Si verifica un criterio che impedisce la gestione di automanage dall'onboarding della macchina virtuale. Verificare i criteri applicati alla sottoscrizione o al gruppo di risorse che contiene la macchina virtuale da caricare in automanage.
"L'assegnazione non è riuscita. non sono disponibili informazioni aggiuntive " | Aprire un caso con supporto Microsoft Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su gestione autogestita di Azure](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Abilitare la gestione automatici per le macchine virtuali nella portale di Azure](quick-create-virtual-machines-portal.md)