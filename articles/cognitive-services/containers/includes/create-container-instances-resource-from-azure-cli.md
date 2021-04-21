---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa dell'istanza del contenitore di Azure dall'interfaccia della riga di comando di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 32b00e031e3cf865093c267084117a8704b6e272
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799975"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Creare una risorsa istanza di Azure Container dall'interfaccia della riga di comando di Azure

Il codice YAML seguente definisce la risorsa Istanza di Azure Container. Copiare e incollare il contenuto in un nuovo file denominato e sostituire i `my-aci.yaml` valori commentati con i propri. Fare riferimento al [formato del modello per][template-format] YAML valido. Fare riferimento ai [repository e alle][repositories-and-images] immagini dei contenitori per i nomi delle immagini disponibili e il relativo repository corrispondente. Per altre informazioni sul riferimento YAML per le istanze del contenitore, vedere Informazioni di riferimento [su YAML: Istanze di Azure Container][aci-yaml-ref].

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
properties:
  imageRegistryCredentials: # This is only required if you are pulling a non-public image that requires authentication to access. For example Text Analytics for health.
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Non tutte le posizioni hanno la stessa disponibilità di CPU e memoria. Fare riferimento alla tabella [percorso e risorse][location-to-resource] per l'elenco delle risorse disponibili per i contenitori per posizione e sistema operativo.

Si baserà sul file YAML creato per il [`az container create`][azure-container-create] comando. Dall'interfaccia della riga di comando di Azure eseguire `az container create` il comando sostituendo con il `<resource-group>` proprio. Inoltre, per proteggere i valori all'interno di una distribuzione YAML, fare riferimento ai [valori sicuri][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

L'output del comando è se valido, dopo qualche tempo l'output viene modificato in una stringa JSON che `Running...` rappresenta la risorsa ACI appena creata. L'immagine del contenitore è molto probabilmente non disponibile per un po', ma la risorsa è ora distribuita.

> [!TIP]
> Prestare particolare attenzione alle posizioni delle offerte di Servizio cognitivo di Azure in anteprima pubblica, in quanto yaml dovrà essere regolato di conseguenza in modo che corrisponda alla posizione.

[azure-container-create]: /cli/azure/container#az_container_create
[template-format]: /azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../container-image-tags.md
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
