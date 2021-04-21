---
title: Personalizzare la configurazione del nodo per servizio Azure Kubernetes pool di nodi (anteprima)
description: Informazioni su come personalizzare la configurazione nei servizio Azure Kubernetes cluster e nei pool di nodi.
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c2173118b58ca92d69286fb36014872c19058bd6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779976"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>Personalizzare la configurazione del nodo per servizio Azure Kubernetes pool di nodi (anteprima)

La personalizzazione della configurazione del nodo consente di configurare o ottimizzare le impostazioni del sistema operativo o i parametri kubelet in base alle esigenze dei carichi di lavoro. Quando si crea un cluster del servizio App Ku-kubelet o si aggiunge un pool di nodi al cluster, è possibile personalizzare un subset di impostazioni del sistema operativo e kubelet di uso comune. Per configurare le impostazioni oltre questo subset, usare un [set di daemon](support-policies.md#shared-responsibility)per personalizzare le configurazioni necessarie senza perdere il supporto del servizio AKS per i nodi .

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>Registrare la `CustomNodeConfigPreview` funzionalità di anteprima

Per creare un cluster o un pool di nodi del servizio Azure Kubelet in grado di personalizzare i parametri kubelet o le impostazioni del sistema operativo, è necessario abilitare il flag di funzionalità `CustomNodeConfigPreview` nella sottoscrizione.

Registrare `CustomNodeConfigPreview` il flag di funzionalità usando il comando [az feature register,][az-feature-register] come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. Verificare lo stato della registrazione usando il [comando az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

Al termine, aggiornare la registrazione del provider *di risorse Microsoft.ContainerService* usando il [comando az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando aks-preview

Per creare un cluster del servizio Azure Kubelet o un pool di nodi in grado di personalizzare i parametri kubelet o le impostazioni del sistema operativo, è necessaria l'estensione dell'interfaccia della riga di comando di Azure *aks-preview* più recente. Installare *l'estensione dell'interfaccia della* riga di comando di Azure aks-preview usando il [comando az extension add.][az-extension-add] In caso di installazione di eventuali aggiornamenti disponibili, usare [il comando az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>Usare la configurazione del nodo personalizzata

### <a name="kubelet-custom-configuration"></a>Configurazione personalizzata di Kubelet

I parametri kubelet supportati e i valori accettati sono elencati di seguito.

| Parametro | Valori/intervallo consentiti | Predefinito | Descrizione |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | none, static | Nessuno | I criteri statici consentono ai contenitori nei [pod garantiti con](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) richieste di CPU intere di accedere a CPU esclusive nel nodo. |
| `cpuCfsQuota` | true, false | true |  Abilitare o disabilitare l'imposizione della quota cfS della CPU per i contenitori che specificano i limiti della CPU. | 
| `cpuCfsQuotaPeriod` | Intervallo in millisecondi (ms) | `100ms` | Imposta il valore del periodo di quota della CPU CFS. | 
| `imageGcHighThreshold` | 0-100 | 85 | Percentuale di utilizzo del disco dopo la quale viene sempre eseguita l'operazione di Garbage Collection per le immagini. Utilizzo minimo del disco che **attiverà** l'operazione di Garbage Collection. Per disabilitare l'operazione di Garbage Collection per immagini, impostare su 100. | 
| `imageGcLowThreshold` | 0-100, non superiore a `imageGcHighThreshold` | 80 | Percentuale di utilizzo del disco prima della quale l'operazione di Garbage Collection per immagini non viene mai eseguita. Utilizzo minimo del disco che **può attivare** l'operazione di Garbage Collection. |
| `topologyManagerPolicy` | none, best-effort, restricted, single-numa-node | Nessuno | Ottimizzare l'allineamento dei nodi NUMA. Per altre informazioni, [vedere qui](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/). Solo kubernetes v1.18+. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | nessuno | Elenco di sysctls non sicuri o modelli sysctl non sicuri. | 

### <a name="linux-os-custom-configuration"></a>Configurazione personalizzata del sistema operativo Linux

Le impostazioni del sistema operativo supportate e i valori accettati sono elencati di seguito.

#### <a name="file-handle-limits"></a>Limiti relativi all'handle di file

Quando si serve una grande quantità di traffico, è comune che il traffico che si sta servendo proviene da un numero elevato di file locali. È possibile modificare le impostazioni del kernel e i limiti predefiniti seguenti per gestire più risorse, a s costo di una certa quantità di memoria di sistema.

| Impostazione | Valori consentiti/intervallo | Predefinito | Descrizione |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192 - 12000500 | 709620 | Numero massimo di handle di file allocati dal kernel Linux, aumentando questo valore è possibile aumentare il numero massimo di file aperti consentiti. |
| `fs.inotify.max_user_watches` | 781250 - 2097152 | 1048576 | Numero massimo di espressioni di controllo file consentite dal sistema. Ogni *orologio* è di circa 90 byte in un kernel a 32 bit e circa 160 byte in un kernel a 64 bit. | 
| `fs.aio-max-nr` | 65536 - 6553500 | 65536 | L'aio-nr mostra il numero corrente di richieste I/O asincrone a livello di sistema. aio-max-nr consente di modificare il valore massimo che aio-nr può aumentare fino a. |
| `fs.nr_open` | 8192 - 20000500 | 1048576 | Numero massimo di handle di file che un processo può allocare. |


#### <a name="socket-and-network-tuning"></a>Ottimizzazione di socket e rete

Per i nodi agente, che dovrebbero gestire un numero molto elevato di sessioni simultanee, è possibile usare il subset di opzioni TCP e di rete seguenti che è possibile modificare per ogni pool di nodi. 

| Impostazione | Valori/intervallo consentiti | Predefinito | Descrizione |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096 - 3240000 | 16384 | Numero massimo di richieste di connessione che possono essere accodati per qualsiasi socket di ascolto specificato. Limite superiore per il valore del parametro backlog passato alla funzione [listen(2).](http://man7.org/linux/man-pages/man2/listen.2.html) Se l'argomento backlog è maggiore di , viene troncato automaticamente `somaxconn` a questo limite.
| `net.core.netdev_max_backlog` | 1000 - 3240000 | 1000 | Numero massimo di pacchetti, accodati sul lato INPUT, quando l'interfaccia riceve i pacchetti più velocemente di quanto il kernel possa elaborarli. |
| `net.core.rmem_max` | 212992 - 134217728 | 212992 | Dimensione massima in byte del buffer del socket di ricezione. |
| `net.core.wmem_max` | 212992 - 134217728 | 212992 | Dimensione massima in byte del buffer del socket di trasmissione. | 
| `net.core.optmem_max` | 20480 - 4194304 | 20480 | Dimensioni massime del buffer accessorio (buffer di memoria delle opzioni) consentite per ogni socket. La memoria delle opzioni socket viene usata in alcuni casi per archiviare strutture aggiuntive relative all'utilizzo del socket. | 
| `net.ipv4.tcp_max_syn_backlog` | 128 - 3240000 | 16384 | Numero massimo di richieste di connessione in coda che non hanno ancora ricevuto un riconoscimento dal client di connessione. Se questo numero viene superato, il kernel inizierà a eliminare le richieste. |
| `net.ipv4.tcp_max_tw_buckets` | 8000 - 1440000 | 32768 | Numero massimo di `timewait` socket mantenuti simultaneamente dal sistema. Se questo numero viene superato, il socket di attesa viene eliminato immediatamente e viene visualizzato un avviso. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | Periodo di tempo per cui una connessione orfana (a cui non fa più riferimento alcuna applicazione) rimarrà nello stato FIN_WAIT_2 prima che venga interrotta all'estremità locale. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | Frequenza con cui TCP invia `keepalive` messaggi quando `keepalive` è abilitato. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | Numero di `keepalive` probe inviati da TCP, fino a quando non viene deciso che la connessione è interrotta. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | Frequenza di invio dei probe. Moltiplicato per rappresenta il tempo necessario per kill di una connessione che `tcp_keepalive_probes` non risponde, dopo l'avvio dei probe. | 
| `net.ipv4.tcp_tw_reuse` | 0 o 1 | 0 | Consente di riutilizzare i socket per le nuove `TIME-WAIT` connessioni quando è sicuro dal punto di vista del protocollo. | 
| `net.ipv4.ip_local_port_range` | Primo: 1024 - 60999 e Ultimo: 32768 - 65000] | Primo: 32768 e Ultimo: 60999 | Intervallo di porte locale utilizzato dal traffico TCP e UDP per scegliere la porta locale. Costituito da due numeri: il primo numero è la prima porta locale consentita per il traffico TCP e UDP nel nodo dell'agente, il secondo è l'ultimo numero di porta locale. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128 - 80000 | 4096 | Numero minimo di voci che possono essere presenti nella cache ARP. L'operazione di Garbage Collection non verrà attivata se il numero di voci è inferiore a questa impostazione. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512 - 90000 | 8192 | Numero massimo di voci che possono essere presenti nella cache ARP. Questa impostazione è probabilmente la più importante, perché l'operazione di Garbage Collection ARP verrà attivata circa 5 secondi dopo il raggiungimento di questo valore massimo. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024 - 100000 | 16384 | Numero massimo rigido di voci nella cache ARP. |
| `net.netfilter.nf_conntrack_max` | 131072 - 589824 | 131072 | `nf_conntrack` è un modulo che tiene traccia delle voci di connessione per NAT in Linux. Il `nf_conntrack` modulo usa una tabella hash per registrare il record di *connessione* stabilito del protocollo TCP. `nf_conntrack_max` è il numero massimo di nodi nella tabella hash, ad esempio il numero massimo di connessioni supportate dal modulo o le `nf_conntrack` dimensioni della tabella di rilevamento delle connessioni. | 
| `net.netfilter.nf_conntrack_buckets` | 65536 - 147456 | 65536 | `nf_conntrack` è un modulo che tiene traccia delle voci di connessione per NAT in Linux. Il `nf_conntrack` modulo usa una tabella hash per registrare il record di *connessione* stabilito del protocollo TCP. `nf_conntrack_buckets` è la dimensione della tabella hash. | 

#### <a name="worker-limits"></a>Limiti del ruolo di lavoro

Analogamente ai limiti del descrittore di file, il numero di thread o di thread che un processo può creare è limitato sia da un'impostazione del kernel che da limiti utente. Il limite di utenti nel server del server del supporto di Microsoft Windows è illimitato. 

| Impostazione | Valori/intervallo consentiti | Predefinito | Descrizione |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | I processi possono creare thread di lavoro. Il numero massimo di tutti i thread che è possibile creare viene impostato con l'impostazione del kernel `kernel.threads-max` . | 

#### <a name="virtual-memory"></a>Memoria virtuale

Le impostazioni seguenti possono essere usate per ottimizzare il funzionamento del sottosistema di memoria virtuale (VM) del kernel Linux e dei `writeout` dati dirty su disco.

| Impostazione | Valori/intervallo consentiti | Predefinito | Descrizione |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530 - 262144 | 65530 | Questo file contiene il numero massimo di aree della mappa di memoria che un processo può avere. Le aree della mappa della memoria vengono usate come effetto collaterale della chiamata a , direttamente da , e , e `malloc` anche durante il caricamento di `mmap` `mprotect` `madvise` librerie condivise. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | Questo valore percentuale controlla la tendenza del kernel a recuperare la memoria, usata per la memorizzazione nella cache degli oggetti directory e inode. |
| `vm.swappiness` | 0 - 100 | 60 | Questo controllo viene usato per definire l'aggressività con cui il kernel scambierà le pagine di memoria. I valori più elevati aumentano l'aggressività, i valori inferiori riducono la quantità di scambio. Il valore 0 indica al kernel di non avviare lo scambio fino a quando la quantità di pagine gratuite e supportate da file non è inferiore al livello massimo in una zona. | 
| `swapFileSizeMB` | 1 MB - Dimensioni del [disco temporaneo](../virtual-machines/managed-disks-overview.md#temporary-disk) (/dev/sdb) | nessuno | SwapFileSizeMB specifica che le dimensioni in MB di un file di scambio verranno create nei nodi dell'agente da questo pool di nodi. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [Transparent Hugepages è](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) una funzionalità del kernel Linux progettata per migliorare le prestazioni grazie all'uso più efficiente dell'hardware di mapping della memoria del processore. Se abilitata, il kernel tenta di eseguire l'allocazione quando possibile e qualsiasi processo Linux riceverà pagine di 2 MB se l'area è allineata naturalmente a `hugepages` `mmap` 2 MB. In alcuni casi in cui `hugepages` sono abilitati a livello di sistema, le applicazioni possono terminare con l'allocazione di più risorse di memoria. Un'applicazione può essere un'area di grandi dimensioni, ma ne tocca solo 1 byte, in questo caso potrebbe essere allocata una pagina di 2 MB anziché una pagina `mmap` di 4.000 per nessun motivo. Questo scenario è il motivo per cui è possibile disabilitare a livello di `hugepages` sistema o solo all'interno delle `MADV_HUGEPAGE madvise` aree. | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | Questo valore controlla se il kernel deve usare in modo aggressivo la compattazione della memoria per rendere più `hugepages` disponibile. | 

> [!IMPORTANT]
> Per semplificare la ricerca e la leggibilità, le impostazioni del sistema operativo vengono visualizzate in questo documento in base al nome, ma devono essere aggiunte al file JSON di configurazione o all'API del servizio Web Del servizio App Usando la convenzione di capitalizzazione [camelCase](/dotnet/standard/design-guidelines/capitalization-conventions).

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

Creare un nuovo cluster specificando le configurazioni kubelet e del sistema operativo usando i file JSON creati nel passaggio precedente. 

> [!NOTE]
> Quando si crea un cluster, è possibile specificare la configurazione kubelet, la configurazione del sistema operativo o entrambi. Se si specifica una configurazione durante la creazione di un cluster, solo i nodi nel pool di nodi iniziale avranno tale configurazione applicata. Tutte le impostazioni non configurate nel file JSON manterranno il valore predefinito.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

Aggiungere un nuovo pool di nodi specificando i parametri Kubelet usando il file JSON creato.

> [!NOTE]
> Quando si aggiunge un pool di nodi a un cluster esistente, è possibile specificare la configurazione kubelet, la configurazione del sistema operativo o entrambe. Se si specifica una configurazione quando si aggiunge un pool di nodi, solo i nodi nel nuovo pool di nodi avranno tale configurazione applicata. Tutte le impostazioni non configurate nel file JSON manterranno il valore predefinito.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [su come configurare il cluster del servizio Web Diaks.](cluster-configuration.md)
- Informazioni su [come aggiornare le immagini dei](node-image-upgrade.md) nodi nel cluster.
- Per informazioni su come aggiornare il cluster alla versione più recente di Kubernetes, vedere [Aggiornare un cluster del servizio Azure Kubernetes](upgrade-cluster.md).
- Per trovare le risposte ad alcune domande comuni su AKS, vedere l'elenco delle [domande frequenti su AKS](faq.md).

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why