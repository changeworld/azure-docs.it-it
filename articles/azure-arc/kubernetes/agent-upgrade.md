---
title: Aggiornamento degli agenti Kubernetes abilitati per Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Aggiornamenti dell'agente di controllo per Azure Arc abilitato Kubernetes
keywords: Kubernetes, Arc, Azure, K8s, contenitori, Agent, aggiornamento
ms.openlocfilehash: d81a00ed4f30f446aeed96d59a455935c652b7d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954548"
---
# <a name="upgrading-azure-arc-enabled-kubernetes-agents"></a>Aggiornamento degli agenti Kubernetes abilitati per Azure Arc

Azure Arc Enabled Kubernetes fornisce funzionalità di aggiornamento automatico e di aggiornamento manuale per i relativi agenti. Se si usa Disabilita aggiornamento automatico e si basano invece sull'aggiornamento manuale, i criteri di supporto della versione sono applicabili per gli agenti Arc e il cluster Kubernetes sottostante.

## <a name="toggle-auto-upgrade-on-or-off-when-connecting-cluster-to-azure-arc"></a>Attivare o disattivare l'aggiornamento automatico durante la connessione del cluster ad Azure Arc

Azure Arc Enabled Kubernetes fornisce ai propri agenti le funzionalità di aggiornamento automatico predefinite.

Il comando seguente connette un cluster ad Azure Arc con l'aggiornamento automatico **abilitato**:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

Con l'aggiornamento automatico abilitato, l'agente esegue il polling di Azure ogni ora per la disponibilità di una versione più recente degli agenti. Se l'agente trova una versione più recente disponibile, attiva un aggiornamento del grafico Helm per gli agenti di Azure Arc.

Per rifiutare esplicitamente l'aggiornamento automatico, specificare il `--disable-auto-upgrade` parametro durante la connessione del cluster ad Azure Arc. Il comando seguente connette un cluster ad Azure Arc con l'aggiornamento automatico **disabilitato**:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest --disable-auto-upgrade
```

> [!TIP]
> Se si prevede di disabilitare l'aggiornamento automatico, vedere i criteri di [supporto della versione](#version-support-policy) per Azure Arc abilitato Kubernetes.

## <a name="toggle-auto-upgrade-onoff-after-connecting-cluster-to-azure-arc"></a>Attiva/disattiva l'aggiornamento automatico dopo la connessione del cluster ad Azure Arc

Dopo aver connesso un cluster ad Azure Arc, è possibile abilitare o disabilitare la funzionalità di aggiornamento automatico con il `az connectedk8s update` comando, come illustrato di seguito:

```console
az connectedk8s update --name AzureArcTest1 --resource-group AzureArcTest --auto-upgrade false
```

## <a name="manually-upgrade-agents"></a>Aggiornare manualmente gli agenti

Se è stato disabilitato l'aggiornamento automatico per gli agenti, è possibile avviare manualmente gli aggiornamenti per questi agenti usando il `az connectedk8s upgrade` comando come illustrato di seguito:

```console
az connectedk8s upgrade -g AzureArcTest1 -n AzureArcTest --agent-version 1.0.1
```

Azure Arc Enabled Kubernetes segue lo [schema di controllo delle versioni semantico](https://semver.org/) standard di `MAJOR.MINOR.PATCH` per il controllo delle versioni degli agenti. 

Ogni numero nella versione indica la compatibilità generale con la versione precedente:

* Le **versioni principali** cambiano quando sono presenti aggiornamenti API incompatibili o la compatibilità con le versioni precedenti può essere interruppe.
* Le **versioni secondarie** cambiano quando le modifiche delle funzionalità sono compatibili con le versioni precedenti.
* Le **versioni delle patch** cambiano quando vengono apportate correzioni di bug compatibili con le versioni precedenti.

## <a name="version-support-policy"></a>Criteri di supporto della versione

Quando si creano problemi di supporto, Azure Arc Enabled Kubernetes pratica i criteri di supporto della versione seguenti:

* Gli agenti Kubernetes abilitati per Azure Arc hanno una finestra di supporto "N-2" dove "N" è la versione secondaria più recente degli agenti. 
  * Ad esempio, se Azure Arc Enabled Kubernetes introduce 0.28. a oggi, le versioni 0.28. a, 0.28. b, 0.27. c, 0.27. d, 0.26. e e 0.26. f sono supportate da Azure Arc.

* I cluster Kubernetes che si connettono ad Azure Arc hanno una finestra di supporto "N-2", dove "N" è la versione secondaria stabile più recente di [upstream Kubernetes](https://github.com/kubernetes/kubernetes/releases). 
  * Ad esempio, se Kubernetes introduce 1.20. a oggi, sono supportate le versioni 1.20. a, 1.20. b, 1.19. c, 1.19. d, 1.18. e e 1.18. f.

### <a name="how-often-are-minor-version-releases-of-azure-arc-enabled-kubernetes-available"></a>Con quale frequenza sono disponibili le versioni secondarie di Azure Arc abilitato Kubernetes?

Una versione secondaria degli agenti Kubernetes abilitati per Azure Arc viene rilasciata approssimativamente una volta al mese.

### <a name="what-happens-if-im-using-an-agent-version-or-a-kubernetes-version-outside-the-official-support-window"></a>Cosa accade se si usa una versione dell'agente o una versione di Kubernetes al di fuori della finestra di supporto ufficiale?

"All'esterno del supporto" significa che le versioni in esecuzione non rientrano nelle versioni "N-2" supportate degli agenti e nei cluster Kubernetes upstream. Per procedere con il problema di supporto, verrà richiesto di aggiornare il cluster e gli agenti a una versione supportata.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare la Guida introduttiva per [connettere un cluster Kubernetes ad Azure Arc](./quickstart-connect-cluster.md).
* Si dispone già di un cluster Kubernetes connesso ad Azure Arc? [Creare configurazioni nel cluster Kubernetes abilitato per l'arco](./tutorial-use-gitops-connected-cluster.md).
* Informazioni su come [usare i criteri di Azure per applicare configurazioni su larga scala](./use-azure-policy.md).