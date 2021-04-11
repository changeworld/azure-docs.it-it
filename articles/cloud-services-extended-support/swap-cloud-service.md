---
title: Scambia/passa tra due servizi cloud di Azure (supporto esteso)
description: Scambia/passa tra due servizi cloud di Azure (supporto esteso)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 6f96656af9afd9874cc6273a9cea9ed43e8c69cc
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294310"
---
# <a name="swapswitch-between-two-azure-cloud-services-extended-support"></a>Scambia/passa tra due servizi cloud di Azure (supporto esteso)
Con servizi cloud (supporto esteso) è possibile scambiare tra due distribuzioni di servizi cloud indipendenti. A differenza dei servizi cloud (versione classica), il concetto di slot non esiste con il modello di Azure Resource Manager. Quando si decide di distribuire una nuova versione di un servizio cloud (supporto esteso), è possibile renderlo "scambiabile" con un altro servizio cloud esistente (supporto esteso) consentendo di organizzare e testare la nuova versione tramite questa distribuzione. Un servizio cloud può essere reso "scambiabile" con un altro servizio cloud solo al momento della distribuzione del secondo servizio cloud (della coppia). Quando si usa il metodo di distribuzione basato su modello ARM, questa operazione viene eseguita impostando la proprietà SwappableCloudService all'interno del profilo di rete dell'oggetto servizio cloud sull'ID del servizio cloud associato. 

```
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
```
> [!Note] 
> Non è possibile scambiare tra un servizio cloud (versione classica) e un servizio cloud (supporto esteso)

Usare **swap** per cambiare gli URL in base ai quali vengono risolti i due servizi cloud, innalzando di livello un nuovo servizio cloud (staging) alla versione di produzione.
È possibile scambiare le distribuzioni dalla pagina Cloud Services o dal dashboard.

1. Nel [portale di Azure](https://portal.azure.com), selezionare il servizio cloud che si desidera aggiornare. Questo passaggio consente di aprire il pannello dell'istanza del servizio cloud.
2. Nel pannello selezionare **Scambia** 
    :::image type="content" source="media/swap-cloud-service-1.png" alt-text="immagine per visualizzare l'opzione di scambio del servizio cloud":::
   
3. Viene visualizzata la seguente richiesta di conferma
   
   :::image type="content" source="media/swap-cloud-service-2.png" alt-text="Immagine che mostra lo scambio del servizio cloud":::
   
4. Dopo avere controllato le informazioni di distribuzione, scegliere OK per scambiare le distribuzioni.
Lo scambio si verifica rapidamente perché l'unico aspetto che cambia è gli indirizzi IP virtuali (VIP) per i due servizi cloud.

Per risparmiare sui costi di calcolo, è possibile eliminare uno dei servizi cloud (designato come ambiente di gestione temporanea per la distribuzione dell'applicazione) dopo aver verificato che il servizio cloud scambiato funzioni come previsto.

L'API REST per eseguire uno scambio tra due distribuzioni del supporto esteso dei servizi cloud è la seguente:
```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```
```
{
  "frontendIPConfigurations": [
    {
    "id": "#LBFE1#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP2#"
    }
      }
    },
   {
    "id": "#LBFE2#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP1#"
     }
       }
    }
  ]
 }
```
## <a name="common-questions-about-swapping-deployments"></a>Domande comuni sullo scambio di distribuzioni

### <a name="what-are-the-prerequisites-for-swapping-between-two-cloud-services"></a>Quali sono i prerequisiti per lo scambio tra due servizi cloud?
Esistono due prerequisiti principali per uno scambio di servizi cloud (supporto esteso) efficace:
* Se si vuole usare un indirizzo IP statico/riservato per uno dei servizi cloud scambiabili, anche l'altro servizio cloud deve usare un IP riservato. In caso contrario, lo scambio ha esito negativo.
* Tutte le istanze dei ruoli devono essere in esecuzione prima di poter eseguire lo scambio. È possibile controllare lo stato delle istanze nel pannello Panoramica del portale di Azure. In alternativa, è possibile usare il comando Get-AzRole in Windows PowerShell.

Gli aggiornamenti del sistema operativo guest e le operazioni di correzione del servizio possono anche causare errori di scambio della distribuzione. Per altre informazioni, vedere Risolvere eventuali problemi di distribuzione dei servizi cloud.

### <a name="can-i-perform-a-vip-swap-in-parallel-with-another-mutating-operation"></a>È possibile eseguire uno scambio di indirizzi VIP in parallelo con un'altra operazione di mutazione?
No. Lo scambio di indirizzi VIP è una modifica di rete che deve essere completata prima che venga eseguita qualsiasi altra operazione di calcolo nei servizi cloud. L'esecuzione di un'operazione di aggiornamento, eliminazione o ridimensionamento automatico nei servizi cloud mentre è in corso uno scambio di indirizzi VIP o l'attivazione di uno scambio VIP mentre è in corso un'altra operazione di calcolo può lasciare il servizio cloud in uno stato indesiderato dal quale il ripristino potrebbe non essere possibile. 

### <a name="does-a-swap-incur-downtime-for-my-application-how-should-i-handle-it"></a>Uno scambio comporta un tempo di inattività per l'applicazione? Come gestire questa situazione?
Come descritto nella sezione precedente, lo scambio di un servizio cloud è in genere rapido perché si tratta solo di una modifica della configurazione in Azure Load Balancer. In alcuni casi, può richiedere più di 10 secondi e causare errori di connessione temporanei. Per limitare l'impatto sui clienti, si consiglia di implementare la logica di ripetizione dei tentativi nel client.

## <a name="next-steps"></a>Passaggi successivi 
- Esaminare i [prerequisiti di distribuzione](deploy-prerequisite.md) per i servizi cloud (supporto esteso).
- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando il [portale di Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), il [modello](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
