---
title: Riferimento YAML per il gruppo di contenitori
description: Riferimento per il file YAML supportato da Istanze di Azure Container per configurare un gruppo di contenitori
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: efbea7b64ccdf685d4c82bd406f2aa09227e53e1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771138"
---
# <a name="yaml-reference-azure-container-instances"></a>Informazioni di riferimento su YAML: Istanze di Azure Container

Questo articolo illustra la sintassi e le proprietà per il file YAML supportato da Istanze di Azure Container configurare un gruppo [di contenitori.](container-instances-container-groups.md) Usare un file YAML per immettere la configurazione del gruppo nel [comando az container create][az-container-create] nell'interfaccia della riga di comando di Azure. 

Un file YAML è un modo pratico per configurare un gruppo di contenitori per distribuzioni riproducibili. Si tratta di un'alternativa concisa [all'uso](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups) di un modello Resource Manager o degli SDK Istanze di Azure Container per creare o aggiornare un gruppo di contenitori.

> [!NOTE]
> Questo riferimento si applica ai file YAML per Istanze di Azure Container api REST `2019-12-01` .

## <a name="schema"></a>SCHEMA 

Segue lo schema per il file YAML, inclusi i commenti per evidenziare le proprietà chiave. Per una descrizione delle proprietà di questo schema, vedere la [sezione Valori delle](#property-values) proprietà.

```yml
name: string  # Name of the container group
apiVersion: '2019-12-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
  sku: string # SKU for the container group
  encryptionProperties:
    vaultBaseUrl: string
    keyName: string
    keyVersion: string
  initContainers: # Array of init containers in the group
  - name: string
    properties:
      image: string
      command:
      - string
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      volumeMounts:
      - name: string
        mountPath: string
        readOnly: boolean
```

## <a name="property-values"></a>Valori delle proprietà

Nelle tabelle seguenti vengono descritti i valori che è necessario impostare nello schema.



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Oggetto Microsoft.ContainerInstance/containerGroups

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  name | string | Sì | Nome del gruppo di contenitori. |
|  apiVersion | enum | Sì | 2018-10-01 |
|  posizione | string | No | Percorso della risorsa. |
|  tags | object | No | Tag di risorsa. |
|  identity | object | No | Identità del gruppo di contenitori, se configurata. - [Oggetto ContainerGroupIdentity](#containergroupidentity-object) |
|  properties | object | Sì | [Oggetto ContainerGroupProperties](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>Oggetto ContainerGroupIdentity

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  type | enum | No | Tipo di identità usato per il gruppo di contenitori. Il tipo 'SystemAssigned, UserAssigned' include sia un'identità creata in modo implicito che un set di identità assegnate dall'utente. Il tipo 'None' rimuoverà le identità dal gruppo di contenitori. - SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | object | No | Elenco di identità utente associate al gruppo di contenitori. I riferimenti alle chiavi del dizionario delle identità utente saranno id risorsa nel formato: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'. Azure Resource Manager |




### <a name="containergroupproperties-object"></a>Oggetto ContainerGroupProperties

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  containers | array | Sì | Contenitori all'interno del gruppo di contenitori. - [Oggetto contenitore](#container-object) |
|  imageRegistryCredentials | array | No | Credenziali del registro immagini da cui viene creato il gruppo di contenitori. - [Oggetto ImageRegistryCredential](#imageregistrycredential-object) |
|  restartPolicy | enum | No | Criteri di riavvio per tutti i contenitori all'interno del gruppo di contenitori. - `Always` Riavvio sempre- `OnFailure` Riavvia in caso di errore- `Never` Non riavviare mai. - Always, OnFailure, Never |
|  IpAddress | object | No | Tipo di indirizzo IP del gruppo di contenitori. - [Oggetto IpAddress](#ipaddress-object) |
|  osType | enum | Sì | Tipo di sistema operativo richiesto dai contenitori nel gruppo di contenitori. - Windows o Linux |
|  volumes | array | No | Elenco di volumi che possono essere montati dai contenitori in questo gruppo di contenitori. - [Oggetto Volume](#volume-object) |
|  diagnostica | object | No | Informazioni di diagnostica per un gruppo di contenitori. - [Oggetto ContainerGroupDiagnostics](#containergroupdiagnostics-object) |
|  networkProfile | object | No | Informazioni sul profilo di rete per un gruppo di contenitori. - [Oggetto ContainerGroupNetworkProfile](#containergroupnetworkprofile-object) |
|  dnsConfig | object | No | Informazioni di configurazione DNS per un gruppo di contenitori. - [Oggetto DnsConfiguration](#dnsconfiguration-object) |
| sku | enum | No | SKU per un gruppo di contenitori - Standard o Dedicato |
| encryptionProperties | object | No | Proprietà di crittografia per un gruppo di contenitori. - [Oggetto EncryptionProperties](#encryptionproperties-object) | 
| initContainers | array | No | Contenitori init per un gruppo di contenitori. - [Oggetto InitContainerDefinition](#initcontainerdefinition-object) |




### <a name="container-object"></a>Oggetto contenitore

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  name | string | Sì | Nome fornito dall'utente dell'istanza del contenitore. |
|  properties | object | Sì | Proprietà dell'istanza del contenitore. - [Oggetto ContainerProperties](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>Oggetto ImageRegistryCredential

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  server | string | Sì | Server del Registro di sistema delle immagini Docker senza un protocollo, ad esempio "http" e "https". |
|  username | string | Sì | Nome utente per il registro privato. |
|  password | stringa | No | Password per il registro privato. |




### <a name="ipaddress-object"></a>Oggetto IpAddress

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  ports | array | Sì | Elenco di porte esposte nel gruppo di contenitori. - [Oggetto Port](#port-object) |
|  type | enum | Sì | Specifica se l'indirizzo IP è esposto alla rete internet pubblica o alla rete virtuale privata. - Pubblico o Privato |
|  Ip | stringa | No | Indirizzo IP esposto alla rete Internet pubblica. |
|  dnsNameLabel | stringa | No | Etichetta del nome DNS per l'indirizzo IP. |




### <a name="volume-object"></a>Oggetto Volume

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  name | string | Sì | Il nome del volume. |
|  azureFile | object | No | Volume di File di Azure. - [Oggetto AzureFileVolume](#azurefilevolume-object) |
|  emptyDir | object | No | Volume di directory vuoto. |
|  secret | object | No | Volume del segreto. |
|  gitRepo | object | No | Volume del repository Git. - [Oggetto GitRepoVolume](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>Oggetto ContainerGroupDiagnostics

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | No | Informazioni di log analytics del gruppo di contenitori. - [Oggetto LogAnalytics](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>Oggetto ContainerGroupNetworkProfile

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  id | string | Sì | Identificatore di un profilo di rete. |




### <a name="dnsconfiguration-object"></a>Oggetto DnsConfiguration

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  Nameserver | array | Sì | Server DNS per il gruppo di contenitori. - stringa |
|  searchDomains | stringa | No | Domini di ricerca DNS per la ricerca del nome host nel gruppo di contenitori. |
|  opzioni | stringa | No | Opzioni DNS per il gruppo di contenitori. |


### <a name="encryptionproperties-object"></a>Oggetto EncryptionProperties

| Nome  | Type  | Obbligatoria  | valore |
|  ---- | ---- | ---- | ---- |
| vaultBaseUrl  | string    | Sì   | URL di base dell'oggetto keyvault. |
| keyName   | string    | Sì   | Nome della chiave di crittografia. |
| keyVersion    | string    | Sì   | Versione della chiave di crittografia. |

### <a name="initcontainerdefinition-object"></a>Oggetto InitContainerDefinition

| Nome  | Type  | Obbligatoria  | valore |
|  ---- | ---- | ---- | ---- |
| name  | string |  Sì | Nome del contenitore init. |
| properties    | object    | Sì   | Proprietà per il contenitore init. - [Oggetto InitContainerPropertiesDefinition](#initcontainerpropertiesdefinition-object)


### <a name="containerproperties-object"></a>Oggetto ContainerProperties

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  image | string | Sì | Nome dell'immagine usata per creare l'istanza del contenitore. |
|  . | array | No | Comandi da eseguire all'interno dell'istanza del contenitore in formato exec. - stringa |
|  ports | array | No | Porte esposte nell'istanza del contenitore. - [Oggetto ContainerPort](#containerport-object) |
|  environmentVariables | array | No | Variabili di ambiente da impostare nell'istanza del contenitore. - [Oggetto EnvironmentVariable](#environmentvariable-object) |
|  resources | object | Sì | Requisiti delle risorse dell'istanza del contenitore. - [Oggetto ResourceRequirements](#resourcerequirements-object) |
|  volumeMounts | array | No | Il volume viene montato disponibile per l'istanza del contenitore. - [Oggetto VolumeMount](#volumemount-object) |
|  livenessProbe | object | No | Probe di liveness. - [Oggetto ContainerProbe](#containerprobe-object) |
|  readinessProbe | object | No | Probe di conformità. - [Oggetto ContainerProbe](#containerprobe-object) |




### <a name="port-object"></a>Oggetto Port

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | No | Protocollo associato alla porta. - TCP o UDP |
|  port | numero intero | Sì | Il numero della porta. |




### <a name="azurefilevolume-object"></a>Oggetto AzureFileVolume

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  Nomecondivisione | string | Sì | Nome della condivisione file di Azure da montare come volume. |
|  readOnly | boolean | No | Flag che indica se il file di Azure condiviso montato come volume è di sola lettura. |
|  storageAccountName | string | Sì | Nome dell'account di archiviazione che contiene la condivisione file di Azure. |
|  storageAccountKey | stringa | No | Chiave di accesso dell'account di archiviazione usata per accedere alla condivisione file di Azure. |




### <a name="gitrepovolume-object"></a>Oggetto GitRepoVolume

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  directory | stringa | No | Nome della directory di destinazione. Non deve contenere o iniziare con '..'.  Se viene specificato ".", la directory del volume sarà il repository Git.  In caso contrario, se specificato, il volume conterrà il repository Git nella sottodirectory con il nome specificato. |
|  repository | string | Sì | Repository URL |
|  revision | stringa | No | Hash di commit per la revisione specificata. |



### <a name="loganalytics-object"></a>Oggetto LogAnalytics

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | Sì | ID dell'area di lavoro per Log Analytics |
|  workspaceKey | string | Sì | Chiave dell'area di lavoro per Log Analytics |
|  logType | enum | No | Tipo di log da utilizzare. - ContainerInsights o ContainerInstanceLogs |
|  metadata | object | No | Metadati per Log Analytics. |


### <a name="initcontainerpropertiesdefinition-object"></a>Oggetto InitContainerPropertiesDefinition

| Nome  | Type  | Obbligatoria  | valore |
|  ---- | ---- | ---- | ---- |
| image | string    | No    | Immagine del contenitore init. |
| .   | array | No    | Comando da eseguire all'interno del contenitore init nel formato exec. - stringa |
| environmentVariables | array  | No |Variabili di ambiente da impostare nel contenitore init. - [Oggetto EnvironmentVariable](#environmentvariable-object)
| volumeMounts |array   | No    | Il volume viene montato disponibile per il contenitore init. - [Oggetto VolumeMount](#volumemount-object)

### <a name="containerport-object"></a>Oggetto ContainerPort

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | No | Protocollo associato alla porta. - TCP o UDP |
|  port | numero intero | Sì | Numero di porta esposto all'interno del gruppo di contenitori. |




### <a name="environmentvariable-object"></a>Oggetto EnvironmentVariable

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  name | string | Sì | Nome della variabile di ambiente. |
|  Valore | string | No | Valore della variabile di ambiente. |
|  secureValue | stringa | No | Valore della variabile di ambiente sicura. |




### <a name="resourcerequirements-object"></a>Oggetto ResourceRequirements

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  requests | object | Sì | Richieste di risorse di questa istanza di contenitore. - [Oggetto ResourceRequests](#resourcerequests-object) |
|  limiti | object | No | Limiti delle risorse di questa istanza di contenitore. - [Oggetto ResourceLimits](#resourcelimits-object) |




### <a name="volumemount-object"></a>Oggetto VolumeMount

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  name | string | Sì | Nome del montaggio del volume. |
|  mountPath | string | Sì | Percorso all'interno del contenitore in cui deve essere montato il volume. Non deve contenere due punti (:). |
|  readOnly | boolean | No | Flag che indica se il montaggio del volume è di sola lettura. |




### <a name="containerprobe-object"></a>Oggetto ContainerProbe

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  exec | object | No | Comando di esecuzione per il probe - [Oggetto ContainerExec](#containerexec-object) |
|  httpGet | object | No | Impostazioni Http Get per probe - [Oggetto ContainerHttpGet](#containerhttpget-object) |
|  initialDelaySeconds | numero intero | No | Secondi di ritardo iniziali. |
|  periodSeconds | numero intero | No | Periodo di secondi. |
|  failureThreshold | numero intero | No | Soglia di errore. |
|  successThreshold | numero intero | No | Soglia di esito positivo. |
|  timeoutSeconds | numero intero | No | Secondi di timeout. |




### <a name="resourcerequests-object"></a>Oggetto ResourceRequests

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | d'acquisto | Sì | Richiesta di memoria in GB di questa istanza del contenitore. |
|  cpu | d'acquisto | Sì | Richiesta CPU di questa istanza del contenitore. |
|  Gpu | object | No | Richiesta GPU di questa istanza del contenitore. - [Oggetto GpuResource](#gpuresource-object) |




### <a name="resourcelimits-object"></a>Oggetto ResourceLimits

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | d'acquisto | No | Limite di memoria in GB di questa istanza del contenitore. |
|  cpu | d'acquisto | No | Limite di CPU di questa istanza del contenitore. |
|  Gpu | object | No | Limite GPU di questa istanza del contenitore. - [Oggetto GpuResource](#gpuresource-object) |




### <a name="containerexec-object"></a>Oggetto ContainerExec

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  . | array | No | Comandi da eseguire all'interno del contenitore. - stringa |




### <a name="containerhttpget-object"></a>Oggetto ContainerHttpGet

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  path | string | No | Percorso di probe. |
|  port | numero intero | Sì | Numero di porta di cui eseguire il probe. |
|  scheme | enum | No | Schema. - http o https |




### <a name="gpuresource-object"></a>Oggetto GpuResource

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  count | numero intero | Sì | Conteggio della risorsa GPU. |
|  sku | enum | Sì | SKU della risorsa GPU. - K80, P100, V100 |


## <a name="next-steps"></a>Passaggi successivi

Vedere l'esercitazione [Distribuire un gruppo multi-contenitore usando un file YAML.](container-instances-multi-container-yaml.md)

Vedere esempi dell'uso di un file YAML per distribuire gruppi di contenitori in una [rete virtuale](container-instances-vnet.md) o che [montano un volume esterno.](container-instances-volume-azure-files.md)

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
