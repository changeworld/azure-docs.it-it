---
title: Distribuire un Azure Arc data controller nel dispositivo Azure Stack Edge Pro GPU| Microsoft Docs
description: Descrive come distribuire un controller Azure Arc e Servizi dati di Azure nel dispositivo Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: d56e03cd650032a775c30b02d939cf934f384fae
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568608"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Distribuire Servizi dati di Azure nel dispositivo Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Questo articolo descrive il processo di creazione di un controller dati Azure Arc e quindi distribuzione di Servizi dati di Azure nel Azure Stack Edge Pro GPU. 

Azure Arc Data Controller è il piano di controllo locale che abilita Servizi dati di Azure in ambienti gestiti dal cliente. Dopo aver creato il controller dati di Azure Arc nel cluster Kubernetes in esecuzione nel dispositivo GPU Azure Stack Edge Pro, è possibile distribuire Servizi dati di Azure, ad esempio SQL Istanza gestita (anteprima) in tale controller dati.

La procedura per creare il controller dati e quindi distribuire un Istanza gestita SQL prevede l'uso di PowerShell e , uno strumento nativo che fornisce l'accesso dalla riga di comando al `kubectl` cluster Kubernetes nel dispositivo.


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. Si ha accesso a un Azure Stack Edge Pro GPU ed è stato attivato il dispositivo come descritto in [Attivare Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

1. È stato abilitato il ruolo di calcolo nel dispositivo. È stato creato anche un cluster Kubernetes nel dispositivo quando è stato configurato il calcolo nel dispositivo in base alle istruzioni riportate in Configurare il calcolo nel [Azure Stack Edge Pro GPU.](azure-stack-edge-gpu-deploy-configure-compute.md)

1. Si dispone dell'endpoint dell'API Kubernetes dalla **pagina Dispositivo** dell'interfaccia utente Web locale. Per altre informazioni, vedere le istruzioni in [Ottenere l'endpoint dell'API Kubernetes.](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)

1. Si ha accesso a un client che si connetterà al dispositivo. 
    1. Questo articolo usa un sistema client Windows che esegue PowerShell 5.0 o versione successiva per accedere al dispositivo. È possibile usare qualsiasi altro client con un [sistema operativo supportato.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 
    1. Installare `kubectl` nel client. Per la versione client:
        1. Identificare la versione del server Kubernetes installata nel dispositivo. Nell'interfaccia utente locale del dispositivo passare alla **pagina Aggiornamenti** software. Prendere nota **della versione del server Kubernetes** in questa pagina.
        1. Scaricare un client che abbia al massimo una versione in meno rispetto al master. La versione client, ma può portare il master a un massimo di una versione secondaria. Ad esempio, un master v1.3 deve funzionare con i nodi v1.1, v1.2 e v1.3 e deve funzionare con i client v1.2, v1.3 e v1.4. Per altre informazioni sulla versione del client Kubernetes, vedere Criteri di supporto della versione e [dell'afa delle versioni di Kubernetes.](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)
    
1. Facoltativamente, [installare gli strumenti client per la distribuzione e la gestione servizi dati abilitati per Azure Arc](../azure-arc/data/install-client-tools.md). Questi strumenti non sono necessari ma consigliati.  
1. Assicurarsi di disporre di risorse sufficienti nel dispositivo per effettuare il provisioning di un controller dati e di un'istanza di SQL Istanza gestita. Per il controller dati e un Istanza gestita SQL, sono necessari almeno 16 GB di RAM e 4 core CPU. Per istruzioni dettagliate, vedere [Requisiti minimi per la servizi dati abilitati per Azure Arc distribuzione](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements).


## <a name="configure-kubernetes-external-service-ips"></a>Configurare gli IP del servizio esterno kubernetes

1. Passare all'interfaccia utente Web locale del dispositivo e quindi passare a **Calcolo**.
1. Selezionare la rete abilitata per il calcolo. 

    ![Pagina di calcolo nell'interfaccia utente locale 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Assicurarsi di fornire altri tre INDIRIZZI IP del servizio esterno Kubernetes (oltre agli indirizzi IP già configurati per altri servizi o contenitori esterni). Il controller dati userà due indirizzi IP del servizio e il terzo IP verrà usato quando si crea un indirizzo SQL Istanza gestita. Sarà necessario un indirizzo IP per ogni servizio dati aggiuntivo che verrà distribuito. 

    ![Pagina di calcolo nell'interfaccia utente locale 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. Applicare le impostazioni e questi nuovi IP avranno effetto immediatamente su un cluster Kubernetes già esistente. 


## <a name="deploy-azure-arc-data-controller"></a>Distribuire Azure Arc Data Controller

Prima di distribuire un controller dati, è necessario creare uno spazio dei nomi.

### <a name="create-namespace"></a>Crea spazio dei nomi 

Creare un nuovo spazio dei nomi dedicato in cui si distribuirà il controller dati. Si creerà anche un utente e quindi si concederà all'utente l'accesso allo spazio dei nomi creato. 

> [!NOTE]
> Per i nomi di spazio dei nomi e utente, si applicano [le convenzioni di denominazione del sottodominio](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) DNS.

1. [Connettersi all'interfaccia di PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Creare uno spazio dei nomi Digitare:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Creare un utente. Digitare: 

    `New-HcsKubernetesUser -UserName <User name>`

1. Un file di configurazione viene visualizzato in testo normale. Copiare questo file e salvarlo come file *di* configurazione. 

    > [!IMPORTANT]
    > Non salvare il file di configurazione come file *txt,* salvare il file senza alcuna estensione.

1. Il file di configurazione deve essere contenuto `.kube` nella cartella del profilo utente nel computer locale. Copiare il file in tale cartella nel profilo utente.

    ![Percorso del file di configurazione nel client](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. Concedere all'utente l'accesso allo spazio dei nomi creato. Digitare: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Di seguito è riportato un output di esempio dei comandi precedenti. In questo esempio viene creato uno spazio dei nomi, un utente e viene `myadstest` `myadsuser` concesso all'utente l'accesso allo spazio dei nomi.
    
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
    2. Usare le informazioni salvate  dalla pagina Dispositivo nell'interfaccia utente locale (prerequisito) per creare la voce nel file hosts. 

        Ad esempio, copiare questo endpoint `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` per creare la voce seguente con l'indirizzo IP del dispositivo e il dominio DNS: 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Per verificare che sia possibile connettersi ai pod Kubernetes, avviare un prompt dei comandi o una sessione di PowerShell. Digitare:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
È ora possibile distribuire le applicazioni del controller dei dati e dei servizi dati nello spazio dei nomi , quindi visualizzare le applicazioni e i relativi log.

### <a name="create-data-controller"></a>Creare un controller dei dati

Il controller dati è una raccolta di pod distribuiti nel cluster Kubernetes per fornire un'API, il servizio controller, il programma di avvio automatico e i database e i dashboard di monitoraggio. Seguire questa procedura per creare un controller dati nel cluster Kubernetes presente nel dispositivo Azure Stack Edge nello spazio dei nomi creato in precedenza.   

1. Raccogliere le informazioni seguenti necessarie per creare un controller dati:

    
    |Colonna 1  |Colonna 2  |
    |---------|---------|
    |Nome del controller dati     |Nome descrittivo per il controller dati. Ad esempio: `arctestdatacontroller`.         |
    |Nome utente del controller dati     |Qualsiasi nome utente per l'utente amministratore del controller dati. Il nome utente e la password del controller dati vengono usati per eseguire l'autenticazione all'API del controller dati per eseguire funzioni amministrative.          |
    |Password del controller dati     |Password per l'utente amministratore del controller dati. Scegliere una password sicura e condividerla solo con quelli che devono disporre dei privilegi di amministratore del cluster.         |
    |Nome dello spazio dei nomi Kubernetes     |Nome dello spazio dei nomi Kubernetes in cui si vuole creare il controller dati.         |
    |ID sottoscrizione di Azure     |GUID della sottoscrizione di Azure per cui si vuole creare la risorsa controller dati in Azure.         |
    |Nome del gruppo di risorse di Azure     |Nome del gruppo di risorse in cui si vuole creare la risorsa controller dati in Azure.         |
    |Località di Azure     |Posizione di Azure in cui verranno archiviati i metadati della risorsa del controller dati in Azure. Per un elenco delle aree disponibili, vedere Infrastruttura globale di Azure/Prodotti per area.|


1. Connettersi all'interfaccia di PowerShell. Per creare il controller di dati, digitare: 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Di seguito è riportato un output di esempio dei comandi precedenti.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    Il completamento della distribuzione potrebbe richiedere circa 5 minuti.

    > [!NOTE]
    > Il controller dati creato nel cluster Kubernetes nel dispositivo GPU Azure Stack Edge Pro funziona solo in modalità disconnessa nella versione corrente. La modalità disconnessa è per il controller di dati e non per il dispositivo.

### <a name="monitor-data-creation-status"></a>Monitorare lo stato di creazione dei dati

1. Aprire un'altra finestra di PowerShell.
1. Usare il comando `kubectl` seguente per monitorare lo stato di creazione del controller dati. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    Quando viene creato il controller, lo stato deve essere `Ready` .
    Di seguito è riportato un output di esempio del comando precedente:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. Per identificare gli ip assegnati ai servizi esterni in esecuzione nel controller dati, usare il `kubectl get svc -n <namespace>` comando . Di seguito è riportato un output di esempio:

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

Dopo aver creato correttamente il controller dati, è possibile usare un modello per distribuire un Istanza gestita SQL nel controller dati.

### <a name="deployment-template"></a>Modello di distribuzione

Usare il modello di distribuzione seguente per distribuire un Istanza gestita SQL nel controller dati nel dispositivo.

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
    
Il nome dei metadati è il nome del Istanza gestita SQL. Il pod associato nel precedente sarà il nome `deployment.yaml` come ( è il numero di pod associati `sqlex-n` `n` all'applicazione). 
    
#### <a name="password-and-username-data"></a>Dati relativi a password e nome utente

Il nome utente e la password del controller dati vengono usati per eseguire l'autenticazione all'API del controller dati per eseguire funzioni amministrative. Il segreto Kubernetes per il nome utente e la password del controller dati nel modello di distribuzione sono stringhe con codifica Base64. 

È possibile usare uno strumento online per codificare il nome utente e la password in base 64 oppure usare gli strumenti dell'interfaccia della riga di comando predefiniti a seconda della piattaforma. Quando si usa uno strumento di codifica Base64 online, specificare le stringhe di nome utente e password (immesse durante la creazione del controller dati) nello strumento e lo strumento genererà le stringhe con codifica Base64 corrispondenti.
    
#### <a name="service-type"></a>Tipo di servizio

Il tipo di servizio deve essere impostato su `LoadBalancer` .
    
#### <a name="storage-class-name"></a>Nome della classe di archiviazione

È possibile identificare la classe di archiviazione nel dispositivo Azure Stack Edge che verrà utilizzato dalla distribuzione per dati, backup, log di dati e log. Usare il  `kubectl get storageclass` comando per distribuire la classe di archiviazione nel dispositivo.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
Nell'output di esempio precedente la classe di `ase-node-local` archiviazione nel dispositivo deve essere specificata nel modello.
 
#### <a name="spec"></a>Spec

Per creare un Istanza gestita SQL nel dispositivo Azure Stack Edge, è possibile specificare i requisiti di memoria e CPU nella sezione specifica di `deployment.yaml` . Ogni istanza gestita di SQL deve richiedere almeno 2 GB di memoria e 1 core CPU, come illustrato nell'esempio seguente. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

Per indicazioni dettagliate sul ridimensionamento per il controller dati e 1 istanza di SQL Istanza gestita, vedere Dettagli sul ridimensionamento dell'istanza gestita [di SQL.](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details)

### <a name="run-deployment-template"></a>Eseguire il modello di distribuzione

Eseguire usando `deployment.yaml` il comando seguente:

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

Di seguito è riportato un output di esempio del comando seguente:

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

Il `sqlex-0` pod nell'output di esempio indica lo stato dell'istanza di SQL Istanza gestita.

## <a name="remove-data-controller"></a>Rimuovere il controller dati

Per rimuovere il controller di dati, eliminare lo spazio dei nomi dedicato in cui è stato distribuito.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Passaggi successivi

- [Distribuire un'applicazione senza stato nel Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).
