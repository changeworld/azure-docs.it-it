---
title: Configurare il livello PremiumV3
description: Informazioni su come migliorare le prestazioni per l'app Web, per dispositivi mobili e per le API in Servizio app di Azure scalabilità al nuovo piano tariffario PremiumV3.
keywords: servizio app, servizio app di azure, scala, scalabile, piano di servizio app, costo del servizio app
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8fbd841626e2a074bc0a35cd1b4ac094e267b34a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833347"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>Configurare il livello PremiumV3 per Servizio app di Azure

Il nuovo **piano tariffario PremiumV3** offre processori più veloci, archiviazione SSD e quadruplo del rapporto memoria-core dei piani tariffari esistenti (il doppio del livello **PremiumV2).** I vantaggi relativi alle prestazioni che ne derivano consentono di eseguire le app con un minor numero di istanze e garantiscono quindi un risparmio economico. Questo articolo illustra come creare un'app nel livello **PremiumV3** o aumentare le dimensioni di un'app al **livello PremiumV3.**

## <a name="prerequisites"></a>Prerequisiti

Per aumentare le dimensioni di un'app a **PremiumV3,** è necessario avere un'app Servizio app di Azure in esecuzione in un piano tariffario inferiore a **PremiumV3** e l'app deve essere in esecuzione in una distribuzione del servizio app che supporta PremiumV3.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>Disponibilità PremiumV3

Il **livello PremiumV3** è disponibile sia per le app native che per quelle contenitore, inclusi i contenitori Windows e i contenitori Linux.

> [!NOTE]
> Tutti i contenitori Windows in esecuzione nel livello **Contenitore Premium** durante il periodo di anteprima continuano a funzionare così come sono, ma il livello Contenitore **Premium** continuerà a rimanere in anteprima. Il **livello PremiumV3** è la sostituzione ufficiale per il **livello Contenitore Premium.** 

**PremiumV3 è** disponibile in alcune aree di Azure e la disponibilità in altre aree viene aggiunta continuamente. Per vedere se è disponibile nella propria area, eseguire in [Azure Cloud Shell](../cloud-shell/overview.md) il comando dell'interfaccia della riga di comando di Azure seguente:

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>Creare un'app nel livello PremiumV3

Il piano tariffario di un'app del servizio app è definito nel [piano di servizio app](overview-hosting-plans.md) su cui è in esecuzione. È possibile creare un piano di Servizio app in modo indipendente o nell'ambito della creazione di un'app.

Durante la configurazione del piano di servizio app nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> selezionare **Piano tariffario**. 

Selezionare **Produzione,** quindi **selezionare P1V3,** **P2V3** o **P3V3,** quindi fare clic su **Applica.**

![Screenshot che mostra i piani tariffari consigliati per l'app.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Se **P1V3,** **P2V3** e **P3V3** non sono visualizzate come opzioni o se le opzioni sono disattivate, è probabile che **PremiumV3** non sia disponibile nella distribuzione del servizio app sottostante che contiene il piano di servizio app. Per altri dettagli, vedere [Passare a un piano superiore da una combinazione di gruppo di risorse e area non supportata](#unsupported).

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>Aumentare le dimensioni di un'app esistente al livello PremiumV3

Prima di ridimensionare un'app **esistente al livello PremiumV3,** assicurarsi che **PremiumV3** sia disponibile. Per informazioni, vedere [Disponibilità PremiumV3.](#availability) Se non è disponibile, vedere [Passare a un piano superiore da una combinazione di gruppo di risorse e area non supportata](#unsupported).

A seconda dell'ambiente di hosting, il passaggio al livello successivo può richiedere operazioni aggiuntive. 

Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> aprire la pagina dell'app del servizio di app.

Nel riquadro di spostamento sinistro della pagina dell'app del servizio app selezionare **Aumenta prestazioni (piano di servizio app)**.

![Screenshot che mostra come aumentare le dimensioni del piano di servizio app.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selezionare **Produzione,** quindi **selezionare P1V3,** **P2V3** o **P3V3,** quindi fare clic su **Applica.**

![Screenshot che mostra i piani tariffari consigliati per l'app.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Se l'operazione viene completata correttamente, la pagina di panoramica dell'app mostra che è ora in un **livello PremiumV3.**

![Screenshot che mostra il piano tariffario PremiumV3 nella pagina di panoramica dell'app.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Se si riceve un errore

Alcuni piani di servizio app non possono aumentare il livello PremiumV3 se la distribuzione del servizio app sottostante non supporta PremiumV3. Per altri dettagli, vedere [Passare a un piano superiore da una combinazione di gruppo di risorse e area non supportata](#unsupported).

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Passare a un piano superiore da una combinazione di gruppo di risorse e area non supportata

Se l'app viene eseguita in una distribuzione del servizio app in cui **PremiumV3** non è disponibile o se l'app viene eseguita in un'area che attualmente non supporta **PremiumV3,** è necessario ridistribuire l'app per sfruttare **PremiumV3.**  Sono disponibili due opzioni:

- Creare un'app in un nuovo gruppo di risorse e con un nuovo piano di servizio app. Quando si crea il piano di servizio app, selezionare un **livello PremiumV3.** Questo passaggio garantisce che il piano di servizio app sia distribuito in un'unità di distribuzione che supporta **PremiumV3.** Ridistribuire quindi il codice dell'applicazione nell'app appena creata. Anche se si riduce il piano di servizio app a un livello inferiore per ridurre i costi, è sempre possibile eseguire il ridimensionamento fino a **PremiumV3** perché l'unità di distribuzione lo supporta.
- Se l'app è già in esecuzione in un livello **Premium** esistente, è possibile clonare l'app con tutte le impostazioni dell'app, le stringhe di connessione e la configurazione della distribuzione in un nuovo gruppo di risorse in un nuovo piano di servizio app che usa **PremiumV3.**

    ![Screenshot che mostra come clonare l'app.](media/app-service-configure-premium-tier/clone-app.png)

    Nella pagina **Clona app** è possibile creare un piano di servizio app usando **PremiumV3** nell'area desiderata e specificare le impostazioni e la configurazione dell'app da clonare.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Passaggio da un contenitore Premium a uno SKU Premium V3

Se si ha un'app che usa lo SKU del contenitore Premium di anteprima e si vuole passare al nuovo SKU Premium V3, è necessario ridistribuire l'app per sfruttare **PremiumV3.** A tale scopo, vedere la prima opzione in Aumentare le risorse da una [combinazione di](#scale-up-from-an-unsupported-resource-group-and-region-combination) gruppo di risorse e area non supportata

## <a name="automate-with-scripts"></a>Automatizzazione con gli script

È possibile automatizzare la creazione di app nel livello **PremiumV3** con gli script, usando l'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) [o Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il comando seguente crea un piano di servizio app in _P1V3._ È possibile eseguirlo in Cloud Shell. Le opzioni per `--sku` sono P1V3, _P2V3_ e _P3V3._

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Il comando seguente crea un piano di servizio app in _P1V3._ Le opzioni per `-WorkerSize` sono _Small_, _Medium_ e _Large_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>Altre risorse

[Aumentare le dimensioni di un'app in Azure](manage-scale-up.md) 
 [Ridimensionare il numero di istanze manualmente o automaticamente](../azure-monitor/autoscale/autoscale-get-started.md)
