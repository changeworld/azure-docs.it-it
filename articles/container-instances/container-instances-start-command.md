---
title: Eseguire l'override del punto di ingresso nell'istanza del contenitore
description: Impostare una riga di comando per eseguire l'override del punto di ingresso in un'immagine del contenitore quando si distribuisce un'istanza di Azure Container
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 5898decbf4108d48bb9e84019d659075b18fd043
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771084"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Impostare la riga di comando in un'istanza del contenitore per eseguire l'override dell'operazione della riga di comando predefinita

Quando si crea un'istanza del contenitore, specificare facoltativamente un comando per eseguire l'override dell'istruzione della riga di comando predefinita nell'immagine del contenitore. Questo comportamento è simile `--entrypoint` all'argomento della riga di comando di `docker run` .

Analogamente [all'impostazione delle](container-instances-environment-variables.md) variabili di ambiente per le istanze del contenitore, la specifica di una riga di comando iniziale è utile per i processi batch in cui è necessario preparare ogni contenitore in modo dinamico con una configurazione specifica dell'attività.

## <a name="command-line-guidelines"></a>Linee guida della riga di comando

* Per impostazione predefinita, la riga di comando specifica un *singolo processo che viene avviato senza una shell* nel contenitore. Ad esempio, la riga di comando potrebbe eseguire uno script Python o un file eseguibile. Il processo può specificare parametri o argomenti aggiuntivi.

* Per eseguire più comandi, iniziare la riga di comando impostando un ambiente della shell supportato nel sistema operativo del contenitore. Esempi:

  |Sistema operativo  |Shell predefinita  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Seguire le convenzioni della shell per combinare più comandi da eseguire in sequenza.

* A seconda della configurazione del contenitore, potrebbe essere necessario impostare un percorso completo per l'eseguibile o gli argomenti della riga di comando.

* Impostare criteri di [riavvio appropriati](container-instances-restart-policy.md) per l'istanza del contenitore, a seconda che la riga di comando specifichi un'attività con esecuzione lunga o un'attività run-once. Ad esempio, è consigliabile un criterio di riavvio di `Never` o `OnFailure` per un'attività run-once. 

* Se sono necessarie informazioni sul punto di ingresso predefinito impostato in un'immagine del contenitore, usare il [comando docker image inspect.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

## <a name="command-line-syntax"></a>Sintassi della riga di comando

La sintassi della riga di comando varia a seconda dell'API o dello strumento di Azure usato per creare le istanze. Se si specifica un ambiente della shell, osservare anche le convenzioni di sintassi dei comandi della shell.

* [comando az container create:][az-container-create] passare una stringa con il `--command-line` parametro . Esempio: `--command-line "python myscript.py arg1 arg2"` ).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell cmdlet: passare una stringa con il `-Command` parametro . Esempio: `-Command "echo hello"`.

* portale di Azure: nella proprietà **override Command** della configurazione del contenitore specificare un elenco di stringhe delimitato da virgole, senza virgolette. Esempio: `python, myscript.py, arg1, arg2` ). 

* Resource Manager modello o file YAML o uno degli Azure SDK: specificare la proprietà della riga di comando come matrice di stringhe. Esempio: matrice JSON `["python", "myscript.py", "arg1", "arg2"]` in un modello Resource Manager dati. 

  Se si ha familiarità con la [sintassi di Dockerfile,](https://docs.docker.com/engine/reference/builder/) questo formato è simile al formato *exec* dell'istruzione CMD.

### <a name="examples"></a>Esempio

|    |  Interfaccia della riga di comando di Azure   | Portale | Modello | 
| ---- | ---- | --- | --- |
| **Comando singolo** | `--command-line "python myscript.py arg1 arg2"` | **Override del comando**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| **Più comandi** | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Override del comando**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Esempio di interfaccia della riga di comando di Azure

Ad esempio, modificare il comportamento dell'immagine del contenitore [microsoft/aci-wordcount,][aci-wordcount] che analizza il testo nell'Amleto di Hamlet di *Hamlet* per trovare le parole più frequenti. Invece di analizzare *Hamlet,* è possibile impostare una riga di comando che punti a un'origine di testo diversa.

Per visualizzare l'output del contenitore [microsoft/aci-wordcount][aci-wordcount] quando analizza il testo predefinito, eseguirlo con il [comando az container create][az-container-create] seguente. Non viene specificata alcuna riga di comando di avvio, quindi viene eseguito il comando del contenitore predefinito. A scopo illustrativo, questo esempio imposta le [variabili di](container-instances-environment-variables.md) ambiente per trovare le prime 3 parole lunghe almeno cinque lettere:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Quando lo stato del contenitore viene visualizzato come *Terminato* (usare [az container show][az-container-show] per controllare lo stato), visualizzare il log con [az container logs][az-container-logs] per visualizzare l'output.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Output:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Configurare ora un secondo contenitore di esempio per analizzare testo diverso specificando una riga di comando diversa. Lo script Python eseguito dal contenitore, *wordcount.py*, accetta un URL come argomento ed elabora il contenuto della pagina anziché il valore predefinito.

Ad esempio, per determinare le prime 3 parole lunghe almeno cinque lettere in *Romeo e Rometo:*

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Di nuovo, quando il contenitore mostra lo stato *Terminato*, visualizzare l'output tramite la visualizzazione dei log del contenitore:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Output:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Passaggi successivi

Gli scenari basati su attività, ad esempio l'elaborazione batch di un set di dati di grandi dimensioni con diversi contenitori, possono trarre vantaggio dalle righe di comando personalizzate in fase di esecuzione. Per altre informazioni sull'esecuzione di contenitori basati su attività, vedere [Eseguire attività in contenitori con criteri di riavvio](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
