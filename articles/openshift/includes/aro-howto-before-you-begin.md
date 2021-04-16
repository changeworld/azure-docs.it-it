---
author: sabbour
ms.author: asabbour
ms.date: 4/5/2020
ms.openlocfilehash: 1fded0ad08af4b1e5d915e32e09087c1a78bd318
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520743"
---
### <a name="create-the-cluster"></a>Creare il cluster

Seguire l'esercitazione [per creare un cluster Azure Red Hat OpenShift .](../tutorial-create-cluster.md) Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.6.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

### <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

Per gestire un cluster Azure Red Hat OpenShift, usare [oc](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html), il client della riga di comando OpenShift.

> [!NOTE]
> È consigliabile [installare la riga di comando OpenShift](../tutorial-connect-cluster.md) Azure Cloud Shell [e](https://shell.azure.com/) usarla per tutte le operazioni della riga di comando riportate di seguito. Avviare la shell da shell.azure.com o facendo clic sul collegamento:
>
> [![Incorpora avvio](https://docs.microsoft.com/azure/includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "Avviare Azure Cloud Shell")](https://shell.azure.com/bash)

Seguire l'esercitazione per installare l'interfaccia della riga di comando, recuperare le credenziali del cluster e connettersi al [cluster](../tutorial-connect-cluster.md) usando la console Web e l'interfaccia della riga di comando OpenShift.

Dopo aver eseguito l'accesso, verrà visualizzato un messaggio che indica che si sta usando il `default` progetto.

```output
Login successful.

You have access to 61 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```
