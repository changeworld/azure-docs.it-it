---
title: Configurare la raccolta dati per l'agente Monitoraggio di Azure (anteprima)
description: Viene descritto come creare una regola di raccolta dati per raccogliere dati dalle macchine virtuali usando Monitoraggio di Azure agente.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.openlocfilehash: 884f048ac099cfc6b799fe266172a0eecef3db6f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478370"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Configurare la raccolta dati per l'agente Monitoraggio di Azure (anteprima)

Le regole di raccolta dati (DCR) definiscono i dati in Monitoraggio di Azure e specificano dove devono essere inviati. Questo articolo descrive come creare una regola di raccolta dati per raccogliere dati dalle macchine virtuali usando l Monitoraggio di Azure agent.

Per una descrizione completa delle regole di raccolta dati, vedere Regole di raccolta dati [in Monitoraggio di Azure (anteprima)](data-collection-rule-overview.md).

> [!NOTE]
> Questo articolo descrive come configurare i dati per le macchine virtuali con l'agente Monitoraggio di Azure attualmente in anteprima. Vedere [Panoramica degli Monitoraggio di Azure](agents-overview.md) per una descrizione degli agenti disponibili a livello generale e su come usarli per raccogliere dati.

## <a name="data-collection-rule-associations"></a>Associazioni di regole di raccolta dati

Per applicare un DCR a una macchina virtuale, creare un'associazione per la macchina virtuale. Una macchina virtuale può avere un'associazione a più controller di dominio e un controller di dominio può avere più macchine virtuali associate. In questo modo è possibile definire un set di controller di dominio, ognuno corrispondente a un requisito specifico, e applicarli solo alle macchine virtuali in cui si applicano. 

Si consideri ad esempio un ambiente con un set di macchine virtuali che eseguono un'applicazione line-of-business e altre che eseguono SQL Server. Potrebbe essere presente una regola di raccolta dati predefinita che si applica a tutte le macchine virtuali e regole di raccolta dati separate che raccolgono dati in modo specifico per l'applicazione line-of-business e per SQL Server. Le associazioni per le macchine virtuali alle regole di raccolta dati sono simili al diagramma seguente.

