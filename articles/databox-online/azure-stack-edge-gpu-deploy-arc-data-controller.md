---
title: Distribuire un controller di dati di Azure Arc sul dispositivo GPU Pro Azure Stack Edge | Microsoft Docs
description: Viene descritto come distribuire un controller di dati di Azure Arc e i servizi dati di Azure sul dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 57633df8c6482a9b0645813519991282bdbf22c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102633513"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Distribuire i servizi dati di Azure sul dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Questo articolo descrive il processo di creazione di un controller di dati di Azure Arc e quindi la distribuzione di servizi dati di Azure sul dispositivo GPU Azure Stack Edge Pro. 

Il controller dati di Azure Arc è il piano di controllo locale che Abilita i servizi dati di Azure negli ambienti gestiti dal cliente. Dopo aver creato il controller di dati di Azure Arc nel cluster Kubernetes in esecuzione nel dispositivo Azure Stack Edge Pro, è possibile distribuire i servizi dati di Azure, ad esempio SQL Istanza gestita (anteprima) sul controller di dati.

La procedura per creare un controller di dati e quindi distribuire un Istanza gestita SQL prevede l'uso di PowerShell e di `kubectl` uno strumento nativo che fornisce l'accesso da riga di comando al cluster Kubernetes nel dispositivo.


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. È possibile accedere a un dispositivo Azure Stack Edge Pro ed è stato attivato il dispositivo come descritto in [attivare Azure stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

1. Il ruolo di calcolo è stato abilitato nel dispositivo. Un cluster Kubernetes è stato creato anche nel dispositivo quando è stato configurato il calcolo nel dispositivo in base alle istruzioni riportate in [configurare il calcolo nel dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-deploy-configure-compute.md).

1. L'endpoint dell'API Kubernetes è presente nella pagina **dispositivo** dell'interfaccia utente Web locale. Per altre informazioni, vedere le istruzioni in [ottenere l'endpoint dell'API Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. È possibile accedere a un client che si connetterà al dispositivo. 
    1. Questo articolo usa un sistema client Windows che esegue PowerShell 5,0 o versione successiva per accedere al dispositivo. È possibile usare qualsiasi altro client con un [sistema operativo supportato](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). 
    1. Installare `kubectl` nel client. Per la versione client:
        1. Identificare la versione del server Kubernetes installata nel dispositivo. Nell'interfaccia utente locale del dispositivo andare alla pagina **aggiornamenti software** . Prendere nota della **versione del server Kubernetes** in questa pagina.
        1. Scaricare un client che abbia al massimo una versione in meno rispetto al master. La versione del client, ma può condurre il master fino a una versione secondaria. Ad esempio, un Master v 1.3 dovrebbe funzionare con i nodi v 1.1, v 1.2 e v 1.3 e dovrebbe funzionare con i client v 1.2, v 1.3 e v 1.4. Per altre informazioni sulla versione del client Kubernetes, vedere [criteri di supporto per versione e sfasamento](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)della versione di Kubernetes.
    
1. Facoltativamente, [installare gli strumenti client per la distribuzione e la gestione di Azure Arc Enabled Data Services](../azure-arc/data/install-client-tools.md). Questi strumenti non sono obbligatori ma consigliati.  
1. Verificare che nel dispositivo siano disponibili risorse sufficienti per eseguire il provisioning di un controller dati e di un Istanza gestita SQL. Per il controller dati e un Istanza gestita SQL sono necessari almeno 16 GB di RAM e 4 core CPU. Per istruzioni dettagliate, vedere [i requisiti minimi per la distribuzione di Azure Arc Enabled Data Services](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements).


## <a name="configure-kubernetes-external-service-ips"></a>Configurare gli indirizzi IP del servizio esterno Kubernetes

1. Passare all'interfaccia utente Web locale del dispositivo e passare a **calcolo**.
1. Selezionare la rete abilitata per il calcolo. 

    ![Pagina di calcolo nell'interfaccia utente locale 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Assicurarsi di fornire altri tre indirizzi IP del servizio esterno Kubernetes (oltre agli indirizzi IP già configurati per altri servizi o contenitori esterni). Il controller dati utilizzerà due indirizzi IP del servizio e il terzo IP verrà usato durante la creazione di un Istanza gestita SQL. È necessario un IP per ogni servizio dati aggiuntivo da distribuire. 

    ![Pagina di calcolo nell'interfaccia utente locale 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. Applicare le impostazioni. questi nuovi indirizzi IP diverranno immediatamente effettivi in un cluster Kubernetes già esistente. 


## <a name="deploy-azure-arc-data-controller"></a>Distribuire il controller dati di Azure Arc

Prima di distribuire un controller di dati, è necessario creare uno spazio dei nomi.

### <a name="create-namespace"></a>Crea spazio dei nomi 

Creare un nuovo spazio dei nomi dedicato in cui si distribuirà il controller di dati. Verrà inoltre creato un utente e quindi verrà concesso all'utente l'accesso allo spazio dei nomi creato. 

> [!NOTE]
> Per i nomi di spazio dei nomi e utente, vengono applicate le [convenzioni di denominazione del sottodominio DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) .

1. [Connettersi all'interfaccia di PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Creare uno spazio dei nomi Digitare:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Creare un utente. Digitare: 

    `New-HcsKubernetesUser -UserName <User name>`

1. Un file di configurazione viene visualizzato in testo normale. Copiare questo file e salvarlo come file di *configurazione* . 

    > [!IMPORTANT]
    > Non salvare il file di configurazione come file con estensione *txt* , salvare il file senza alcuna estensione di file.

1. Il file di configurazione deve risiedere nella `.kube` cartella del profilo utente nel computer locale. Copiare il file nella cartella nel profilo utente.

    ![Percorso del file di configurazione nel client](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. Concedere all'utente l'accesso allo spazio dei nomi creato. Digitare: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Di seguito è riportato un esempio di output dei comandi precedenti. In questo esempio viene creato uno `myadstest` spazio dei nomi, un `myadsuser` utente e viene concesso all'utente l'accesso allo spazio dei nomi.
    
    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesNamespace -Namespace myadstest
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName myadsuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBD=======//snipped//=======VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: myadsuser
      name: myadsuser@kubernetes
    current-context: myadsuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: myadsuser
      user:
        client-certificate-data: LS0tLS1CRUdJTiBDRV=========//snipped//=====EE9PQotLS0kFURSBLRVktLS0tLQo=
    
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myadstest -UserName myadsuser
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS>
    ```
1. Aggiungere una voce DNS al file hosts nel sistema. 

    1. Eseguire blocco note come amministratore e aprire il `hosts` file che si trova in `C:\windows\system32\drivers\etc\hosts` . 
    2. Usare le informazioni salvate dalla pagina **dispositivo** nell'interfaccia utente locale (prerequisito) per creare la voce nel file degli host. 

        Ad esempio, copiare questo endpoint `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` per creare la voce seguente con l'indirizzo IP del dispositivo e il dominio DNS: 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Per verificare che sia possibile connettersi ai pod Kubernetes, avviare un prompt dei comandi o una sessione di PowerShell. Digitare:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
È ora possibile distribuire il controller dati e le applicazioni di Data Services nello spazio dei nomi, quindi visualizzare le applicazioni e i relativi log.

### <a name="create-data-controller"></a>Creare un controller dei dati

Il controller dati è una raccolta di Pod distribuiti nel cluster Kubernetes per fornire un'API, il servizio controller, il programma di avvio automatico e i dashboard e i database di monitoraggio. Seguire questa procedura per creare un controller di dati nel cluster Kubernetes esistente nel dispositivo Azure Stack Edge nello spazio dei nomi creato in precedenza.   

1. Raccogliere le informazioni seguenti che è necessario creare un controller di dati:

    
    |Colonna 1  |Colonna 2  |
    |---------|---------|
    |Nome controller dati     |Nome descrittivo per il controller dati. Ad esempio: `arctestdatacontroller`.         |
    |Nome utente del controller dati     |Qualsiasi nome utente per l'utente amministratore del controller di dati. Il nome utente e la password del controller dati vengono usati per eseguire l'autenticazione all'API del controller dei dati per eseguire le funzioni amministrative.          |
    |Password del controller dei dati     |Password per l'utente amministratore del controller di dati. Scegliere una password sicura e condividerla con solo quelle che devono disporre dei privilegi di amministratore del cluster.         |
    |Nome dello spazio dei nomi Kubernetes     |Nome dello spazio dei nomi Kubernetes in cui si desidera creare il controller di dati.         |
    |ID sottoscrizione di Azure     |GUID della sottoscrizione di Azure in cui si vuole creare la risorsa del controller dei dati in Azure.         |
    |Nome del gruppo di risorse di Azure     |Nome del gruppo di risorse in cui si vuole creare la risorsa del controller dei dati in Azure.         |
    |Località di Azure     |Località di Azure in cui verranno archiviati i metadati delle risorse del controller dati in Azure. Per un elenco delle aree disponibili, vedere infrastruttura globale/prodotti di Azure in base all'area.|


1. Connettersi all'interfaccia di PowerShell. Per creare il controller dati, digitare: 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Di seguito è riportato un esempio di output dei comandi precedenti.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    Per il completamento della distribuzione potrebbero essere necessari circa 5 minuti.

    > [!NOTE]
    > Il controller dati creato nel cluster Kubernetes nel dispositivo Azure Stack Edge Pro funziona solo in modalità disconnessa nella versione corrente.

### <a name="monitor-data-creation-status"></a>Monitorare lo stato di creazione dei dati

1. Aprire un'altra finestra di PowerShell.
1. Usare il `kubectl` comando seguente per monitorare lo stato di creazione del controller di dati. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    Quando si crea il controller, lo stato deve essere `Ready` .
    Di seguito è riportato un esempio di output del comando precedente:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. Per identificare gli indirizzi IP assegnati ai servizi esterni in esecuzione sul controller dati, usare il `kubectl get svc -n <namespace>` comando. Di seguito è riportato un output di esempio:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get svc -n myadstest
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                       AGE
    controldb-svc             ClusterIP      172.28.157.130   <none>        1433/TCP,8311/TCP,8411/TCP                    3d21h
    controller-svc            ClusterIP      172.28.123.251   <none>        443/TCP,8311/TCP,8301/TCP,8411/TCP,8401/TCP   3d21h
    controller-svc-external   LoadBalancer   172.28.154.30    10.57.48.63   30080:31090/TCP                               3d21h
    logsdb-svc                ClusterIP      172.28.52.196    <none>        9200/TCP,8300/TCP,8400/TCP                    3d20h
    logsui-svc                ClusterIP      172.28.85.97     <none>        5601/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdb-svc             ClusterIP      172.28.255.103   <none>        8086/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdc-svc             ClusterIP      172.28.208.191   <none>        8300/TCP,8400/TCP                             3d20h
    metricsui-svc             ClusterIP      172.28.158.163   <none>        3000/TCP,8300/TCP,8400/TCP                    3d20h
    mgmtproxy-svc             ClusterIP      172.28.228.229   <none>        443/TCP,8300/TCP,8311/TCP,8400/TCP,8411/TCP   3d20h
    mgmtproxy-svc-external    LoadBalancer   172.28.166.214   10.57.48.64   30777:30621/TCP                               3d20h
    sqlex-svc                 ClusterIP      None             <none>        1433/TCP                                      3d20h
    PS C:\WINDOWS\system32>
    ```

## <a name="deploy-sql-managed-instance"></a>Distribuire un'istanza gestita di SQL

Dopo aver creato il controller di dati, è possibile usare un modello per distribuire un Istanza gestita SQL sul controller dati.

### <a name="deployment-template"></a>Modello di distribuzione

Usare il modello di distribuzione seguente per distribuire un Istanza gestita SQL sul controller dati del dispositivo.

```yml
apiVersion: v1
data:
    password: UGFzc3dvcmQx
    username: bXlhZHN1c2Vy
kind: Secret
metadata:
    name: sqlex-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
    name: sqlex
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
    service:
    type: LoadBalancer
    storage:
    backups:
        className: ase-node-local
        size: 5Gi
    data:
        className: ase-node-local
        size: 5Gi
    datalogs:
        className: ase-node-local
        size: 5Gi
    logs:
        className: ase-node-local
        size: 1Gi
```


#### <a name="metadata-name"></a>Nome dei metadati
    
Il nome dei metadati è il nome del Istanza gestita SQL. Il Pod associato nella precedente `deployment.yaml` verrà denominato come `sqlex-n` ( `n` è il numero di Pod associato all'applicazione). 
    
#### <a name="password-and-username-data"></a>Dati di nome utente e password

Il nome utente e la password del controller dati vengono usati per eseguire l'autenticazione all'API del controller dei dati per eseguire le funzioni amministrative. Il segreto Kubernetes per nome utente e password del controller dati nel modello di distribuzione sono stringhe con codifica Base64. 

È possibile usare uno strumento online per codificare con Base64 il nome utente e la password desiderati oppure è possibile usare gli strumenti dell'interfaccia della riga di comando predefiniti a seconda della piattaforma. Quando si usa uno strumento di codifica Base64 online, fornire le stringhe di nome utente e password (immesse durante la creazione del controller dati) nello strumento e lo strumento genererà le stringhe con codifica Base64 corrispondenti.
    
#### <a name="service-type"></a>Tipo di servizio

Il tipo di servizio deve essere impostato su `LoadBalancer` .
    
#### <a name="storage-class-name"></a>Nome della classe di archiviazione

È possibile identificare la classe di archiviazione nel dispositivo Azure Stack Edge che la distribuzione userà per i dati, i backup, i log di dati e i log. Usare il  `kubectl get storageclass` comando per ottenere la classe di archiviazione distribuita nel dispositivo.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
Nell'output di esempio precedente, la classe di archiviazione nel `ase-node-local` dispositivo deve essere specificata nel modello.
 
#### <a name="spec"></a>Spec

Per creare un Istanza gestita SQL sul dispositivo Azure Stack Edge, è possibile specificare i requisiti di memoria e CPU nella sezione specifica del `deployment.yaml` . Ogni istanza gestita di SQL deve richiedere almeno 2 GB di memoria e 1 core CPU, come illustrato nell'esempio seguente. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

Per indicazioni dettagliate sul dimensionamento per il controller dati e 1 Istanza gestita SQL, vedere [Dettagli sul dimensionamento dell'istanza gestita di SQL](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details).

### <a name="run-deployment-template"></a>Esegui modello di distribuzione

Eseguire `deployment.yaml` usando il comando seguente:

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

Di seguito è riportato un esempio di output del comando seguente:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
No resources found.
PS C:\WINDOWS\system32> 
PS C:\WINDOWS\system32> kubectl create -n myadstest -f C:\azure-arc-data-services\sqlex.yaml  secret/sqlex-login-secret created
sqlmanagedinstance.sql.arcdata.microsoft.com/sqlex created
PS C:\WINDOWS\system32> kubectl get pods --namespace myadstest
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-mv2cd   1/1     Running   0          83m
control-w9s9l        2/2     Running   0          78m
controldb-0          2/2     Running   0          78m
controlwd-4bmc5      1/1     Running   0          64m
logsdb-0             1/1     Running   0          64m
logsui-wpmw2         1/1     Running   0          64m
metricsdb-0          1/1     Running   0          64m
metricsdc-fb5r5      1/1     Running   0          64m
metricsui-56qzs      1/1     Running   0          64m
mgmtproxy-2ckl7      2/2     Running   0          64m
sqlex-0              3/3     Running   0          13m
PS C:\WINDOWS\system32>
```

Il `sqlex-0` pod nell'output di esempio indica lo stato di SQL istanza gestita.

## <a name="remove-data-controller"></a>Rimuovi controller dati

Per rimuovere il controller dati, eliminare lo spazio dei nomi dedicato in cui è stato distribuito.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Passaggi successivi

- [Distribuire un'applicazione senza stato nel Azure stack Edge Pro](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
