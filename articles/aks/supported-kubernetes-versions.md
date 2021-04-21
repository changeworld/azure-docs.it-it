---
title: Versioni Kubernetes supportate nel servizio Azure Kubernetes
description: Comprendere i criteri di supporto di della versione di Kubernetes e il ciclo di vita dei cluster nel servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 03/29/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: 43b0304ec6ac4266c3727990bf013c3dcfe523a3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750249"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versioni Kubernetes supportate nel servizio Azure Kubernetes

La community di Kubernetes rilascia le versioni secondarie all'incirca ogni tre mesi. Di recente, la community di Kubernetes ha aumentato la finestra di supporto per ogni versione da 9 mesi a [12](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/)mesi, a partire dalla versione 1.19. 

Le versioni secondarie includono nuove funzionalità e miglioramenti. Le versioni delle patch sono più frequenti (talvolta settimanali) e sono destinate a correzioni di bug critiche all'interno di una versione secondaria. Le versioni patch includono correzioni per vulnerabilità della sicurezza o bug principali.

## <a name="kubernetes-versions"></a>Versioni di Kubernetes

Kubernetes usa lo schema di [controllo delle versioni semantico](https://semver.org/) standard per ogni versione:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Ogni numero nella versione indica la compatibilità generale con la versione precedente:

* **Le versioni principali cambiano** quando gli aggiornamenti delle API incompatibili o la compatibilità con le versioni precedenti possono essere interrotte.
* **Le versioni secondarie** cambiano quando vengono apportati aggiornamenti delle funzionalità compatibili con le versioni precedenti delle altre versioni secondarie.
* **Le versioni delle** patch cambiano quando vengono apportate correzioni di bug compatibili con le versioni precedenti.

Eseguire la versione patch più recente della versione secondaria in esecuzione. Ad esempio, il cluster di produzione si trova in **`1.17.7`** . **`1.17.8`** è l'ultima versione di patch disponibile per la *serie 1.17.* È consigliabile eseguire **`1.17.8`** l'aggiornamento a appena possibile per assicurarsi che il cluster sia completamente aggiornato e supportato.

## <a name="kubernetes-version-support-policy"></a>Criteri di supporto della versione di Kubernetes

Il servizio Diaks definisce una versione disponibile a livello generale come versione abilitata in tutte le misurazioni SLO o sla e disponibile in tutte le aree. Il servizio Kubernetes supporta tre versioni secondarie ga di Kubernetes:

* L'ultima versione secondaria disponibile a tutti i costi rilasciata nel servizio Servizio Web di gestione degli aggiornamenti (che verrà referenziato come N).
* Due versioni secondarie precedenti.
    * Ogni versione secondaria supportata supporta anche un massimo di due (2) patch stabili.

Il servizio AKS può anche supportare le versioni di anteprima, etichettate in modo esplicito e soggette ai termini [e alle condizioni dell'anteprima.][preview-terms]

> [!NOTE]
> Il servizio AKS usa procedure di distribuzione sicure che implicano una distribuzione graduale dell'area. Ciò significa che potrebbero essere necessario fino a 10 giorni lavorativi per la disponibilità di una nuova versione o di una nuova versione in tutte le aree.

La finestra supportata delle versioni di Kubernetes nel servizio Kubernetes è nota come "N-2": (N (versione più recente) - 2 (versioni secondarie)).

Ad esempio, se il servizio AKS introduce *oggi la versione 1.17.a,* viene fornito il supporto per le versioni seguenti:

Nuova versione secondaria    |    Elenco delle versioni supportate
-----------------    |    ----------------------
1.17.a               |    1.17.a, 1.17.b, 1.16.c, 1.16.d, 1.15.e, 1.15.f

Dove ".letter" è rappresentativo delle versioni della patch.

Quando viene introdotta una nuova versione secondaria, la versione secondaria meno recente e le versioni di patch supportate vengono deprecate e rimosse. Ad esempio, l'elenco di versioni supportate corrente è:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

AKS versione 1.18. , rimuovendo tutte le \* versioni 1.15. dal \* supporto in 30 giorni.

> [!NOTE]
> Se i clienti eseguono una versione di Kubernetes non supportata, verrà chiesto loro di eseguire l'aggiornamento quando richiedono il supporto per il cluster. I cluster che eseguono versioni kubernetes non supportate non sono coperti dai criteri di supporto [del servizio Kubernetes.](./support-policies.md)

Oltre a quello precedente, il servizio AKS supporta un massimo di due **versioni di patch** di una determinata versione secondaria. Di seguito sono riportate le versioni supportate:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Se AKS rilascia e , le versioni delle patch meno recenti vengono deprecate e rimosse `1.17.9` `1.16.11` e l'elenco delle versioni supportate diventa:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>Versioni `kubectl` supportate

È possibile usare una versione secondaria precedente o più recente di rispetto alla versione `kubectl` *kube-apiserver,* coerente con i criteri di supporto di [Kubernetes per kubectl](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl).

Ad esempio, se *kube-apiserver* è alla *versione 1.17,* è possibile usare le versioni *da 1.16* a *1.18* di con `kubectl` tale *kube-apiserver*.

Per installare o aggiornare la versione di `kubectl` , eseguire `az aks install-cli` .

## <a name="release-and-deprecation-process"></a>Processo di rilascio e deprecazione

È possibile fare riferimento alle versioni future e alle deprecazioni nel calendario delle versioni di [Kubernetes del servizio Kubernetes.](#aks-kubernetes-release-calendar)

Per le **nuove versioni** secondarie di Kubernetes:
  * Il servizio Web Diaks pubblica un pre-annuncio con la data pianificata di una nuova versione e la deprecazione della rispettiva versione precedente nelle note sulla versione del servizio Web Disattesa almeno 30 giorni prima della rimozione. [](https://aka.ms/aks/releasenotes)
  * Il servizio Azure Advisor servizio [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) per avvisare gli utenti se una nuova versione causerà problemi nel cluster a causa di API deprecate. Azure Advisor viene usato anche per avvisare l'utente se attualmente non è più disponibile.
  * Il servizio AzureKs pubblica una notifica sull'integrità del servizio disponibile per tutti gli utenti con accesso al portale e al servizio Azure Service Pack e invia un messaggio di posta elettronica agli amministratori della sottoscrizione con le date di rimozione della versione pianificate. [](../service-health/service-health-overview.md)

    > [!NOTE]
    > Per informazioni su chi sono gli amministratori della sottoscrizione o per modificarlo, vedere Gestire [le sottoscrizioni di Azure.](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)
    
  * Gli utenti hanno **30 giorni dalla** rimozione della versione all'aggiornamento a una versione secondaria supportata per continuare a ricevere supporto.

Per le **nuove versioni patch** di Kubernetes:
  * A causa della natura urgente delle versioni delle patch, possono essere introdotte nel servizio non appena diventano disponibili.
  * In generale, il prodotto AKS non comunica ampiamente il rilascio di nuove versioni di patch. Tuttavia, il servizio AKS monitora e convalida costantemente le patch CVE disponibili per supportarle in modo appropriato. Se viene trovata una patch critica o è necessaria un'azione da parte dell'utente, il servizio AKS invierà una notifica agli utenti di eseguire l'aggiornamento alla patch appena disponibile.
  * Gli utenti hanno a che fare con **30** giorni dalla rimozione di una versione di patch dal servizio Disacks per eseguire l'aggiornamento a una patch supportata e continuare a ricevere supporto.

### <a name="supported-versions-policy-exceptions"></a>Eccezioni dei criteri delle versioni supportate

Il componente AKS si riserva il diritto di aggiungere o rimuovere versioni nuove o esistenti con uno o più bug critici che influiscono sulla produzione o problemi di sicurezza senza preavviso.

È possibile ignorare o accelerare l'implementazione di versioni di patch specifiche, a seconda della gravità del bug o del problema di sicurezza.

## <a name="azure-portal-and-cli-versions"></a>portale di Azure e dell'interfaccia della riga di comando

Quando si distribuisce un cluster del servizio AzureKs nel portale o con l'interfaccia della riga di comando di Azure, il cluster utilizza per impostazione predefinita la versione secondaria N-1 e la patch più recente. Ad esempio, se il servizio Gateway Gateway supporta *1.17.a*, *1.17.b*, *1.16.c*, *1.16.d*, *1.15.e* e *1.15.f*, la versione predefinita selezionata è *1.16.c*.

Per scoprire quali versioni sono attualmente disponibili per la sottoscrizione e l'area in uso, usare il comando [az servizio Azure Kubernetes get-versions][az-aks-get-versions]. L'esempio seguente elenca le versioni di Kubernetes disponibili per l'area *EastUS*:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>Calendario delle versioni di Kubernetes del servizio Kubernetes del servizio Kubernetes

Per la cronologia delle versioni precedenti, vedere [Kubernetes](https://en.wikipedia.org/wiki/Kubernetes#History).

|  Versione K8s | Versione upstream  | Anteprima di AKS  | AKS GA  | Fine del ciclo di vita |
|--------------|-------------------|--------------|---------|-------------|
| 1.18  | 23-20 marzo  | Maggio 2020   | Agosto 2020  | 1.21 Ga | 
| 1,19  | 04-20 agosto  | Settembre 2020   | Novembre 2020  | 1.22 Ga | 
| 1,20  | Dic-08-20  | Gennaio 2021   | Mar 2021  | 1.23 Ga |
| 1.21  | Apr-08-21 | Maggio 2021   | Giugno 2021  | 1.24 Ga |



## <a name="faq"></a>Domande frequenti

**In che modo Microsoft invia una notifica delle nuove versioni di Kubernetes?**

Il team del servizio Azure Kubernetes pubblica pre-annunci con date pianificate per le nuove versioni di Kubernetes nella documentazione, [gitHub](https://github.com/Azure/AKS/releases) e messaggi di posta elettronica agli amministratori della sottoscrizione proprietari di cluster che non saranno più supportati.  Oltre agli annunci, il servizio Azure Azure Advisor usa anche [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) per informare il cliente all'interno del portale di Azure per avvisare gli utenti che non sono più supportati, nonché per avvisarli di API deprecate che influiranno sull'applicazione o sul processo di sviluppo. 

**Con quale frequenza è necessario prevedere di aggiornare le versioni di Kubernetes per mantenere il supporto?**

A partire da Kubernetes 1.19, la community open source ha esteso il supporto [a 1 anno.](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/) Il servizio AKS esegue il commit per abilitare le patch e supporta la corrispondenza degli impegni upstream. Per i cluster del servizio Web Diaks nella versione 1.19 e successive, sarà possibile eseguire l'aggiornamento almeno una volta all'anno per mantenere una versione supportata. 

Per le versioni 1.18 o precedenti, la finestra di supporto rimane di 9 mesi, richiedendo un aggiornamento una volta ogni 9 mesi per mantenere una versione supportata. Testare regolarmente nuove versioni ed essere preparati a eseguire l'aggiornamento a versioni più recenti per acquisire i miglioramenti stabili più recenti all'interno di Kubernetes.

**Cosa accade quando un utente aggiorna un cluster Kubernetes con una versione secondaria non supportata?**

Se si ha la versione *n-3* o una versione precedente, significa che non si è supportati e verrà richiesto di eseguire l'aggiornamento. Quando l'aggiornamento dalla versione n-3 a n-2 ha esito positivo, si torna entro i criteri di supporto. Ad esempio:

- Se la versione del servizio AKS supportata meno recente è *1.15.a* e si è nella versione *1.14.b* o precedente, il supporto non è disponibile.
- Quando si esegue l'aggiornamento *dalla versione 1.14.b* alla *versione 1.15.a* o successiva, si torna entro i criteri di supporto.

I downgrade non sono supportati.

**Cosa significa "Al di fuori del supporto"**

"Al di fuori del supporto" significa che:
* La versione in esecuzione non è in linea con l'elenco delle versioni supportate.
* Verrà richiesto di aggiornare il cluster a una versione supportata quando si richiede il supporto, a meno che non si sia entro il periodo di tolleranza di 30 giorni dopo la deprecazione della versione. 

Inoltre, il servizio AKS non garantisce il runtime o altre garanzie per i cluster al di fuori dell'elenco delle versioni supportate.

**Cosa accade quando un utente ridimensiona un cluster Kubernetes con una versione secondaria non supportata?**

Per le versioni secondarie non supportate dal server del supporto di Microsoft Office, il ridimensionamento deve continuare a funzionare. Poiché non sono presenti garanzie di qualità del servizio, è consigliabile eseguire l'aggiornamento per riportare il cluster nel supporto.

**Un utente può rimanere in una versione di Kubernetes per sempre?**

Se un cluster non è più in supporto per più di tre (3) versioni secondarie ed è stato rilevato che comporta rischi per la sicurezza, Azure contatta in modo proattivo l'utente per aggiornare il cluster. Se non si prendono altre misure, Azure si riserva il diritto di aggiornare automaticamente il cluster per conto dell'utente.

**Quale versione supporta il piano di controllo se il pool di nodi non si trova in una delle versioni supportate del servizio AKS?**

Il piano di controllo deve essere all'interno di una finestra di versioni di tutti i pool di nodi. Per informazioni dettagliate sull'aggiornamento del piano di controllo o dei pool di nodi, vedere la documentazione [sull'aggiornamento dei pool di nodi.](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)

**È possibile ignorare più versioni del servizio AKS durante l'aggiornamento del cluster?**

Quando si aggiorna un cluster del servizio Kubernetes supportato, le versioni secondarie di Kubernetes non possono essere ignorate. Ad esempio, gli aggiornamenti tra:
  * *1.12.x*  ->  *1.13.x:* consentito.
  * *1.13.x*  ->  *1.14.x:* consentito.
  * *1.12.x*  ->  *1.14.x:* non consentito.

Per eseguire *l'aggiornamento dalla versione 1.12.x*  ->  *1.14.x:*
1. Eseguire *l'aggiornamento da 1.12.x*  ->  *1.13.x*.
1. Eseguire *l'aggiornamento da 1.13.x*  ->  *1.14.x*.

L'omissione di più versioni può essere eseguita solo quando si esegue l'aggiornamento da una versione non supportata a una versione supportata. Ad esempio, è possibile eseguire l'aggiornamento da una *versione 1.10.x* non supportata a una *versione 1.15.x supportata.*

**È possibile creare un nuovo cluster 1.xx.x durante la finestra di supporto di 30 giorni?**

No. Dopo che una versione è stata deprecata o rimossa, non è possibile creare un cluster con tale versione. Durante l'implementazione della modifica, si inizierà a vedere la versione precedente rimossa dall'elenco delle versioni. Questo processo può richiedere fino a due settimane dall'annuncio, progressivamente in base all'area.

**Se si è in una versione appena deprecata, è comunque possibile aggiungere nuovi pool di nodi? Oppure è necessario eseguire l'aggiornamento?**

No. Non sarà consentito aggiungere pool di nodi della versione deprecata al cluster. È possibile aggiungere pool di nodi di una nuova versione. Tuttavia, potrebbe essere necessario aggiornare prima il piano di controllo. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come aggiornare il cluster, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
