---
title: Limitare il traffico in uscita in un cluster Azure Red Hat OpenShift (ARO)
description: Informazioni sulle porte e gli indirizzi necessari per controllare il traffico in uscita Azure Red Hat OpenShift (ARO)
author: sakthi-vetrivel
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 04/09/2021
ms.openlocfilehash: 24c4686306aff9d84fe7bf74ddfdccff987244d9
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368688"
---
# <a name="control-egress-traffic-for-your-azure-red-hat-openshift-aro-cluster-preview"></a>Controllare il traffico in uscita per il cluster Azure Red Hat OpenShift (ARO) (anteprima)

Questo articolo fornisce i dettagli necessari che consentono di proteggere il traffico in uscita dal cluster Azure Red Hat OpenShift (ARO). Contiene i requisiti del cluster per una distribuzione ARO di base e altri requisiti per red hat facoltativi e componenti di terze parti. Alla [fine](#private-aro-cluster-setup) verrà fornito un esempio di come configurare questi requisiti con Firewall di Azure. Tenere presente che è possibile applicare queste informazioni ai dispositivi Firewall di Azure o a qualsiasi metodo di restrizione in uscita o appliance.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si crei un nuovo cluster. Se è necessario un cluster ARO di base, vedere la guida [introduttiva di ARO.](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster)

> [!IMPORTANT]
> Le funzionalità di anteprima di ARO sono disponibili in modalità self-service e con consenso esplicito. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime di ARO sono parzialmente coperte dal supporto clienti in base al massimo sforzo.

## <a name="minimum-required-fqdn--application-rules"></a>Fqdn minimo richiesto/regole dell'applicazione

Questo elenco è basato sull'elenco di FQDN disponibili nella documentazione di OpenShift qui: https://docs.openshift.com/container-platform/4.6/installing/install_config/configuring-firewall.html

Le regole delle applicazioni e FQDN seguenti sono obbligatorie:

| FQDN di destinazione | Porta | Uso |
| ----------- | ----------- | ------------- |
| **`quay.io`** | **HTTPS:443** | Obbligatorio per l'installazione, usato dal cluster. Viene usato dal cluster per scaricare le immagini del contenitore della piattaforma. |
| **`registry.redhat.io`** | **HTTPS:443** | Obbligatorio per i componenti aggiuntivi di base. Viene usato dal cluster per scaricare componenti di base, ad esempio strumenti di sviluppo, componenti aggiuntivi basati su operatori e immagini del contenitore fornite da Red Hat.
| **`mirror.openshift.com`** | **HTTPS:443** | Questa operazione è necessaria nell'ambiente VDI o nel computer portatile per accedere al contenuto e alle immagini di installazione con mirroring. È necessario nel cluster per scaricare le firme di versione della piattaforma per conoscere le immagini da cui eseguire il pull quay.io. |
| **`api.openshift.com`** | **HTTPS:443** | Richiesto dal cluster per verificare se sono disponibili aggiornamenti prima di scaricare le firme dell'immagine. |
| **`arosvc.azurecr.io`** | **HTTPS:443** | Registro privato interno per gli operatori ARO.  Obbligatorio se non si consentono gli endpoint di servizio Microsoft.ContainerRegistry nelle subnet. |
| **`management.azure.com`** | **HTTPS:443** | Viene usato dal cluster per accedere alle API di Azure. |
| **`login.microsoftonline.com`** | **HTTPS:443** | Viene usato dal cluster per l'autenticazione in Azure. |
| **`gcs.prod.monitoring.core.windows.net`** | **HTTPS:443** | Viene usato per Microsoft Geneva Monitoring in modo che il team ARO possa monitorare i cluster del cliente. |
| **`*.blob.core.windows.net`** | **HTTPS:443** | Viene usato per Microsoft Geneva Monitoring in modo che il team ARO possa monitorare i cluster del cliente. |
| **`*.servicebus.windows.net`** | **HTTPS:443** | Viene usato per Microsoft Geneva Monitoring in modo che il team ARO possa monitorare i cluster del cliente. |
| **`*.table.core.windows.net`** | **HTTPS:443** | Viene usato per il monitoraggio di Microsoft ARO in modo che il team ARO possa monitorare i cluster del cliente. |

> [!NOTE] 
> Per molti clienti che espongono *.blob, *.table e altri spazi indirizzi di grandi dimensioni crea un potenziale problema di esfiltrazione dei dati. È consigliabile usare [openshift Egress Firewall](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/configuring-egress-firewall.html) per proteggere le applicazioni distribuite nel cluster dal raggiungimento di queste destinazioni e usare collegamento privato di Azure per esigenze specifiche dell'applicazione.

---

## <a name="complete-list-of-required-and-optional-fqdns"></a>Elenco completo dei nomi fqdn obbligatori e facoltativi

### <a name="first-group-installing-and-downloading-packages-and-tools"></a>PRIMO GRUPPO: INSTALLAZIONE E DOWNLOAD DI PACCHETTI E STRUMENTI

- **`quay.io`**: obbligatorio per l'installazione, usato dal cluster. Viene usato dal cluster per scaricare le immagini del contenitore della piattaforma.
- **`registry.redhat.io`**: obbligatorio per i componenti aggiuntivi di base. Viene usato dal cluster per scaricare i componenti principali, ad esempio strumenti di sviluppo, componenti aggiuntivi basati su operatore o immagini del contenitore fornite da Red Hat, ad esempio il middleware, l'immagine di base universale...
- **`sso.redhat.com`**: questa opzione è necessaria nell'ambiente VDI o nel portatile per connettersi cloud.redhat.com. Questo è il sito in cui è possibile scaricare il segreto pull e usare alcune delle soluzioni SaaS offerte in Red Hat per facilitare il monitoraggio delle sottoscrizioni, dell'inventario del cluster, dei report di chargeback, tra le altre cose.
- **`openshift.org`**: questa è necessaria nell'ambiente VDI o nel portatile per connettersi per scaricare le immagini RH CoreOS, ma in Azure vengono selezionate dal marketplace, non è necessario scaricare le immagini del sistema operativo.

---

### <a name="second-group-telemetry"></a>SECONDO GRUPPO: TELEMETRIA

Tutte queste sezioni possono essere rifiutate esplicitamente, ma prima di sapere come procedere, verificare che cos'è: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/about-remote-health-monitoring.html
- **`cert-api.access.redhat.com`**: da usare nell'ambiente VDI o portatile.
- **`api.access.redhat.com`**: da usare nell'ambiente VDI o portatile.
- **`infogw.api.openshift.com`**: da usare nell'ambiente VDI o portatile.
- **`https://cloud.redhat.com/api/ingress`**: usare nel cluster per l'operatore insights che si integra con aaS Red Hat Insights.
In OpenShift Container Platform i clienti possono rifiutare esplicitamente di segnalare informazioni sull'integrità e sull'utilizzo. Tuttavia, i cluster connessi consentono a Red Hat di reagire più rapidamente ai problemi e di supportare meglio i clienti e comprendere meglio in che modo i cluster vengono aggiornati dal prodotto. Controllare i dettagli qui: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/opting-out-of-remote-health-reporting.html .

---

### <a name="third-group-cloud-apis"></a>TERZO GRUPPO: API CLOUD

- **`management.azure.com`**: viene usato dal cluster per accedere alle API di Azure.

---

### <a name="fourth-group-other-openshift-requirements"></a>QUARTO GRUPPO: ALTRI REQUISITI DI OPENSHIFT

- **`mirror.openshift.com`**: questa è necessaria nell'ambiente VDI o nel computer portatile per accedere al contenuto e alle immagini di installazione con mirroring e nel cluster per scaricare le firme di versione della piattaforma, usate dal cluster per sapere quali immagini eseguire il pull da quay.io.
- **`storage.googleapis.com/openshift-release`**: sito alternativo per il download delle firme di versione della piattaforma, usato dal cluster per conoscere le immagini da cui eseguire il pull quay.io.
- **`*.apps.<cluster_name>.<base_domain>`** (O URL ARO equivalente): Quando si consente l'elenco dei domini, viene utilizzato nella rete aziendale per raggiungere le applicazioni distribuite in OpenShift o per accedere alla console openshift.
- **`api.openshift.com`**: richiesto dal cluster per verificare se sono disponibili aggiornamenti prima di scaricare le firme dell'immagine.
- **`registry.access.redhat.com`**: l'accesso al Registro di sistema è necessario nell'ambiente VDI o laptop per scaricare le immagini di sviluppo quando si usa lo strumento dell'interfaccia della riga di comando ODO. Questo strumento dell'interfaccia della riga di comando è uno strumento alternativo dell'interfaccia della riga di comando per gli sviluppatori che non hanno familiarità con kubernetes. https://docs.openshift.com/container-platform/4.6/cli_reference/developer_cli_odo/understanding-odo.html

---

### <a name="fifth-group-microsoft--red-hat-aro-monitoring-service"></a>QUINTO GRUPPO: MICROSOFT & RED HAT ARO MONITORING SERVICE

- **`login.microsoftonline.com`**: viene usato dal cluster per l'autenticazione in Azure.
- **`gcs.prod.monitoring.core.windows.net`**: viene usato per Microsoft Geneva Monitoring in modo che il team ARO possa monitorare i cluster del cliente.
- **`*.blob.core.windows.net`**: viene usato per Microsoft Geneva Monitoring in modo che il team ARO possa monitorare i cluster del cliente.
- **`*.servicebus.windows.net`**: viene usato per Microsoft Geneva Monitoring in modo che il team ARO possa monitorare i cluster del cliente.
- **`*.table.core.windows.net`**: viene usato per Microsoft Geneva Monitoring in modo che il team ARO possa monitorare i cluster del cliente.

## <a name="aro-integrations"></a>Integrazioni ARO

### <a name="azure-monitor-for-containers"></a>Monitoraggio di Azure per contenitori

Sono disponibili due opzioni per fornire l'accesso Monitoraggio di Azure per i contenitori. È possibile consentire il [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags)  di Monitoraggio di Azure o fornire l'accesso al nome di dominio completo o alle regole dell'applicazione necessarie.  Di seguito sono [riportate le](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-azure-redhat4-setup) istruzioni su come aggiungere Monitoraggio di Azure al cluster ARO esistente.

#### <a name="required-network-rules"></a>Regole di rete necessarie

Le regole delle applicazioni e FQDN seguenti sono obbligatorie:

| Endpoint di destinazione                                                             | Protocollo | Porta    | Uso  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Questo endpoint viene usato per inviare i dati e i log delle metriche Monitoraggio di Azure e Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Fqdn/regole dell'applicazione obbligatorie

Per i cluster ARO in cui è abilitata l'Monitoraggio di Azure per i contenitori sono necessarie le regole di fqdn/applicazione seguenti:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| **`dc.services.visualstudio.com`** | **`HTTPS:443`**    | Questo endpoint viene usato per le metriche e il monitoraggio dei dati di telemetria Monitoraggio di Azure. |
| **`*.ods.opinsights.azure.com`**    | **`HTTPS:443`**    | Questo endpoint viene usato dai Monitoraggio di Azure per l'inserimento di dati di Log Analytics. |
| **`*.oms.opinsights.azure.com`** | **`HTTPS:443`** | Questo endpoint viene usato da omsagent, che viene usato per autenticare il servizio Log Analytics. |
| **`*.monitoring.azure.com`** | **`HTTPS:443`** | Questo endpoint viene usato per inviare i dati delle metriche Monitoraggio di Azure. |


## <a name="private-aro-cluster-setup"></a>Configurazione del cluster ARO privato
L'obiettivo è proteggere il cluster ARO instradndo il traffico in uscita attraverso un Firewall di Azure
### <a name="before"></a>Prima di:
![Prima](media/concepts-networking/aro-private.jpg)
### <a name="after"></a>Dopo:
![After](media/concepts-networking/aro-fw.jpg)

## <a name="create-a-private-aro-cluster"></a>Creare un cluster di Azure Red Hat OpenShift privato

### <a name="set-up-vars-for-your-environment"></a>Configurare VARS per l'ambiente
```bash

CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster
AROVNET=aro-vnet # The name of your vnet from your created ARO cluster
JUMPSUBNET=jump-subnet
LOCATION=eastus # The location where ARO cluster is deployed

```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
```bash
az group create -g "$RESOURCEGROUP" -l $LOCATION
```

### <a name="create-the-virtual-network"></a>Creare la rete virtuale
```bash
az network vnet create \
  -g $RESOURCEGROUP \
  -n $AROVNET \
  --address-prefixes 10.0.0.0/8
```

### <a name="add-two-empty-subnets-to-your-virtual-network"></a>Aggiungere due subnet vuote alla rete virtuale
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "$CLUSTER-master" \
    --address-prefixes 10.10.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry

  az network vnet subnet create \
    -g $RESOURCEGROUP \
    --vnet-name $AROVNET \
    -n "$CLUSTER-worker" \
    --address-prefixes 10.20.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```

### <a name="disable-network-policies-for-private-link-service-on-your-virtual-network-and-subnets-this-is-a-requirement-for-the-aro-service-to-access-and-manage-the-cluster"></a>Disabilitare i criteri di rete per il servizio Collegamento privato nella rete virtuale e nelle subnet. Si tratta di un requisito che il servizio ARO deve accedere al cluster e gestirvi.
```bash
az network vnet subnet update \
  -g "$RESOURCEGROUP" \
  --vnet-name $AROVNET \
  -n "$CLUSTER-master" \
  --disable-private-link-service-network-policies true
```
### <a name="create-a-firewall-subnet"></a>Creare una subnet del firewall
```bash
az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "AzureFirewallSubnet" \
    --address-prefixes 10.100.1.0/26
```

## <a name="create-a-jump-host-vm"></a>Creare una macchina virtuale jump-host
### <a name="create-a-jump-subnet"></a>Creare una jump-subnet
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n $JUMPSUBNET \
    --address-prefixes 10.30.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```
### <a name="create-a-jump-host-vm"></a>Creare una macchina virtuale jump-host
```bash
VMUSERNAME=aroadmin

az vm create --name ubuntu-jump \
             --resource-group $RESOURCEGROUP \
             --ssh-key-values ~/.ssh/id_rsa.pub \
             --admin-username $VMUSERNAME \
             --image UbuntuLTS \
             --subnet $JUMPSUBNET \
             --public-ip-address jumphost-ip \
             --vnet-name $AROVNET 
```

## <a name="create-an-azure-red-hat-openshift-cluster"></a>Creare un cluster Azure Red Hat OpenShift
### <a name="get-a-red-hat-pull-secret-optional"></a>Ottenere un segreto pull di Red Hat (facoltativo)

Un segreto pull Red Hat consente al cluster di accedere ai registri contenitori Red Hat insieme ad altri contenuti. Questo passaggio è facoltativo ma consigliato.

1. **[Passare al portale di gestione cluster di Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) ed eseguire l'accesso.**

   È necessario accedere all'account Red Hat o creare un nuovo account Red Hat con l'indirizzo di posta elettronica aziendale e accettare i termini e le condizioni.

2. **Fare clic su Download pull secret (Scarica segreto pull).**

Conservare il file `pull-secret.txt` salvato in una posizione sicura perché verrà usato ogni volta che si crea un cluster.

Quando si esegue il comando `az aro create`, è possibile fare riferimento al segreto di pull usando il parametro `--pull-secret @pull-secret.txt`. Eseguire `az aro create` dalla directory in cui è stato archiviato il file `pull-secret.txt`. In caso contrario, sostituire `@pull-secret.txt` con `@<path-to-my-pull-secret-file`.

Se si copia il segreto pull o vi si fa riferimento in altri script, il segreto pull deve essere formattato come stringa JSON valida.

```bash
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet $AROVNET \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --apiserver-visibility Private \
  --ingress-visibility Private \
  --pull-secret @pull-secret.txt
```

## <a name="create-an-azure-firewall"></a>Creare un'istanza di Firewall di Azure

### <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico
```bash
az network public-ip create -g $RESOURCEGROUP -n fw-ip --sku "Standard" --location $LOCATION
```
### <a name="update-install-azure-firewall-extension"></a>Aggiornare l'Firewall di Azure di installazione
```bash
az extension add -n azure-firewall
az extension update -n azure-firewall
```

### <a name="create-azure-firewall-and-configure-ip-config"></a>Creare Firewall di Azure e configurare la configurazione IP
```bash
az network firewall create -g $RESOURCEGROUP -n aro-private -l $LOCATION
az network firewall ip-config create -g $RESOURCEGROUP -f aro-private -n fw-config --public-ip-address fw-ip --vnet-name $AROVNET

```

### <a name="capture-azure-firewall-ips-for-a-later-use"></a>Acquisire Firewall di Azure IP per un uso successivo
```bash
FWPUBLIC_IP=$(az network public-ip show -g $RESOURCEGROUP -n fw-ip --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RESOURCEGROUP -n aro-private --query "ipConfigurations[0].privateIpAddress" -o tsv)

echo $FWPUBLIC_IP
echo $FWPRIVATE_IP
```

### <a name="create-a-udr-and-routing-table-for-azure-firewall"></a>Creare una tabella di routing e una UDR per Firewall di Azure
```bash
az network route-table create -g $RESOURCEGROUP --name aro-udr

az network route-table route create -g $RESOURCEGROUP --name aro-udr --route-table-name aro-udr --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP
```

### <a name="add-application-rules-for-azure-firewall"></a>Aggiungere regole applicazione per Firewall di Azure
Regola per il funzionamento di OpenShift in base [all'elenco seguente:](https://docs.openshift.com/container-platform/4.3/installing/install_config/configuring-firewall.html#configuring-firewall_configuring-firewall)
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'ARO' \
 --action allow \
 --priority 100 \
 -n 'required' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns 'registry.redhat.io' '*.quay.io' 'sso.redhat.com' 'management.azure.com' 'mirror.openshift.com' 'api.openshift.com' 'quay.io' '*.blob.core.windows.net' 'gcs.prod.monitoring.core.windows.net' 'registry.access.redhat.com' 'login.microsoftonline.com' '*.servicebus.windows.net' '*.table.core.windows.net' 'grafana.com'
```
Regole facoltative per le immagini Docker:
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'Docker' \
 --action allow \
 --priority 200 \
 -n 'docker' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns '*cloudflare.docker.com' '*registry-1.docker.io' 'apt.dockerproject.org' 'auth.docker.io'
```

### <a name="associate-aro-subnets-to-fw"></a>Associare subnet ARO a FW
```bash
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-master" --route-table aro-udr
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-worker" --route-table aro-udr
```

## <a name="test-the-configuration-from-the-jumpbox"></a>Testare la configurazione dal Jumpbox
Questi passaggi funzionano solo se sono state aggiunte regole per le immagini Docker. 
### <a name="configure-the-jumpbox"></a>Configurare il jumpbox
Accedere a una macchina virtuale jumpbox e installare `azure-cli` `oc-cli` , e `jq` utils. Per l'installazione di openshift-cli, controllare il portale clienti di Red Hat.
```bash
#Install Azure-cli
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
#Install jq
sudo apt install jq -y
```
### <a name="log-into-the-aro-cluster"></a>Accedere al cluster ARO
Elencare le credenziali del cluster:
```bash

# Login to Azure
az login
# Set Vars in Jumpbox
CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster

#Get the cluster credentials
ARO_PASSWORD=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminPassword')
ARO_USERNAME=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminUsername')
```
Ottenere un endpoint server API:
```bash
ARO_URL=$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.apiserverProfile.url')
```

### <a name="download-the-oc-cli-to-the-jumpbox"></a>Scaricare l'interfaccia della riga di comando di oc nel jumpbox
```bash
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

Accedere usando `oc login` :
```bash
oc login $ARO_URL -u $ARO_USERNAME -p $ARO_PASSWORD
```

### <a name="run-centos-to-test-outside-connectivity"></a>Eseguire CentOS per testare la connettività esterna
Creare un pod
```bash
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: centos
spec:
  containers:
  - name: centos
    image: centos
    ports:
    - containerPort: 80
    command:
    - sleep
    - "3600"
EOF
```
Quando il pod è in esecuzione, eseguire al suo interno e testare la connettività esterna.

```bash
oc exec -it centos -- /bin/bash
curl microsoft.com
```

## <a name="access-the-web-console-of-the-private-cluster"></a>Accedere alla console Web del cluster privato

### <a name="set-up-ssh-forwards-commands"></a>Configurare i comandi ssh forwards

```bash
sudo ssh -i $SSH_PATH -L 443:$CONSOLE_URL:443 aroadmin@$JUMPHOST

example:
sudo ssh -i /Users/jimzim/.ssh/id_rsa -L 443:console-openshift-console.apps.d5xm5iut.eastus.aroapp.io:443 aroadmin@104.211.18.56
```

### <a name="modify-the-etc-hosts-file-on-your-local-machine"></a>Modificare il file hosts etc. nel computer locale
```bash
##
# Host Database
#
127.0.0.1 console-openshift-console.apps.d5xm5iut.eastus.aroapp.io
127.0.0.1 oauth-openshift.apps.d5xm5iut.eastus.aroapp.io
```

### <a name="use-sshuttle-as-another-option"></a>Usare sshuttle come altra opzione

[SSHuttle](https://github.com/sshuttle/sshuttle)


## <a name="clean-up-resources"></a>Pulire le risorse

```bash

# Clean up the ARO cluster, vnet, firewall and jumpbox

# Remove udr from master and worker subnets first or will get error when deleting ARO cluster
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-master -g $RESOURCEGROUP --route-table aro-udr --remove routeTable
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-worker -g $RESOURCEGROUP --route-table aro-udr --remove routeTable

# Remove ARO Cluster
az aro delete -n $CLUSTER -g $RESOURCEGROUP

# Remove the resource group that contains the firewall, jumpbox and vnet
az group delete -n $RESOURCEGROUP
```