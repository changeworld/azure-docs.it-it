---
title: includere file
description: includere file
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 03/05/2020
ms.openlocfilehash: 0d7622217d192a100fd809c32c9e85970cf61fd7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511010"
---
1. Usare le istruzioni riportate in [Azure Machine Learning SDK](/python/api/overview/azure/ml/install) per installare Azure Machine Learning SDK per Python

1. Creare un'[area di lavoro di Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Scrivere un [file di configurazione](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Clonare [il repository GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Avviare il server notebook dalla directory clonata.

    ```bash
    jupyter notebook
    ```