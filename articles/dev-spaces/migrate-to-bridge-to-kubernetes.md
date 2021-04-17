---
title: Migrazione a Bridge per Kubernetes
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Descrive il processo di migrazione da Azure Dev Spaces a Bridge to Kubernetes
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, servizio Azure Kubernetes, servizio Azure Kubernetes, contenitori, Bridge to Kubernetes
ms.openlocfilehash: 8ffb7693ff223a9cb952964ded1e6967ceeb326e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499292"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Migrazione a Bridge per Kubernetes

> [!IMPORTANT]
> Azure Dev Spaces verrà ritirato il 31 ottobre 2023. I clienti devono passare a usando Bridge to Kubernetes, uno strumento di sviluppo client.
>
> Lo scopo di Azure Dev Spaces riguardava l'allerta degli utenti nello sviluppo in Kubernetes. Un compromesso significativo nell'approccio di Azure Dev Spaces consisteva nell'onere aggiuntivo per gli utenti di comprendere le configurazioni Docker e Kubernetes e i concetti di distribuzione di Kubernetes. Nel corso del tempo, è stato anche chiaro che l'approccio Azure Dev Spaces non ha ridotto in modo efficace la velocità di sviluppo del ciclo interno in Kubernetes. Bridge to Kubernetes riduce in modo efficace la velocità di sviluppo del ciclo interno ed evita inutili oneri per gli utenti.
>
> La missione principale rimane invariata: creare le esperienze migliori per sviluppare, testare ed eseguire il debug del codice di microservizio nel contesto dell'applicazione più grande.

Bridge to Kubernetes offre un'alternativa più leggera a molti degli scenari di sviluppo che funzionano con Azure Dev Spaces. Bridge to Kubernetes è un'esperienza solo lato client che usa le estensioni [in][vs]Visual Studio   e [Visual Studio Code][vsc].  

Bridge to Kubernetes'esperienza di sviluppo consentendo a un'applicazione Kubernetes stabilita di includere un servizio in esecuzione nella workstation di sviluppo locale. A differenza di Dev Spaces, Bridge to Kubernetes riduce le complessità del ciclo interno mediante l'esecuzione affiancata della necessità di creare configurazioni Docker e Kubernetes, consentendo agli sviluppatori di concentrarsi esclusivamente sulla logica di business del codice del microservizio.

Bridge to Kubernetes consente di eseguire l'iterazione del codice in esecuzione nel computer di sviluppo durante l'utilizzo delle dipendenze e della configurazione esistente dall'ambiente Kubernetes. Al contrario, Azure Dev Spaces il microservizio nell'ambiente Kubernetes prima di poter eseguire il debug remoto del servizio e scorrere il codice.

Questo articolo fornisce un confronto tra Azure Dev Spaces e Bridge to Kubernetes nonché istruzioni per la migrazione da Azure Dev Spaces a Bridge to Kubernetes.

## <a name="development-approaches"></a>Approcci di sviluppo

