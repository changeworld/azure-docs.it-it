---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: 357223751112af03bf797ae9a0e6352a10132ab9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103464949"
---
Il servizio metadati dell'istanza di Azure (IMDS) fornisce informazioni sulle istanze di macchine virtuali attualmente in esecuzione. È possibile usarlo per gestire e configurare le macchine virtuali.
Queste informazioni includono lo SKU, l'archiviazione, le configurazioni di rete e gli eventi di manutenzione imminenti. Per un elenco completo dei dati disponibili, vedere [riepilogo categorie endpoint](#endpoint-categories).

IMDS è disponibile per l'esecuzione di istanze di macchine virtuali (VM) e di istanze del set di scalabilità di macchine virtuali. Tutti gli endpoint supportano le VM create e gestite con [Azure Resource Manager](/rest/api/resources/). Solo la categoria e la parte di rete attestata della categoria di istanze supportano le VM create usando il modello di distribuzione classica. L'endpoint attestato esegue questa operazione solo in un extent limitato.

IMDS è un'API REST disponibile a un indirizzo IP noto e non instradabile ( `169.254.169.254` ). È possibile accedervi solo dall'interno della macchina virtuale. La comunicazione tra la macchina virtuale e IMDS non lascia mai l'host.
Fare in modo che i client HTTP ignorino i proxy Web all'interno della VM durante l'esecuzione di query su IMDS e trattino `169.254.169.254` gli stessi di [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="usage"></a>Utilizzo

### <a name="access-azure-instance-metadata-service"></a>Accedere al servizio metadati dell'istanza di Azure

Per accedere a IMDS, creare una macchina virtuale da [Azure Resource Manager](/rest/api/resources/) o [portale di Azure](https://portal.azure.com)e usare gli esempi seguenti.
Per altri esempi, vedere [esempi di metadati dell'istanza di Azure](https://github.com/microsoft/azureimds).

Di seguito è riportato il codice di esempio per recuperare tutti i metadati per un'istanza. Per accedere a un'origine dati specifica, vedere [categorie di endpoint](#endpoint-categories) per una panoramica di tutte le funzionalità disponibili.

**Richiesta**

> [!IMPORTANT]
> Questo esempio Ignora i proxy. È **necessario** ignorare i proxy durante l'esecuzione di query su IMDS. Per ulteriori informazioni, vedere [proxy](#proxies) .

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | jq
```

---

**Risposta**

> [!NOTE]
> La risposta è una stringa JSON. La risposta di esempio che segue è di tipo pretty-print per una migliore leggibilità.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>Sicurezza e autenticazione

Il servizio metadati dell'istanza è accessibile solo dall'interno di un'istanza di macchina virtuale in esecuzione in un indirizzo IP non instradabile. Le macchine virtuali sono limitate all'interazione con i metadati e le funzionalità relative a se stessi. L'API è solo HTTP e non lascia mai l'host.

Per assicurarsi che le richieste siano destinate direttamente a IMDS e impediscano il reindirizzamento non intenzionale o indesiderato delle richieste, le richieste:
- **Deve** contenere l'intestazione `Metadata: true`
- **Non** devono contenere un' `X-Forwarded-For` intestazione

Qualsiasi richiesta che non soddisfi **entrambi** questi requisiti verrà rifiutata dal servizio.

> [!IMPORTANT]
> IMDS **non** è un canale per i dati sensibili. L'API non è autenticata e si apre a tutti i processi nella macchina virtuale. Le informazioni esposte tramite questo servizio devono essere considerate come informazioni condivise a tutte le applicazioni in esecuzione nella macchina virtuale.

## <a name="proxies"></a>Proxy

IMDS non è progettato per essere usato dietro un proxy e questa operazione **non** è supportata. La maggior parte dei client HTTP fornisce un'opzione per disabilitare i proxy sulle richieste e questa funzionalità deve essere utilizzata durante la comunicazione con IMDS. Per informazioni dettagliate, vedere la documentazione del client.

> [!IMPORTANT]
> Anche se non si è a conoscenza di alcuna configurazione proxy nell'ambiente, **è comunque necessario eseguire l'override di tutte le impostazioni proxy client predefinite**. Le configurazioni proxy possono essere individuate automaticamente e il mancato bypass di tali configurazioni espone i rischi di interruzione in caso di modifica della configurazione del computer in futuro.

## <a name="rate-limiting"></a>Limitazione della frequenza

In generale, le richieste a IMDS sono limitate a 5 richieste al secondo. Le richieste che superano questa soglia verranno rifiutate con 429 risposte. Le richieste alla categoria [identità gestita](#managed-identity) sono limitate a 20 richieste al secondo e 5 richieste simultanee.

## <a name="http-verbs"></a>Verbi HTTP

Attualmente sono supportati i verbi HTTP seguenti:

| Verbo | Descrizione |
|------|-------------|
| `GET` | Recuperare la risorsa richiesta

## <a name="parameters"></a>Parametri

Gli endpoint possono supportare i parametri obbligatori e/o facoltativi. Per informazioni dettagliate, vedere lo [schema](#schema) e la documentazione per l'endpoint specifico in questione.

### <a name="query-parameters"></a>Parametri di query

Gli endpoint IMDS supportano i parametri della stringa di query HTTP. Ad esempio: 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

Specifica i parametri:

| Nome | Valore |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

Le richieste con nomi di parametri di query duplicati verranno rifiutate.

### <a name="route-parameters"></a>Parametri di route

Per alcuni endpoint che restituiscono BLOB JSON di dimensioni maggiori, è supportata l'aggiunta di parametri di route all'endpoint della richiesta per filtrare in base a un subset della risposta: 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
I parametri corrispondono agli indici/chiavi che verrebbero usati per scorrere l'oggetto JSON durante l'interazione con una rappresentazione analizzata.

Ad esempio, `/metatadata/instance` restituisce l'oggetto JSON:
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

Se si vuole filtrare la risposta in base alla sola proprietà di calcolo, si invia la richiesta: 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

Analogamente, se si desidera filtrare in base a una proprietà annidata o a un elemento di matrice specifico, vengono mantenute le chiavi di Accodamento: 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
Filtra il primo elemento della `Network.interface` proprietà e restituisce:

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> Quando si applica il filtro a un nodo foglia, `format=json` non funziona. Per queste query `format=text` è necessario specificare in modo esplicito poiché il formato predefinito è JSON.

## <a name="schema"></a>SCHEMA

### <a name="data-format"></a>Formato dati

Per impostazione predefinita, IMDS restituisce i dati in formato JSON ( `Content-Type: application/json` ). Tuttavia, gli endpoint che supportano il filtro delle risposte (vedere [parametri di route](#route-parameters)) supportano anche il formato `text` .

Per accedere a un formato di risposta non predefinito, specificare il formato richiesto come parametro della stringa di query nella richiesta. Ad esempio:

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

Nelle risposte JSON tutte le primitive saranno di tipo `string` e i valori mancanti o non applicabili verranno sempre inclusi ma verranno impostati su una stringa vuota.

### <a name="versioning"></a>Controllo delle versioni

IMDS viene sottoversione e viene specificata la versione dell'API nella richiesta HTTP obbligatoria. L'unica eccezione a questo requisito è l'endpoint [Versions](#versions) , che può essere usato per recuperare in modo dinamico le versioni API disponibili.

Quando vengono aggiunte versioni più recenti, quelle precedenti rimangono comunque accessibili per la compatibilità, se gli script presentano dipendenze in formati di dati specifici.

Quando non si specifica una versione, viene visualizzato un errore con un elenco delle versioni supportate più recenti:
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### <a name="supported-api-versions"></a>Versioni API supportate
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 01/06/2019
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01
- 2020-12-01

### <a name="swagger"></a>Swagger

Una definizione di spavalderia completa per IMDS è disponibile all'indirizzo: https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>Disponibilità a livello di area

Il servizio è **disponibile** a livello generale in tutti i cloud di Azure.

## <a name="root-endpoint"></a>Endpoint radice

L'endpoint radice è `http://169.254.169.254/metadata` .

## <a name="endpoint-categories"></a>Categorie di endpoint

L'API IMDS contiene più categorie di endpoint che rappresentano origini dati diverse, ognuna delle quali contiene uno o più endpoint. Per informazioni dettagliate, vedere ogni categoria.

| Radice categoria | Descrizione | Versione introdotta |
|---------------|-------------|--------------------|
| `/metadata/attested` | Vedere [Dati con attestazione](#attested-data) | 2018-10-01
| `/metadata/identity` | Vedere [identità gestita tramite IMDS](#managed-identity) | 2018-02-01
| `/metadata/instance` | Vedere i [metadati dell'istanza](#instance-metadata) | 2017-04-02
| `/metadata/loadbalancer` | Vedere [recuperare i metadati di Load Balancer tramite IMDS](#load-balancer-metadata) | 2020-10-01
| `/metadata/scheduledevents` | Vedere [eventi pianificati tramite IMDS](#scheduled-events) | 2017-08-01
| `/metadata/versions` | Vedere le [versioni](#versions) | N/D

## <a name="versions"></a>Versioni

> [!NOTE]
> Questa funzionalità è stata rilasciata insieme alla versione 2020-10-01, attualmente implementata e potrebbe non essere ancora disponibile in ogni area.

### <a name="list-api-versions"></a>Elencare le versioni dell'API

Restituisce il set di versioni API supportate.

```
GET /metadata/versions
```

#### <a name="parameters"></a>Parametri

None (l'endpoint non viene sottoversione).

#### <a name="response"></a>Risposta

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>Metadati dell'istanza

### <a name="get-vm-metadata"></a>Ottenere i metadati della macchina virtuale

Espone i metadati importanti per l'istanza di macchina virtuale, inclusi calcolo, rete e archiviazione. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>Parametri

| Nome | Obbligatorio/facoltativo | Descrizione |
|------|-------------------|-------------|
| `api-version` | Obbligatoria | Versione utilizzata per il servizio della richiesta.
| `format` | Opzionale | Formato `json` o `text` della risposta. * Nota: potrebbe essere necessario quando si usano i parametri della richiesta

Questo endpoint supporta il filtraggio della risposta tramite [parametri di route](#route-parameters).

#### <a name="response"></a>Risposta

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Suddivisione schema:

**Calcolo**

| Data | Descrizione | Versione introdotta |
|------|-------------|--------------------|
| `azEnvironment` | Ambiente di Azure in cui è in esecuzione la macchina virtuale | 2018-10-01
| `customData` | Questa funzionalità è attualmente disabilitata. Questa documentazione verrà aggiornata quando diventerà disponibile | 2019-02-01
| `evictionPolicy` | Imposta come verrà eliminata una [macchina virtuale spot](../articles/virtual-machines/spot-vms.md) . | 2020-12-01
| `isHostCompatibilityLayerVm` | Identifica se la macchina virtuale è in esecuzione nel livello di compatibilità dell'host | 2020-06-01
| `licenseType` | Tipo di licenza per [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit). Questa funzionalità è disponibile solo per le macchine virtuali abilitate per vantaggio Azure Hybrid | 2020-09-01
| `location` | Area di Azure in cui la macchina virtuale è in esecuzione | 2017-04-02
| `name` | Nome della VM | 2017-04-02
| `offer` | Offre informazioni per l'immagine di macchina virtuale ed è presente solo per le immagini distribuite dalla raccolta immagini di Azure | 2017-04-02
| `osProfile.adminUsername` | Specifica il nome dell'account amministratore | 2020-07-15
| `osProfile.computerName` | Specifica il nome del computer | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Specifica se l'autenticazione della password è disabilitata. Questa operazione è disponibile solo per le macchine virtuali Linux | 2020-10-01
| `osType` | Linux o Windows | 2017-04-02
| `placementGroupId` | [Gruppo di posizionamento](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) del set di scalabilità di macchine virtuali | 2017-08-01
| `plan` | [Piano](/rest/api/compute/virtualmachines/createorupdate#plan) che contiene il nome, il prodotto e il server di pubblicazione per una macchina virtuale se si tratta di un'immagine di Azure Marketplace | 2018-04-02
| `platformUpdateDomain` |  [Dominio di aggiornamento](../articles/virtual-machines/availability.md) in cui è in esecuzione la macchina virtuale | 2017-04-02
| `platformFaultDomain` | [Dominio di errore](../articles/virtual-machines/availability.md) in cui è in esecuzione la macchina virtuale | 2017-04-02
| `priority` | Priorità della macchina virtuale. Per ulteriori informazioni, vedere le [VM spot](../articles/virtual-machines/spot-vms.md) | 2020-12-01
| `provider` | Provider della macchina virtuale | 2018-10-01
| `publicKeys` | [Raccolta di chiavi pubbliche](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) assegnate alla macchina virtuale e ai percorsi | 2018-04-02
| `publisher` | Autore dell'immagine della macchina virtuale | 2017-04-02
| `resourceGroupName` | [Gruppo di risorse](../articles/azure-resource-manager/management/overview.md) per la macchina virtuale | 2017-08-01
| `resourceId` | ID [completo](/rest/api/resources/resources/getbyid) della risorsa | 2019-03-11
| `sku` | SKU specifica per l'immagine della macchina virtuale | 2017-04-02
| `securityProfile.secureBootEnabled` | Identifica se l'avvio protetto UEFI è abilitato nella macchina virtuale | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Indica se la Trusted Platform Module virtuale (TPM) è abilitata nella macchina virtuale | 2020-06-01
| `storageProfile` | Vedere profilo di archiviazione di seguito | 01/06/2019
| `subscriptionId` | Sottoscrizione di Azure per la macchina virtuale | 2017-08-01
| `tags` | [Tag](../articles/azure-resource-manager/management/tag-resources.md) per la macchina virtuale  | 2017-08-01
| `tagsList` | Tag formattati come matrice JSON per un'analisi più semplice a livello programmatico  | 2019-06-04
| `version` | Versione dell'immagine della macchina virtuale | 2017-04-02
| `vmId` | [Identificatore univoco](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) della macchina virtuale | 2017-04-02
| `vmScaleSetName` | [Nome del set di scalabilità di macchine virtuali](../articles/virtual-machine-scale-sets/overview.md) del proprio set di scalabilità di macchine virtuali | 2017-12-01
| `vmSize` | [Dimensioni macchina virtuale](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | [Zona di disponibilità](../articles/availability-zones/az-overview.md) della macchina virtuale | 2017-12-01

**Profilo di archiviazione**

Il profilo di archiviazione di una macchina virtuale è suddiviso in tre categorie: riferimento immagine, disco del sistema operativo e dischi dati.

L'oggetto di riferimento immagine contiene le informazioni seguenti sull'immagine del sistema operativo:

| Dati | Descrizione |
|------|-------------|
| `id` | ID risorsa
| `offer` | Offerta dell'immagine della piattaforma o del marketplace
| `publisher` | Editore immagine
| `sku` | SKU dell'immagine
| `version` | Versione dell'immagine della piattaforma o del marketplace

L'oggetto disco del sistema operativo contiene le informazioni seguenti sul disco del sistema operativo usato dalla macchina virtuale:

| Dati | Descrizione |
|------|-------------|
| `caching` | Requisiti per la memorizzazione nella cache
| `createOption` | Informazioni sul modo in cui è stata creata la macchina virtuale
| `diffDiskSettings` | Impostazioni disco temporaneo
| `diskSizeGB` | Dimensioni del disco in GB
| `image`   | Disco rigido virtuale dell'immagine utente di origine
| `lun`     | Numero di unità logica del disco
| `managedDisk` | Parametri del disco gestito
| `name`    | Nome del disco
| `vhd`     | Disco rigido virtuale
| `writeAcceleratorEnabled` | Indica se writeAccelerator è abilitato sul disco

L'array di dischi dati contiene un elenco di dischi dati collegati alla macchina virtuale. Ogni oggetto disco dati contiene le informazioni seguenti:

Dati | Descrizione |
-----|-------------|
| `caching` | Requisiti per la memorizzazione nella cache
| `createOption` | Informazioni sul modo in cui è stata creata la macchina virtuale
| `diffDiskSettings` | Impostazioni disco temporaneo
| `diskSizeGB` | Dimensioni del disco in GB
| `encryptionSettings` | Impostazioni di crittografia per il disco
| `image` | Disco rigido virtuale dell'immagine utente di origine
| `managedDisk` | Parametri del disco gestito
| `name` | Nome del disco
| `osType` | Tipo di sistema operativo incluso nel disco
| `vhd` | Disco rigido virtuale
| `writeAcceleratorEnabled` | Indica se writeAccelerator è abilitato sul disco

**Network**

| Data | Descrizione | Versione introdotta |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | Indirizzo IPv4 locale della macchina virtuale | 2017-04-02
| `ipv4.publicIpAddress` | Indirizzo IPv4 pubblico della macchina virtuale | 2017-04-02
| `subnet.address` | Indirizzo della subnet della macchina virtuale | 2017-04-02
| `subnet.prefix` | Prefisso della subnet, ad esempio 24 | 2017-04-02
| `ipv6.ipAddress` | Indirizzo IPv6 locale della macchina virtuale | 2017-04-02
| `macAddress` | Indirizzo mac della macchina virtuale | 2017-04-02


#### <a name="sample-1-tracking-vm-running-on-azure"></a>Esempio 1. Rilevamento della macchina virtuale in esecuzione in Azure

Come provider di servizi, potrebbe essere necessario tenere traccia del numero di macchine virtuali che eseguono il proprio software o avere agenti che devono verificare l'univocità della macchina virtuale. Per poter ottenere un ID univoco per una macchina virtuale, usare il campo `vmId` dal Servizio metadati dell'istanza.

**Richiesta**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**Risposta**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>Esempio 2: posizionamento di repliche di dati diverse

Per alcuni scenari, il posizionamento di repliche dati diverse è di importanza primaria. Ad esempio, il [posizionamento della replica](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) o il posizionamento del contenitore di HDFS tramite un agente di [orchestrazione](https://kubernetes.io/docs/user-guide/node-selection/) potrebbe richiedere la conoscenza di `platformFaultDomain` e `platformUpdateDomain` la macchina virtuale è in esecuzione in.
Per prendere decisioni di questo tipo è anche possibile basarsi sulle [zone di disponibilità](../articles/availability-zones/az-overview.md) per le istanze.
È possibile eseguire query su questi dati direttamente tramite IMDS.

**Richiesta**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Risposta**

```
0
```

#### <a name="sample-3-get-vm-tags"></a>Esempio 3: ottenere i tag della VM

I tag delle macchine virtuali sono inclusi nell'endpoint instance/compute/tags dell'API.
È possibile che i tag siano stati applicati alla macchina virtuale di Azure per essere organizzati in modo logico in una tassonomia. I tag assegnati a una macchina virtuale possono essere recuperati tramite la richiesta seguente.

**Richiesta**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Risposta**

```
Department:IT;ReferenceNumber:123456;TestStatus:Pending
```

Il campo `tags` è una stringa con i tag delimitati da punto e virgola. Questo output può essere un problema se i punti e virgola vengono usati nei tag stessi. Se viene scritto un parser per estrarre i tag a livello di codice, è consigliabile utilizzare il `tagsList` campo. Il `tagsList` campo è una matrice JSON senza delimitatori e, di conseguenza, più facile da analizzare. L'oggetto Tags assegnato a una macchina virtuale può essere recuperato usando la richiesta seguente.

**Richiesta**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | jq
```

---

**Risposta**

#### <a name="windows"></a>[Windows](#tab/windows/)

```json
{
    "value":  [
                  {
                      "name":  "Department",
                      "value":  "IT"
                  },
                  {
                      "name":  "ReferenceNumber",
                      "value":  "123456"
                  },
                  {
                      "name":  "TestStatus",
                      "value":  "Pending"
                  }
              ],
    "Count":  3
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "ReferenceNumber",
    "value": "123456"
  },
  {
    "name": "TestStatus",
    "value": "Pending"
  }
]
```

---


#### <a name="sample-4-get-more-information-about-the-vm-during-support-case"></a>Esempio 4: ottenere altre informazioni sulla macchina virtuale durante il caso di supporto

Come provider di servizi è possibile ricevere una chiamata di supporto per la quale occorre sapere altre informazioni sulla macchina virtuale. Chiedere al cliente di condividere i metadati di calcolo può risultare utile per avere informazioni di base che consentano al personale del supporto tecnico di conoscere il tipo di macchina virtuale in Azure.

**Richiesta**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Risposta**

> [!NOTE]
> La risposta è una stringa JSON. La risposta di esempio che segue è di tipo pretty-print per una migliore leggibilità.

#### <a name="windows"></a>[Windows](#tab/windows/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "WindowsServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Windows",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "2019-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "WindowsServer",
            "publisher": "MicrosoftWindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Windows",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "UbuntuServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "Canonical",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "18.04-LTS",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

---

#### <a name="sample-5-get-the-azure-environment-where-the-vm-is-running"></a>Esempio 5: ottenere l'ambiente Azure in cui è in esecuzione la macchina virtuale

Azure offre vari cloud sovrani, ad esempio [Azure per enti pubblici](https://azure.microsoft.com/overview/clouds/government/). In taluni casi, per alcune decisioni di runtime è necessario l'ambiente di Azure. L'esempio seguente illustra come è possibile ottenere questo comportamento.

**Richiesta**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**Risposta**

```
AzurePublicCloud
```

Il cloud e i valori dell'ambiente Azure sono elencati qui.

| Cloud | Ambiente Azure |
|-------|-------------------|
| [Tutte le aree globali di Azure disponibili a livello generale](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [21Vianet per Azure Cina](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure Germania](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-6-retrieve-network-information"></a>Esempio 6: recuperare informazioni sulla rete

**Richiesta**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**Risposta**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}
```

#### <a name="sample-7-retrieve-public-ip-address"></a>Esempio 7: recuperare l'indirizzo IP pubblico

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>Dati attestati

### <a name="get-attested-data"></a>Ottenere i dati attestati

IMDS consente di garantire che i dati forniti provengano da Azure. Microsoft firma parte di queste informazioni, quindi è possibile verificare che un'immagine in Azure Marketplace sia quella in esecuzione in Azure.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>Parametri

| Nome | Obbligatorio/facoltativo | Descrizione |
|------|-------------------|-------------|
| `api-version` | Obbligatoria | Versione utilizzata per il servizio della richiesta.
| `nonce` | Facoltativo | Stringa a 10 cifre che funge da nonce crittografico. Se non viene specificato alcun valore, IMDS usa il timestamp UTC corrente.

#### <a name="response"></a>Risposta

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> A causa del meccanismo di memorizzazione nella cache di IMDS, è possibile che venga restituito un valore nonce precedentemente memorizzato nella cache.

Il BLOB della firma è una versione con firma [PKCS7](https://aka.ms/pkcs7)del documento. Contiene il certificato usato per la firma insieme ad alcuni dettagli specifici della VM.

Per le macchine virtuali create con Azure Resource Manager, il documento include,,, `vmId` `sku` `nonce` `subscriptionId` , `timeStamp` per la creazione e la scadenza del documento e le informazioni del piano sull'immagine. Le informazioni sul piano vengono popolate solo per le immagini di Azure Marketplace.

Per le macchine virtuali create con il modello di distribuzione classica, solo il `vmId` viene popolato. È possibile estrarre il certificato dalla risposta e usarlo per verificare che la risposta sia valida e che provenga da Azure.

Il documento decodificato contiene i campi seguenti:

| Data | Descrizione | Versione introdotta |
|------|-------------|--------------------|
| `licenseType` | Tipo di licenza per [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit). Questa funzionalità è disponibile solo per le macchine virtuali abilitate per vantaggio Azure Hybrid. | 2020-09-01
| `nonce` | Stringa che può essere fornita facoltativamente con la richiesta. Se non `nonce` è stato specificato alcun parametro, viene utilizzato il timestamp UTC (Coordinated Universal Time) corrente. | 2018-10-01
| `plan` | Il [piano immagine di Azure Marketplace](/rest/api/compute/virtualmachines/createorupdate#plan). Contiene l'ID del piano (nome), l'immagine del prodotto o l'offerta (prodotto) e l'ID editore (editore). | 2018-10-01
| `timestamp.createdOn` | Timestamp UTC del momento in cui è stato creato il documento firmato | 2018-20-01
| `timestamp.expiresOn` | Timestamp UTC per la scadenza del documento firmato | 2018-10-01
| `vmId` | [Identificatore univoco](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) della macchina virtuale | 2018-10-01
| `subscriptionId` | Sottoscrizione di Azure per la macchina virtuale | 2019-04-30
| `sku` | SKU specifica per l'immagine della macchina virtuale | 2019-11-01

> [!NOTE]
> Per le macchine virtuali classiche (non Azure Resource Manager), è garantito che venga popolato solo vmId.

Documento di esempio:
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>Esempio 1: verificare che la macchina virtuale sia in esecuzione in Azure

I fornitori di Azure Marketplace vogliono garantire che il software sia concesso in licenza per l'esecuzione solo in Azure. Se un utente copia il disco rigido virtuale in un ambiente locale, il fornitore deve essere in grado di rilevarlo. Tramite IMDS, questi fornitori possono ottenere dati firmati che garantiscono la risposta solo da Azure.

> [!NOTE]
> Questo esempio richiede l'installazione dell'utilità JQ.

**Convalida**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Verificare che la firma provenga da Microsoft Azure e verificare la presenza di errori nella catena di certificati.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Risultati**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Verificare che la firma provenga da Microsoft Azure e verificare la presenza di errori nella catena di certificati.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

---

> [!NOTE]
> A causa del meccanismo di memorizzazione nella cache di IMDS, `nonce` potrebbe essere restituito un valore precedentemente memorizzato nella cache.

Nel `nonce` documento firmato può essere confrontato se è stato specificato un `nonce` parametro nella richiesta iniziale.

> [!NOTE]
> Il certificato per il cloud pubblico e ogni cloud sovrano sarà diverso.

| Cloud | Certificato |
|-------|-------------|
| [Tutte le aree globali di Azure disponibili a livello generale](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [21Vianet per Azure Cina](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure Germania](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> I certificati potrebbero non corrispondere esattamente `metadata.azure.com` a per il cloud pubblico. Per questo motivo, la convalida della certificazione dovrebbe consentire un nome comune da qualsiasi `.metadata.azure.com` sottodominio.

Nei casi in cui non è possibile scaricare il certificato intermedio a causa di vincoli di rete durante la convalida, è possibile aggiungere il certificato intermedio. Azure esegue il rollup dei certificati, ovvero la pratica PKI standard. È necessario aggiornare i certificati aggiunti quando si verifica il rollover. Ogni volta che viene pianificata una modifica per l'aggiornamento del certificato intermedio, il Blog di Azure viene aggiornato e i clienti di Azure ricevono una notifica. 

È possibile trovare i certificati intermedi nel [repository PKI](https://www.microsoft.com/pki/mscorp/cps/default.htm). I certificati intermedi per ognuna delle aree possono essere diversi.

> [!NOTE]
> Il certificato intermedio per Azure Cina 21Vianet proverrà dalla CA radice globale DigiCert anziché da Baltimora.
Se sono stati aggiunti i certificati intermedi per Azure Cina come parte di una modifica dell'autorità di catena principale, è necessario aggiornare i certificati intermedi.


## <a name="managed-identity"></a>Identità gestita

Un'identità gestita, assegnata dal sistema, può essere abilitata nella macchina virtuale. È anche possibile assegnare una o più identità gestite assegnate dall'utente alla macchina virtuale.
È quindi possibile richiedere token per le identità gestite da IMDS. Usare questi token per eseguire l'autenticazione con altri servizi di Azure, ad esempio Azure Key Vault.

Per i passaggi dettagliati per abilitare questa funzionalità, vedere [Acquisire un token di accesso](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="load-balancer-metadata"></a>Metadati di Load Balancer
Quando si inseriscono le istanze di macchine virtuali o set di macchine virtuali dietro una Load Balancer Standard di Azure, è possibile usare IMDS per recuperare i metadati relativi al servizio di bilanciamento del carico e alle istanze. Per altre informazioni, vedere [recuperare le informazioni sul](../articles/load-balancer/instance-metadata-service-load-balancer.md)servizio di bilanciamento del carico.

## <a name="scheduled-events"></a>Eventi pianificati
È possibile ottenere lo stato degli eventi pianificati usando IMDS. L'utente può quindi specificare un set di azioni da eseguire su questi eventi. Per altre informazioni, vedere [eventi pianificati per Linux](../articles/virtual-machines/linux/scheduled-events.md) o [eventi pianificati per Windows](../articles/virtual-machines/windows/scheduled-events.md).


## <a name="sample-code-in-different-languages"></a>Codice di esempio in linguaggi diversi

La tabella seguente elenca esempi di chiamata a IMDS usando linguaggi diversi all'interno della macchina virtuale:

| Linguaggio | Esempio |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Go | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>Errori e debug

In caso di elementi dati non trovati o di richiesta non valida, il Servizio metadati dell'istanza restituisce errori HTTP standard. Ad esempio:

| Codice di stato HTTP | Motivo |
|------------------|--------|
| `200 OK` | La richiesta è stata completata.
| `400 Bad Request` | Manca `Metadata: true` l'intestazione o il parametro mancante `format=json` quando si esegue una query su un nodo foglia
| `404 Not Found` | L'elemento richiesto non esiste
| `405 Method Not Allowed` | Il metodo HTTP (verbo) non è supportato nell'endpoint.
| `410 Gone` | Riprovare tra qualche istante per un massimo di 70 secondi
| `429 Too Many Requests` | Sono stati superati i [limiti di frequenza](#rate-limiting) delle API
| `500 Service Error` | Ripetere l'operazione in un secondo momento

## <a name="frequently-asked-questions"></a>Domande frequenti

- Viene visualizzato l'errore `400 Bad Request, Required metadata header not specified`. Che cosa significa?
  - IMDS richiede che l'intestazione `Metadata: true` venga passata nella richiesta. Il passaggio di questa intestazione nella chiamata REST consente l'accesso a IMDS.

- Perché non riesco a ottenere le informazioni di calcolo per la macchina virtuale?
  - Attualmente, IMDS supporta solo le istanze create con Azure Resource Manager.

- La macchina virtuale è stata creata tramite Azure Resource Manager tempo fa. Perché non riesco a vedere le informazioni sui metadati di calcolo?
  - Se la macchina virtuale è stata creata dopo il 2016 settembre, aggiungere un [tag](../articles/azure-resource-manager/management/tag-resources.md) per iniziare a visualizzare i metadati di calcolo. Se la macchina virtuale è stata creata prima del 2016 settembre, aggiungere o rimuovere estensioni o dischi dati nell'istanza di macchina virtuale per aggiornare i metadati.

- Perché non vengono visualizzati tutti i dati popolati per una nuova versione?
  - Se la macchina virtuale è stata creata dopo il 2016 settembre, aggiungere un [tag](../articles/azure-resource-manager/management/tag-resources.md) per iniziare a visualizzare i metadati di calcolo. Se la macchina virtuale è stata creata prima del 2016 settembre, aggiungere o rimuovere estensioni o dischi dati nell'istanza di macchina virtuale per aggiornare i metadati.

- Perché viene ricevuto l'errore `500 Internal Server Error` o `410 Resource Gone` ?
  - Ripetere la richiesta. Per ulteriori informazioni, vedere [gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults). Se il problema persiste, creare un problema di supporto nella portale di Azure per la macchina virtuale.

- Questo funziona per le istanze del set di scalabilità di macchine virtuali?
  - Sì, IMDS è disponibile per le istanze del set di scalabilità di macchine virtuali.

- I tag sono stati aggiornati nei set di scalabilità di macchine virtuali, ma non vengono visualizzati nelle istanze (a differenza delle macchine virtuali a istanza singola). Si è verificato un errore?
  - Attualmente i tag per i set di scalabilità di macchine virtuali vengono visualizzati solo alla macchina virtuale al riavvio, alla ricreazione dell'immagine o alla modifica del disco nell'istanza.

- Perché non vengono visualizzati i dettagli delle informazioni sugli SKU per la VM `instance/compute` ?
  - Per le immagini personalizzate create da Azure Marketplace, la piattaforma Azure non mantiene le informazioni sullo SKU per l'immagine personalizzata e i dettagli per le macchine virtuali create dall'immagine personalizzata. Si tratta di un modello che non viene quindi esposto nei dettagli della macchina virtuale `instance/compute` .

- Perché si è verificato un timeout della richiesta per la chiamata al servizio?
  - Le chiamate ai metadati devono essere effettuate dall'indirizzo IP primario assegnato alla scheda di rete primaria della macchina virtuale. Inoltre, se sono state modificate le route, è necessario che sia presente una route per l'indirizzo 169.254.169.254/32 nella tabella di routing locale della macchina virtuale.

    ### <a name="windows"></a>[Windows](#tab/windows/)

    1. Esegui il dump della tabella di routing locale e cerca la voce IMDS. Ad esempio:
        ```console
        > route print
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
            168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
        169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
        ... (continues) ...
        ```
    1. Verificare che esista una route per `169.254.169.254` e annotare l'interfaccia di rete corrispondente (ad esempio, `172.16.69.7` ).
    1. Eseguire il dump della configurazione dell'interfaccia e trovare l'interfaccia che corrisponde a quella a cui viene fatto riferimento nella tabella di routing, annotando l'indirizzo MAC (fisico).
        ```console
        > ipconfig /all
        ... (continues) ...
        Ethernet adapter Ethernet:

        Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
        Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
        Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
        DHCP Enabled. . . . . . . . . . . : Yes
        Autoconfiguration Enabled . . . . : Yes
        Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
        IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
        Subnet Mask . . . . . . . . . . . : 255.255.255.0
        ... (continues) ...
        ```
    1. Verificare che l'interfaccia corrisponda alla scheda di interfaccia di rete primaria e all'indirizzo IP primario della macchina virtuale. È possibile trovare la scheda di interfaccia di rete e l'indirizzo IP primari osservando la configurazione di rete nel portale di Azure oppure cercandolo con l'interfaccia della riga di comando di Azure. Prendere nota degli indirizzi IP privati (e dell'indirizzo MAC se si usa l'interfaccia della riga di comando). Ecco un esempio di interfaccia della riga di comando di PowerShell:
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: wintest767 True 00-0D-3A-E5-1C-C0
        ```
    1. Se non corrispondono, aggiornare la tabella di routing in modo che la scheda di interfaccia di rete primaria e l'IP siano assegnati.

    ### <a name="linux"></a>[Linux](#tab/linux/)

    1. Esegui il dump della tabella di routing locale con un comando come `netstat -r` e cerca la voce IMDS, ad esempio:
        ```console
        ~$ netstat -r
        Kernel IP routing table
        Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
        default         _gateway        0.0.0.0         UG        0 0          0 eth0
        168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
        169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
        172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
        ```
    1. Verificare che esista una route per `169.254.169.254` e annotare l'interfaccia di rete corrispondente (ad esempio `eth0` ).
    1. Dump della configurazione dell'interfaccia per l'interfaccia corrispondente nella tabella di routing (si noti che il nome esatto del file di configurazione può variare)
        ```console
        ~$ cat /etc/netplan/50-cloud-init.yaml
        network:
        ethernets:
            eth0:
                dhcp4: true
                dhcp4-overrides:
                    route-metric: 100
                dhcp6: false
                match:
                    macaddress: 00:0d:3a:e4:c7:2e
                set-name: eth0
        version: 2
        ```
    1. Se si usa un indirizzo IP dinamico, prendere nota dell'indirizzo MAC. Se si usa un indirizzo IP statico, è possibile notare gli IP e/o l'indirizzo MAC elencati.
    1. Verificare che l'interfaccia corrisponda alla scheda di interfaccia di rete primaria e all'indirizzo IP primario della macchina virtuale. È possibile trovare la scheda di interfaccia di rete e l'indirizzo IP primari osservando la configurazione di rete nel portale di Azure oppure cercandolo con l'interfaccia della riga di comando di Azure. Prendere nota degli indirizzi IP privati (e dell'indirizzo MAC se si usa l'interfaccia della riga di comando). Ecco un esempio di interfaccia della riga di comando di PowerShell:
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: ipexample606 True 00-0D-3A-E4-C7-2E
        ```
    1. Se non corrispondono, aggiornare la tabella di routing in modo che l'interfaccia di rete primaria/IP sia destinata a.

    ---

- Clustering di failover in Windows Server
  - Quando si esegue una query su IMDS con il clustering di failover, è talvolta necessario aggiungere una route alla tabella di routing. Ecco come:

    1. Aprire un prompt dei comandi con privilegi di amministratore.

    1. Eseguire il comando seguente e prendere nota dell'indirizzo dell'interfaccia per la destinazione di rete ( `0.0.0.0` ) nella tabella di route IPv4.

    ```bat
    route print
    ```

    > [!NOTE]
    > L'output di esempio seguente è da una macchina virtuale Windows Server con cluster di failover abilitato. Per semplicità, l'output contiene solo la tabella di route IPv4.

    ```
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
            10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
            10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
    127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
        169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
    169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
            224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
    255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
    ```

    Eseguire il comando seguente e usare l'indirizzo dell'interfaccia per la destinazione di rete ( `0.0.0.0` ), ovvero ( `10.0.1.10` ) in questo esempio.

    ```bat
    route add 169.254.169.254/32 10.0.1.10 metric 1 -p
    ```

## <a name="support"></a>Supporto

Se non si riesce a ottenere una risposta ai metadati dopo più tentativi, è possibile creare un problema di supporto nella portale di Azure.

## <a name="product-feedback"></a>Feedback sul prodotto

È possibile fornire commenti e suggerimenti sul prodotto al canale di feedback degli utenti in macchine virtuali > servizio metadati dell'istanza [qui](https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627)

## <a name="next-steps"></a>Passaggi successivi

- [Acquisire un token di accesso per la macchina virtuale](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

- [Eventi pianificati per Linux](../articles/virtual-machines/linux/scheduled-events.md)

- [Eventi pianificati per Windows](../articles/virtual-machines/windows/scheduled-events.md)
