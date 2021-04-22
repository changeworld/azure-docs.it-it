---
title: Integrazione git per Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su Azure Machine Learning si integra con un repository Git locale per tenere traccia delle informazioni sul repository, sul ramo e sul commit corrente come parte di un'esecuzione di training.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 04/08/2021
ms.openlocfilehash: 60dca43f95b190791c8fb593042ed612340a3af5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874550"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integrazione git per Azure Machine Learning

[Git](https://git-scm.com/) è un sistema di controllo della versione diffuso che consente di condividere e collaborare ai progetti. 

Azure Machine Learning supporta completamente i repository Git per il rilevamento del lavoro: è possibile clonare i repository direttamente nell'area di lavoro condivisa file system, usare Git nella workstation locale o usare Git da una pipeline CI/CD.

Quando si invia un processo Azure Machine Learning, se i file di origine sono archiviati in un repository Git locale, le informazioni sul repository vengono rilevate come parte del processo di training.

Poiché Azure Machine Learning tiene traccia delle informazioni da un repository Git locale, non è associato a un repository centrale specifico. Il repository può essere clonato da GitHub, GitLab, Bitbucket, Azure DevOps o da qualsiasi altro servizio compatibile con Git.

> [!TIP]
> Usare Visual Studio Code per interagire con Git tramite un'interfaccia utente grafica. Per connettersi a un'Azure Machine Learning di calcolo remota usando Visual Studio Code, vedere Connettersi a un'istanza di calcolo Azure Machine Learning in Visual Studio Code [(anteprima)](how-to-set-up-vs-code-remote.md)
>
> Per altre informazioni sulle funzionalità Visual Studio Code controllo della versione, vedere [Using Version Control in VS Code](https://code.visualstudio.com/docs/editor/versioncontrol) and Working with [GitHub in VS Code](https://code.visualstudio.com/docs/editor/github).

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Clonare i repository GIT nel file system dell'area di lavoro
Azure Machine Learning fornisce un file system condiviso per tutti gli utenti nell'area di lavoro.
Per clonare un repository Git in questa condivisione file, è consigliabile creare un'istanza di calcolo & [aprire un terminale](how-to-access-terminal.md).
Dopo aver aperto il terminale, è possibile accedere a un client Git completo e clonare e usare Git tramite l'esperienza dell'interfaccia della riga di comando di Git.

È consigliabile clonare il repository nella directory degli utenti in modo che altri utenti non si collisionino direttamente nel ramo di lavoro.

È possibile clonare qualsiasi repository Git a cui è possibile eseguire l'autenticazione (GitHub, Azure Repos, BitBucket e così via)

Per altre informazioni sulla clonazione, vedere la guida [sull'uso dell'interfaccia della riga di comando di Git.](https://guides.github.com/introduction/git-handbook/)

## <a name="authenticate-your-git-account-with-ssh"></a>Autenticare l'account Git con SSH
### <a name="generate-a-new-ssh-key"></a>Generare una nuova chiave SSH
1) [Aprire la finestra del terminale](./how-to-access-terminal.md) nella Azure Machine Learning Notebook.

2) Incollare il testo seguente, sostituendo l'indirizzo di posta elettronica.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Verrà creata una nuova chiave SSH, usando il messaggio di posta elettronica specificato come etichetta.

```
> Generating public/private rsa key pair.
```

3) Quando viene richiesto di immettere un file in cui salvare il tasto, premere INVIO. In questo modo viene accettato il percorso del file predefinito.

4) Verificare che il percorso predefinito sia "/home/azureuser/.ssh" e premere INVIO. In caso contrario, specificare il percorso '/home/azureuser/.ssh'.

> [!TIP]
> Assicurarsi che la chiave SSH sia salvata in '/home/azureuser/.ssh'. Questo file viene salvato nell'istanza di calcolo è accessibile solo dal proprietario dell'istanza di calcolo

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) Al prompt digitare una passphrase sicura. È consigliabile aggiungere una passphrase alla chiave SSH per una sicurezza più avanzata

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>Aggiungere la chiave pubblica all'account Git
1) Nella finestra del terminale copiare il contenuto del file di chiave pubblica. Se la chiave è stata rinominata, sostituire id_rsa.pub con il nome del file di chiave pubblica.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **Copiare e incollare nel terminale**
> * Windows: `Ctrl-Insert` per copiare e usare `Ctrl-Shift-v` o per `Shift-Insert` incollare.
> * Mac OS: `Cmd-c` per copiare e `Cmd-v` per incollare.
> * FireFox o Internet Explorer potrebbero non supportare correttamente le autorizzazioni per gli appunti.

