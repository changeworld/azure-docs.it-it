---
title: Filtro basato sull'intelligence sulle minacce del firewall di Azure
description: I filtri basati sull'intelligence per le minacce possono essere abilitati per il firewall per la creazione di avvisi e il rifiuto del traffico da o verso indirizzi IP e domini dannosi noti.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/12/2020
ms.author: victorh
ms.openlocfilehash: 77e36d5ab98f1177b5a0b89ee10b3e6bd22560c2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791460"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Filtro basato sull'intelligence sulle minacce del firewall di Azure

I filtri basati sull'intelligence per le minacce possono essere abilitati per il firewall per la creazione di avvisi e il rifiuto del traffico da o verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft. La soluzione Microsoft Threat Intelligence, basata su [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence), viene usata da più servizi, tra cui Centro sicurezza di Azure.<br>
<br>

:::image type="content" source="media/threat-intel/firewall-threat.png" alt-text="Intelligence sulle minacce del firewall" border="false":::

Se è stato abilitato il filtro basato sull'intelligence sulle minacce, le regole associate vengono elaborate prima delle regole NAT, delle regole di rete o delle regole dell'applicazione.

È possibile scegliere di registrare un avviso solo quando viene attivata una regola oppure scegliere la modalità Avvisa e nega.

Per impostazione predefinita, il filtro basato sull'intelligence sulle minacce è abilitato in modalità di avviso. Non è possibile disattivare questa funzionalità o cambiare la modalità finché l'interfaccia del portale non diventa disponibile nella propria area.

:::image type="content" source="media/threat-intel/threat-intel-ui.png" alt-text="Interfaccia del portale per il filtro basato sull'intelligence sulle minacce":::

## <a name="logs"></a>Log

L'estratto di log seguente mostra una regola attivata:

```
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

- Vedere [Esempi di Log Analytics per Firewall di Azure](./firewall-workbook.md)
- Informazioni su come [distribuire e configurare Firewall di Azure](tutorial-firewall-deploy-portal.md)
- Esaminare il [Microsoft Security Intelligence Report](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)