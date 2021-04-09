---
title: Configurazione di intelligence per le minacce di Azure firewall
description: Informazioni su come configurare il filtro basato su Intelligence per le minacce per i criteri del firewall di Azure per segnalare e negare il traffico da e verso domini e indirizzi IP dannosi noti.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 7ede1c917bb44dd31aa59855a0b7c83eb478700a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651723"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Configurazione di intelligence per le minacce di Azure firewall

È possibile configurare il filtro basato sull'Intelligence per le minacce per i criteri del firewall di Azure in modo da avvisare e negare il traffico da e verso domini e indirizzi IP dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft. La soluzione Microsoft Threat Intelligence, basata su [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence), viene usata da più servizi, tra cui Centro sicurezza di Azure.<br>

Se è stato configurato il filtro basato su Intelligence per le minacce, le regole associate vengono elaborate prima delle regole NAT, delle regole di rete o delle regole dell'applicazione.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Criteri di intelligence per le minacce":::

## <a name="threat-intelligence-mode"></a>Modalità di intelligence per le minacce

È possibile configurare l'Intelligence per le minacce in una delle tre modalità descritte nella tabella seguente. Per impostazione predefinita, il filtro basato sull'intelligence sulle minacce è abilitato in modalità di avviso.

|Mode |Descrizione  |
|---------|---------|
|`Off`     | La funzionalità di intelligence per le minacce non è abilitata per il firewall. |
|`Alert only`     | Si riceveranno avvisi con attendibilità elevata per il traffico che passa attraverso il firewall a o da indirizzi IP dannosi noti e domini. |
|`Alert and deny`     | Il traffico è bloccato e si riceveranno avvisi con attendibilità elevata quando viene rilevato traffico che tenta di passare attraverso il firewall a o da domini e indirizzi IP dannosi noti. |

> [!NOTE]
> La modalità di intelligence per le minacce viene ereditata dai criteri padre ai criteri figlio. Un criterio figlio deve essere configurato con la stessa modalità o con una modalità più restrittiva rispetto al criterio padre.

## <a name="allowlist-addresses"></a>Indirizzi consentiti

Intelligence per le minacce potrebbe attivare falsi positivi e bloccare il traffico effettivamente valido. È possibile configurare un elenco di indirizzi IP consentiti in modo che Intelligence per le minacce non filtri alcun indirizzo, intervallo o subnet specificato.  

![Indirizzi consentiti](media/threat-intelligence-settings/allow-list.png)

È possibile aggiornare l'oggetto Consenti con più voci contemporaneamente caricando un file CSV. Il file CSV può contenere solo indirizzi IP e intervalli. Il file non può contenere intestazioni.

> [!NOTE]
> Gli indirizzi degli elementi consentiti di intelligence per le minacce vengono ereditati dai criteri padre ai criteri figlio. Qualsiasi indirizzo IP o intervallo aggiunto a un criterio padre si applica anche a tutti i criteri figlio.

## <a name="logs"></a>Log

L'Estratto di log seguente mostra una regola attivata per il traffico in uscita verso un sito dannoso:

```json
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Test

- **Test in uscita**: gli avvisi del traffico in uscita dovrebbero essere un evento raro, poiché indicano che l'ambiente è stato compromesso. Per testare il funzionamento degli avvisi in uscita, è stato creato un nome di dominio completo di test che attiva un avviso. Usare **testmaliciousdomain.eastus.cloudapp.azure.com** per i test in uscita.

- **Test in ingresso**: gli avvisi sul traffico in ingresso vengono in genere visualizzati se le regole DNAT sono configurate sul firewall. Questo vale anche se sono consentite solo origini specifiche nella regola DNAT e il traffico viene negato negli altri casi. Il firewall di Azure non invia avvisi su tutti gli scanner di porta noti, ma solo su quelli che sono anche notoriamente coinvolti in attività dannose.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare il [Microsoft Security Intelligence Report](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)
