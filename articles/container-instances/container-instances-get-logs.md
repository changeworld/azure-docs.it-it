---
title: Ottenere i log dell'istanza del contenitore & eventi
description: Informazioni su come recuperare i log e gli eventi dei contenitori Istanze di Azure Container risolvere i problemi relativi ai contenitori
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: b2b7ffb2cb4a7b1171afa42c2ef5a64b2bd928f8
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379282"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Recuperare log ed eventi dei contenitori in Istanze di Azure Container

Quando si dispone di un contenitore di gestione non Istanze di Azure Container, iniziare visualizzando i log con [az container logs][az-container-logs]e trasmettere il relativo errore standard out e standard con [az container attach][az-container-attach]. È anche possibile visualizzare i log e gli eventi per le istanze del contenitore nel portale di Azure oppure inviare i dati di log ed eventi per i gruppi di contenitori [Monitoraggio di Azure log](container-instances-log-analytics.md).

## <a name="view-logs"></a>Visualizzare i log

Per visualizzare i log generati dal codice dell'applicazione all'interno di un contenitore, è possibile usare il comando [az container logs][az-container-logs].

Di seguito è riportato l'output del log del contenitore basato su attività di esempio in Impostare la riga di comando [in](container-instances-start-command.md#azure-cli-example)un'istanza del contenitore , dopo aver fornito un URL non valido usando un override della riga di comando:

```azurecli
az container logs --resource-group myResourceGroup --name mycontainer
```

```output
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>Collegare i flussi di output

Il comando [az container attach][az-container-attach] fornisce informazioni diagnostiche durante l'avvio del contenitore. Dopo l'avvio, il contenitore trasmette STDOUT e STDERR alla console locale.

Ad esempio, di seguito è riportato l'output del contenitore basato su attività in Impostare la riga di comando [in](container-instances-start-command.md#azure-cli-example)un'istanza del contenitore , dopo aver fornito un URL valido di un file di testo di grandi dimensioni da elaborare:

```azurecli
az container attach --resource-group myResourceGroup --name mycontainer
```

```output
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Recuperare gli eventi di diagnostica

Se la distribuzione del contenitore non riesce, esaminare le informazioni di diagnostica fornite dal provider Istanze di Azure Container risorse. Per visualizzare gli eventi per il proprio contenitore, eseguire il comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

L'output include le proprietà principali del contenitore e gli eventi di distribuzione (visualizzati qui troncati):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>Passaggi successivi
Scoprire come [risolvere i problemi comuni dei contenitori e delle distribuzioni](container-instances-troubleshooting.md) per Istanze di Azure Container.

Informazioni su come inviare dati di log ed eventi per i gruppi di contenitori [Monitoraggio di Azure log .](container-instances-log-analytics.md)

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
