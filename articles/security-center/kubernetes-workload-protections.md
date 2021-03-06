---
title: Protezioni dei carichi di lavoro per i carichi di lavoro Kubernetes
description: Informazioni su come usare la serie di consigli sulla sicurezza del carico di lavoro Kubernetes del Centro sicurezza di Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: 1a6a9080a8957b56d12eca289a500fd45f0e709b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310819"
---
# <a name="protect-your-kubernetes-workloads"></a>Proteggere i carichi di lavoro Kubernetes

Questa pagina descrive come usare il set di raccomandazioni per la sicurezza del Centro sicurezza di Azure dedicato alla protezione dei carichi di lavoro Kubernetes.

Altre informazioni su queste funzionalità sono disponibili in [procedure consigliate per la protezione del carico di lavoro usando il controllo Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

Il Centro sicurezza offre altre funzionalità di sicurezza del contenitore se si Abilita Azure Defender. In particolare:

- Analizza i registri dei contenitori per individuare le vulnerabilità con [Azure Defender per i registri contenitori](defender-for-container-registries-introduction.md)
- Ottenere avvisi di rilevamento delle minacce in tempo reale per i cluster K8s [Azure Defender per Kubernetes](defender-for-kubernetes-introduction.md)

> [!TIP]
> Per un elenco di *tutte le* raccomandazioni sulla sicurezza che potrebbero essere visualizzate per i cluster e i nodi Kubernetes, vedere la [sezione calcolo](recommendations-reference.md#recs-compute) della tabella di riferimento per le raccomandazioni.



## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibilità generale (GA)|
|Prezzi:|Livello gratuito|
|Autorizzazioni e ruoli obbligatori:|**Proprietario** o **amministratore della sicurezza** per modificare un'assegnazione<br>**Lettore** per visualizzare le raccomandazioni|
|Requisiti dell'ambiente:|Kubernetes v 1.14 (o versione successiva) è obbligatorio<br>Nessuna risorsa PodSecurityPolicy (modello PSP precedente) nei cluster<br>I nodi di Windows non sono supportati|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||


## <a name="set-up-your-workload-protection"></a>Configurare la protezione del carico di lavoro

Il Centro sicurezza di Azure include un bundle di raccomandazioni disponibili quando è stato installato il **componente aggiuntivo criteri di Azure per Kubernetes**.

### <a name="step-1-deploy-the-add-on"></a>Passaggio 1: distribuire il componente aggiuntivo

Per configurare le raccomandazioni, installare il  **componente aggiuntivo criteri di Azure per Kubernetes**. 

- È possibile distribuire automaticamente questo componente aggiuntivo come descritto in [abilitare il provisioning automatico dell'agente e delle estensioni log Analytics](security-center-enable-data-collection.md#auto-provision-mma). Con il provisioning automatico attivato per il componente aggiuntivo, l'estensione viene abilitata per impostazione predefinita in tutti i cluster esistenti e futuri che soddisfano i requisiti dell'installazione.

    :::image type="content" source="media/defender-for-kubernetes-usage/policy-add-on-auto-provision.png" alt-text="Uso dello strumento di provisioning automatico del Centro sicurezza per installare il componente aggiuntivo dei criteri per Kubernetes":::

- Per distribuire manualmente il componente aggiuntivo:

    1. Dalla pagina raccomandazioni cercare la raccomandazione "il **componente aggiuntivo criteri di Azure per Kubernetes deve essere installato e abilitato nei cluster**". 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="Raccomandazione * * il componente aggiuntivo criteri di Azure per Kubernetes deve essere installato e abilitato nei cluster * *":::

        > [!TIP]
        > La raccomandazione è inclusa in cinque diversi controlli di sicurezza e non è importante selezionare il passaggio successivo.

    1. Da uno dei controlli di sicurezza, selezionare la raccomandazione per visualizzare le risorse in cui è possibile installare il componente aggiuntivo.
    1. Selezionare il cluster pertinente e **correggere**.

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="Pagina dei dettagli delle raccomandazioni per * * il componente aggiuntivo criteri di Azure per Kubernetes deve essere installato e abilitato nei cluster * *":::

### <a name="step-2-view-and-configure-the-bundle-of-13-recommendations"></a>Passaggio 2: visualizzare e configurare il bundle di 13 raccomandazioni

1. Circa 30 minuti dopo che l'installazione del componente aggiuntivo è stata completata, il Centro sicurezza Mostra lo stato di integrità dei cluster per le seguenti raccomandazioni, ciascuna nel controllo di sicurezza pertinente, come illustrato:

    > [!TIP]
    > Alcune raccomandazioni includono parametri che devono essere personalizzati tramite criteri di Azure per usarli in modo efficace. Ad esempio, per trarre vantaggio dalla raccomandazione le **Immagini del contenitore devono essere distribuite solo da registri attendibili**, sarà necessario definire i registri attendibili.
    > 
    > Se non si immettono i parametri necessari per le raccomandazioni che richiedono la configurazione, i carichi di lavoro verranno visualizzati come non integri.

    | Nome raccomandazione                                                         | Controllo di sicurezza                         | Configurazione necessaria |
    |-----------------------------------------------------------------------------|------------------------------------------|------------------------|
    | È consigliabile applicare limiti per la CPU e la memoria dei contenitori                          | Proteggi le applicazioni da attacchi DDoS | No                     |
    | I contenitori con privilegi devono essere evitati                                     | Gestire l'accesso e le autorizzazioni            | No                     |
    | Per i contenitori deve essere imposto il file system radice non modificabile (di sola lettura)     | Gestire l'accesso e le autorizzazioni            | No                     |
    | È consigliabile evitare i contenitori con escalation dei privilegi                       | Gestire l'accesso e le autorizzazioni            | No                     |
    | È consigliabile evitare l'esecuzione di contenitori come utente radice                           | Gestire l'accesso e le autorizzazioni            | No                     |
    | I contenitori che condividono spazi dei nomi host sensibili devono essere evitati              | Gestire l'accesso e le autorizzazioni            | No                     |
    | Per i contenitori è necessario applicare le funzionalità di Linux con privilegi minimi       | Gestire l'accesso e le autorizzazioni            | **Sì**                |
    | L'utilizzo dei montaggi dei volumi HostPath dei pod deve essere limitato a un elenco noto    | Gestire l'accesso e le autorizzazioni            | **Sì**                |
    | I contenitori devono essere in ascolto solo sulle porte consentite                              | Limitare l'accesso alla rete non autorizzato     | **Sì**                |
    | I servizi devono essere in ascolto solo sulle porte consentite                                | Limitare l'accesso alla rete non autorizzato     | **Sì**                |
    | L'utilizzo della rete host e delle porte deve essere limitato                     | Limita l'accesso non autorizzato alla rete     | **Sì**                |
    | La sovrascrittura o la disabilitazione del profilo AppArmor dei contenitori deve essere limitata | Correggere le configurazioni di sicurezza        | **Sì**                |
    | Le immagini del contenitore devono essere distribuite solo da registri attendibili            | Correggere le vulnerabilità                | **Sì**                |
    |||


1. Per le raccomandazioni con i parametri devono essere personalizzati, impostare i parametri:

    1. Dal menu del Centro sicurezza selezionare **criteri di sicurezza**.
    1. Selezionare la sottoscrizione pertinente.
    1. Nella sezione **criteri predefiniti del Centro sicurezza** selezionare **Visualizza criteri validi**.
    1. Selezionare "ASC default".
    1. Aprire la scheda **parametri** e modificare i valori come richiesto.
    1. Selezionare **Verifica e salva**.
    1. Selezionare **Salva**.


1. Per applicare una qualsiasi delle raccomandazioni, 

    1. Aprire la pagina dei dettagli delle raccomandazioni e selezionare **Nega**:

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="Opzione Deny per il parametro di criteri di Azure":::

        Verrà aperto il riquadro in cui è stato impostato l'ambito. 

    1. Dopo aver impostato l'ambito, selezionare **modifica per negare**.

1. Per vedere quali consigli si applicano ai cluster:

    1. Aprire la pagina [inventario asset](asset-inventory.md) del Centro sicurezza e usare il filtro del tipo di risorsa per i **Servizi Kubernetes**.

    1. Selezionare un cluster per esaminare ed esaminare le indicazioni disponibili disponibili. 

1. Quando si visualizza una raccomandazione dal set di protezione del carico di lavoro, verrà visualizzato il numero di Pod interessati ("componenti Kubernetes") elencati insieme al cluster. Per un elenco di Pod specifici, selezionare il cluster e quindi fare clic su **azione**.

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="Visualizzazione dei Pod interessati per una raccomandazione K8s"::: 

1. Per testare l'applicazione, usare le due distribuzioni di Kubernetes seguenti:

    - Uno è per una distribuzione integra, conforme al bundle di consigli sulla protezione del carico di lavoro.
    - L'altro riguarda una distribuzione non integra, non conforme a una *qualsiasi* delle raccomandazioni.

    Distribuire i file con estensione YAML di esempio così come sono o usarli come riferimento per correggere il proprio carico di lavoro (passaggio VIII)  


## <a name="healthy-deployment-example-yaml-file"></a>Esempio di distribuzione integro file con estensione YAML

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: healthyClusterRegistry.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>Esempio di distribuzione non integro. file YAML

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-unhealthy-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:      
      labels:
        app: nginx
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come configurare la protezione del carico di lavoro Kubernetes. 

Per altri materiali correlati, vedere le pagine seguenti: 

- [Raccomandazioni del Centro sicurezza per il calcolo](recommendations-reference.md#recs-compute)
- [Avvisi per il livello del cluster AKS](alerts-reference.md#alerts-akscluster)
- [Avvisi per il livello host del contenitore](alerts-reference.md#alerts-containerhost)
