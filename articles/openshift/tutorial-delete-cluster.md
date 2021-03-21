---
title: Esercitazione - Eliminare un cluster Azure Red Hat OpenShift
description: Questa esercitazione illustra come eliminare un cluster Azure Red Hat OpenShift usando l'interfaccia della riga di comando di Azure
author: sakthi-vetrivel
ms.custom: fasttrack-edit
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 04/24/2020
ms.openlocfilehash: 3830942d63c3db9646b84d43d65f7c68cb5fcf9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102209793"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Esercitazione: Eliminare un cluster di Azure Red Hat OpenShift 4

In questa esercitazione, parte tre di tre, viene eliminato un cluster Azure Red Hat OpenShift che esegue OpenShift 4. Si apprenderà come:

> [!div class="checklist"]
> * Eliminare un cluster di Azure Red Hat OpenShift


## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato e connesso un cluster Azure Red Hat OpenShift con la console Web di OpenShift. Se questi passaggi non sono stati eseguiti e si vuole procedere, iniziare con [Esercitazione 1: Creare un cluster di Azure Red Hat OpenShift 4](tutorial-create-cluster.md).

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure 2.6.0 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Se si esegue l'interfaccia della riga di comando di Azure in locale, eseguire `az login` per accedere ad Azure.

```bash
az login
```

Se si ha accesso a più sottoscrizioni, eseguire `az account set -s {subscription ID}` sostituendo `{subscription ID}` con la sottoscrizione che si vuole usare.

## <a name="delete-the-cluster"></a>Eliminare il cluster

Nelle esercitazioni precedenti sono state impostate le variabili seguenti.

```bash
CLUSTER=yourclustername
RESOURCEGROUP=yourresourcegroup
```

Usando questi valori, eliminare il cluster:

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

Verrà quindi richiesto di confermare se si vuole eliminare il cluster. Dopo aver confermato con `y`, l'eliminazione del cluster richiederà diversi minuti. Al termine del comando, l'intero gruppo di risorse e le risorse al suo interno, tra cui il cluster, verranno eliminati.

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso come:
> [!div class="checklist"]
> * Eliminare un cluster di Azure Red Hat OpenShift 4

Altre informazioni sull'uso di OpenShift sono disponibili nella [documentazione ufficiale di Red Hat OpenShift](https://docs.openshift.com/container-platform/4.6/welcome/index.html)
