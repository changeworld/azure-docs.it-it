---
title: Configurare le identità gestite con Configurazione app di Azure
description: Informazioni sul funzionamento delle identità gestite in Configurazione app di Azure e su come configurare un'identità gestita
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: e4fdff2515dde941b2e9037a21ad931ac27b6fef
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764226"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Come usare le identità gestite per Configurazione app di Azure

Questo argomento illustra come creare un'identità gestita per Configurazione app di Azure. Un'identità gestita Azure Active Directory (AAD) consente Configurazione app di Azure accedere facilmente ad altre risorse protette da AAD, ad esempio Azure Key Vault. L'identità è gestita dalla piattaforma Azure. Non è necessario effettuare il provisioning o ruotare i segreti. Per altre informazioni sulle identità gestite in AAD, vedere [Identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

All'applicazione possono essere concessi due tipi di identità:

- **Un'identità assegnata dal sistema** è associata all'archivio di configurazione. Viene eliminato se l'archivio di configurazione viene eliminato. Un archivio di configurazione può avere una sola identità assegnata dal sistema.
- **Un'identità assegnata dall'utente** è una risorsa di Azure autonoma che può essere assegnata all'archivio di configurazione. Un archivio di configurazione può avere più identità assegnate dall'utente.

## <a name="adding-a-system-assigned-identity"></a>Aggiunta di un'identità assegnata dal sistema

La creazione di un archivio di Configurazione app con un'identità assegnata dal sistema richiede l'impostazione di una proprietà aggiuntiva nell'archivio.

### <a name="using-the-azure-cli"></a>Con l'interfaccia della riga di comando di Azure

Per configurare un'identità gestita usando l'interfaccia della riga di comando di Azure, usare il [comando az appconfig identity assign] in un archivio di configurazione esistente. Sono disponibili tre opzioni per l'esecuzione degli esempi di questa sezione:

- Usare [Azure Cloud Shell](../cloud-shell/overview.md) dal portale di Azure.
- Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", situato nell'angolo in alto a destra di ogni blocco di codice indicato di seguito.
- [Installare la versione più recente dell'interfaccia della](/cli/azure/install-azure-cli) riga di comando di Azure (2.1 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale.

La procedura seguente illustra la creazione di un archivio di Configurazione app e l'assegnazione di un'identità tramite l'interfaccia della riga di comando:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login]. Usare un account associato alla sottoscrizione di Azure:

    ```azurecli-interactive
    az login
    ```

1. Creare un archivio di Configurazione app usando l'interfaccia della riga di comando. Per altri esempi su come usare l'interfaccia della riga di comando con Configurazione app di Azure, vedere Esempi dell'interfaccia della riga [di comando di Configurazione app:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Eseguire il [comando az appconfig identity assign] per creare l'identità assegnata dal sistema per questo archivio di configurazione:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Aggiunta di un'identità assegnata dall'utente

Per creare un archivio di Configurazione app con un'identità assegnata dall'utente, è necessario creare l'identità e quindi assegnarne l'identificatore di risorsa all'archivio.

### <a name="using-the-azure-cli"></a>Con l'interfaccia della riga di comando di Azure

Per configurare un'identità gestita tramite l'interfaccia della riga di comando di Azure, usare il [comando az appconfig identity assign] in un archivio di configurazione esistente. Sono disponibili tre opzioni per l'esecuzione degli esempi di questa sezione:

- Usare [Azure Cloud Shell](../cloud-shell/overview.md) dal portale di Azure.
- Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", situato nell'angolo in alto a destra di ogni blocco di codice indicato di seguito.
- [Installare la versione più recente dell'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) (2.0.31 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale.

La procedura seguente illustra come creare un'identità assegnata dall'utente e un archivio di Configurazione app e quindi assegnare l'identità all'archivio tramite l'interfaccia della riga di comando:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login]. Usare un account associato alla sottoscrizione di Azure:

    ```azurecli-interactive
    az login
    ```

1. Creare un archivio di Configurazione app usando l'interfaccia della riga di comando. Per altri esempi su come usare l'interfaccia della riga di comando con Configurazione app di Azure, vedere Esempi dell'interfaccia della riga di comando [di Configurazione app](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Creare un'identità assegnata dall'utente denominata `myUserAssignedIdentity` usando l'interfaccia della riga di comando.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    Nell'output di questo comando prendere nota del valore della `id` proprietà .

1. Eseguire il [comando az appconfig identity assign] per assegnare la nuova identità assegnata dall'utente a questo archivio di configurazione. Usare il valore `id` della proprietà specificata nel passaggio precedente.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Rimozione di un'identità

Un'identità assegnata dal sistema può essere rimossa disabilitando la funzionalità usando il [comando az appconfig identity remove](/cli/azure/appconfig/identity#az_appconfig_identity_remove) nell'interfaccia della riga di comando di Azure. Le identità assegnate dall'utente possono essere rimosse separatamente. La rimozione di un'identità assegnata dal sistema in questo modo ne comporterà l'eliminazione anche da AAD. Le identità assegnate dal sistema vengono rimosse automaticamente anche da AAD quando viene eliminata la risorsa app.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app ASP.NET Core con Configurazione app di Azure](quickstart-aspnet-core-app.md)

[az appconfig identity assign]: /cli/azure/appconfig/identity#az_appconfig_identity_assign
[az login]: /cli/azure/reference-index#az_login
