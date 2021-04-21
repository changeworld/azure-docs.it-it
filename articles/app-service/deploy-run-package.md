---
title: Eseguire l'app da un pacchetto ZIP
description: Distribuire il pacchetto ZIP dell'app con atomicità. Migliorare la prevedibilità e l'affidabilità del comportamento dell'app durante il processo di distribuzione ZIP.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: d3315370342f54091598aa3f77f70f03bda4ad33
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772740"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Eseguire l'app in Servizio app di Azure direttamente da un pacchetto ZIP

In [Servizio app di Azure](overview.md), è possibile eseguire le app direttamente da un file di pacchetto ZIP di distribuzione. Questo articolo illustra come abilitare questa funzionalità nell'app.

Tutti gli altri metodi di distribuzione nel servizio app hanno qualcosa in comune: i file vengono distribuiti in *D:\home\site\wwwroot* nell'app (o */home/site/wwwroot* per le app Linux). Poiché la stessa directory viene usata dall'app in fase di esecuzione, è possibile che la distribuzione non riesca a causa di conflitti di blocco dei file e che l'app si comporti in modo imprevedibile perché alcuni file non sono ancora aggiornati.

Al contrario, quando si esegue direttamente da un pacchetto, i file nel pacchetto non vengono copiati nella directory *wwwroot.* Il pacchetto ZIP viene invece montato direttamente come directory *wwwroot* di sola lettura. L'esecuzione direttamente da un pacchetto offre diversi vantaggi:

- Elimina i conflitti di blocco dei file tra distribuzione e runtime.
- Garantisce che solo le app completamente distribuite siano in esecuzione in qualsiasi momento.
- Possono essere distribuiti in un'app di produzione (con il riavvio).
- Migliora le prestazioni delle distribuzioni Azure Resource Manager.
- Si possono ridurre i tempi di avvio a freddo, in particolare per le funzioni di JavaScript con grandi alberi del pacchetto npm.

> [!NOTE]
> Attualmente sono supportati solo i file di pacchetto ZIP.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Abilitare l'esecuzione dal pacchetto

`WEBSITE_RUN_FROM_PACKAGE`L'impostazione dell'app consente l'esecuzione da un pacchetto. Per impostarlo, eseguire il comando seguente con l'interfaccia della riga di comando di Azure.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` consente di eseguire l'app da un pacchetto locale all'app. È anche possibile [eseguire da un pacchetto remoto.](#run-from-external-url-instead)

## <a name="run-the-package"></a>Eseguire il pacchetto

Il modo più semplice per eseguire un pacchetto nel servizio app è con il comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_zip) dell'interfaccia della riga di comando di Azure. Ad esempio:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Poiché l'impostazione dell'app è impostata, questo comando non estrae il contenuto del pacchetto `WEBSITE_RUN_FROM_PACKAGE` nella directory *D:\home\site\wwwroot* dell'app. Carica invece il file ZIP così com'è in *D:\home\data\SitePackages* e crea un *packagename.txt* nella stessa directory, che contiene il nome del pacchetto ZIP da caricare in fase di esecuzione. Se si carica il pacchetto ZIP in un modo diverso, ad esempio [FTP,](deploy-ftp.md)è necessario creare manualmente la directory *D:\home\data\SitePackages* e il file *packagename.txt* file.

Il comando riavvia anche l'app. Poiché è impostato, il servizio app monta il pacchetto caricato come directory wwwroot di sola lettura ed esegue l'app direttamente `WEBSITE_RUN_FROM_PACKAGE` da tale directory montata. 

## <a name="run-from-external-url-instead"></a>Eseguire invece da URL esterno

È anche possibile eseguire un pacchetto da un URL esterno, ad esempio Archiviazione BLOB di Azure. È possibile usare [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) per caricare i file di pacchetto nell'account di archiviazione BLOB. È consigliabile usare un contenitore di archiviazione privato con una firma di accesso condiviso [per](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) consentire al runtime del servizio app di accedere al pacchetto in modo sicuro. 

Dopo aver caricato il file nell'archivio BLOB e avere un URL di firma di accesso condiviso per il file, impostare `WEBSITE_RUN_FROM_PACKAGE` l'impostazione dell'app sull'URL. L'esempio seguente usa l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Se si pubblica un pacchetto aggiornato con lo stesso nome nell'archivio BLOB, è necessario riavviare l'app in modo che il pacchetto aggiornato venga caricato nel servizio app.

## <a name="troubleshooting"></a>Risoluzione dei problemi

- L'esecuzione diretta da un pacchetto rende `wwwroot` di sola lettura. L'app riceverà un errore se tenta di scrivere file in questa directory.
- I formati TAR e GZIP non sono supportati.
- Il file ZIP può essere al massimo di 1 GB
- Questa funzionalità non è compatibile con la [cache locale.](overview-local-cache.md)
- Per migliorare le prestazioni di avvio a freddo, usare l'opzione zip locale ( `WEBSITE_RUN_FROM_PACKAGE` =1).

## <a name="more-resources"></a>Altre risorse

- [Distribuzione continua per Servizio app di Azure](deploy-continuous-deployment.md)
- [Distribuire il codice con un file ZIP o WAR](deploy-zip.md)
