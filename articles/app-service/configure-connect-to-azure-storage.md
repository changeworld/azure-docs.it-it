---
title: Aggiungere Archiviazione di Azure (contenitore)
description: Informazioni su come collegare una condivisione di rete personalizzata in un'app in contenitori in Servizio app di Azure. Condividere file tra app, gestire il contenuto statico in remoto e accedere in locale e così via.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: f844b5b413cda2cb16f9701970857be65fe6d91d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777618"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>Accedere ad Archiviazione di Azure (anteprima) come una condivisione di rete da un contenitore nel servizio app

::: zone pivot="container-windows"

Questa guida illustra come collegare file Archiviazione di Azure come condivisione di rete a un contenitore windows nel servizio app. Sono [supportate solo File di Azure e](../storage/files/storage-how-to-use-files-cli.md) le [condivisioni file Premium.](../storage/files/storage-how-to-create-file-share.md) I vantaggi includono la protezione e la portabilità del contenuto, l'accesso a più app e molteplici metodi di trasferimento.

> [!NOTE]
>Archiviazione di Azure nel servizio app è **disponibile in anteprima e** non è supportato **per** gli scenari **di produzione**.

::: zone-end

::: zone pivot="container-linux"

Questa guida illustra come collegare le Archiviazione di Azure a un servizio app del contenitore Linux. I vantaggi includono contenuto protetto, portabilità del contenuto, archiviazione permanente, accesso a più app e più metodi di trasferimento.

> [!NOTE]
>Archiviazione di Azure nel servizio app è **disponibile in anteprima** per Servizio app in Linux e app Web per contenitori. Non è supportata **per gli** scenari **di produzione**.

::: zone-end

## <a name="prerequisites"></a>Prerequisiti

::: zone pivot="container-windows"

- [Un'app contenitore Windows esistente in Servizio app di Azure](quickstart-custom-container.md)
- [Creare una condivisione file di Azure](../storage/files/storage-how-to-use-files-cli.md)
- [Caricare file nella condivisione file di Azure](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- [Un'app Servizio app in Linux esistente.](index.yml)
- Un [account Archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-cli)
- Una [condivisione file di Azure e una directory](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> File di Azure è un'archiviazione non predefinita e fatturata separatamente, non inclusa nell'app Web. Non supporta l'uso della configurazione del firewall a causa di limitazioni dell'infrastruttura.
>

## <a name="limitations"></a>Limitazioni

::: zone pivot="container-windows"

- Archiviazione di Azure nel servizio app non è attualmente **supportato** per scenari bring your own code (app di Windows non in contenitori).
- Archiviazione di Azure nel servizio app **non supporta l'uso** della configurazione del **firewall di** archiviazione a causa di limitazioni dell'infrastruttura.
- Archiviazione di Azure con il servizio app consente di specificare fino **a cinque punti** di montaggio per ogni app.
- Archiviazione di Azure montato in un'app non è accessibile tramite endpoint FTP/FTPs del servizio app. Usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

::: zone pivot="container-linux"

- Archiviazione di Azure nel servizio app supporta il **montaggio File di Azure** contenitori (lettura/scrittura) e i contenitori BLOB di **Azure** (sola lettura)
- Archiviazione di Azure nel servizio app consente di specificare fino **a cinque punti** di montaggio per ogni app.
- Archiviazione di Azure montato in un'app non è accessibile tramite endpoint FTP/FTPs del servizio app. Usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

## <a name="link-storage-to-your-app"></a>Collegare l'archiviazione all'app

::: zone pivot="container-windows"

Dopo aver creato [l'account Archiviazione di Azure,](#prerequisites)la condivisione file e la directory , è ora possibile configurare l'app con Archiviazione di Azure.

Per montare un File di Azure share in una directory nell'app del servizio app, usare il [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) comando . Il tipo di archiviazione deve essere AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

È consigliabile eseguire questa operazione per qualsiasi altra directory che si vuole collegare a una File di Azure condivisione.

::: zone-end

::: zone pivot="container-linux"

Dopo aver creato [l'account Archiviazione di Azure,](#prerequisites)la condivisione file e la directory , è ora possibile configurare l'app con Archiviazione di Azure.

Per montare un account di archiviazione in una directory nell'app del servizio app, usare il [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) comando . Il tipo di archiviazione può essere AzureBlob o AzureFiles. AzureFiles viene usato in questo esempio. L'impostazione del percorso di montaggio corrisponde alla cartella all'interno del contenitore che si vuole montare Archiviazione di Azure. Impostandolo su '/' l'intero contenitore viene montato Archiviazione di Azure.


> [!CAUTION]
> La directory specificata come percorso di montaggio nell'app Web deve essere vuota. Qualsiasi contenuto archiviato in questa directory verrà eliminato quando viene aggiunto un montaggio esterno. Se si esegue la migrazione dei file di un'app esistente, creare un copia di backup dell'app e del relativo contenuto prima di iniziare.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

È consigliabile eseguire questa operazione per tutte le altre directory che si vogliono collegare a un account di archiviazione.

::: zone-end

## <a name="verify-linked-storage"></a>Verificare l'archiviazione collegata

Dopo aver collegato la condivisione all'app, è possibile verificarlo eseguendo il comando seguente:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>Passaggi successivi

::: zone pivot="container-windows"

- [Eseguire la migrazione di software personalizzato Servizio app di Azure usando un contenitore personalizzato.](tutorial-custom-container.md?pivots=container-windows)

::: zone-end

::: zone pivot="container-linux"

- [Configurare un contenitore personalizzato](configure-custom-container.md?pivots=platform-linux).

::: zone-end