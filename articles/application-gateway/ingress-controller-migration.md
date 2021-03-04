---
title: Come eseguire la migrazione da applicazione Azure del controller di ingresso del gateway al componente aggiuntivo AGIC
description: Questo articolo fornisce istruzioni su come eseguire la migrazione da AGIC distribuite tramite Helm a AGIC distribuite come componente aggiuntivo AKS
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: e83834fd5f8ca95826118c952f7884a494c7abbb
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050835"
---
# <a name="migrate-from-agic-helm-to-agic-add-on"></a>Eseguire la migrazione da AGIC Helm al componente aggiuntivo AGIC 

Se AGIC è già stato distribuito tramite Helm ma si vuole eseguire la migrazione a AGIC distribuito come componente aggiuntivo AKS, i passaggi seguenti consentiranno di semplificare il processo di migrazione. 

## <a name="prerequisites"></a>Prerequisiti 
Prima di avviare il processo di migrazione, è necessario verificare alcuni aspetti. 
  - Si usano le funzionalità con AGIC Helm che non sono [attualmente supportate con il componente aggiuntivo AGIC](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)?
  - Si sta usando più di una distribuzione di AGIC Helm per ogni cluster AKS? 
  - Si usano più distribuzioni di AGIC Helm per un gateway applicazione? 

Se hai risposto sì a una delle domande precedenti, il componente aggiuntivo AGIC non supporterà il tuo caso d'uso, quindi potrai continuare a usare AGIC Helm nel frattempo. In caso contrario, continuare con il processo di migrazione riportato di seguito durante gli orari di minore attività. 

## <a name="find-the-application-gateway-resource-id-that-agic-helm-is-currently-targeting"></a>Trovare l'ID di risorsa del gateway applicazione a cui è destinato attualmente AGIC Helm 
Passare al gateway applicazione a cui è destinata la distribuzione di AGIC Helm. Copiare e salvare l'ID risorsa del gateway applicazione. L'ID risorsa sarà necessario in un passaggio successivo. L'ID risorsa si trova nel portale, nella scheda proprietà del gateway applicazione o tramite l'interfaccia della riga di comando di Azure. L'esempio seguente salva l'ID risorsa del gateway applicazione in *appgwId* per un gateway denominato *myApplicationGateway* nel gruppo di risorse *myResourceGroup*.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
```

## <a name="delete-agic-helm-from-your-aks-cluster"></a>Eliminare AGIC Helm dal cluster AKS
Tramite l'interfaccia della riga di comando di Azure, eliminare la distribuzione di AGIC Helm dal cluster. Prima di poter abilitare il componente aggiuntivo AGIC AKS è necessario eliminare la distribuzione di AGIC Helm. Si noti che tutte le modifiche apportate all'interno del cluster AKS tra il momento in cui si elimina la distribuzione di AGIC Helm e il momento in cui si Abilita il componente aggiuntivo AGIC non vengono riflesse nel gateway applicazione e pertanto questo processo di migrazione deve essere eseguito al di fuori dell'orario di lavoro per ridurre al minimo l'effetto. Il gateway applicazione continuerà ad applicare l'ultima configurazione applicata da AGIC, in modo che le regole di routing esistenti non saranno interessate. 

## <a name="enable-agic-add-on-using-your-existing-application-gateway"></a>Abilitare il componente aggiuntivo AGIC con il gateway applicazione esistente 
È ora possibile abilitare il componente aggiuntivo AGIC nel cluster AKS per indirizzare il gateway applicazione esistente tramite l'interfaccia della riga di comando di Azure o il portale. Eseguire il comando dell'interfaccia della riga di comando di Azure seguente per abilitare il componente aggiuntivo AGIC nel cluster AKS. L'esempio Abilita il componente aggiuntivo in un cluster denominato *cluster*, in un gruppo di risorse denominato *myResourceGroup*, usando l'ID risorsa del gateway applicazione *appgwId* salvato in precedenza nel passaggio precedente. 


```azurecli-interactive
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

In alternativa, è possibile passare al cluster AKS nel portale usando questo [collegamento](https://portal.azure.com/?feature.aksagic=true) e abilitare il componente aggiuntivo AGIC nella scheda rete del cluster. Selezionare il gateway applicazione esistente dal menu a discesa quando si sceglie il gateway applicazione di destinazione del componente aggiuntivo. 

![Portale del controller di ingresso del gateway applicazione](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="next-steps"></a>Passaggi successivi
- [**Risoluzione dei problemi del controller**](ingress-controller-troubleshoot.md)di ingresso del gateway applicazione: Guida alla risoluzione dei problemi per AGIC 
- [**Annotazioni del controller**](ingress-controller-annotations.md)di ingresso del gateway applicazione: elenco di annotazioni in AGIC 