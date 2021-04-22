---
title: Aggiornare il sistema operativo host per il cluster di calcolo e l'istanza
titleSuffix: Azure Machine Learning
description: Aggiornare il sistema operativo host per il cluster di calcolo e l'istanza di calcolo da Ubuntu 16.04 LTS a 18.04 LTS.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 7445de8349d025679b1560e065ed15d9eec3b08f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872012"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>Aggiornare l'istanza di calcolo e il sistema operativo host del cluster di calcolo

Azure Machine Learning cluster di __calcolo e l'istanza__ __di calcolo sono__ infrastruttura di calcolo gestita. Come servizio gestito, Microsoft gestisce il sistema operativo host e i pacchetti e le versioni software installate.

Il sistema operativo host per il cluster di calcolo e l'istanza di calcolo è Ubuntu 16.04 LTS. Il **30 aprile 2021,** Ubuntu termina il supporto per la versione 16.04. A partire __dal 15 marzo 2021,__ Microsoft aggiornerà automaticamente il sistema operativo host a Ubuntu 18.04 LTS. L'aggiornamento alla versione 18.04 garantirà continui aggiornamenti della sicurezza e il supporto della community di Ubuntu. Questo aggiornamento verrà eseguito in tutte le aree di Azure e sarà disponibile in tutte le aree entro il 9 aprile __2021.__ Per altre informazioni sul supporto finale di Ubuntu per la versione 16.04, vedere il blog sulla versione [di Ubuntu.](https://wiki.ubuntu.com/Releases)

> [!TIP]
> * Il sistema operativo host non è la versione del sistema operativo che è possibile specificare per un [ambiente durante](how-to-use-environments.md) il training o la distribuzione di un modello. Gli ambienti vengono eseguiti all'interno di Docker. Docker viene eseguito nel sistema operativo host.
> * Se attualmente si usano ambienti basati su Ubuntu 16.04 per il training o la distribuzione, Microsoft consiglia di passare all'uso di immagini basate su Ubuntu 18.04. Per altre informazioni, vedere [Come usare gli ambienti e](how-to-use-environments.md) il repository Azure Machine Learning [contenitori.](https://github.com/Azure/AzureML-Containers/tree/master/base)
> * Quando si usa Azure Machine Learning di calcolo basata su Ubuntu 18.04, la versione predefinita di Python è _Python 3.8._
## <a name="creating-new-resources"></a>Creazione di nuove risorse

Per impostazione predefinita, il cluster di calcolo o le istanze di calcolo create dopo il 09 aprile __2021__ usano Ubuntu 18.04 LTS come sistema operativo host. Non è possibile selezionare un sistema operativo host diverso.

## <a name="upgrade-existing-resources"></a>Aggiornare le risorse esistenti

Se sono già stati creati cluster di calcolo o istanze di calcolo prima del __15 marzo 2021,__ è necessario intervenire per aggiornare il sistema operativo host a Ubuntu 18.04. A seconda dell'area da cui si accede Azure Machine Learning, è consigliabile eseguire queste azioni dopo il 09 aprile __2021__ per assicurarsi che le modifiche siano state apportate a tutte le aree:

* __Azure Machine Learning cluster di calcolo__:

    * Se il cluster è configurato con __min nodes = 0,__ verrà aggiornato automaticamente al completamento di tutti i processi e si riduce a zero nodi.
    * Se __min nodes > 0,__ modificare temporaneamente i nodi minimi su zero e consentire al cluster di ridurre a zero nodi.

    Per altre informazioni sulla modifica dei nodi minimi, vedere il [comando az ml computetarget update amlcompute](https://docs.microsoft.com/cli/azure/ml/computetarget/update#az_ml_computetarget_update_amlcompute) dell'interfaccia della riga di comando di Azure o le informazioni di riferimento sull'SDK [AmlCompute.update().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-)

* __Azure Machine Learning di calcolo:__ creare una nuova istanza di calcolo (che userà Ubuntu 18.04) ed eliminare l'istanza precedente.

    * Tutti i notebook archiviati nella condivisione file dell'area di lavoro, gli archivi dati, dei set di dati saranno accessibili dalla nuova istanza di calcolo.
    * Se sono stati creati ambienti Conda personalizzati, è possibile esportare tali ambienti dall'istanza esistente e importare nella nuova istanza. Per informazioni sull'esportazione e l'importazione di Conda, vedere la [documentazione di Conda](https://docs.conda.io/) all'docs.conda.io.

    Per altre informazioni, vedere gli articoli [Che cos'è un'istanza di calcolo](concept-compute-instance.md) e Creare e gestire [un Azure Machine Learning di calcolo](how-to-create-manage-compute-instance.md)

## <a name="check-host-os-version"></a>Controllare la versione del sistema operativo host

Per informazioni sul controllo della versione del sistema operativo host, vedere la pagina wiki della community di Ubuntu sul controllo [della versione di Ubuntu.](https://help.ubuntu.com/community/CheckingYourUbuntuVersion)

> [!TIP]
> Per usare il comando dal wiki, è possibile usare una sessione `lsb_release -a` [terminal in un'istanza di calcolo](how-to-access-terminal.md).
## <a name="next-steps"></a>Passaggi successivi

Per altre domande o dubbi, contattare Microsoft all'indirizzo [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .
