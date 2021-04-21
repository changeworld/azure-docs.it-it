---
title: Usare SCP per spostare i file da e verso una macchina virtuale
description: Spostare in sicurezza file da e verso una macchina virtuale Linux in Azure usando SCP e una coppia di chiavi SSH.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/20/2021
ms.author: cynthn
ms.subservice: ''
ms.openlocfilehash: edfc44f79cff25486fde6326ac954fe5b575d846
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816441"
---
# <a name="use-scp-to-move-files-to-and-from-a-linux-vm"></a>Usare SCP per spostare i file da e verso una macchina virtuale Linux 

Questo articolo illustra come spostare file da una workstation a una macchina virtuale Linux di Azure, o viceversa, usando Secure Copy (SCP). Il trasferimento di file tra una workstation e una macchina virtuale Linux in modo rapido e sicuro è un aspetto essenziale della gestione dell'infrastruttura di Azure. 

Per questo articolo è necessaria una macchina virtuale Linux distribuita in Azure tramite [file di chiavi SSH pubbliche e private](mac-create-ssh-keys.md). È necessario anche un client SCP per il computer locale: È basato su SSH ed è incluso nella shell Bash predefinita della maggior parte dei computer Linux e Mac e PowerShell.


## <a name="quick-commands"></a>Comandi rapidi

Copiare un file in una macchina virtuale Linux

```bash
scp file azureuser@azurehost:directory/targetfile
```

Copiare un file da una macchina virtuale Linux

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata

A titolo di esempio, si sposterà un file di configurazione di Azure in una macchina virtuale Linux e si scaricherà la directory di un file di log, usando in entrambi i casi SCP e chiavi SSH.   

## <a name="ssh-key-pair-authentication"></a>Autenticazione della coppia di chiavi SSH

SCP usa SSH per il livello di trasporto. SSH gestisce l'autenticazione nell'host di destinazione e sposta il file in un tunnel crittografato fornito per impostazione predefinita con SSH. Per l'autenticazione SSH è possibile usare nomi utente e password. Per una maggiore sicurezza, tuttavia, è consigliabile eseguire l'autenticazione tramite file di chiavi SSH pubbliche e private. Dopo che SSH ha autenticato la connessione, SCP avvia il processo di copia del file. Combinando un file `~/.ssh/config` correttamente configurato con chiavi SSH pubbliche e private, è possibile stabilire la connessione SCP usando solo un nome di server (o un indirizzo IP). Se si ha una sola chiave SSH, SCP la cerca nella directory `~/.ssh/` e, per impostazione predefinita, la usa per accedere alla macchina virtuale.

Per altre informazioni sulla configurazione del file `~/.ssh/config` e delle chiavi SSH pubbliche e private, vedere [Creare chiavi SSH](mac-create-ssh-keys.md).

## <a name="scp-a-file-to-a-linux-vm"></a>Copia sicura di un file in una macchina virtuale Linux

Nel primo esempio si copia un file di configurazione di Azure in una macchina virtuale Linux usata per distribuire l'automazione. Poiché questo file contiene credenziali di API di Azure, che includono informazioni riservate, la sicurezza è particolarmente importante e il tunnel crittografato fornito con SSH assicura la protezione dei contenuti del file.

Il comando seguente consente di copiare il file *.azure/config* locale in una macchina virtuale di Azure con FQDN: *myserver.eastus.cloudapp.azure.com*. Se non è impostato un [FQDN,](../create-fqdn.md)è anche possibile usare l'indirizzo IP della macchina virtuale. Il nome utente dell'amministratore nella macchina virtuale di Azure è *azureuser*. Il file è destinato ad essere inserito nella directory */home/azureuser/*. Nel comando sostituire i valori predefiniti con quelli personali.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Copia sicura di una directory da una macchina virtuale Linux

In questo esempio si copia una directory di file di log dalla macchina virtuale Linux in una workstation. Poiché è possibile che un file di log contenga dati riservati, con SCP si ha la certezza che i contenuti dei file di log vengano crittografati. L'uso di SCP per il trasferimento dei file costituisce il modo più semplice per spostare in totale sicurezza file e directory di log in una workstation.

Il comando seguente consente di copiare i file contenuti nella directory */home/azureuser/logs/* nella directory /tmp locale della macchina virtuale di Azure:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

Il flag indica a SCP di copiare in modo ricorsivo i file e le directory dal punto `-r` della directory elencata nel comando .  Osservare inoltre come la sintassi della riga di comando sia simile al comando di copia `cp`.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire utenti, SSH e dischi di controllo o di ripristino in VM Linux di Azure tramite l'estensione VMAccess](../extensions/vmaccess.md?toc=/azure/virtual-machines/linux/toc.json)
