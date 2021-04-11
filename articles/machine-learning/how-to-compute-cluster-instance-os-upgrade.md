---
title: Aggiornare il sistema operativo host per il cluster di calcolo e l'istanza
titleSuffix: Azure Machine Learning
description: Aggiornare il sistema operativo host per il cluster di calcolo e l'istanza di calcolo da Ubuntu 16,04 LTS a 18,04 LTS.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 710a860b1ed87f176b6f42b4963dad17acb323b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954055"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>Aggiornare l'istanza di calcolo e il sistema operativo host del cluster di calcolo

Azure Machine Learning __cluster di calcolo__ e l' __istanza di calcolo__ sono un'infrastruttura di calcolo gestita. In qualità di servizio gestito, Microsoft gestisce il sistema operativo host e i pacchetti e le versioni software installati.

Il sistema operativo host per il cluster di calcolo e l'istanza di calcolo è stato Ubuntu 16,04 LTS. Il **30 aprile 2021**, Ubuntu sta terminando il supporto per 16,04. A partire dal __15 marzo 2021__, Microsoft aggiornerà automaticamente il sistema operativo host a Ubuntu 18,04 LTS. L'aggiornamento a 18,04 garantirà aggiornamenti della sicurezza e supporto continui dalla community Ubuntu. Questo aggiornamento verrà implementato tra le aree di Azure e sarà disponibile in tutte le aree entro il __09 aprile 2021__. Per ulteriori informazioni sul supporto di Ubuntu finale per 16,04, vedere il [Blog della versione di Ubuntu](https://wiki.ubuntu.com/Releases).

> [!TIP]
> * Il sistema operativo host non è la versione del sistema operativo che è possibile specificare per un [ambiente](how-to-use-environments.md) durante il training o la distribuzione di un modello. Gli ambienti vengono eseguiti all'interno di Docker. Docker è in esecuzione nel sistema operativo host.
> * Se attualmente si usano ambienti basati su Ubuntu 16,04 per il training o la distribuzione, Microsoft consiglia di passare all'uso delle immagini basate su Ubuntu 18,04. Per altre informazioni, vedere [come usare gli ambienti](how-to-use-environments.md) e il [repository dei contenitori Azure Machine Learning](https://github.com/Azure/AzureML-Containers/tree/master/base).
> * Quando si usa un'istanza di calcolo Azure Machine Learning basata su Ubuntu 18,04, la versione predefinita di Python è _python 3,8_.
## <a name="creating-new-resources"></a>Creazione di nuove risorse

Il cluster di calcolo o le istanze di calcolo create dopo il __09 aprile 2021__ usano Ubuntu 18,04 LTS come sistema operativo host per impostazione predefinita. Non è possibile selezionare un sistema operativo host diverso.

## <a name="upgrade-existing-resources"></a>Aggiornare le risorse esistenti

Se sono già stati creati cluster di calcolo o istanze di calcolo prima del __15 marzo 2021__, è necessario intervenire per aggiornare il sistema operativo host a Ubuntu 18,04. A seconda dell'area a cui si accede Azure Machine Learning, è consigliabile eseguire queste azioni dopo il __09 aprile 2021__ per assicurarsi che le modifiche siano state implementate in tutte le aree:

* __Azure Machine Learning cluster di calcolo__:

    * Se il cluster è configurato con __min nodes = 0__, verrà aggiornato automaticamente al termine di tutti i processi e diminuirà fino a zero nodi.
    * Se __min nodes > 0__, impostare temporaneamente i nodi minimi su zero e consentire al cluster di ridurre fino a zero nodi.

    Per ulteriori informazioni sulla modifica del numero minimo di nodi, vedere il comando [AZ ml computetarget Update amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/update#ext_azure_cli_ml_az_ml_computetarget_update_amlcompute) dell'interfaccia della riga di comando di Azure o il riferimento a [amlcompute. Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) SDK.

* __Azure Machine Learning istanza di calcolo__: creare una nuova istanza di calcolo (che userà Ubuntu 18,04) ed eliminare l'istanza precedente.

    * Qualsiasi notebook archiviato nella condivisione file dell'area di lavoro, gli archivi dati e i set di dati saranno accessibili dalla nuova istanza di calcolo.
    * Se sono stati creati ambienti conda personalizzati, è possibile esportare tali ambienti dall'istanza esistente e importarli nella nuova istanza. Per informazioni sull'esportazione e l'importazione di conda, vedere la [documentazione di conda](https://docs.conda.io/) in docs.conda.io.

    Per altre informazioni, vedere l'articolo [che cos'è l'istanza di calcolo](concept-compute-instance.md) e [creare e gestire un Azure Machine Learning di istanze di calcolo](how-to-create-manage-compute-instance.md)

## <a name="check-host-os-version"></a>Controllare la versione del sistema operativo host

Per informazioni sul controllo della versione del sistema operativo host, vedere la pagina wiki della community Ubuntu relativa alla [Verifica della versione di Ubuntu](https://help.ubuntu.com/community/CheckingYourUbuntuVersion).

> [!TIP]
> Per usare il `lsb_release -a` comando dal wiki, è possibile [usare una sessione terminal in un'istanza di calcolo](how-to-access-terminal.md).
## <a name="next-steps"></a>Passaggi successivi

In caso di domande o dubbi, contattaci all'indirizzo [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .
