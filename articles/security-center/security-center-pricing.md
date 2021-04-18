---
title: Centro sicurezza di Azure gratuito e Azure Defender abilitata
description: Informazioni sui vantaggi dell'abilitazione Azure Defender protezione dei carichi di lavoro cloud in Centro sicurezza di Azure
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/23/2021
ms.openlocfilehash: aa65989953f761ff915383fcb59da7f36ea98dab
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600483"
---
# <a name="azure-security-center-free-vs-azure-defender-enabled"></a>Centro sicurezza di Azure gratuito e Azure Defender abilitata
Azure Defender è gratuito per i primi 30 giorni. Al termine dei 30 giorni, se si sceglie di continuare a usare il servizio, l'utilizzo inizierà a essere addebitato automaticamente.

È possibile eseguire **l'aggiornamento dalla pagina impostazioni &** prezzi, come descritto in Avvio [rapido: Abilitare Azure Defender](enable-azure-defender.md). Per informazioni dettagliate sui prezzi nella valuta scelta e in base alla propria area, vedere [Prezzi del Centro sicurezza.](https://azure.microsoft.com/pricing/details/security-center/)

## <a name="what-are-the-benefits-of-enabling-azure-defender"></a>Quali sono i vantaggi dell'abilitazione Azure Defender?

Il Centro sicurezza è disponibile in due modalità:

- **Azure Defender - Off** (modalità gratuita): il Centro sicurezza senza Azure Defender viene abilitato gratuitamente in tutte le sottoscrizioni di Azure quando si visita per la prima volta il dashboard Centro sicurezza di Azure nel portale di Azure; in alternativa, viene abilitato a livello di codice tramite API. L'uso di questa modalità gratuita prevede criteri di sicurezza, valutazione continua della sicurezza e raccomandazioni pratiche sulla sicurezza per contribuire a proteggere le risorse di Azure.

- **Azure Defender - On**: l'abilitazione di Azure Defender estende le funzionalità della modalità gratuita ai carichi di lavoro in esecuzione nel cloud privato e in altri cloud pubblici, offrendo la gestione unificata della sicurezza e la protezione dalle minacce per tutti i carichi di lavoro del cloud ibrido. Alcune delle principali funzionalità di Azure Defender sono:

    - **Microsoft Defender per endpoint**: Azure Defender per server include [Microsoft Defender per endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) per funzionalità complete di rilevamento di endpoint e risposta. Per altre informazioni sui vantaggi associati all'uso di Microsoft Defender per endpoint insieme ad Azure Defender, vedere [Usare la soluzione di rilevamento di endpoint e risposta integrata nel Centro sicurezza](security-center-wdatp.md).
    - **Analisi delle vulnerabilità per macchine virtuali e registri contenitori**: uno strumento di analisi facile da distribuire in tutte le macchine virtuali rappresenta la soluzione più avanzata del settore per la gestione delle vulnerabilità. È possibile visualizzare, analizzare e correggere i problemi rilevati direttamente all'interno del Centro sicurezza. 
    - **Sicurezza ibrida**: per ottenere una visualizzazione unificata della sicurezza in tutti i carichi di lavoro locali e cloud; applicare i criteri di sicurezza e la valutazione continua della sicurezza dei carichi di lavoro cloud ibridi per garantire la conformità con gli standard di sicurezza; Raccogliere, cercare e analizzare i dati sulla sicurezza provenienti da più origini, tra cui firewall e altre soluzioni dei partner.
    - **Avvisi di protezione dalle minacce**: l'analisi comportamentale avanzata e Microsoft Intelligent Security Graph offrono maggiori possibilità di contrastare gli attacchi informatici in continua evoluzione. L'analisi comportamentale predefinita e le tecniche di Machine Learning consentono di identificare gli attacchi e gli exploit zero-day. Monitora le reti, i computer e i servizi cloud per rilevare attacchi imminenti e attività post violazione. Semplifica l'investigazione con strumenti interattivi e intelligence per le minacce contestuale.
    - **Controlli di accesso e dell'applicazione**: applicando le raccomandazioni basate su Machine Learning e adattate a specifici carichi di lavoro, è possibile creare elenchi di elementi consentiti e rifiutati per bloccare il malware e altre applicazioni indesiderate. È inoltre possibile ridurre la superficie di attacco di rete con accesso JIT controllato alle porte di gestione nelle macchine virtuali di Azure, in modo da ridurre drasticamente l'esposizione ad attacchi di forza bruta e di altro tipo sulla rete.
    - **Funzionalità di sicurezza dei contenitori**: consentono di sfruttare la gestione delle vulnerabilità e la protezione dalle minacce in tempo reale negli ambienti in contenitori. L'abilitazione di tutte le funzionalità di **Azure Defender per i registri contenitori** può richiedere fino a 12 ore. Gli addebiti sono basati sul numero di immagini di contenitori univoche inserite nel registro contenitori. Dopo la prima analisi, non sono più previsti addebiti per l'immagine a meno che non venga modificata e inserita di nuovo.
    - **Ampia protezione dalle minacce per risorse connesse all'ambiente di Azure** - Azure Defender include un'ampia protezione dalle minacce nativa di Azure per i servizi di Azure comuni per tutte le risorse: Azure Resource Manager, DNS di Azure, livello di rete di Azure e Azure Key Vault. Azure Defender offre una visibilità esclusiva del livello di gestione di Azure e del livello di DNS di Azure e può quindi proteggere risorse cloud connesse a tali livelli.


