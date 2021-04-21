---
title: Gestire reti virtuali - Interfaccia della riga di comando di Azure - Database di Azure per MySQL - Server flessibile
description: Creare e gestire reti virtuali per il server flessibile di Database di Azure per MySQL usando l'interfaccia della riga di comando di Azure
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7da8062f18d737af9d19df54863bc56c7268910c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776916"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Creare e gestire reti virtuali per il server flessibile di Database di Azure per MySQL usando l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica

Il server flessibile del Database di Azure per MySQL supporta due tipi di metodi di connettività di rete che si escludono a vicenda per la connessione al server flessibile. Le due opzioni sono:

- Accesso pubblico (indirizzi IP consentiti)
- Accesso privato (integrazione rete virtuale)

In questo articolo verrà illustrata la creazione del server MySQL con accesso privato **(integrazione rete virtuale)** tramite l'interfaccia della riga di comando di Azure. Con *l'accesso privato (integrazione rete virtuale)* è possibile distribuire il server flessibile nella propria [rete virtuale di Azure.](../../virtual-network/virtual-networks-overview.md) Le reti virtuali di Azure forniscono comunicazioni di rete private e sicure. In Accesso privato le connessioni al server MySQL sono limitate solo all'interno della rete virtuale. Per altre informazioni, vedere Accesso [privato (integrazione rete virtuale).](./concepts-networking.md#private-access-vnet-integration)

Nel server flessibile di Database di Azure per MySQL è possibile distribuire il server solo in una rete virtuale e in una subnet durante la creazione del server. Dopo aver distribuito il server flessibile in una rete virtuale e in una subnet, non è possibile spostarlo in un'altra rete virtuale, subnet o in Accesso pubblico *(indirizzi IP consentiti).*

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile aprire Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere **INVIO** per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questo argomento di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Prerequisiti

È necessario accedere all'account con il comando [az login](/cli/azure/reference-index#az_login). Si noti la **proprietà ID,** che fa riferimento **all'ID sottoscrizione** per l'account Azure.

```azurecli-interactive
az login
```

Selezionare la sottoscrizione specifica nell'account tramite il comando [az account set](/cli/azure/account#az_account_set). Prendere nota del valore **ID** dell'output **di az login** da usare come valore per l'argomento **subscription** nel comando. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Per ottenere tutte le sottoscrizioni, usare [az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-mysql-flexible-server-using-cli"></a>Creare un server flessibile di Database di Azure per MySQL tramite l'interfaccia della riga di comando
È possibile usare il `az mysql flexible-server` comando per creare il server flessibile con accesso privato *(integrazione rete virtuale).* Questo comando usa l'accesso privato (integrazione rete virtuale) come metodo di connettività predefinito. Se non viene specificata, verranno create automaticamente una rete virtuale e una subnet. È anche possibile specificare la rete virtuale e la subnet già esistenti usando l'ID subnet. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Sono disponibili diverse opzioni per creare un server flessibile usando l'interfaccia della riga di comando, come illustrato negli esempi seguenti.

>[!Important]
> L'uso di questo comando delega la subnet **a Microsoft.DBforMySQL/flexibleServers.** Con questa delega solo i server flessibili di Database di Azure per MySQL possono usare tale subnet. Gli altri tipi di risorsa di Azure non possono trovarsi nella subnet delegata.
>

Per l'elenco completo dei parametri configurabili [dell'interfaccia](/cli/azure/mysql/flexible-server) della riga di comando, vedere la documentazione di riferimento dell'interfaccia della riga di comando di Azure. Ad esempio, nei comandi seguenti è possibile specificare facoltativamente il gruppo di risorse.

- Creare un server flessibile usando la rete virtuale predefinita, la subnet con il prefisso dell'indirizzo predefinito
    ```azurecli-interactive
    az mysql flexible-server create
    ```
- Creare un server flessibile usando una rete virtuale e una subnet già esistenti. Se la rete virtuale e la subnet fornite non esistono, verranno create la rete virtuale e la subnet con il prefisso dell'indirizzo predefinito.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --subnet mySubnet
    ```

- Creare un server flessibile usando la rete virtuale, la subnet e l'ID subnet già esistenti. Nella subnet specificata non deve essere distribuita alcuna altra risorsa e questa subnet verrà delegata a **Microsoft.DBforMySQL/flexibleServers,** se non è già stata delegata.
    ```azurecli-interactive
    az mysql flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > La rete virtuale e la subnet devono essere nella stessa area e nella stessa sottoscrizione del server flessibile.
<
- Creare un server flessibile usando una nuova rete virtuale, una subnet con prefisso di indirizzo non predefinito.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
Per l'elenco completo dei parametri configurabili [dell'interfaccia](/cli/azure/mysql/flexible-server) della riga di comando, vedere la documentazione di riferimento dell'interfaccia della riga di comando di Azure.


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla rete nel server flessibile [di Database di Azure per MySQL.](./concepts-networking.md)
- [Creare e gestire la rete virtuale del server flessibile di Database di Azure per MySQL tramite il portale di Azure](./how-to-manage-virtual-network-portal.md).
- Altre informazioni sulla rete virtuale del server flessibile di [Database di Azure per MySQL](./concepts-networking.md#private-access-vnet-integration).