![Approcci di sviluppo](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces ha consentito agli sviluppatori Kubernetes di lavorare con il codice in esecuzione direttamente nel cluster del servizio App Kubernetes, evitando la necessità di un ambiente locale che non assomigliasse all'ambiente distribuito. Questo approccio ha migliorato alcuni aspetti dello sviluppo, ma ha anche introdotto un prerequisito per l'apprendimento e la gestione di concetti aggiuntivi, ad esempio Docker, Kubernetes e Helm, prima di poter iniziare a usare Azure Dev Spaces.

Bridge to Kubernetes consente agli sviluppatori di lavorare direttamente sui computer di sviluppo interagendo con il resto del cluster. Questo approccio sfrutta la familiarità e la velocità di esecuzione del codice direttamente nei computer di sviluppo condividendo le dipendenze e l'ambiente forniti dal cluster. Questo approccio sfrutta anche la fedeltà e la scalabilità che deriva dall'esecuzione in Kubernetes.

## <a name="feature-comparison"></a>Confronto tra le funzionalità

Azure Dev Spaces e Bridge to Kubernetes hanno funzionalità simili, differiscono anche in diverse aree:

| Requisito  | Azure Dev Spaces  | Bridge per Kubernetes  |
|---------------|-------------------|--------------------------------|
| Servizio Azure Kubernetes | In 15 aree di Azure | Qualsiasi area del servizio Servizio Web Del servizio AKS    |
| **Sicurezza** |
| Accesso di sicurezza necessario nel cluster  | Collaboratore al cluster del servizio Servizio Web Disaks  | Controllo degli accessi in base al ruolo di Kubernetes - Aggiornamento della distribuzione   |
| Accesso di sicurezza necessario nel computer di sviluppo  | N/D  | Amministratore locale/sudo   |
| **Usabilità** |
| Indipendente dagli artefatti Kubernetes e Docker  | No  | Sì   |
| Rollback automatico delle modifiche, post-debug  | No  | Sì   |
| **Strumenti client supportati** |
| Funziona con Visual Studio 2019  | Sì  | Sì   |
| Funziona con Visual Studio Code  | Sì  | Sì   |
| Funziona con un'interfaccia della riga di comando  | Sì  | No   |
| **Compatibilità sistema operativo** |
| Funziona in Windows 10  | Sì  | Sì  |
| Funziona in Linux  | Sì  | Sì  |
| Funziona in macOS  | Sì  | Sì  |
| **Capabilities** |
| Isolamento dello sviluppatore o sviluppo in team  | Sì  | Sì  |
| Sovrascrivere in modo selettivo le variabili di ambiente  | No  | Sì  |
| Creazione di Dockerfile e grafico Helm  | Sì  | No  |
| Distribuzione permanente del codice in Kubernetes  | Sì  | No  |
| Debug remoto in un pod Kubernetes  | Sì  | No  |
| Debug locale, connesso a Kubernetes  | No  | Sì  |
| Debug di più servizi contemporaneamente nella stessa workstation  | Sì  | Sì  |

## <a name="kubernetes-inner-loop-development"></a>Sviluppo di cicli interni Kubernetes

La differenza principale tra Azure Dev Spaces e Bridge to Kubernetes è la posizione in cui viene eseguito il codice. Azure Dev Spaces consente di sviluppare ed eseguire il debug del codice del microservizio, ma richiede l'esecuzione di tale codice nel cluster. Bridge to Kubernetes consente di sviluppare ed eseguire il debug del codice del microservizio direttamente nel computer di sviluppo, pur rimanendo nel contesto dell'applicazione più grande in esecuzione in Kubernetes. Bridge to Kubernetes estende il perimetro del cluster Kubernetes e consente ai processi locali di ereditare la configurazione da Kubernetes.

![Sviluppo di cicli interni](media/migrate-to-btk/btk-graphic-non-isolated.gif)

Quando si Bridge to Kubernetes, viene stabilita una connessione di rete tra il computer di sviluppo e il cluster.Per la durata di questa connessione, viene aggiunto un proxy al cluster al posto della distribuzione kubernetes che reindirizza le richieste al servizio al computer di sviluppo. Quando si esegue la disconnessione, la distribuzione dell'applicazione ripristina l'uso della versione originale della distribuzione in esecuzione nel cluster. Questo approccio è diverso dal Azure Dev Spaces in cui il codice viene sincronizzato con il cluster, compilato e quindi eseguito. Azure Dev Spaces inoltre non esegue il rollback del codice.

Bridge to Kubernetes offre la flessibilità necessaria per lavorare con le applicazioni in esecuzione in Kubernetes indipendentemente dal metodo di distribuzione. Se si usa CI/CD per compilare ed eseguire l'applicazione, indipendentemente dal fatto che si usino strumenti stabiliti o script personalizzati, è comunque possibile usare Bridge to Kubernetes per sviluppare ed eseguire il debug del codice.

> [!TIP]
>  [L'estensione Microsoft Kubernetes][kubernetes-extension] consente di sviluppare rapidamente manifesti Kubernetes con Intellisense e di eseguire lo scaffold di grafici Helm.  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Transizione a Bridge to Kubernetes da Azure Dev Spaces

1. Se si usa Visual Studio, aggiornare l'IDE di Visual Studio alla versione 16.7 o successiva e installare l'estensione Bridge to Kubernetes dal Visual Studio Marketplace [.][vs-marketplace] Se si usa Visual Studio Code, installare [l'estensione Bridge to Kubernetes .][vsc-marketplace]
1. Disabilitare il controller Azure Dev Spaces usando il portale di Azure o [Azure Dev Spaces CLI][azds-delete].
1. Usare [Azure Cloud Shell](https://shell.azure.com). In caso contrario, in Mac, Linux o Windows con bash installato aprire un prompt della shell bash. Assicurarsi che nell'ambiente della riga di comando siano disponibili gli strumenti seguenti: interfaccia della riga di comando di Azure, docker, kubectl, curl, tar e gunzip.
1. Creare un registro contenitori o usarne uno esistente. È possibile creare un registro contenitori in Azure [usando Registro Azure Container](https://azure.microsoft.com/services/container-registry/) o usando [Docker Hub](https://hub.docker.com/). Quando si usa Azure Cloud Shell, Registro Azure Container è disponibile solo per l'hosting di immagini Docker.
1. Eseguire lo script di migrazione per convertire Azure Dev Spaces asset in Bridge to Kubernetes asset. Lo script compila una nuova immagine compatibile con Bridge to Kubernetes, la carica nel registro designato e quindi usa [Helm](https://helm.sh) per aggiornare il cluster con l'immagine. È necessario specificare il gruppo di risorse, il nome del cluster del servizio Container e un registro contenitori. Sono disponibili altre opzioni della riga di comando, come illustrato di seguito:

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   Lo script supporta i flag seguenti:

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -c Docker build context path. (default = project root path passed to '-r' option)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. Eseguire manualmente la migrazione di eventuali personalizzazioni, ad esempio le impostazioni delle variabili di ambiente, in *azds.yaml* nel file *values.yml del* progetto.
1. (facoltativo) Rimuovere il `azds.yaml` file dal progetto.
1. Configurare Bridge to Kubernetes nell'applicazione distribuita. Per altre informazioni sull'uso Bridge to Kubernetes in Visual Studio, vedere Usare Bridge to Kubernetes [in Visual Studio][use-btk-vs]. Per VS Code, vedere [Usare Bridge to Kubernetes in VS Code][use-btk-vsc].
1. Avviare il debug usando il profilo di Bridge to Kubernetes di debug/avvio appena creato.
1. È possibile eseguire di nuovo lo script in base alle esigenze per ridistribuire nel cluster.

## <a name="team-development-in-a-shared-cluster"></a>Sviluppo in team in un cluster condiviso

È anche possibile usare il routing specifico dello sviluppatore con Bridge to Kubernetes. Lo Azure Dev Spaces di sviluppo in team usa più spazi dei nomi Kubernetes per isolare un servizio dal resto dell'applicazione usando il concetto di spazi dei nomi padre e figlio. Bridge to Kubernetes offre la stessa funzionalità, ma con caratteristiche migliorate delle prestazioni e all'interno dello stesso spazio dei nomi dell'applicazione.

Sia Bridge to Kubernetes che Azure Dev Spaces le intestazioni HTTP devono essere presenti e propagate in tutta l'applicazione. Se l'applicazione è già stata configurata per gestire la propagazione dell'intestazione Azure Dev Spaces, è necessario aggiornare l'intestazione. Per eseguire la transizione Bridge to Kubernetes da Azure Dev Spaces, aggiornare l'intestazione configurata *da azds-route-as* a *kubernetes-route-as*.

## <a name="evaluate-bridge-to-kubernetes"></a>Valutare Bridge to Kubernetes

Se si desidera sperimentare con Bridge to Kubernetes prima di disabilitare Azure Dev Spaces nel cluster, il modo più semplice è usare un nuovo cluster. Se si tenta di usare Azure Dev Spaces e Bridge to Kubernetes nello stesso cluster, si verificano problemi quando si usano le funzionalità di routing in entrambi.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Usare Visual Studio per valutare Bridge to Kubernetes

1. Aggiornare l Visual Studio IDE alla versione 16.7 o successiva e installare l'estensione Bridge to Kubernetes dal [Visual Studio Marketplace][vs-marketplace].
1. Creare un nuovo cluster del servizio Web Diaks e distribuire l'applicazione. È anche possibile usare [un'applicazione di esempio][btk-sample-app].
1. Configurare Bridge to Kubernetes'applicazione distribuita. Per altre informazioni sull'uso Bridge to Kubernetes in Visual Studio, vedere [Usare Bridge to Kubernetes][use-btk-vs].
1. Avviare il debug in Visual Studio usando il profilo di debug Bridge to Kubernetes appena creato.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Usare Visual Studio Code per valutare Bridge to Kubernetes

1. Installare [l'estensione Bridge to Kubernetes .][vsc-marketplace]
1. Creare un nuovo cluster del servizio Web Diaks e distribuire l'applicazione. È anche possibile usare [un'applicazione di esempio][btk-sample-app].
1. Configurare Bridge to Kubernetes'applicazione distribuita. Per altre informazioni sull'uso Bridge to Kubernetes in Visual Studio Code, vedere [Usare Bridge to Kubernetes][use-btk-vsc].
1. Avviare il debug in Visual Studio usando il profilo di avvio Bridge to Kubernetes appena creato.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul funzionamento Bridge to Kubernetes.

> [!div class="nextstepaction"]
> [Come funziona Bridge per Kubernetes][how-it-works-bridge-to-kubernetes]


[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