![Il diagramma mostra le macchine virtuali che ospitano applicazioni line-of-business e SQL Server associate alle regole di raccolta dati denominate central-i t-default e lob-app per l'applicazione line-of-business e central-i t-default e s q l per SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Creare una regola e un'associazione in portale di Azure

È possibile usare il portale di Azure per creare una regola di raccolta dati e associare le macchine virtuali nella sottoscrizione a tale regola. L Monitoraggio di Azure'agente verrà installato automaticamente e verrà creata un'identità gestita per tutte le macchine virtuali in cui non è già installato.

> [!IMPORTANT]
> Esiste attualmente un problema noto per cui se la regola di raccolta dati crea un'identità gestita in una macchina virtuale che dispone già di un'identità gestita assegnata dall'utente, l'identità assegnata dall'utente viene disabilitata.

Nel menu **Monitoraggio di Azure** nella portale di Azure selezionare **Regole di raccolta** dati nella **sezione** Impostazioni. Fare **clic su** Aggiungi per aggiungere una nuova regola di raccolta dati e una nuova assegnazione.

[![Regole di raccolta dati](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

Fare **clic su** Aggiungi per creare una nuova regola e un set di associazioni. Specificare un **Nome regola e** specificare una sottoscrizione **e** un gruppo **di risorse.** Specifica la posizione in cui verrà creato il Riconoscimento automatico di dominio. Le macchine virtuali e le relative associazioni possono essere in qualsiasi sottoscrizione o gruppo di risorse nel tenant.

[![Nozioni di base sulla regola di raccolta dati](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

Nella scheda **Macchine virtuali** aggiungere le macchine virtuali a cui deve essere applicata la regola di raccolta dati. Dovrebbero essere elencati sia le macchine Azure Arc azure che i server abilitati per l'uso nell'ambiente. L Monitoraggio di Azure agent verrà installato nelle macchine virtuali in cui non è già installato.

[![Macchine virtuali delle regole di raccolta dati](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

Nella scheda **Raccogli e recapita** fare clic **su Aggiungi origine dati** per aggiungere un'origine dati e un set di destinazione. Selezionare un **Tipo di origine dati** e verranno visualizzati i dettagli corrispondenti da selezionare. Per i contatori delle prestazioni, è possibile scegliere da un set predefinito di oggetti e dalla relativa frequenza di campionamento. Per gli eventi, è possibile scegliere tra un set di log o strutture e il livello di gravità. 

[![Origine dati di base](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Per specificare altri log e contatori delle prestazioni dalle [origini](azure-monitor-agent-overview.md#data-sources-and-destinations) dati attualmente supportate o per filtrare gli eventi tramite query XPath, selezionare **Personalizzato.** È quindi possibile specificare un [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) per tutti i valori specifici da raccogliere. Per [esempi, vedere DCR](data-collection-rule-overview.md#sample-data-collection-rule) di esempio.

[![Origine dati personalizzata](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

Nella scheda **Destinazione** aggiungere una o più destinazioni per l'origine dati. Le origini dati Syslog e degli eventi di Windows possono essere inviate solo Monitoraggio di Azure log. I contatori delle prestazioni possono essere inviati Monitoraggio di Azure metriche e Monitoraggio di Azure log.

[![Destination](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Fare **clic su Aggiungi origine** dati e quindi su **Rivedi** e crea per esaminare i dettagli della regola di raccolta dati e l'associazione al set di macchine virtuali. Fare **clic su** Crea per crearlo.

> [!NOTE]
> Dopo aver creato la regola di raccolta dati e le associazioni, l'invio dei dati alle destinazioni potrebbe richiedere fino a 5 minuti.

## <a name="limit-data-collection-with-custom-xpath-queries"></a>Limitare la raccolta dati con query XPath personalizzate
Poiché vengono addebitati i dati raccolti in un'area di lavoro Log Analytics, è consigliabile raccogliere solo i dati necessari. Usando la configurazione di base nel portale di Azure, è possibile filtrare solo gli eventi da raccogliere. Per i log di applicazione e di sistema, si tratta di tutti i log con una gravità specifica. Per i log di sicurezza, si tratta di tutti i log di controllo con esito positivo o tutti i log degli errori di controllo.

Per specificare filtri aggiuntivi, è necessario usare la configurazione personalizzata e specificare un XPath che filtra gli eventi che non si verificano. Le voci XPath vengono scritte nel formato `LogName!XPathQuery` . Ad esempio, può essere necessario restituire solo gli eventi dal registro eventi dell'applicazione con ID evento 1035. XPathQuery per questi eventi è `*[System[EventID=1035]]` . Poiché si desidera recuperare gli eventi dal registro eventi dell'applicazione, L'XPath sarà `Application!*[System[EventID=1035]]`

Vedere [Limitazioni di XPath 1.0](/windows/win32/wes/consuming-events#xpath-10-limitations) per un elenco delle limitazioni nel registro eventi di XPath supportato da Windows.

> [!TIP]
> Usare il cmdlet di PowerShell `Get-WinEvent` con il parametro per verificare la `FilterXPath` validità di XPathQuery. Lo script seguente mostra un esempio.
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - Se vengono restituiti eventi, la query è valida.
> - Se viene visualizzato il messaggio Non sono stati trovati eventi che corrispondono ai criteri di selezione *specificati.*, la query potrebbe essere valida, ma non sono presenti eventi corrispondenti nel computer locale.
> - Se viene visualizzato il messaggio *La query specificata non è valida,* la sintassi della query non è valida. 

Nella tabella seguente vengono illustrati esempi per filtrare gli eventi utilizzando un XPath personalizzato.

| Descrizione |  XPath |
|:---|:---|
| Raccogliere solo gli eventi di sistema con ID evento = 4648 |  `System!*[System[EventID=4648]]`
| Raccogliere solo gli eventi di sistema con ID evento = 4648 e nome di processo consent.exe | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| Raccogliere tutti gli eventi critici, di errore, di avviso e di informazioni dal registro eventi di sistema, ad eccezione dell'ID evento = 6 (driver caricato) |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| Raccogliere tutti gli eventi di sicurezza con esito positivo e negativo ad eccezione dell'ID evento 4624 (Accesso riuscito) |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>Creare una regola e un'associazione con l'API REST

Seguire questa procedura per creare una regola di raccolta dati e le associazioni usando l'API REST.

1. Creare manualmente il file DCR usando il formato JSON illustrato in [Sample DCR](data-collection-rule-overview.md#sample-data-collection-rule).

2. Creare la regola usando [l'API REST](/rest/api/monitor/datacollectionrules/create#examples).

3. Creare un'associazione per ogni macchina virtuale alla regola di raccolta dati usando [l'API REST](/rest/api/monitor/datacollectionruleassociations/create#examples).


## <a name="create-association-using-resource-manager-template"></a>Creare un'associazione usando Resource Manager modello

È possibile creare un'associazione tra una macchina virtuale di Azure o Azure Arc server abilitato usando un modello Resource Manager distribuzione. Vedere [Resource Manager di modelli per le regole di raccolta dati in Monitoraggio di Azure](./resource-manager-data-collection-rules.md) per i modelli di esempio.



## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni [sull'agente Monitoraggio di Azure.](azure-monitor-agent-overview.md)
- Altre informazioni sulle [regole di raccolta dati](data-collection-rule-overview.md).
