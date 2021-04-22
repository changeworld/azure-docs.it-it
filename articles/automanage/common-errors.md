---
title: Risolvere i problemi Gestione automatica di Azure errori di onboarding
description: Errori comuni di onboarding di automanage e come risolverli
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 6ee0164dd8243d30cf691350352757f2503e34c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862976"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Risolvere i problemi comuni di onboarding di Automanage
La gestione automatica potrebbe non riuscire a eseguire l'onboardaggio di un computer nel servizio. Questo documento illustra come risolvere gli errori di distribuzione, condivide alcuni motivi comuni per cui le distribuzioni potrebbero non riuscire e descrive i potenziali passaggi successivi per la mitigazione.

## <a name="troubleshooting-deployment-failures"></a>Risoluzione dei problemi relativi agli errori di distribuzione
L'onboarding di un computer in Gestione automatica comporta la creazione Azure Resource Manager distribuzione. Per altre informazioni, vedere la distribuzione per altri dettagli sul motivo per cui non è riuscita. Sono disponibili collegamenti alle distribuzioni nel riquadro a comparsa dei dettagli dell'errore, illustrato di seguito.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Riquadro a comparsa Dettagli errore di maneggio automatico.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Controllare le distribuzioni per il gruppo di risorse contenente la macchina virtuale non riuscita
Il riquadro a comparsa degli errori conterrà un collegamento alle distribuzioni nel gruppo di risorse contenente il computer che non ha superato l'onboarding. Il riquadro a comparsa conterrà anche un nome di prefisso che è possibile usare per filtrare le distribuzioni. Facendo clic sul collegamento alla distribuzione si verrà visualizzati nel pannello distribuzioni, in cui è possibile filtrare le distribuzioni per visualizzare Gestione automatica delle distribuzioni nel computer. Se si esegue la distribuzione in più aree, assicurarsi di fare clic sulla distribuzione nell'area corretta.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Controllare le distribuzioni per la sottoscrizione contenente la macchina virtuale non riuscita
Se non vengono visualizzati errori nella distribuzione del gruppo di risorse, il passaggio successivo consiste nell'esaminare le distribuzioni nella sottoscrizione contenente la macchina virtuale che non ha superato l'onboarding. Fare clic **sul collegamento Distribuzioni per la** sottoscrizione nel riquadro a comparsa degli errori e filtrare le distribuzioni usando il filtro **Automanage-DefaultResourceGroup.** Usare il nome del gruppo di risorse nel pannello degli errori per filtrare le distribuzioni. Il nome della distribuzione verrà suffisso con un nome di area. Se si esegue la distribuzione in più aree, assicurarsi di fare clic sulla distribuzione nell'area corretta.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Controllare le distribuzioni in una sottoscrizione collegata a un'area di lavoro Log Analytics
Se non viene visualizzata alcuna distribuzione non riuscita nel gruppo di risorse o nella sottoscrizione contenente la macchina virtuale non riuscita e se la macchina virtuale in errore è connessa a un'area di lavoro Log Analytics in un'altra sottoscrizione, passare alla sottoscrizione collegata all'area di lavoro Log Analytics e verificare la presenza di distribuzioni non riuscite.

## <a name="common-deployment-errors"></a>Errori di distribuzione comuni

Errore |  Mitigazione
:-----|:-------------|
Errore di gestione automatica delle autorizzazioni insufficienti per l'account | Questo errore può verificarsi se di recente è stata spostata una sottoscrizione contenente un nuovo account di gestione automatica in un nuovo tenant. I passaggi per risolvere l'errore sono disponibili [qui.](./repair-automanage-account.md)
Area dell'area di lavoro non corrispondente ai requisiti di mapping dell'area | Gestione automatica non è riuscito a caricare il computer perché l'area di lavoro Log Analytics a cui è attualmente collegato il computer non è mappata a un'area di Automazione supportata. Assicurarsi che l'area di lavoro Log Analytics e l'account di Automazione esistenti si trovino in un [mapping di area supportato.](../automation/how-to/region-mappings.md)
"Accesso negato a causa dell'assegnazione di rifiuto con nome 'Assegnazione di rifiuto di sistema creata dall'applicazione gestita'" | È [stata creata una denyAssignment](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) nella risorsa, che ha impedito a Gestione automatica di accedere alla risorsa. Questo denyAssignment potrebbe essere stato creato da un progetto o [da](https://docs.microsoft.com/azure/governance/blueprints/concepts/resource-locking) [un'applicazione gestita.](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/overview)
"OS Information: Name='(null)', ver='(null)', agent status='Not Ready'." | Assicurarsi di eseguire una versione minima supportata dell'agente [,](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)che l'agente sia in esecuzione ([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) e [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) e che l'agente sia aggiornato ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) e [Windows).](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)
"Unable to determine the OS for the VM OS Name:, ver . Verificare che l'agente di macchine virtuali sia in esecuzione e che lo stato corrente sia Pronto." | Assicurarsi di eseguire una versione minima supportata dell'agente [,](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)che l'agente sia in esecuzione ([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) e [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) e che l'agente sia aggiornato ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) e [Windows).](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)
"La macchina virtuale ha segnalato un errore durante l'elaborazione dell'estensione 'IaaSAntimalware'" | Assicurarsi che non sia già installata un'altra offerta antimalware/antivirus nella macchina virtuale. In caso di errore, contattare il supporto tecnico.
Area di lavoro asc: la gestione automatica non supporta attualmente il servizio Log Analytics nel _percorso_. | Verificare che la macchina virtuale si trovi in [un'area supportata.](./automanage-virtual-machines.md#supported-regions)
La distribuzione del modello non è riuscita a causa di una violazione dei criteri. Per altre informazioni, vedere i dettagli. | Esiste un criterio che impedisce l'onboarding della macchina virtuale da parte di Automanage. Controllare i criteri applicati alla sottoscrizione o al gruppo di risorse contenente la macchina virtuale di cui si vuole eseguire l'onboardaggio in Gestione automatica.
"L'assegnazione non è riuscita. non sono disponibili informazioni aggiuntive" | Aprire un caso con il Microsoft Azure supporto tecnico.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sulle Gestione automatica di Azure](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Abilitare la gestione automatica per le macchine virtuali nel portale di Azure](quick-create-virtual-machines-portal.md)