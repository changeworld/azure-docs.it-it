---
title: Hosting dedicato di funzioni di Azure
description: Informazioni sui vantaggi derivanti dall'esecuzione di funzioni di Azure in un piano di hosting dedicato del servizio app.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 0ebf83aa919d91f161b247539ae20873242a8ed8
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937624"
---
# <a name="dedicated-hosting-plans-for-azure-functions"></a>Piani di hosting dedicati per funzioni di Azure

Questo articolo descrive come ospitare l'app per le funzioni in un piano di servizio app, incluso in un ambiente del servizio app (ASE). Per altre opzioni di hosting, vedere l'articolo relativo al [piano di hosting](functions-scale.md).

Un piano di servizio app definisce un set di risorse di calcolo per l'esecuzione di un'app. Queste risorse di calcolo sono analoghe alla [_server farm_](https://wikipedia.org/wiki/Server_farm) nell'hosting convenzionale. È possibile configurare una o più app per le funzioni per l'esecuzione con le stesse risorse di elaborazione (piano di servizio app) come altre app del servizio app, ad esempio le app Web. Questi piani includono SKU Basic, standard, Premium e isolati. Per informazioni dettagliate sul funzionamento del piano di servizio app, vedere [Panoramica approfondita dei piani di servizio app di Azure](../app-service/overview-hosting-plans.md).

Si consideri un piano di servizio app nelle situazioni seguenti:

* Sono presenti macchine virtuali sottoutilizzate, che eseguono già altre istanze del servizio app.
* Si desidera fornire un'immagine personalizzata su cui eseguire le funzioni.

## <a name="billing"></a>Fatturazione

Si paga per le app per le funzioni in un piano di servizio app come per le altre risorse del servizio app. Questo comportamento differisce dal [piano a consumo](consumption-plan.md) di funzioni di Azure o dall'hosting di [piani Premium](functions-premium-plan.md) , che hanno componenti di costo basati sul consumo. Viene addebitato solo il piano, indipendentemente dal numero di app per le funzioni o di app Web eseguite nel piano. Per altre informazioni, vedere la [pagina dei prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/windows/). 

## <a name="always-on"></a><a name="always-on"></a> Always On

Se si esegue in un piano di servizio app, è necessario abilitare l'impostazione **Always on in** modo che l'app per le funzioni venga eseguita correttamente. In un piano di servizio app il runtime delle funzioni risulta inattivo dopo pochi minuti di inattività. Solo i trigger HTTP "attiveranno" quindi le funzioni. L'impostazione **Always on** è disponibile solo in un piano di servizio app. In un piano a consumo, la piattaforma attiva automaticamente le app per le funzioni.

Anche con Always On abilitato, il timeout di esecuzione per le singole funzioni è controllato dall'impostazione `functionTimeout` nel file di progetto [host.json](functions-host-json.md#functiontimeout).

## <a name="scaling"></a>Ridimensionamento

Usando un piano di servizio app, è possibile aumentare manualmente il numero di istanze aggiungendo altre istanze di VM. È anche possibile abilitare la scalabilità automatica, anche se la scalabilità automatica sarà più lenta rispetto alla scalabilità elastica del piano Premium. Per altre informazioni, vedere [Scalare il conteggio delle istanze manualmente o automaticamente](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Per aumentare le prestazioni è anche possibile scegliere un piano di servizio App diverso. Per altre informazioni, vedere [Aumentare le prestazioni di un'app in Azure](../app-service/manage-scale-up.md). 

> [!NOTE] 
> Quando si eseguono funzioni JavaScript (Node.js) in un piano di servizio app, è necessario scegliere un piano con un minor numero di vCPU. Per altre informazioni, vedere [scegliere i piani di servizio app Single Core](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

## <a name="app-service-environments"></a>Ambienti del servizio app

L'esecuzione in un [ambiente del servizio app](../app-service/environment/intro.md) (ASE) consente di isolare completamente le funzioni e sfruttare i vantaggi di un numero maggiore di istanze rispetto a un piano di servizio app. Per iniziare, consultare .

Se si vuole solo eseguire l'app per le funzioni in una rete virtuale, è possibile usare il [piano Premium](functions-premium-plan.md). Per altre informazioni, vedere [stabilire l'accesso al sito privato di funzioni di Azure](functions-create-private-site-access.md). 

## <a name="next-steps"></a>Passaggi successivi

+ [Opzioni di hosting di funzioni di Azure](functions-scale.md)
+ [Panoramica del piano di servizio app di Azure](../app-service/overview-hosting-plans.md)