2) Selezionare e copiare l'output della chiave negli Appunti.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure DevOps](/azure/devops/repos/git/use-ssh-keys-to-authenticate#step-2--add-the-public-key-to-azure-devops-servicestfs)  Iniziare dal **passaggio 2.**

+ [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2). Iniziare dal **passaggio 4.**

### <a name="clone-the-git-repository-with-ssh"></a>Clonare il repository Git con SSH

1) Copiare l'URL del clone SSH Git dal repository Git.

2) Incollare l'URL `git clone` nel comando seguente per usare l'URL del repository Git SSH. L'aspetto sarà simile al seguente:

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

Verrà visualizzata una risposta simile alla seguente:

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

SSH può visualizzare l'impronta digitale SSH del server e chiedere di verificarla. È necessario verificare che l'impronta digitale visualizzata corrisponda a una delle impronte digitali nella pagina delle chiavi pubbliche SSH.

SSH visualizza questa impronta digitale quando si connette a un host sconosciuto per proteggersi dagli attacchi [man-in-the-middle](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10)). Dopo aver accettato l'impronta digitale dell'host, SSH non richiederà di nuovo l'impronta digitale a meno che non cambi.

3) Quando viene chiesto se si vuole continuare la connessione, digitare `yes` . Git clona il repository e configura l'origine remota per la connessione con SSH per i comandi Git futuri.

## <a name="track-code-that-comes-from-git-repositories"></a>Tenere traccia del codice che proviene dai repository Git

Quando si invia un'esecuzione di training da Python SDK o Machine Learning dell'interfaccia della riga di comando, i file necessari per eseguire il training del modello vengono caricati nell'area di lavoro. Se il comando è disponibile nell'ambiente di sviluppo, il processo di caricamento lo usa per verificare se i `git` file sono archiviati in un repository Git. In tal caso, le informazioni dal repository Git vengono caricate anche come parte dell'esecuzione del training. Queste informazioni vengono archiviate nelle proprietà seguenti per l'esecuzione del training:

| Proprietà | Comando Git usato per ottenere il valore | Descrizione |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | URI da cui è stato clonato il repository. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | URI da cui è stato clonato il repository. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Ramo attivo al momento dell'invio dell'esecuzione. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Ramo attivo al momento dell'invio dell'esecuzione. |
| `azureml.git.commit` | `git rev-parse HEAD` | Hash di commit del codice inviato per l'esecuzione. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Hash di commit del codice inviato per l'esecuzione. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`se il ramo/commit è dirty; in caso contrario, `false` . |

Queste informazioni vengono inviate per le esecuzioni che usano uno strumento di stima, una pipeline di Machine Learning o un'esecuzione di script.

Se i file di training non si trovano in un repository Git nell'ambiente di sviluppo o il comando non è disponibile, non viene rilevata alcuna `git` informazione correlata a Git.

> [!TIP]
> Per verificare se il comando git è disponibile nell'ambiente di sviluppo, aprire una sessione della shell, un prompt dei comandi, PowerShell o un'altra interfaccia della riga di comando e digitare il comando seguente:
>
> ```
> git --version
> ```
>
> Se è installato e nel percorso, si riceve una risposta simile a `git version 2.4.1` . Per altre informazioni sull'installazione di Git nell'ambiente di sviluppo, vedere il [sito Web Git](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Visualizzare le informazioni registrate

Le informazioni git vengono archiviate nelle proprietà per un'esecuzione di training. È possibile visualizzare queste informazioni usando il portale di Azure, Python SDK e l'interfaccia della riga di comando. 

### <a name="azure-portal"></a>Portale di Azure

1. Nel portale [di Studio selezionare](https://ml.azure.com)l'area di lavoro.
1. Selezionare __Experiments__(Esperimenti) e quindi selezionare uno degli esperimenti.
1. Selezionare una delle esecuzioni dalla __colonna NUMERO DI__ ESECUZIONE .
1. Selezionare __Output e log__ e quindi espandere le voci __logs__ e __azureml.__ Selezionare il collegamento che inizia con __### \_ azure.__

Le informazioni registrate contengono testo simile al codice JSON seguente:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

Dopo l'invio di un'esecuzione di training, [viene restituito un oggetto](/python/api/azureml-core/azureml.core.run%28class%29) Run. `properties`L'attributo di questo oggetto contiene le informazioni git registrate. Ad esempio, il codice seguente recupera l'hash del commit:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

Il `az ml run` comando dell'interfaccia della riga di comando può essere usato per recuperare le proprietà da un'esecuzione. Ad esempio, il comando seguente restituisce le proprietà per l'ultima esecuzione nell'esperimento denominato `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Per altre informazioni, vedere la documentazione [di riferimento di az ml run.](/cli/azure/ml/run)

## <a name="next-steps"></a>Passaggi successivi

* [Usare destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md)