---
title: Configurare la raccolta dati per l'agente di monitoraggio di Azure (anteprima)
description: Viene descritto come creare una regola di raccolta dati per raccogliere dati dalle macchine virtuali tramite l'agente di monitoraggio di Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.openlocfilehash: 8943986bf8e8c082889d3a0b18618ac54c75e6d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022977"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Configurare la raccolta dati per l'agente di monitoraggio di Azure (anteprima)

Le regole di raccolta dati (DCR) definiscono i dati in arrivo in monitoraggio di Azure e specificano la posizione in cui devono essere inviati. Questo articolo descrive come creare una regola di raccolta dati per raccogliere dati dalle macchine virtuali usando l'agente di monitoraggio di Azure.

Per una descrizione completa delle regole di raccolta dati, vedere [regole di raccolta dati in monitoraggio di Azure (anteprima)](data-collection-rule-overview.md).

> [!NOTE]
> Questo articolo descrive come configurare i dati per le macchine virtuali con l'agente di monitoraggio di Azure, attualmente disponibile in anteprima. Vedere [Panoramica degli agenti di monitoraggio di Azure](agents-overview.md) per una descrizione degli agenti disponibili a livello generale e come usarli per raccogliere i dati.

## <a name="data-collection-rule-associations"></a>Associazioni regola di raccolta dati

Per applicare un DCR a una macchina virtuale, è necessario creare un'associazione per la macchina virtuale. Una macchina virtuale può avere un'associazione a più DCR e a un DCR possono essere associate più macchine virtuali. In questo modo è possibile definire un set di DCR, ognuno dei quali corrisponde a un requisito specifico, e applicarli solo alle macchine virtuali in cui si applicano. 

Si consideri, ad esempio, un ambiente con un set di macchine virtuali che eseguono un'applicazione line-of-business e altri che eseguono SQL Server. Potrebbe essere presente una regola di raccolta dati predefinita che si applica a tutte le macchine virtuali e a regole di raccolta dati separate che raccolgono i dati in modo specifico per l'applicazione line-of-business e per SQL Server. Le associazioni per le macchine virtuali alle regole di raccolta dati saranno simili a quelle del diagramma seguente.

