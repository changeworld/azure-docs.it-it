---
title: Usare le chiavi SSH per connettersi alle macchine virtuali Linux
description: Informazioni su come generare e usare chiavi SSH da un computer Windows per connettersi a una macchina virtuale Linux in Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: 0199a47b2306d7d461ba61057c7ab1015015df08
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835561"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Come usare le chiavi SSH con Windows in Azure

Questo articolo è per gli [](#create-an-ssh-key-pair) utenti di Windows che vogliono [](#connect-to-your-vm) creare e usare chiavi *SSH (Secure Shell)* per connettersi alle macchine virtuali Linux in Azure. È anche possibile [generare e archiviare chiavi SSH](../ssh-keys-portal.md) nel portale di Azure da usare durante la creazione di macchine virtuali nel portale.


Per usare le chiavi SSH da un client Linux o macOS, vedere i [passaggi rapidi](mac-create-ssh-keys.md). Per una panoramica più dettagliata di SSH, vedere Procedura dettagliata: Creare e gestire chiavi SSH per l'autenticazione a una [macchina virtuale Linux in Azure.](create-ssh-keys-detailed.md)

## <a name="overview-of-ssh-and-keys"></a>Panoramica di SSH e delle chiavi

[SSH](https://www.ssh.com/ssh/) è un protocollo di connessione crittografato che consente l'accesso sicuro tramite connessioni non protette. SSH è il protocollo di connessione predefinito per le macchine virtuali Linux ospitate in Azure. Anche se SSH stesso fornisce una connessione crittografata, l'uso di password con SSH rende comunque la macchina virtuale vulnerabile agli attacchi di forza bruta. È consigliabile connettersi a una macchina virtuale tramite SSH usando una coppia di chiavi pubblica-privata, nota anche come *chiavi SSH.* 

La coppia di chiavi pubblica-privata è simile al blocco sulla porta principale. Il blocco è esposto al **pubblico, chiunque** abbia la chiave giusta può aprire la porta. La chiave è **privata e** viene data solo alle persone attendibili perché può essere usata per sbloccare la porta. 

- La *chiave pubblica viene* inserita nella macchina virtuale Linux quando si crea la macchina virtuale. 

- La *chiave privata* rimane nel sistema locale. Sulla chiave privata è necessario mantenere la massima riservatezza, evitando di condividerla.

Quando ci si connette alla macchina virtuale Linux, la macchina virtuale testa il client SSH per assicurarsi che abbia la chiave privata corretta. In caso positivo, al client viene concesso il diritto di accesso alla macchina virtuale. 

A seconda dei criteri di sicurezza dell'organizzazione, è possibile riutilizzare una singola coppia di chiavi per accedere a più macchine virtuali e servizi di Azure. Non è necessaria una coppia di chiavi separata per ogni macchina virtuale. 

La chiave pubblica può essere condivisa con chiunque, ma solo l'utente (o l'infrastruttura di sicurezza locale) deve avere accesso alla chiave privata.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>Client SSH

Le versioni recenti Windows 10 includono comandi [client OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) per creare e usare chiavi SSH ed eseguire connessioni SSH da PowerShell o da un prompt dei comandi. Questo è il modo più semplice per creare una connessione SSH alla macchina virtuale Linux da un computer Windows. 

È anche possibile usare Bash nel [Azure Cloud Shell](../../cloud-shell/overview.md) per connettersi alla macchina virtuale. È possibile usare Cloud Shell in [un Web browser,](https://shell.azure.com/bash)dal [portale di Azure](https://portal.azure.com)o come terminale in Visual Studio Code usando l'estensione Account [di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

È anche possibile installare [il](/windows/wsl/about) sottosistema Windows per Linux per connettersi alla macchina virtuale tramite SSH e usare altri strumenti Linux nativi all'interno di una shell Bash.

## <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH

Creare una coppia di chiavi SSH usando il `ssh-keygen` comando . Immettere un nome file o usare il valore predefinito visualizzato tra parentesi (ad esempio `C:\Users\username/.ssh/id_rsa` ).  Immettere una passphrase per il file o lasciare vuota la passphrase se non si vuole usare una passphrase. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>Creare una macchina virtuale usando la chiave

Per creare una macchina virtuale Linux che usa chiavi SSH per l'autenticazione, specificare la chiave pubblica SSH durante la creazione della macchina virtuale.

Usando l'interfaccia della riga di comando di Azure è possibile specificare il percorso e il nome file per la chiave pubblica usando `az vm create` e il `--ssh-key-value` parametro .

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

Con PowerShell usare `New-AzVM` e aggiungere la chiave SSH alla configurazione della macchina virtuale usando '. Per un esempio, vedere [Avvio rapido: Creare una macchina virtuale Linux in Azure con PowerShell.](quick-create-powershell.md)

Se si esegue molte distribuzioni tramite il portale, è possibile caricare la chiave pubblica in Azure, dove può essere facilmente selezionata quando si crea una macchina virtuale dal portale. Per altre informazioni, vedere [Caricare una chiave SSH.](../ssh-keys-portal.md#upload-an-ssh-key)


## <a name="connect-to-your-vm"></a>Connettersi alla macchina virtuale

Con la chiave pubblica distribuita nella VM di Azure e la chiave privata nel sistema locale, stabilire una connessione SSH alla VM usando l'indirizzo IP o il nome DNS della VM. Sostituire *azureuser* e *10.111.12.123* nel comando seguente con il nome utente amministratore, l'indirizzo IP (o il nome di dominio completo) e il percorso della chiave privata:

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

Se è stata configurata una passphrase al momento della creazione della coppia di chiavi, immettere la passphrase quando richiesto.

Se la macchina virtuale usa i criteri di accesso JIT, è necessario richiedere l'accesso per connettersi alla macchina virtuale. Per altre informazioni sui criteri JIT, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)](../../security-center/security-center-just-in-time.md).


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle chiavi SSH nel portale di Azure, vedere Generare e archiviare chiavi [SSH](../ssh-keys-portal.md) nel portale di Azure da usare per la creazione di macchine virtuali nel portale.

- Per passaggi dettagliati, opzioni ed esempi avanzati di utilizzo delle chiavi SSH, vedere Procedura dettagliata [per creare coppie di chiavi SSH.](create-ssh-keys-detailed.md)

- Si può anche usare PowerShell in Azure Cloud Shell per generare le chiavi SSH e stabilire connessioni SSH alle VM Linux. Vedere le [informazioni di avvio rapido di PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

- Se si verificano problemi nell'uso di SSH per connettersi alle macchine virtuali Linux, vedere [Risoluzione dei problemi di connessione SSH a una macchina virtuale Linux di Azure](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
