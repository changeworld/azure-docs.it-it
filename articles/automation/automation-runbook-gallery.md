---
title: Usare runbook e moduli di Automazione di Azure in PowerShell Gallery
description: Questo articolo illustra come usare manuali operativi e i moduli da repository GitHub Microsoft e i PowerShell Gallery.
services: automation
ms.subservice: process-automation
ms.date: 04/07/2021
ms.topic: conceptual
ms.openlocfilehash: 2df019888d293cd8a25a34e6f0f4e7dd215c6a41
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030634"
---
# <a name="use-existing-runbooks-and-modules"></a>USA manuali operativi e moduli esistenti

Anziché creare runbook e moduli personalizzati in Automazione di Azure, si può ricorrere ad alcuni scenari già creati da Microsoft e dalla community. È possibile ottenere i manuali operativi PowerShell e Python correlati ad Azure dalla raccolta Runbook nel portale di Azure e i [moduli](#modules-in-the-powershell-gallery) e [manuali operativi](#runbooks-in-the-powershell-gallery) (che potrebbero essere specifici di Azure) dalla PowerShell Gallery. È anche possibile contribuire alla community condividendo gli [scenari sviluppati personalmente](#contribute-to-the-community).

> [!NOTE]
> Il sito TechNet Script Center è in ritiro. Tutti i manuali operativi da script Center nella raccolta di Runbook sono stati spostati nell'organizzazione di [GitHub di automazione](https://github.com/azureautomation) per altre informazioni, vedere [manuali operativi di automazione di Azure per il passaggio a GitHub](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="import-runbooks-from-github-with-the-azure-portal"></a>Importare manuali operativi da GitHub con la portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
2. In **Automazione processi** selezionare **Raccolta di runbook**.
3. Selezionare **source: github**.
4. È possibile utilizzare i filtri sopra l'elenco per limitare la visualizzazione in base al server di pubblicazione, al tipo e all'ordinamento. Individuare l'elemento della raccolta desiderato e selezionarlo per visualizzarne i dettagli.

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-github.png" alt-text="Esplorazione di Runbook Gallery." lightbox="./media/automation-runbook-gallery/browse-gallery-github-expanded.png":::

5. Per importare un elemento, fare clic su **Importa** nella pagina dettagli.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import.png" alt-text="Importazione elementi della raccolta.":::

6. Facoltativamente, modificare il nome del Runbook nel pannello importa, quindi fare clic su **OK** per importare il Runbook.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import-blade.png" alt-text="Pannello Importa elementi della raccolta.":::

7. Il runbook viene visualizzato nella scheda **Runbook** per l'account di automazione.
 
## <a name="runbooks-in-the-powershell-gallery"></a>Manuali operativi nella PowerShell Gallery

> [!IMPORTANT]
> È necessario convalidare il contenuto di qualsiasi manuali operativi ottenuto dalla PowerShell Gallery. Usare estrema cautela per l'installazione e l'esecuzione in un ambiente di produzione.

Il [PowerShell Gallery](https://www.powershellgallery.com/packages) fornisce varie Manuali operativi da Microsoft e dalla community che è possibile importare in automazione di Azure. Per usare un runbook, scaricarne uno dalla raccolta disponibile oppure importare runbook direttamente dalla raccolta o dall'account di automazione nel portale di Azure.

> [!NOTE]
> PowerShell Gallery non supporta i runbook grafici.

È possibile importare solo direttamente da PowerShell Gallery usando il portale di Azure. Non è possibile eseguire questa funzione tramite PowerShell. La procedura è identica a quella illustrata in [importare manuali operativi da GitHub con il portale di Azure](#import-runbooks-from-github-with-the-azure-portal), ad eccezione del fatto che l' **origine** verrà **PowerShell Gallery**.

:::image type="content" source="./media/automation-runbook-gallery/source-runbook-gallery-small.png" alt-text="Visualizzazione della selezione dell'origine della raccolta Runbook." lightbox="./media/automation-runbook-gallery/source-runbook-gallery-large.png":::

## <a name="modules-in-the-powershell-gallery"></a>Moduli nell'PowerShell Gallery

I moduli di PowerShell contengono cmdlet che è possibile usare nella manuali operativi. I moduli esistenti che è possibile installare in automazione di Azure sono disponibili nella [PowerShell Gallery](https://www.powershellgallery.com). È possibile avviare questa raccolta dal portale di Azure e installare i moduli direttamente in automazione di Azure oppure è possibile scaricarli e installarli manualmente.

È anche possibile trovare i moduli da importare nel portale di Azure. Sono elencate per l'account di automazione nella **raccolta moduli** in **risorse condivise**.

## <a name="common-scenarios-available-in-the-powershell-gallery"></a>Scenari comuni disponibili nell'PowerShell Gallery

L'elenco seguente contiene alcuni runbook che supportano scenari comuni. Per un elenco completo dei runbook creati dal team di Automazione di Azure, vedere il [profilo AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/): importa la versione più recente di tutti i moduli di un account di automazione da PowerShell Gallery.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/): configura Diagnostica di Azure e Log Analytics per ricevere i log di Automazione di Azure contenenti lo stato e i flussi del processo.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/): copia un file remoto da una macchina virtuale di Windows Azure.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) : copia un file locale in una macchina virtuale di Azure.

## <a name="contribute-to-the-community"></a>Contribuisci alla community

Si consiglia vivamente di collaborare e contribuire alla crescita della community di automazione di Azure. Condividi le straordinarie manuali operativi che hai creato con la community. I tuoi contributi saranno apprezzati.

### <a name="add-a-runbook-to-the-github-runbook-gallery"></a>Aggiungere un Runbook alla raccolta Runbook di GitHub

È possibile aggiungere nuovi manuali operativi PowerShell o Python alla raccolta runbook con questo flusso di lavoro GitHub.

1. Creare un repository pubblico su GitHub e aggiungere il Runbook e tutti gli altri file necessari, ad esempio readme.md, Description e così via.
1. Aggiungere l'argomento `azureautomationrunbookgallery` per assicurarsi che il repository venga individuato dal servizio, in modo che possa essere visualizzato nella raccolta di Runbook di automazione.
1. Se il Runbook creato è un flusso di lavoro di PowerShell, aggiungere l'argomento `PowerShellWorkflow` . Se è un Runbook Python 3, aggiungere `Python3` . Non sono necessari altri argomenti specifici per Azure manuali operativi, ma si consiglia di aggiungere altri argomenti che possono essere usati per la categorizzazione e la ricerca nella raccolta di Runbook.

   >[!NOTE]
   >Vedere manuali operativi esistenti nella raccolta per elementi come la formattazione, le intestazioni e i tag esistenti che è possibile usare, ad esempio `Azure Automation` o `Linux Azure Virtual Machines` .

Per suggerire modifiche a un Runbook esistente, archiviare una richiesta pull. 

Se si decide di clonare e modificare un Runbook esistente, è consigliabile assegnargli un nome diverso. Se si usa di nuovo il nome precedente, questo verrà visualizzato due volte nell'elenco della raccolta Runbook.

>[!NOTE]
>Attendere almeno 12 ore per la sincronizzazione tra GitHub e la raccolta di Runbook di automazione, sia per i manuali operativi aggiornati che per quelli nuovi.

### <a name="add-a-powershell-runbook-to-the-powershell-gallery"></a>Aggiungere un Runbook di PowerShell a PowerShell Gallery

Microsoft consiglia di aggiungere a PowerShell Gallery i runbook ritenuti più utili per gli altri clienti. PowerShell Gallery accetta moduli e script PowerShell. È possibile aggiungere un runbook [caricandolo in PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-modules-gallery-in-the-azure-portal"></a>Importare un modulo dalla raccolta di moduli nella portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
1. In **risorse condivise** selezionare **raccolta moduli** per aprire l'elenco di moduli.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="Visualizzazione della raccolta di moduli." lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. Nella pagina "Esplora raccolta" è possibile eseguire la ricerca in base ai campi seguenti:

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

## <a name="request-a-runbook-or-module"></a>Richiedere un runbook o un modulo

È possibile inviare richieste a [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Se è necessario supporto per la scrittura di un runbook o per inviare domande relative a PowerShell, inserire una domanda sulla [pagina delle domande di Domande e risposte Microsoft](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare i runbook PowerShell, vedere [Esercitazione: Creare un runbook di PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Per usare i runbook, vedere [Gestire runbook in Automazione di Azure](manage-runbooks.md).
* Per altre informazioni sullo scripting di PowerShell, vedere la [documentazione di PowerShell](/powershell/scripting/overview).
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](/powershell/module/az.automation).
