---
title: Scambiare o cambiare le distribuzioni in Servizi cloud di Azure (supporto esteso)
description: Informazioni su come scambiare o passare da una distribuzione all'Servizi cloud di Azure (supporto esteso).
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f5e01075ffb460c7ddd70b40a6b19f7ea70dd776
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748827"
---
# <a name="swap-or-switch-deployments-in-azure-cloud-services-extended-support"></a>Scambiare o cambiare le distribuzioni in Servizi cloud di Azure (supporto esteso)

È possibile scambiare due distribuzioni di servizi cloud indipendenti in Servizi cloud di Azure (supporto esteso). A differenza di Servizi cloud di Azure (versione classica), il modello Azure Resource Manager in Servizi cloud di Azure (supporto esteso) non usa gli slot di distribuzione. In Servizi cloud di Azure (supporto esteso), quando si distribuisce una nuova versione di un servizio cloud, è possibile rendere il servizio cloud "scambiabile" con un servizio cloud esistente in Servizi cloud di Azure (supporto esteso).

Dopo aver scambiato le distribuzioni, è possibile eseguire lo stage e testare la nuova versione usando la nuova distribuzione del servizio cloud. In effetti, lo scambio alza di livello un nuovo servizio cloud di gestione di gestione delle fasi al rilascio in produzione.

> [!NOTE]
> Non è possibile passare da una distribuzione Servizi cloud di Azure (classica) a una distribuzione Servizi cloud di Azure (supporto esteso).

È necessario rendere un servizio cloud scambiabile con un altro servizio cloud quando si distribuisce il secondo di una coppia di servizi cloud.

È possibile scambiare le distribuzioni usando un modello di Azure Resource Manager (modello arm), il portale di Azure o l'API REST.

## <a name="arm-template"></a>Modello ARM

Se si usa un metodo di distribuzione del modello arm, per rendere i servizi cloud scambiabili, impostare la proprietà nell'oggetto sull'ID del `SwappableCloudService` `networkProfile` servizio cloud `cloudServices` associato:

```json
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
        }
```

## <a name="azure-portal"></a>Portale di Azure

Per scambiare una distribuzione nel portale di Azure:

1. Nel menu del portale selezionare **Servizi cloud (supporto esteso) o** **Dashboard.**
1. Selezionare il servizio cloud da aggiornare.
1. In **Panoramica** per il servizio cloud selezionare **Scambia:**

   :::image type="content" source="media/swap-cloud-service-portal-swap.png" alt-text="Screenshot che mostra la scheda Swap per il servizio cloud.":::

1. Nel riquadro di conferma dello scambio verificare le informazioni sulla distribuzione e quindi selezionare **OK** per scambiare le distribuzioni:

   :::image type="content" source="media/swap-cloud-service-portal-confirm.png" alt-text="Screenshot che mostra la conferma delle informazioni di scambio della distribuzione.":::

Le distribuzioni vengono scambiate rapidamente perché l'unica cosa che cambia è l'indirizzo IP virtuale per il servizio cloud distribuito.

Per risparmiare sui costi di calcolo, è possibile eliminare uno dei servizi cloud (designati come ambiente di staging per la distribuzione dell'applicazione) dopo aver verificato che il servizio cloud scambiato funzioni come previsto.

## <a name="rest-api"></a>API REST

Per usare l'API REST per passare a una nuova distribuzione di servizi cloud in Servizi cloud di Azure (supporto esteso), usare il comando e la configurazione JSON seguenti:

```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```

```json
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
}
```

## <a name="common-questions-about-swapping-deployments"></a>Domande comuni sullo scambio di distribuzioni

Esaminare queste risposte alle domande comuni sugli scambi di distribuzione Servizi cloud di Azure (supporto esteso).

### <a name="what-are-the-prerequisites-for-swapping-to-a-new-cloud-services-deployment"></a>Quali sono i prerequisiti per lo scambio con una nuova distribuzione di servizi cloud?

È necessario soddisfare due prerequisiti chiave per un corretto scambio di distribuzione in Servizi cloud di Azure (supporto esteso):

* Se si vuole usare un indirizzo IP statico o riservato per uno dei servizi cloud scambiabili, anche l'altro servizio cloud deve usare un indirizzo IP riservato. In caso contrario, lo scambio ha esito negativo.
* Per l'esito positivo dello scambio, è necessario che tutte le istanze dei ruoli siano in esecuzione. Per controllare lo stato delle istanze, nel portale di Azure  passare a Panoramica per il servizio cloud appena distribuito o usare il comando `Get-AzRole` in Windows PowerShell.

Gli aggiornamenti del sistema operativo guest e le operazioni di correzione del servizio potrebbero causare un errore di scambio della distribuzione. Per altre informazioni, vedere [Risolvere i problemi relativi alle distribuzioni del servizio cloud.](../cloud-services/cloud-services-troubleshoot-deployment-problems.md)

### <a name="can-i-make-a-vip-swap-in-parallel-with-another-mutating-operation"></a>È possibile eseguire uno scambio VIP in parallelo con un'altra operazione di modifica?

No. Uno scambio VIP è una modifica solo di rete che deve essere completata prima che qualsiasi altra operazione di calcolo venga avviata in un servizio cloud. L'avvio di un'operazione di aggiornamento, eliminazione o scalabilità automatica per un servizio cloud mentre è in corso uno scambio VIP o l'attivazione di uno scambio VIP mentre è in corso un'altra operazione di calcolo potrebbe mettere il servizio cloud in uno stato di errore irreversibile.

### <a name="does-a-swap-incur-downtime-for-my-application-and-how-should-i-handle-it"></a>Uno scambio comporta tempi di inattività per l'applicazione e come è necessario gestirlo?

Uno scambio di servizi cloud è in genere veloce perché si tratta solo di una modifica della configurazione nel servizio di bilanciamento del carico di Azure. In alcuni casi, lo scambio potrebbe richiedere 10 o più secondi e causare errori di connessione temporanei. Per limitare l'effetto dello scambio sugli utenti, è consigliabile implementare la logica di ripetizione dei tentativi del client.

## <a name="next-steps"></a>Passaggi successivi 

* Esaminare [i prerequisiti di distribuzione](deploy-prerequisite.md) per Servizi cloud di Azure (supporto esteso).
* Esaminare [le domande frequenti per](faq.md) Servizi cloud di Azure (supporto esteso).
* Distribuire un Servizi cloud di Azure cloud (supporto esteso) usando una delle opzioni seguenti:
  * [Portale di Azure](deploy-portal.md)
  * [PowerShell](deploy-powershell.md)
  * [Modello ARM](deploy-template.md)
  * [Visual Studio](deploy-visual-studio.md)
