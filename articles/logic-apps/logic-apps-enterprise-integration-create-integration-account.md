---
title: Creare o gestire account di integrazione B2B
description: Creare, collegare e gestire account di integrazione per l'integrazione aziendale con App per la logica di Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: 9a7a0795a15de52c34b8591c4224c3ca5883445c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772074"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Creare e gestire account di integrazione per integrazioni aziendali B2B in App per la logica di Azure

Prima di poter creare [soluzioni di integrazione aziendale e B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) tramite [App per la logica di Azure](../logic-apps/logic-apps-overview.md) è necessario creare un account di integrazione, ovvero una risorsa di Azure separata che fornisca un contenitore sicuro, scalabile e gestibile per gli artefatti di integrazione definiti e usati con i flussi di lavoro di app per la logica.

Ad esempio, è possibile creare, archiviare e gestire elementi B2B, ad esempio partner commerciali, contratti, mappe, schemi, certificati e configurazioni batch. Inoltre, prima che l'app per la logica possa usare questi elementi e usare i connettori App per la logica B2B, è necessario collegare [l'account](#link-account) di integrazione all'app per la logica. Sia l'account di integrazione che l'app per la logica devono esistere nella *stessa* posizione o area.

> [!IMPORTANT]
> In base al tipo di account di integrazione selezionato, la creazione di un account di integrazione comporta costi. Per altre informazioni, vedere Prezzi e modelli [di fatturazione](logic-apps-pricing.md#integration-accounts) di App per la logica e Prezzi di App per la [logica.](https://azure.microsoft.com/pricing/details/logic-apps/)

Questo argomento illustra come eseguire queste attività:

* Creare l'account di integrazione.

  > [!TIP]
  > Per creare un account di integrazione all'interno di un [ambiente del servizio di integrazione,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)vedere Creare account di integrazione in un ambiente [ISE.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)

* Collegare l'account di integrazione a un'app per la logica.

* Modificare il piano tariffario per l'account di integrazione.

* Scollegare l'account di integrazione da un'app per la logica.

* Spostare l'account di integrazione in un gruppo di risorse o in una sottoscrizione di Azure differente.

* Eliminare l'account di integrazione.

## <a name="prerequisites"></a>Prerequisiti

* Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Creare un account di integrazione

### <a name="portal"></a>[Portale](#tab/azure-portal)

Per questa attività, è possibile usare il portale di Azure seguendo la procedura descritta in questa sezione, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)o l'interfaccia della riga di comando [di Azure.](/cli/azure/resource#az_resource_create)

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nel menu principale di Azure scegliere **Crea una risorsa**. Nella casella di ricerca immettere "account di integrazione" come filtro e selezionare **Account di integrazione**.

   ![Creare un nuovo account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. In **Account di integrazione** selezionare **Crea**.

   ![Scegliere "Aggiungi" per creare un account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Specificare queste informazioni sull'account di integrazione:

   ![Specificare i dettagli dell'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Nome** | Sì | <*integration-account-name*> | Nome dell'account di integrazione, che può contenere solo lettere, numeri, trattini ( ), caratteri di sottolineatura `-` ( ), parentesi ( , ) e punti ( `_` `(` `)` `.` ). Questo esempio usa "Fabrikam-Integration". |
   | **Sottoscrizione** | Sì | <*nome sottoscrizione di Azure*> | Nome della sottoscrizione di Azure |
   | **Gruppo di risorse** | Sì | <*Azure-resource-group-name*> | Nome del gruppo [di risorse di Azure da](../azure-resource-manager/management/overview.md) usare per organizzare le risorse correlate. Per questo esempio, creare un nuovo gruppo di risorse denominato "FabrikamIntegration-RG". |
   | **Piano tariffario** | Sì | <*piano tariffario*> | Piano tariffario per l'account di integrazione, che è possibile modificare in un secondo momento. Per questo esempio, selezionare **Gratuito.** Per altre informazioni, vedere gli argomenti seguenti: <p>- [Modello di determinazione prezzi di App per la logica](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Limiti e configurazione di App per la logica](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Posizione** | Sì | <*Area di Azure*> | Area in cui archiviare i metadati dell'account di integrazione. Selezionare la stessa posizione dell'app per la logica o creare le app per la logica nella stessa posizione dell'account di integrazione. Per questo esempio, usare "Stati Uniti occidentali". <p>**Nota:** per creare un account di integrazione all'interno di un ambiente del servizio di integrazione [(ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)selezionare tale ISE come percorso. Per altre informazioni, vedere [Creare account di integrazione in un ISE.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment) |
   | **Log Analytics** | No | Off, On | Mantenere **l'impostazione Disattivato** per questo esempio. |
   |||||

1. Al termine, selezionare **Crea**.

   Al termine della distribuzione, Azure apre l'account di integrazione.

   ![Azure apre l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Prima che l'app per la logica possa usare l'account di integrazione, seguire i passaggi successivi per collegare l'account di integrazione e l'app per la logica.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile creare un account di integrazione usando i comandi dell'interfaccia della riga di comando di Azure in questa sezione.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-an-integration-account"></a>Creare un account di integrazione

Usare questi comandi per creare un account di integrazione.

1. Per aggiungere [l'estensione az logic integration-account,](/cli/azure/ext/logic/logic/integration-account) usare il [comando az extension add:](/cli/azure/extension#az_extension_add)

   ```azurecli
   az extension add –-name logic
   ```

1. Per creare un gruppo di risorse o usare un gruppo di risorse esistente, eseguire il [comando az group create:](/cli/azure/group#az_group_create)

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   Per elencare gli account di integrazione per un gruppo di risorse, usare il [comando az logic integration-account list:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_list)

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. Per creare un account di integrazione, eseguire [il comando az logic integration-account create:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_create)

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   Il nome dell'account di integrazione può contenere solo lettere, numeri, trattini (-), caratteri di sottolineatura (_), parentesi ((, )) e punti (.).

   > [!TIP]
   > Per creare un account di integrazione all'interno di un ambiente del servizio di integrazione [(ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)selezionare tale ise come posizione. Per altre informazioni, vedere [Creare account di integrazione in un ise](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

   Per visualizzare un account di integrazione specifico, usare [il comando az logic integration-account show:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_show)

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   È possibile modificare lo SKU o il piano tariffario usando il [comando az logic integration-account update:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_update)

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   Per altre informazioni sui prezzi, vedere queste risorse:

   * [Modello di determinazione prezzi delle app per la logica](../logic-apps/logic-apps-pricing.md#integration-accounts)
   * [Limiti e configurazione per App per la logica](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)
   * [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/)

Per importare un account di integrazione usando un file JSON, usare il [comando az logic integration-account import:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_import)

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

È possibile eliminare un account di integrazione usando il [comando az logic integration-account delete:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_delete)

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

Prima che l'app per la logica possa usare l'account di integrazione, collegare l'account di integrazione e l'app per la logica. La sezione successiva descrive il collegamento.

---
<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Collegare all'app per la logica

Per concedere alle app per la logica l'accesso a un account di integrazione che contiene gli artefatti B2B, è prima necessario collegare l'account di integrazione all'app per la logica. Sia l'app per la logica che l'account di integrazione devono esistere nella stessa area. Per completare questa attività, è possibile usare il portale di Azure. Se si usa Visual Studio'app per la logica si trova in un progetto gruppo di risorse di [Azure,](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)è possibile collegare [l'app](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account)per la logica a un account di integrazione usando Visual Studio .

1. Nel portale di Azure trovare e aprire l'app per la logica.

1. Nel [portale di Azure](https://portal.azure.com)aprire un'app per la logica esistente o creare una nuova app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**. In **Account di integrazione** aprire l'elenco Selezionare un account **di** integrazione. Selezionare l'account di integrazione da collegare all'app per la logica.

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Per completare il collegamento, selezionare **Salva.**

   ![Screenshot che mostra dove selezionare Salva per scegliere l'account di integrazione.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Al termine del collegamento dell'account di integrazione, Azure visualizza un messaggio di conferma.

   ![Azure conferma l'esito positivo del collegamento](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Ora l'app per la logica può usare gli artefatti nell'account di integrazione oltre ai connettori B2B, ad esempio la convalida XML e la codifica o decodifica di file flat.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Modificare il piano tariffario

Per aumentare i [limiti per](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) un account di integrazione, è possibile eseguire l'aggiornamento a un [piano tariffario superiore,](#upgrade-pricing-tier)se disponibile. Ad esempio, è possibile eseguire l'aggiornamento dal livello Gratuito al livello Basic o Standard. È anche possibile [eseguire il downgrade a un livello inferiore,](#downgrade-pricing-tier)se disponibile. Per altre informazioni sui prezzi, vedere gli argomenti seguenti:

* [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Modello di determinazione prezzi delle app per la logica](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Aggiornare il piano tariffario

Per apportare questa modifica, è possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure.

#### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nella casella di ricerca principale di Azure immettere "account di integrazione" come filtro e selezionare **Account di integrazione.**

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure mostra tutti gli account di integrazione nelle sottoscrizioni di Azure.

1. In **Account di integrazione** selezionare l'account di integrazione che si vuole spostare. Nel menu dell'account di integrazione selezionare **Panoramica.**

   ![Nel menu dell'account di integrazione selezionare "Panoramica"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Nel riquadro Panoramica selezionare Aggiorna piano **tariffario**, che elenca tutti i livelli superiori disponibili. Quando si seleziona un livello, la modifica viene immediatamente apportata.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Se non è già stato fatto, installare i prerequisiti [dell'interfaccia della riga di comando di Azure.](/cli/azure/get-started-with-azure-cli)

1. Nel portale di Azure aprire [l'Azure Cloud Shell](../cloud-shell/overview.md) locale.

   ![Aprire Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Al prompt dei comandi immettere il [ **comando az resource**](/cli/azure/resource#az_resource_update)e impostare sul livello superiore `skuName` desiderato.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Ad esempio, se si dispone del livello Basic, è possibile impostare `skuName` su `Standard` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Piano tariffario di downgrade

Per apportare questa modifica, usare l'interfaccia della riga [di comando di Azure.](/cli/azure/get-started-with-azure-cli)

1. Se non è già stato fatto, installare i prerequisiti [dell'interfaccia](/cli/azure/get-started-with-azure-cli)della riga di comando di Azure.

1. Nel portale di Azure aprire [l'Azure Cloud Shell](../cloud-shell/overview.md) locale.

   ![Aprire Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Al prompt dei comandi immettere il [ **comando az resource** e](/cli/azure/resource#az_resource_update) impostare sul livello inferiore `skuName` desiderato.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Ad esempio, se si dispone del livello Standard, è possibile impostare `skuName` su `Basic` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Scollegare da un'app per la logica

Se si vuole collegare l'app per la logica a un altro account di integrazione o non usare più un account di integrazione con l'app per la logica, eliminare il collegamento usando Azure Resource Explorer.

1. Aprire la finestra del browser e passare a [Azure Resource Explorer ( https://resources.azure.com) ](https://resources.azure.com). Accedere con le stesse credenziali dell'account Azure.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Nella casella di ricerca immettere il nome dell'app per la logica in modo da poter trovare e selezionare l'app per la logica.

   ![Trovare e selezionare l'app per la logica](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Sulla barra del titolo di Explorer selezionare **Lettura/Scrittura.**

   ![Attivare la modalità "Lettura/Scrittura"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Nella scheda **Dati** selezionare **Modifica**.

   ![Nella scheda "Dati" selezionare "Modifica"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. Nell'editor trovare `integrationAccount` l'oggetto ed eliminare la proprietà, che ha il formato seguente:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Ad esempio:

   ![Trovare l'oggetto "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Nella scheda **Dati** selezionare **Inserisci per** salvare le modifiche.

   ![Per salvare le modifiche, selezionare "Put"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Nel portale di Azure trovare e selezionare l'app per la logica. Nelle impostazioni del flusso di lavoro **dell'app** verificare che la proprietà **Account di integrazione** sia ora vuota.

   ![Verificare che l'account di integrazione non sia collegato](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Spostare l'account di integrazione

È possibile spostare l'account di integrazione in un altro gruppo di risorse di Azure o in un'altra sottoscrizione di Azure. Quando si spostano le risorse, Azure crea nuovi ID risorsa, quindi assicurarsi di usare i nuovi ID e aggiornare gli script o gli strumenti associati alle risorse spostate. Se si vuole modificare la sottoscrizione, è necessario specificare anche un gruppo di risorse nuovo o esistente.

Per questa attività, è possibile usare il portale di Azure seguendo i passaggi descritti in questa sezione o l'interfaccia della riga di [comando di Azure.](/cli/azure/resource#az_resource_move)

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nella casella di ricerca principale di Azure immettere "account di integrazione" come filtro e selezionare **Account di integrazione.**

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure mostra tutti gli account di integrazione nelle sottoscrizioni di Azure.

1. In **Account di integrazione** selezionare l'account di integrazione che si vuole spostare. Nel menu dell'account di integrazione selezionare **Panoramica.**

   ![Nel menu dell'account di integrazione selezionare "Panoramica"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Accanto a Gruppo **di risorse o** Nome **sottoscrizione** selezionare **Modifica.**

   ![Modificare il gruppo di risorse o la sottoscrizione](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Selezionare le risorse correlate che si vuole spostare.

1. In base alla selezione, seguire questa procedura per modificare il gruppo di risorse o la sottoscrizione:

   * Gruppo di risorse: **nell'elenco Gruppo di** risorse selezionare il gruppo di risorse di destinazione. In caso contrario, per creare un gruppo di risorse diverso, **selezionare Crea un nuovo gruppo di risorse.**

   * Sottoscrizione: **nell'elenco Sottoscrizione** selezionare la sottoscrizione di destinazione. **Nell'elenco Gruppo di** risorse selezionare il gruppo di risorse di destinazione. In caso contrario, per creare un gruppo di risorse diverso, **selezionare Crea un nuovo gruppo di risorse.**

1. Per confermare che tutti gli script o gli strumenti associati alle risorse spostate non funzionano fino a quando non vengono aggiornati con i nuovi ID risorsa, selezionare la casella di conferma e quindi **selezionare OK.**

1. Al termine, assicurarsi di aggiornare tutti gli script con i nuovi ID risorsa per le risorse spostate.  

## <a name="delete-integration-account"></a>Eliminare l'account di integrazione

Per questa attività, è possibile usare il portale di Azure seguendo la procedura descritta in questa sezione, l'interfaccia della riga di comando di [Azure](/cli/azure/resource#az_resource_delete) [o Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nella casella di ricerca principale di Azure immettere "account di integrazione" come filtro e selezionare **Account di integrazione**.

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure mostra tutti gli account di integrazione nelle sottoscrizioni di Azure.

1. In **Account di integrazione** selezionare l'account di integrazione che si vuole eliminare. Scegliere Panoramica dal menu dell'account **di integrazione.**

   ![Nel menu dell'account di integrazione selezionare "Panoramica"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Nel riquadro Panoramica selezionare **Elimina**.

   ![Nel riquadro "Panoramica" selezionare "Elimina"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Per confermare che si vuole eliminare l'account di integrazione, selezionare **Sì.**

   ![Per confermare l'eliminazione, selezionare "Sì"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare partner commerciali nell'account di integrazione](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Creare contratti tra partner nell'account di integrazione](../logic-apps/logic-apps-enterprise-integration-agreements.md)
