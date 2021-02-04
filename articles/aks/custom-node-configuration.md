---
title: Personalizzare la configurazione dei nodi per i pool di nodi del servizio Kubernetes di Azure (AKS) (anteprima)
description: Informazioni su come personalizzare la configurazione nei nodi del cluster di Azure Kubernetes Service (AKS) e nei pool di nodi.
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 589081149d08983d3cd5a4a8822873f5a6cfca0e
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99559429"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>Personalizzare la configurazione dei nodi per i pool di nodi del servizio Kubernetes di Azure (AKS) (anteprima)

La personalizzazione della configurazione del nodo consente di configurare o ottimizzare le impostazioni del sistema operativo o i parametri kubelet in modo che corrispondano alle esigenze dei carichi di lavoro. Quando si crea un cluster AKS o si aggiunge un pool di nodi al cluster, è possibile personalizzare un subset di impostazioni di sistema operativo e kubelet di uso comune. Per configurare le impostazioni oltre questo subset, [usare un daemon impostato per personalizzare le configurazioni necessarie senza perdere il supporto di AKS per i nodi](support-policies.md#shared-responsibility).

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>Registrare la `CustomNodeConfigPreview` funzionalità di anteprima

Per creare un cluster AKS o un pool di nodi in grado di personalizzare i parametri o le impostazioni del sistema operativo kubelet, è necessario abilitare il `CustomNodeConfigPreview` flag funzionalità per la sottoscrizione.

Registrare il `CustomNodeConfigPreview` flag funzionalità usando il comando [AZ feature Register][az-feature-register] , come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. Verificare lo stato della registrazione usando il comando [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft. servizio contenitore* usando il comando [AZ provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando aks-preview

Per creare un cluster del servizio contenitore di Azure o un pool di nodi in grado di personalizzare i parametri o le impostazioni del sistema operativo kubelet, è necessaria l'ultima estensione dell'interfaccia della riga di comando *di* Azure. Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] . In alternativa, installare gli eventuali aggiornamenti disponibili usando il comando [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>Usare la configurazione personalizzata del nodo

### <a name="kubelet-custom-configuration"></a>Configurazione personalizzata di Kubelet

I parametri Kubelet supportati e i valori accettati sono elencati di seguito.

| Parametro | Valori consentiti/intervallo | Predefinito | Descrizione |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | nessuno, statico | Nessuno | Il criterio statico consente ai contenitori in [Pod garantiti](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) con CPU di tipo Integer di accedere a CPU esclusive nel nodo. |
| `cpuCfsQuota` | true, false | true |  Abilitare/disabilitare l'imposizione della quota CFS CPU per i contenitori che specificano i limiti della CPU. | 
| `cpuCfsQuotaPeriod` | Intervallo in millisecondi (MS) | `100ms` | Imposta il valore del periodo di quota di CPU CFS. | 
| `imageGcHighThreshold` | 0-100 | 85 | Percentuale di utilizzo del disco dopo il quale l'immagine Garbage Collection viene sempre eseguita. Utilizzo minimo del disco **che attiverà** Garbage Collection. Per disabilitare Garbage Collection immagine, impostare su 100. | 
| `imageGcLowThreshold` | 0-100, non superiore a `imageGcHighThreshold` | 80 | Percentuale di utilizzo del disco prima del quale l'immagine Garbage Collection non viene mai eseguita. Utilizzo minimo del disco che **può** attivare Garbage Collection. |
| `topologyManagerPolicy` | None, best-effort, Restricted, Single-NUMA-node | Nessuno | Per ottimizzare l'allineamento del nodo NUMA, vedere [qui](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/). Solo kubernetes v 1.18 +. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | nessuno | Elenco consentiti di sysctl non sicuri o di modelli sysctl non sicuri. | 

### <a name="linux-os-custom-configuration"></a>Configurazione personalizzata del sistema operativo Linux

Le impostazioni del sistema operativo e i valori accettati supportati sono elencati di seguito.

#### <a name="file-handle-limits"></a>Limiti dell'handle di file

Quando si tratta di una grande quantità di traffico, è normale che il traffico che si sta servendo provenga da un numero elevato di file locali. È possibile modificare le impostazioni del kernel riportate di seguito e i limiti predefiniti per consentire la gestione di un numero maggiore, al costo di una memoria di sistema.

| Impostazione | Valori consentiti/intervallo | Predefinito | Descrizione |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192-12000500 | 709620 | Numero massimo di handle di file che verrà allocato dal kernel Linux, aumentando questo valore è possibile aumentare il numero massimo di file aperti consentiti. |
| `fs.inotify.max_user_watches` | 781250-2097152 | 1048576 | Numero massimo di orologi di file consentiti dal sistema. Ogni espressione di *controllo* è approssimativamente di 90 byte in un kernel a 32 bit e approssimativamente 160 byte in un kernel a 64 bit. | 
| `fs.aio-max-nr` | 65536-6553500 | 65536 | Il numero AIO-Nr indica il numero corrente di sistema di richieste io asincrone. AIO-max-Nr consente di modificare il valore massimo di AIO-Nr che può raggiungere. |
| `fs.nr_open` | 8192-20000500 | 1048576 | Numero massimo di handle di file che possono essere allocati da un processo. |


#### <a name="socket-and-network-tuning"></a>Socket e ottimizzazione di rete

Per i nodi dell'agente, che devono gestire un numero molto elevato di sessioni simultanee, è possibile usare il subset di opzioni TCP e di rete seguenti che è possibile modificare per ogni pool di nodi. 

| Impostazione | Valori consentiti/intervallo | Predefinito | Descrizione |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096-3240000 | 16384 | Numero massimo di richieste di connessione che possono essere accodate per ogni socket di ascolto specificato. Limite massimo per il valore del parametro di backlog passato alla funzione [Listen (2)](http://man7.org/linux/man-pages/man2/listen.2.html) . Se l'argomento del backlog è maggiore di `somaxconn` , viene troncato automaticamente a questo limite.
| `net.core.netdev_max_backlog` | 1000-3240000 | 1000 | Numero massimo di pacchetti, in coda sul lato INPUT, quando l'interfaccia riceve i pacchetti più velocemente di quanto il kernel possa elaborarli. |
| `net.core.rmem_max` | 212992-134217728 | 212992 | Dimensioni massime del buffer del socket di ricezione in byte. |
| `net.core.wmem_max` | 212992-134217728 | 212992 | Dimensioni massime del buffer del socket di invio in byte. | 
| `net.core.optmem_max` | 20480-4194304 | 20480 | Dimensioni massime del buffer ausiliario (opzione buffer di memoria) consentita per socket. L'opzione socket Memory viene utilizzata in alcuni casi per archiviare strutture aggiuntive relative all'utilizzo del socket. | 
| `net.ipv4.tcp_max_syn_backlog` | 128-3240000 | 16384 | Numero massimo di richieste di connessione in coda che non hanno ancora ricevuto un riconoscimento dal client di connessione. Se questo numero viene superato, il kernel inizierà a rilasciare le richieste. |
| `net.ipv4.tcp_max_tw_buckets` | 8000-1440000 | 32768 | Numero massimo di `timewait` Socket utilizzati simultaneamente dal sistema. Se questo numero viene superato, il socket di tempo di attesa viene immediatamente eliminato e viene visualizzato un avviso. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | Il periodo di tempo in cui una connessione orfana (a cui non viene più fatto riferimento da alcuna applicazione) rimarrà nello stato FIN_WAIT_2 prima che venga interrotta all'estremità locale. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | Frequenza con cui TCP Invia `keepalive` messaggi quando `keepalive` è abilitato. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | Il numero di `keepalive` Probe inviati da TCP, fino a quando non decide che la connessione è interruppe. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | Frequenza di invio dei probe. Moltiplicato per `tcp_keepalive_probes` il tempo per terminare una connessione che non risponde, dopo l'avvio dei probe. | 
| `net.ipv4.tcp_tw_reuse` | 0 o 1 | 0 | Consente a di riutilizzare `TIME-WAIT` i socket per le nuove connessioni quando è sicuro dal punto di vista del protocollo. | 
| `net.ipv4.ip_local_port_range` | Primo: 1024-60999 e ultimo: 32768-65000] | Primo: 32768 e ultimo: 60999 | Intervallo di porte locali utilizzato dal traffico TCP e UDP per scegliere la porta locale. Costituito da due numeri: il primo numero è la prima porta locale consentita per il traffico TCP e UDP sul nodo Agent, il secondo è l'ultimo numero di porta locale. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128-80000 | 4096 | Numero minimo di voci che possono trovarsi nella cache ARP. L'operazione di Garbage Collection non verrà attivata se il numero di voci è inferiore a questa impostazione. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512-90000 | 8192 | Numero massimo di voci soft che possono trovarsi nella cache ARP. Questa impostazione è probabilmente la più importante, perché ARP Garbage Collection verrà attivato circa 5 secondi dopo il raggiungimento di questo limite massimo. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024-100000 | 16384 | Numero massimo di voci nella cache ARP. |
| `net.netfilter.nf_conntrack_max` | 131072-589824 | 131072 | `nf_conntrack` è un modulo che tiene traccia delle voci di connessione per NAT in Linux. Il `nf_conntrack` modulo usa una tabella hash per registrare il record di *connessione stabilito* del protocollo TCP. `nf_conntrack_max` numero massimo di nodi nella tabella hash, ovvero il numero massimo di connessioni supportate dal `nf_conntrack` modulo o le dimensioni della tabella di rilevamento della connessione. | 
| `net.netfilter.nf_conntrack_buckets` | 65536-147456 | 65536 | `nf_conntrack` è un modulo che tiene traccia delle voci di connessione per NAT in Linux. Il `nf_conntrack` modulo usa una tabella hash per registrare il record di *connessione stabilito* del protocollo TCP. `nf_conntrack_buckets` dimensioni della tabella hash. | 

#### <a name="worker-limits"></a>Limiti del ruolo di lavoro

Analogamente ai limiti del descrittore di file, il numero di thread di lavoro o thread che un processo può creare è limitato dall'impostazione del kernel e dai limiti utente. Il limite utente per AKS è illimitato. 

| Impostazione | Valori consentiti/intervallo | Predefinito | Descrizione |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | I processi possono attivare i thread di lavoro. Il numero massimo di thread che è possibile creare è impostato con l'impostazione kernel `kernel.threads-max` . | 

#### <a name="virtual-memory"></a>Memoria virtuale

Le impostazioni riportate di seguito possono essere usate per ottimizzare il funzionamento del sottosistema di memoria virtuale (VM) del kernel Linux e `writeout` dei dati dirty su disco.

| Impostazione | Valori consentiti/intervallo | Predefinito | Descrizione |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530-262144 | 65530 | Questo file contiene il numero massimo di aree mappa di memoria che possono essere presenti in un processo. Le aree della mappa di memoria vengono utilizzate come effetto collaterale della chiamata di `malloc` , direttamente da `mmap` , `mprotect` e e `madvise` anche durante il caricamento di librerie condivise. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | Questo valore percentuale controlla la tendenza del kernel a recuperare la memoria usata per la memorizzazione nella cache degli oggetti directory e inode. |
| `vm.swappiness` | 0 - 100 | 60 | Questo controllo viene usato per definire l'aggressività con cui il kernel scambierà le pagine di memoria. I valori più elevati aumentano l'aggressività e i valori più bassi diminuiscono la quantità di spazio di swapping. Il valore 0 indica al kernel di non avviare lo scambio fino a quando la quantità di pagine libere e con backup di file è inferiore al limite massimo in una zona. | 
| `swapFileSizeMB` | 1 MB: dimensioni del [disco temporaneo](../virtual-machines/managed-disks-overview.md#temporary-disk) (/dev/sdb) | nessuno | SwapFileSizeMB specifica la dimensione in MB di un file di scambio che verrà creata nei nodi dell'agente da questo pool di nodi. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [Transparent hugepage](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) è una funzionalità di kernel Linux progettata per migliorare le prestazioni grazie a un uso più efficiente dell'hardware di mapping della memoria del processore. Quando è abilitata, il kernel tenta di allocare `hugepages` ogni volta che è possibile e qualsiasi processo di Linux riceverà le pagine da 2 MB se l' `mmap` area è allineata naturalmente a 2 MB. In alcuni casi `hugepages` , quando sono abilitati a livello di sistema, è possibile che le applicazioni finiscano di allocare più risorse di memoria. Un'applicazione può `mmap` avere un'area di grandi dimensioni, ma solo 1 byte, in questo caso una pagina da 2 MB potrebbe essere allocata anziché una pagina 4K per nessun motivo valido. Questo scenario è il motivo per cui è possibile disabilitare l' `hugepages` intero sistema o solo in `MADV_HUGEPAGE madvise` aree. | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | Questo valore determina se il kernel deve usare in modo aggressivo la compattazione della memoria per rendere più `hugepages` disponibile. | 

> [!IMPORTANT]
> Per semplificare la ricerca e la leggibilità, le impostazioni del sistema operativo vengono visualizzate in questo documento in base al nome, ma devono essere aggiunte al file JSON di configurazione o all'API AKS usando la convenzione per la [CamelCase delle maiuscole](/dotnet/standard/design-guidelines/capitalization-conventions).

Creare un `kubeletconfig.json` file con il contenuto seguente:

```json
{
 "cpuManagerPolicy": "static",
 "cpuCfsQuota": true,
 "cpuCfsQuotaPeriod": "200ms",
 "imageGcHighThreshold": 90,
 "imageGcLowThreshold": 70,
 "topologyManagerPolicy": "best-effort",
 "allowedUnsafeSysctls": [
  "kernel.msg*",
  "net.*"
],
 "failSwapOn": false
}
```
Creare un `linuxosconfig.json` file con il contenuto seguente:

```json
{
 "transparentHugePageEnabled": "madvise",
 "transparentHugePageDefrag": "defer+madvise",
 "swapFileSizeMB": 1500,
 "sysctls": {
  "netCoreSomaxconn": 163849,
  "netIpv4TcpTwReuse": true,
  "netIpv4IpLocalPortRange": "32000 60000"
 }
}
```

Creare un nuovo cluster specificando le configurazioni di kubelet e del sistema operativo usando i file JSON creati nel passaggio precedente. 

> [!NOTE]
> Quando si crea un cluster, è possibile specificare la configurazione kubelet, la configurazione del sistema operativo o entrambi. Se durante la creazione di un cluster si specifica una configurazione, viene applicata solo la configurazione dei nodi nel pool iniziale del nodo. Le impostazioni non configurate nel file JSON manterranno il valore predefinito.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

Aggiungere un nuovo pool di nodi specificando i parametri Kubelet usando il file JSON creato.

> [!NOTE]
> Quando si aggiunge un pool di nodi a un cluster esistente, è possibile specificare la configurazione kubelet, la configurazione del sistema operativo o entrambi. Se si specifica una configurazione quando si aggiunge un pool di nodi, viene applicata solo la configurazione dei nodi nel nuovo pool di nodi. Le impostazioni non configurate nel file JSON manterranno il valore predefinito.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [su come configurare il cluster AKS](cluster-configuration.md).
- Informazioni su come [aggiornare le immagini dei nodi](node-image-upgrade.md) nel cluster.
- Per informazioni su come aggiornare il cluster alla versione più recente di Kubernetes, vedere [Aggiornare un cluster del servizio Azure Kubernetes](upgrade-cluster.md).
- Per trovare le risposte ad alcune domande comuni su AKS, vedere l'elenco delle [domande frequenti su AKS](faq.md).

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-master-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why