![Il diagramma mostra le macchine virtuali che ospitano le applicazioni line-of-business e SQL Server associate alle regole di raccolta dati denominate Central-i t-default e LOB-app per l'applicazione line-of-business e i t-i t-default centrali e s q l per SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Crea regola e associa in portale di Azure

È possibile utilizzare il portale di Azure per creare una regola di raccolta dati e associare le macchine virtuali nella sottoscrizione a tale regola. L'agente di monitoraggio di Azure verrà installato automaticamente e verrà creata un'identità gestita per le macchine virtuali in cui non è già installato.

> [!IMPORTANT]
> Attualmente esiste un problema noto in cui se la regola di raccolta dati crea un'identità gestita in una macchina virtuale che ha già un'identità gestita assegnata dall'utente, l'identità assegnata dall'utente è disabilitata.

Nel menu **monitoraggio di Azure** della portale di Azure selezionare regole di **raccolta dati** nella sezione **Impostazioni** . Fare clic su **Aggiungi** per aggiungere una nuova regola di raccolta dati e assegnazione.

[![Regole di raccolta dati](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

Fare clic su **Aggiungi** per creare una nuova regola e un set di associazioni. Fornire un **nome di regola** e specificare una **sottoscrizione** e un **gruppo di risorse**. Specifica la posizione in cui verrà creato il DCR. Le macchine virtuali e le relative associazioni possono trovarsi in qualsiasi sottoscrizione o gruppo di risorse nel tenant.

[![Nozioni fondamentali sulle regole di raccolta dati](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

Nella scheda **macchine virtuali** aggiungere le macchine virtuali a cui deve essere applicata la regola di raccolta dati. È necessario elencare sia le macchine virtuali di Azure che i server abilitati per Azure Arc nell'ambiente in uso. L'agente di monitoraggio di Azure verrà installato nelle macchine virtuali in cui non è già installato.

[![Macchine virtuali della regola di raccolta dati](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

Nella scheda **Raccogli e Distribuisci** fare clic su **Aggiungi origine dati** per aggiungere un'origine dati e un set di destinazione. Selezionare un **tipo di origine dati**. verranno visualizzati i dettagli corrispondenti da selezionare. Per i contatori delle prestazioni, è possibile scegliere tra un set predefinito di oggetti e la relativa frequenza di campionamento. Per gli eventi, è possibile scegliere da un set di log o strutture e il livello di gravità. 

[![Base origine dati](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Per specificare altri log e contatori delle prestazioni dalle [origini dati attualmente supportate](azure-monitor-agent-overview.md#data-sources-and-destinations) o filtrare gli eventi tramite query XPath, selezionare **personalizzato**. È quindi possibile specificare un [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) per tutti i valori specifici da raccogliere. Per esempi, vedere l' [esempio DCR](data-collection-rule-overview.md#sample-data-collection-rule) .

[![Personalizzata origine dati](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

Nella scheda **destinazione** aggiungere una o più destinazioni per l'origine dati. Le origini dati degli eventi di Windows e syslog possono essere inviate solo ai log di monitoraggio di Azure. I contatori delle prestazioni possono inviare le metriche di monitoraggio di Azure e i log di monitoraggio di Azure.

[![Destination](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Fare clic su **Aggiungi origine dati** , quindi su **+ Crea** per esaminare i dettagli della regola di raccolta dati e l'associazione con il set di macchine virtuali. Fare clic su **Crea** per crearlo.

> [!NOTE]
> Dopo la creazione della regola di raccolta dati e delle associazioni, potrebbero essere necessari fino a 5 minuti per l'invio dei dati alle destinazioni.

## <a name="limit-data-collection-with-custom-xpath-queries"></a>Limitare la raccolta dei dati con query XPath personalizzate
Poiché vengono addebitati tutti i dati raccolti in un'area di lavoro di Log Analytics, è consigliabile raccogliere solo i dati necessari. Utilizzando la configurazione di base nel portale di Azure, è possibile filtrare solo gli eventi da raccogliere. Per i registri applicazioni e di sistema, si tratta di tutti i log con una particolare gravità. Per i log di sicurezza, si tratta di tutti i registri di esito positivo o di controllo.

Per specificare filtri aggiuntivi, è necessario usare la configurazione personalizzata e specificare un XPath che filtra gli eventi che non è possibile eseguire. Le voci XPath vengono scritte nel formato `LogName!XPathQuery` . Ad esempio, è possibile che si desideri restituire solo gli eventi dal registro eventi dell'applicazione con ID evento 1035. XPathQuery per questi eventi sarà `*[System[EventID=1035]]` . Poiché si desidera recuperare gli eventi dal registro eventi dell'applicazione, XPath è `Application!*[System[EventID=1035]]`

Vedere [limitazioni di xpath 1,0](/windows/win32/wes/consuming-events#xpath-10-limitations) per un elenco di limitazioni nell'XPath supportato dal registro eventi di Windows.

> [!TIP]
> Usare il cmdlet di PowerShell `Get-WinEvent` con il `FilterXPath` parametro per verificare la validità di un XPathQuery. Lo script seguente mostra un esempio.
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - Se vengono restituiti gli eventi, la query è valida.
> - Se viene visualizzato il messaggio *non è stato trovato alcun evento corrispondente ai criteri di selezione specificati.* la query può essere valida, ma non sono presenti eventi corrispondenti nel computer locale.
> - Se viene visualizzato il messaggio *la query specificata non è valida* , la sintassi della query non è valida. 

Nella tabella seguente vengono illustrati esempi per filtrare gli eventi utilizzando un XPath personalizzato.

| Descrizione |  XPath |
|:---|:---|
| Raccogli solo eventi di sistema con ID evento = 4648 |  `System!*[System[EventID=4648]]`
| Raccogliere solo gli eventi di sistema con ID evento = 4648 e un nome di processo di consent.exe | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| Raccolta di tutti gli eventi critici, di errore, di avviso e informativi dal registro eventi di sistema, ad eccezione di ID evento = 6 (driver caricato) |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| Raccolta di tutti gli eventi di sicurezza con esito positivo e negativo ad eccezione dell'ID evento 4624 (accesso riuscito) |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>Creare una regola e un'associazione usando l'API REST

Attenersi alla procedura seguente per creare una regola di raccolta dati e le associazioni usando l'API REST.

1. Creare manualmente il file DCR usando il formato JSON illustrato nell' [esempio DCR](data-collection-rule-overview.md#sample-data-collection-rule).

2. Creare la regola usando l' [API REST](/rest/api/monitor/datacollectionrules/create#examples).

3. Creare un'associazione per ogni macchina virtuale alla regola di raccolta dati usando l' [API REST](/rest/api/monitor/datacollectionruleassociations/create#examples).


## <a name="create-association-using-resource-manager-template"></a>Crea associazione usando il modello di Gestione risorse

Non è possibile creare una regola di raccolta dati usando un modello di Gestione risorse, ma è possibile creare un'associazione tra una macchina virtuale di Azure o un server abilitato Azure ARC usando un modello di Gestione risorse. Per i modelli di esempio, vedere [Gestione risorse esempi di modelli per le regole di raccolta dati in monitoraggio di Azure](./resource-manager-data-collection-rules.md) .



## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull' [agente di monitoraggio di Azure](azure-monitor-agent-overview.md).
- Altre informazioni sulle [regole di raccolta dati](data-collection-rule-overview.md).
