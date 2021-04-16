---
title: Gestire la disponibilità elevata con ridondanza della zona - Interfaccia della riga di comando di Azure - Server flessibile di Database di Azure per MySQL
description: Questo articolo descrive come configurare la disponibilità elevata con ridondanza della zona nel server flessibile di Database di Azure per MySQL con l'interfaccia della riga di comando di Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/1/2021
ms.custom: references_regions
ms.openlocfilehash: fb53ad309c741fc898bcf3e27347038c0e382ea4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509149"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Gestire la disponibilità elevata con ridondanza della zona nel server flessibile di Database di Azure per MySQL con l'interfaccia della riga di comando di Azure

> [!NOTE]
> Il server flessibile di Database di Azure per MySQL è in anteprima pubblica. 

L'articolo descrive come abilitare o disabilitare la configurazione a disponibilità elevata con ridondanza della zona al momento della creazione del server nel server flessibile. È possibile disabilitare la disponibilità elevata con ridondanza della zona anche dopo la creazione del server. L'abilitazione della disponibilità elevata con ridondanza della zona dopo la creazione del server non è supportata.

La funzionalità a disponibilità elevata effettua il provisioning della replica primaria e standby fisicamente separata in zone diverse. Per altre informazioni, vedere la [documentazione sui concetti relativi alla disponibilità elevata](./concepts/../concepts-high-availability.md). L'abilitazione o la disabilitazione della disponibilità elevata non modifica le altre impostazioni, tra cui la configurazione della rete virtuale, le impostazioni del firewall e la conservazione dei backup. La disabilitazione della disponibilità elevata non influisce sulla connettività e sulle operazioni dell'applicazione.

> [!IMPORTANT]
> La disponibilità elevata con ridondanza della zona è disponibile in un set limitato di aree. Esaminare le aree supportate [qui.](https://docs.microsoft.com/azure/mysql/flexible-server/overview#azure-regions) 

## <a name="prerequisites"></a>Prerequisiti
- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Installare o aggiornare l'interfaccia della riga di comando di Azure alla versione più recente. Vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
-  Accedere all'account Azure usando [il comando az login.](/cli/azure/reference-index#az-login) Annotare la proprietà **id** che fa riferimento all'**ID sottoscrizione** per l'account Azure.

    ```azurecli-interactive
    az login
    ````

- Se si dispone di più sottoscrizioni, scegliere la sottoscrizione appropriata in cui si vuole creare il server usando il ```az account set``` comando .
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

## <a name="enable-high-availability-during-server-creation"></a>Abilitare la disponibilità elevata durante la creazione del server
È possibile creare server solo usando piani tariffari per utilizzo generico o ottimizzati per la memoria con disponibilità elevata. È possibile abilitare la disponibilità elevata per un server solo durante la fase di creazione.

**Utilizzo:**

```azurecli
az mysql flexible-server create [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Esempio:**
```azurecli
az mysql flexible-server create --name myservername --sku-name Standard-D2ds_v4 --resource-group myresourcegroup --high-availability Enabled
```

## <a name="disable-high-availability"></a>Disabilitare la disponibilità elevata

È possibile disabilitare la disponibilità elevata usando [il comando az mysql flexible-server update.](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) Si noti che la disabilitazione della disponibilità elevata è supportata solo se il server è stato creato con disponibilità elevata. 

```azurecli
az mysql flexible-server update [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Esempio:**
```azurecli
az mysql flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
```


## <a name="next-steps"></a>Passaggi successivi

-   Informazioni sulla [continuità aziendale](./concepts-business-continuity.md)
-   Informazioni sulla disponibilità [elevata con ridondanza della zona](./concepts-high-availability.md)
