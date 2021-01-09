---
title: Tipi di runbook di Automazione di Azure
description: Questo articolo descrive i tipi di runbook che è possibile usare in Automazione di Azure e fornisce considerazioni per determinare il tipo da usare.
services: automation
ms.subservice: process-automation
ms.date: 01/08/2021
ms.topic: conceptual
ms.openlocfilehash: c1398d25b6d2540abea0012acd69555e5e53e25c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050970"
---
# <a name="azure-automation-runbook-types"></a>Tipi di runbook di Automazione di Azure

La funzionalità Automazione processi di Automazione di Azure supporta diversi tipi di runbook, come definito nella tabella seguente. Per altre informazioni sull'ambiente di automazione dei processi, consultare [Esecuzione di runbook in Automazione di Azure](automation-runbook-execution.md).

| Type | Descrizione |
|:--- |:--- |
| [Grafico](#graphical-runbooks)|Runbook grafico basato su Windows PowerShell e creato e modificato completamente nell'editor grafico nel portale di Azure. |
| [Grafico del flusso di lavoro di PowerShell](#graphical-runbooks)|Runbook grafico basato su flusso di lavoro Windows PowerShell e creato e modificato completamente nell'editor grafico nel portale di Azure. |
| [PowerShell](#powershell-runbooks) |Runbook testuale basato sullo scripting di Windows PowerShell. |
| [Flusso di lavoro PowerShell](#powershell-workflow-runbooks)|Runbook testuale basato sullo scripting del flusso di lavoro di Windows PowerShell. |
| [Python](#python-runbooks) |Runbook testuale basato sullo scripting di Python. |

Tenere conto delle considerazioni seguenti per determinare quale tipo usare per un runbook specifico.

* Non è possibile convertire i runbook dal tipo grafico al tipo testuale e viceversa.
* Esistono limitazioni di uso dei runbook di tipi diversi come runbook figlio. Per altre informazioni, vedere [Runbook figlio in Automazione di Azure](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Runbook grafici

È possibile creare e modificare runbook grafici e runbook del flusso di lavoro PowerShell grafico usando l'editor grafico nel portale di Azure. Tuttavia, non è possibile creare o modificare questo tipo di runbook con un altro strumento. Funzionalità principali dei runbook grafici:

* Esportati nei file nell'account di automazione e quindi importati in un altro account di automazione.
* Generano codice di PowerShell.
* Convertito in o da manuali operativi del flusso di lavoro PowerShell grafico durante l'importazione.

### <a name="advantages"></a>Vantaggi

* Uso di un modello di creazione con inserimento, collegamento e configurazione visivi.
* Possibilità di analizzare il flusso dei dati attraverso il processo.
* Rappresentazione grafica dei processi di gestione.
* Inclusione di altri runbook come runbook figlio per creare flussi di lavoro di livello elevato.
* Agevolazione della programmazione modulare.

### <a name="limitations"></a>Limitazioni

* Non è possibile creare o modificare all'esterno del portale di Azure.
* Potrebbe richiedere un'attività di codice contenente il codice di PowerShell per eseguire una logica complessa.
* Non è possibile eseguire la conversione in uno dei [formati di testo](automation-runbook-types.md), né convertire un runbook di testo in formato grafico. 
* Non è possibile visualizzare o modificare direttamente il codice di PowerShell creato dal flusso di lavoro grafico. È possibile visualizzare il codice creato in qualsiasi attività di codice.
* Non è possibile eseguire runbook in un ruolo di lavoro ibrido per runbook Linux. Consultare [Automatizzare le risorse nel centro dati o nel cloud usando i ruoli di lavoro ibridi per runbook](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>Runbook di PowerShell

I runbook di PowerShell sono basati su Windows PowerShell. È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure.  È anche possibile usare un editor di testo offline e [importare il runbook](manage-runbooks.md) in Automazione di Azure.

### <a name="advantages"></a>Vantaggi

* Implementare tutta la logica complessa con codice PowerShell senza le altre complessità del flusso di lavoro PowerShell.
* Avvio più rapido rispetto ai runbook del flusso di lavoro PowerShell poiché non è necessaria la compilazione prima dell'esecuzione.
* Esecuzione in Azure e nei ruoli di lavoro ibridi per runbook sia per Windows che per Linux.

### <a name="limitations"></a>Limitazioni

* Necessità di familiarità con lo scripting di PowerShell.
* I runbook non possono usare l'[elaborazione parallela](automation-powershell-workflow.md#use-parallel-processing) per eseguire più azioni in parallelo.
* I runbook non possono usare i [checkpoint](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) per riprendere il runbook in caso di errore.
* Possibilità di includere solo i runbook del flusso di lavoro PowerShell e grafici come runbook figlio solo mediante il cmdlet [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) che crea un nuovo processo.

### <a name="known-issues"></a>Problemi noti

Di seguito sono descritti i problemi noti correnti relativi ai runbook di PowerShell:

* I runbook di PowerShell non sono in grado di recuperare un [asset di tipo variabile](./shared-resources/variables.md) non crittografato con valore Null.
* I runbook di PowerShell non sono in grado di recuperare un asset di tipo variabile con `*~*` nel nome.
* Un'operazione [Get-Process](/powershell/module/microsoft.powershell.management/get-process) in un ciclo in un runbook di PowerShell può arrestarsi in modo anomalo dopo circa 80 iterazioni.
* Un runbook di PowerShell può avere esito negativo se tenta di scrivere una quantità elevata di dati nel flusso di output in una sola volta. È possibile risolvere questo problema in genere facendo in modo che il runbook restituisca solo le informazioni necessarie quando si usano oggetti di grandi dimensioni. Ad esempio, invece di usare `Get-Process` senza limitazioni, è possibile fare in modo che il cmdlet restituisca solo i parametri obbligatori come in `Get-Process | Select ProcessName, CPU`.

## <a name="powershell-workflow-runbooks"></a>Runbook del flusso di lavoro PowerShell

I runbook del flusso di lavoro PowerShell sono runbook di testo basati sul [flusso di lavoro Windows PowerShell](automation-powershell-workflow.md). È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure. È anche possibile usare un editor di testo offline e [importare il runbook](manage-runbooks.md) in Automazione di Azure.

### <a name="advantages"></a>Vantaggi

* Implementazione di tutta la logica complessa con codice del flusso di lavoro PowerShell.
* Uso dei [checkpoint](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) per riprendere l'operazione in caso di errore.
* Uso dell'[elaborazione parallela](automation-powershell-workflow.md#use-parallel-processing) per eseguire più azioni in parallelo.
* Può includere altri runbook grafici e runbook di flusso di lavoro di PowerShell come runbook figli per creare flussi di lavoro di livello elevato.

### <a name="limitations"></a>Limitazioni

* Necessità che l'autore abbia familiarità con il flusso di lavoro PowerShell.
* Gestione nei runbook della complessità aggiuntiva del flusso di lavoro PowerShell, ad esempio gli [oggetti deserializzati](automation-powershell-workflow.md#deserialized-objects).
* Tempi di avvio maggiori rispetto ai runbook di PowerShell perché è necessaria la compilazione prima dell'esecuzione.
* È possibile includere solo runbook di PowerShell come runbook figlio usando il cmdlet `Start-AzAutomationRunbook`.
* I runbook non possono essere eseguiti in un ruolo di lavoro ibrido per runbook Linux.

## <a name="python-runbooks"></a>Runbook Python

Python manuali operativi compila in Python 2 e Python 3. Python 3 manuali operativi è attualmente in fase di anteprima. È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure. È anche possibile usare un editor di testo offline e [importare il runbook](manage-runbooks.md) in Automazione di Azure.

### <a name="advantages"></a>Vantaggi

* Usare le affidabili librerie di Python.
* Può essere eseguito in Azure o in ruoli di lavoro ibridi per Runbook.
* Per Python 2, i ruoli di lavoro ibridi per Runbook di Windows sono supportati con [python 2,7](https://www.python.org/downloads/release/latest/python2) installato.
* Per i processi cloud Python 3 è supportata la versione Python 3,8. Gli script e i pacchetti di qualsiasi versione 3. x potrebbero funzionare se il codice è compatibile con versioni diverse.  
* Per i processi ibridi Python 3 nei computer Windows, è possibile scegliere di installare qualsiasi versione 3. x che si vuole usare.  
* Per i processi ibridi Python 3 nei computer Linux, si dipende dalla versione di Python 3 installata nel computer per eseguire programma omsconfig DSC e il ruolo di lavoro ibrido per Linux. Si consiglia di installare 3,6 nei computer Linux. Tuttavia, le diverse versioni dovrebbero funzionare anche se non sono presenti modifiche di rilievo nelle firme dei metodi o nei contratti tra le versioni di Python 3.

### <a name="limitations"></a>Limitazioni

* Conoscenza delle nozioni di scripting di Python.
* Per poter usare librerie di terze parti, è necessario [importare il pacchetto](python-packages.md) nell'account di Automazione.
* Usare il cmdlet **Start-AutomationRunbook**   nel flusso di lavoro PowerShell/PowerShell per avviare un Runbook di Python 3 (anteprima) non funziona. Per ovviare a questa limitazione, è possibile usare il cmdlet **Start-AzAutomationRunbook** del modulo AZ. Automation o il cmdlet **Start-AzureRmAutomationRunbook** del modulo AzureRm. Automation.  
* Python 3 manuali operativi (anteprima) e i pacchetti non funzionano con PowerShell.
* Automazione di Azure non supporta **sys. stderr**.

### <a name="known-issues"></a>Problemi noti

I processi di Python 3 talvolta hanno esito negativo e il *percorso eseguibile dell'interprete non è valido*. Questa eccezione può essere visualizzata se un processo viene ritardato, iniziando più di 10 minuti o usando **Start-AutomationRunbook** per avviare Python 3 manuali operativi. Se il processo viene ritardato, il riavvio del Runbook dovrebbe essere sufficiente.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui runbook PowerShell, vedere [Esercitazione: Creare un runbook di PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Per altre informazioni sui runbook del flusso di lavoro PowerShell, vedere [Esercitazione: Creare un runbook del flusso di lavoro PowerShell](learn/automation-tutorial-runbook-textual.md).
* Per altre informazioni sui runbook grafici, vedere [Esercitazione: Creare un runbook grafico](learn/automation-tutorial-runbook-graphical.md).
* Per altre informazioni sui runbook Python, vedere [Esercitazione: Creare un runbook di Python](learn/automation-tutorial-runbook-textual-python2.md).
