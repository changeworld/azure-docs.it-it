---
title: 'Esercitazione: Distribuire LAMP e WordPress in una macchina virtuale'
description: Questa esercitazione illustra come installare lo stack LAMP e WordPress in una macchina virtuale Linux in Azure.
author: cynthn
ms.collection: linux
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.openlocfilehash: 5365bad5fdea2a8213defc103f0cdd966ebe50a5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816350"
---
# <a name="tutorial-install-a-lamp-stack-on-an-azure-linux-vm"></a>Esercitazione: Installare uno stack LAMP in una macchina virtuale Linux di Azure

Questo articolo illustra come distribuire un server Web Apache, MySQL e PHP (lo stack LAMP) in una VM Ubuntu in Azure. Per verificare il funzionamento del server LAMP, è facoltativamente possibile installare e configurare un sito WordPress. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una VM Ubuntu 
> * Aprire la porta 80 per il traffico Web
> * Installare Apache, MySQL e PHP
> * Verificare l'installazione e la configurazione
> * Installare WordPress 

Questa installazione è utilizzabile per i test rapidi o il modello di prova. Per altre informazioni sullo stack LAMP, incluse le raccomandazioni per un ambiente di produzione, vedere la [documentazione di Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

Questa esercitazione usa l'interfaccia della riga di comando all'interno di [Azure Cloud Shell](../../cloud-shell/overview.md), che viene costantemente aggiornato alla versione più recente. Per aprire Cloud Shell, selezionare **Prova** nella parte superiore di qualsiasi blocco di codice.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una VM con il comando [az vm create](/cli/azure/vm). 

L'esempio seguente crea una macchina virtuale denominata *myVM* e le chiavi SSH, se non esistono già in un percorso predefinito. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`. Il comando imposta anche *azureuser* come nome utente dell'amministratore. Questo nome verrà usato nei passaggi successivi per la connessione alla macchina virtuale. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Dopo che la VM è stata creata, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente. Prendere nota di `publicIpAddress`. Questo indirizzo verrà usato nei passaggi successivi per l'accesso alla macchina virtuale.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web 

Per impostazione predefinita, nelle VM Linux distribuite in Azure sono consentite solo le connessioni SSH. Poiché questa macchina virtuale verrà usata come server Web, è necessario aprire la porta 80 da Internet. Usare il comando [az vm open-port](/cli/azure/vm) per aprire la porta.  
 
```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

Per altre informazioni sull'apertura di porte alla macchina virtuale, vedere [Aprire le porte](nsg-quickstart.md).

## <a name="ssh-into-your-vm"></a>Usare SSH per connettersi alla macchina virtuale

Se non si conosce già l'indirizzo IP pubblico della macchina virtuale, eseguire il [comando az network public-ip list.](/cli/azure/network/public-ip) Questo indirizzo IP sarà necessario in alcuni passaggi successivi.

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Usare il comando seguente per creare una sessione SSH con la macchina virtuale. Sostituire l'indirizzo IP pubblico corretto della macchina virtuale. In questo esempio l'indirizzo IP è *40.68.254.142*. *azureuser* è il nome utente dell'amministratore impostato al momento della creazione della macchina virtuale.

```bash
ssh azureuser@40.68.254.142
```


## <a name="install-apache-mysql-and-php"></a>Installare Apache, MySQL e PHP

Usare il comando seguente per aggiornare le origini dei pacchetti Ubuntu e installare Apache, MySQL e PHP. Si noti che alla fine del comando è presente un accento circonflesso (^) che fa parte del nome del pacchetto `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```

Viene chiesto di installare i pacchetti e le altre dipendenze. In questo modo vengono installate le estensioni PHP minime richieste per l'uso di PHP con MySQL.  

## <a name="verify-apache"></a>Verificare Apache

Controllare la versione di Apache con il comando seguente:
```bash
apache2 -v
```

Con Apache installato e la porta 80 aperta per la macchina virtuale, è ora possibile accedere al server Web da Internet. Per visualizzare Apache2 Ubuntu Default Page (Pagina predefinita Apache2 Ubuntu), aprire un Web browser e immettere l'indirizzo IP pubblico della VM. Usare l'indirizzo IP pubblico usato per stabilire la connessione SSH alla VM:

![Pagina predefinita di Apache][3]


## <a name="verify-and-secure-mysql"></a>Verificare e proteggere MySQL

Controllare la versione di MySQL con il comando seguente. Si noti il parametro `V` in maiuscolo:

```bash
mysql -V
```

Per proteggere l'installazione di MySQL, impostando anche una password radice, eseguire lo script `mysql_secure_installation`. 

```bash
sudo mysql_secure_installation
```

Facoltativamente, è possibile configurare il plug-in di convalida password (scelta consigliata). Impostare quindi una password per l'utente ROOT MySQL e configurare le restanti impostazioni di sicurezza per l'ambiente. È consigliabile rispondere "Y" (sì) a tutte le domande.

Per provare le funzionalità di MySQL, ovvero creare un database MySQL, aggiungere utenti o modificare le impostazioni di configurazione, accedere a MySQL. Questo passaggio non è obbligatorio per completare l'esercitazione.

```bash
sudo mysql -u root -p
```

Al termine, chiudere il prompt mysql digitando `\q`.

## <a name="verify-php"></a>Verificare PHP

Controllare la versione di PHP con il comando seguente:

```bash
php -v
```

Per eseguire altri test, creare rapidamente una pagina di informazioni PHP da visualizzare in un browser. Il comando seguente crea la pagina di informazioni di PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Ora è possibile controllare la pagina di informazioni di PHP creata. Aprire un browser e passare a `http://yourPublicIPAddress/info.php`. Sostituire l'indirizzo IP pubblico della VM. Dovrebbe avere un aspetto simile a questa immagine.

![Pagina di informazioni di PHP][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è distribuito un server LAMP in Azure. Si è appreso come:

> [!div class="checklist"]
> * Creare una VM Ubuntu
> * Aprire la porta 80 per il traffico Web
> * Installare Apache, MySQL e PHP
> * Verificare l'installazione e la configurazione
> * Installare WordPress nel server LAMP

Passare all'esercitazione successiva per apprendere come proteggere i server Web con i certificati TLS/SSL.

> [!div class="nextstepaction"]
> [Proteggere il server Web con TLS](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
