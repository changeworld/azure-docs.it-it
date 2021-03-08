---
title: Usare runbook e moduli di Automazione di Azure in PowerShell Gallery
description: Questo articolo descrive come usare runbook e moduli di Microsoft e della community in PowerShell Gallery.
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c38a6236fe3ad9164d11d94e5563a7dddf5b4b32
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452782"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>Usare runbook e moduli in PowerShell Gallery

Anziché creare runbook e moduli personalizzati in Automazione di Azure, si può ricorrere ad alcuni scenari già creati da Microsoft e dalla community. È possibile ottenere manuali operativi e [moduli](#modules-in-powershell-gallery) di PowerShell dal PowerShell Gallery e [manuali operativi Python](#use-python-runbooks) dall'organizzazione GitHub di automazione di Azure. È anche possibile contribuire alla community condividendo gli [scenari sviluppati personalmente](#add-a-powershell-runbook-to-the-gallery).

> [!NOTE]
> Il sito TechNet Script Center è in ritiro. Tutti i manuali operativi da script Center nella raccolta di Runbook sono stati spostati nell'organizzazione di [GitHub di automazione](https://github.com/azureautomation) per ulteriori informazioni, vedere [qui](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="runbooks-in-powershell-gallery"></a>Runbook in PowerShell Gallery

[PowerShell Gallery](https://www.powershellgallery.com/packages) offre un'ampia gamma di runbook di Microsoft e della community che è possibile importare in Automazione di Azure. Per usare un runbook, scaricarne uno dalla raccolta disponibile oppure importare runbook direttamente dalla raccolta o dall'account di automazione nel portale di Azure.

> [!NOTE]
> PowerShell Gallery non supporta i runbook grafici.

È possibile importare solo direttamente da PowerShell Gallery usando il portale di Azure. Non è possibile eseguire questa funzione tramite PowerShell.

> [!NOTE]
> È necessario convalidare il contenuto di qualsiasi runbook ottenuto da PowerShell Gallery e prestare particolare attenzione in caso di installazione ed esecuzione in un ambiente di produzione.

## <a name="modules-in-powershell-gallery"></a>Moduli in PowerShell Gallery

I moduli di PowerShell contengono cmdlet che è possibile usare nella manuali operativi. I moduli esistenti che è possibile installare in automazione di Azure sono disponibili nella [PowerShell Gallery](https://www.powershellgallery.com). È possibile avviare questa raccolta dal portale di Azure e installare i moduli direttamente in automazione di Azure oppure è possibile scaricarli e installarli manualmente.

## <a name="common-scenarios-available-in-powershell-gallery"></a>Scenari comuni disponibili in PowerShell Gallery

L'elenco seguente contiene alcuni runbook che supportano scenari comuni. Per un elenco completo dei runbook creati dal team di Automazione di Azure, vedere il [profilo AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/): importa la versione più recente di tutti i moduli di un account di automazione da PowerShell Gallery.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/): configura Diagnostica di Azure e Log Analytics per ricevere i log di Automazione di Azure contenenti lo stato e i flussi del processo.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/): copia un file remoto da una macchina virtuale di Windows Azure.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) : copia un file locale in una macchina virtuale di Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Importare un runbook PowerShell dalla raccolta di runbook con il portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
1. In **Automazione processi** selezionare **Raccolta di runbook**.
1. Selezionare **Origine: PowerShell Gallery**. Viene visualizzato un elenco di manuali operativi disponibili che è possibile esplorare.
1. È possibile utilizzare la casella di ricerca sopra l'elenco per restringere l'elenco oppure è possibile utilizzare i filtri per limitare la visualizzazione in base al server di pubblicazione, al tipo e all'ordinamento. Individuare l'elemento della raccolta desiderato e selezionarlo per visualizzarne i dettagli.

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-sm.png" alt-text="Esplorazione della raccolta di Runbook." lightbox="media/automation-runbook-gallery/browse-gallery-lg.png":::

1. Per importare un elemento, fare clic su **Importa** nel pannello dei dettagli.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-detail-sm.png" alt-text="Mostra i dettagli dell'elemento della raccolta Runbook." lightbox="media/automation-runbook-gallery/gallery-item-detail-lg.png":::

1. Facoltativamente, modificare il nome del runbook e quindi fare clic su **OK** per importare il runbook.
1. Il runbook viene visualizzato nella scheda **Runbook** per l'account di automazione.

## <a name="import-a--powershell-runbook-from-github-with-the-azure-portal"></a>Importare un Runbook di PowerShell da GitHub con il portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
1. In **Automazione processi** selezionare **Raccolta di runbook**.
1. Selezionare **source: github**.
1. È possibile utilizzare i filtri sopra l'elenco per limitare la visualizzazione in base al server di pubblicazione, al tipo e all'ordinamento. Individuare l'elemento della raccolta desiderato e selezionarlo per visualizzarne i dettagli.

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-github-sm.png" alt-text="Esplorazione della raccolta GitHub." lightbox="media/automation-runbook-gallery/browse-gallery-github-lg.png":::

1. Per importare un elemento, fare clic su **Importa** nel pannello dei dettagli.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-github-sm.png" alt-text="Visualizzazione dettagliata di un Runbook dalla raccolta di GitHub." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-github-lg.png":::

1. Facoltativamente, modificare il nome del runbook e quindi fare clic su **OK** per importare il runbook.
1. Il runbook viene visualizzato nella scheda **Runbook** per l'account di automazione.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Aggiungere un runbook di PowerShell alla raccolta

Microsoft consiglia di aggiungere a PowerShell Gallery i runbook ritenuti più utili per gli altri clienti. PowerShell Gallery accetta moduli e script PowerShell. È possibile aggiungere un runbook [caricandolo in PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Importare un modulo dalla raccolta di moduli con il portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
1. Selezionare **Moduli** in **Risorse condivise** per aprire l'elenco di moduli.
1. Fare clic su **Esplora raccolta** nella parte superiore della pagina.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="Visualizzazione della raccolta di moduli." lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. Nella pagina Esplora raccolta è possibile usare la casella di ricerca per trovare le corrispondenze in uno dei campi seguenti:

   * Nome del modulo
   * Tag
   * Autore
   * Nome della risorsa cmdlet/DSC

1. Cercare il modulo desiderato e selezionarlo per visualizzarne i dettagli.

   Eseguendo il drill-down di un modulo specifico si accede a ulteriori informazioni. Queste informazioni includono un collegamento che riporta alla PowerShell Gallery, le eventuali dipendenze necessarie e tutti i cmdlet o le risorse DSC contenute nel modulo.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-sm.png" alt-text="Visualizzazione dettagliata di un modulo della raccolta." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-lg.png":::

1. Per installare il modulo direttamente in Automazione di Azure, fare clic su **Importa**.
1. Nel riquadro Importa è possibile visualizzare il nome del modulo da importare. Se tutte le dipendenze sono installate, il pulsante **OK** è attivo. Se non sono presenti tutte le dipendenze richieste, è necessario importarle prima di importare il modulo.
1. Nel riquadro Importa, fare clic su **OK** per importare il modulo. Durante l'importazione del modulo nell'account, Automazione di Azure estrae i metadati sul modulo e i cmdlet. Questa azione può richiedere alcuni minuti, in quanto occorre estrarre ogni attività.
1. Si riceverà una notifica iniziale per indicare che è in corso la distribuzione del modulo e un'altra notifica al termine dell'operazione.
1. Dopo l'importazione del modulo si vedranno le attività disponibili. È possibile usare le risorse del modulo nelle risorse DSC e dei runbook.

> [!NOTE]
> I moduli che supportano solo i componenti di base di PowerShell non sono supportati in automazione di Azure e non sono in grado di essere importati nel portale di Azure o distribuiti direttamente da PowerShell Gallery.

## <a name="use-python-runbooks"></a>Usare runbook di Python

Manuali operativi Python sono disponibili nell' [organizzazione GitHub di automazione di Azure](https://github.com/azureautomation). Quando si contribuisce al repository GitHub, aggiungere il tag **(argomento GitHub): python3** quando si carica il contributo.

## <a name="request-a-runbook-or-module"></a>Richiedere un runbook o un modulo

È possibile inviare richieste a [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Se è necessario supporto per la scrittura di un runbook o per inviare domande relative a PowerShell, inserire una domanda sulla [pagina delle domande di Domande e risposte Microsoft](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare un runbook PowerShell, vedere [Esercitazione: Creare un runbook di PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Per usare i runbook, vedere [Gestire runbook in Automazione di Azure](manage-runbooks.md).
* Per informazioni dettagliate su PowerShell, vedere [Documentazione di PowerShell](/powershell/scripting/overview).
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](/powershell/module/az.automation).