## <a name="faq---pricing-and-billing"></a>Domande frequenti - Prezzi e fatturazione 

- [Come è possibile verificare chi nell'organizzazione ha abilitato le modifiche di Azure Defender nel Centro sicurezza di?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [Quali sono i piani offerti dal Centro sicurezza?](#what-are-the-plans-offered-by-security-center)
- [Come abilitare Azure Defender per la sottoscrizione?](#how-do-i-enable-azure-defender-for-my-subscription)
- [È possibile abilitare Azure Defender per i server in un subset di server nella sottoscrizione?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Se si ha già una licenza di Microsoft Defender per endpoint, è possibile ottenere uno sconto per Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Se nella sottoscrizione è abilitato Azure Defender per i server, sono previsti addebiti per i server non in esecuzione?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Si riceveranno addebiti per i computer senza l'agente di Log Analytics installato?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Se un agente di Log Analytics è associato a più aree di lavoro, l'addebito verrà applicato due volte?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Se l'agente di Log Analytics è associato a più aree di lavoro, la funzionalità gratuita di inserimento di 500 MB di dati è disponibile per tutte?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [L'inserimento gratuito di 500 MB di dati viene calcolato per un'intera area di lavoro o rigorosamente per computer?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [Quali tipi di dati sono inclusi nella memoria giornaliera di 500 MB?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>Come è possibile verificare chi nell'organizzazione ha abilitato le modifiche di Azure Defender nel Centro sicurezza di?
Le sottoscrizioni di Azure possono avere più amministratori con le autorizzazioni per cambiare le impostazioni dei prezzi. Per individuare l'utente che ha apportato una modifica, usare il log attività di Azure.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Log attività di Azure che mostra un evento di modifica dei prezzi":::

Se le informazioni dell'utente non sono riportate nella colonna **Evento avviato da**, esplorare il codice JSON dell'evento per trovare i dettagli appropriati.

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Finestra di esplorazione del codice JSON dei log attività di Azure":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Quali sono i piani offerti dal Centro sicurezza? 
Il Centro sicurezza prevede due offerte: 

- Centro sicurezza di Azure gratuito 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Come abilitare Azure Defender per la sottoscrizione? 
Per abilitare Azure Defender per la sottoscrizione, è possibile usare uno dei metodi seguenti: 

| Metodo                                          | Istruzioni                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Pagine del Centro sicurezza di Azure nel portale di Azure | [Abilitare Azure Defender](enable-azure-defender.md)                                                                                                  |
| API REST                                        | [API di prezzi](/rest/api/securitycenter/pricings)                                                                                                  |
| Interfaccia della riga di comando di Azure                                       | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Criteri di Azure                                    | [Prezzi del bundle](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>È possibile abilitare Azure Defender per i server in un subset di server nella sottoscrizione?
No. Quando si abilita [Azure Defender per i server](defender-for-servers-introduction.md) in una sottoscrizione, verranno protetti dal servizio tutti i server al suo interno. 

In alternativa, è possibile abilitare Azure Defender per i server a livello di area di lavoro Log Analytics. In questo caso, verranno protetti e fatturati solo i server associati a tale area di lavoro. Tuttavia, diverse funzionalità risulteranno non disponibili, tra cui l'accesso JIT alle VM, i rilevamenti in rete, la conformità alle normative, la protezione adattiva della rete, il controllo applicazioni adattivo e altre ancora. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Se si ha già una licenza di Microsoft Defender per endpoint, è possibile ottenere uno sconto per Azure Defender?
Se si ha già una licenza per Microsoft Defender per endpoint, non sarà necessario pagare per tale parte della licenza di Azure Defender.

Per confermare lo sconto, contattare il team di supporto del Centro sicurezza e fornire le informazioni appropriate per ID area di lavoro, area e licenza per ogni licenza pertinente.

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Se nella sottoscrizione è abilitato Azure Defender per i server, sono previsti addebiti per i server non in esecuzione? 
No. Quando si abilita [Azure Defender](defender-for-servers-introduction.md) per i server in una sottoscrizione, non verranno addebitati costi per i computer in stato di alimentazione deallocato mentre sono in tale stato. I computer vengono fatturati in base al relativo stato di alimentazione, come illustrato nella tabella seguente:

| State        | Descrizione                                                                                                                                      | Utilizzo dell'istanza fatturato |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Avvio in corso     | Avvio della macchina virtuale in corso.                                                                                                                               | Non fatturato            |
| In esecuzione      | Stato di funzionamento normale per una macchina virtuale                                                                                                                    | Fatturati                |
| Stopping     | Si tratta di uno stato transitorio. Al termine, verrà visualizzato lo stato Arrestato.                                                                           | Fatturati                |
| Arrestato      | La macchina virtuale è stato chiusa dall'interno del sistema operativo guest o tramite le API PowerOff. L'hardware è ancora allocata alla macchina virtuale e rimane nell'host. | Fatturati                |
| Deallocazione | Stato transitorio. Al termine, lo stato visualizzato per la macchina virtuale sarà Deallocato.                                                                             | Non fatturato            |
| Deallocato  | La macchina virtuale è stata arrestata correttamente e rimossa dall'host.                                                                                  | Non fatturato            |

:::image type="content" source="media/security-center-pricing/deallocated-virtual-machines.png" alt-text="Macchine virtuali di Azure con una macchina deallocata":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Si riceveranno addebiti per i computer senza l'agente di Log Analytics installato?
Sì. Se si abilita [Azure Defender per i server](defender-for-servers-introduction.md) in una sottoscrizione, i computer al suo interno ottengono una gamma di protezioni anche se l'agente di Log Analytics non è stato installato.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Se un agente di Log Analytics è associato a più aree di lavoro, l'addebito verrà applicato due volte? 
Sì. Se l'agente di Log Analytics è stato configurato per l'invio di dati a due o più aree di lavoro Log Analytics diverse (multihoming), si riceverà un addebito per ogni area di lavoro in cui è installata una soluzione 'Sicurezza' o 'Antimalware'. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Se l'agente di Log Analytics è associato a più aree di lavoro, la funzionalità gratuita di inserimento di 500 MB di dati è disponibile per tutte?
Sì. Se l'agente di Log Analytics è stato configurato per l'invio di dati a due o più aree di lavoro Log Analytics diverse (multihoming), si riceverà la funzionalità gratuita di inserimento di 500 MB di dati. Il calcolo viene eseguito per nodo, per area di lavoro associata, al giorno e la funzionalità è disponibile per ogni area di lavoro in cui è installata una soluzione 'Sicurezza' o 'Antimalware'. L'inserimento di più 500 MB di dati comporterà un addebito.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>L'inserimento gratuito di 500 MB di dati viene calcolato per un'intera area di lavoro o rigorosamente per computer?
Si ottiene l'inserimento gratuito di 500 MB di dati al giorno, per ogni computer connesso all'area di lavoro. In particolare, per i tipi di dati sulla sicurezza raccolti direttamente dal Centro sicurezza di Azure.

Questi dati vengono calcolati come media di frequenza giornaliera tra tutti i nodi. Quindi se alcuni computer inviano 100 MB di dati e altri ne inviano 800 MB, se il totale non supera il limite gratuito **[numero di computer] x 500 MB**, non verranno addebitati altri costi.

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>Quali tipi di dati sono inclusi nella memoria giornaliera di 500 MB?

La fatturazione del Centro sicurezza è strettamente collegata alla fatturazione per Log Analytics. Il Centro sicurezza offre un'allocazione di 500 MB/nodo al giorno per il subset seguente di tipi [di dati di sicurezza:](/azure/azure-monitor/reference/tables/tables-category#security)
- WindowsEvent
- SecurityAlert
- SecurityBaseline
- SecurityBaselineSummary
- SecurityDetection
- SecurityEvent
- WindowsFirewall
- MaliciousIPCommunication
- LinuxAuditLog
- SysmonEvent
- ProtectionStatus
- Tipi di dati Update e UpdateSummary quando la soluzione Gestione aggiornamenti non è in esecuzione nell'area di lavoro o la destinazione della soluzione è abilitata

Se l'area di lavoro si trova nel piano tariffario Legacy Per Nodo, le allocazioni del Centro sicurezza e di Log Analytics vengono combinate e applicate congiuntamente a tutti i dati inseriti fatturabili.

## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha illustrato le opzioni per i prezzi del Centro sicurezza. Per informazioni correlate, vedere:

- [Come ottimizzare i costi dei carichi di lavoro di Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Dettagli relativi ai prezzi nella valuta preferita e in base all'area geografica](https://azure.microsoft.com/pricing/details/security-center/)
- Si consiglia di gestire i costi e limitare la quantità di dati raccolti per una soluzione limitandola a un determinato set di agenti. Il [targeting della soluzione](../azure-monitor/insights/solution-targeting.md) consente di applicare un ambito alla soluzione e avere come target un sottoinsieme di computer nell'area di lavoro. Se si usa il targeting della soluzione, il Centro sicurezza elenca l'area di lavoro come priva di una soluzione.
