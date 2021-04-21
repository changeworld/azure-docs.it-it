---
title: Spostare Azure Analysis Services in un'area diversa | Microsoft Docs
description: Viene descritto come spostare una risorsa Azure Analysis Services in un'area diversa.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions , devx-track-azurepowershell
ms.openlocfilehash: 2b698ffaddb4bc818eaabda34022ab58ff05fe5f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786352"
---
# <a name="move-analysis-services-to-a-different-region"></a>Spostare Analysis Services in un'area diversa

Questo articolo descrive come spostare una risorsa Analysis Services server in un'area di Azure diversa. È possibile spostare il server in un'altra area per diversi motivi, ad esempio per sfruttare un'area di Azure più vicina agli utenti, per usare i piani di servizio supportati solo in aree specifiche o per soddisfare i requisiti interni di criteri e governance. 

In questo articolo e negli articoli collegati associati si apprenderà come:

> [!div class="checklist"]
> 
> * Eseguire il backup di un database modello del server di origine [in Archiviazione BLOB](../storage/blobs/storage-blobs-introduction.md).
> * Esportare un modello di risorsa del server [di origine](../azure-resource-manager/templates/overview.md).
> * Ottenere una firma [di accesso condiviso (SAS) di archiviazione.](../storage/common/storage-sas-overview.md)
> * Modificare il modello di risorsa.
> * Distribuire il modello per creare un nuovo server di destinazione.
> * Ripristinare un database modello nel nuovo server di destinazione.
> * Verificare il nuovo server di destinazione e il nuovo database.
> * Eliminare il server di origine.

Questo articolo descrive l'uso di un modello di risorsa  per eseguire la migrazione di un singolo server Analysis Services con una configurazione di base a un'area *e* a un gruppo di risorse diversi nella stessa sottoscrizione. L'uso di un modello mantiene le proprietà del server configurate assicurando che il server di destinazione sia configurato con le stesse proprietà, ad eccezione dell'area e del gruppo di risorse, del server di origine. Questo articolo non descrive lo spostamento delle risorse associate che possono far parte dello stesso gruppo di risorse, ad esempio le risorse di origine dati, archiviazione e gateway. 

Prima di spostare un server in un'area diversa, è consigliabile creare un piano dettagliato. Prendere in considerazione risorse aggiuntive, ad esempio gateway e archiviazione, che possono anche essere spostate. Con qualsiasi piano, è importante completare una o più operazioni di spostamento della versione di valutazione usando server di test prima di spostare un server di produzione.

> [!IMPORTANT]
> Le applicazioni client e le stringhe di connessione si connettono al Analysis Services usando il nome completo del server, ovvero un URI che include l'area in cui si trova il server. Ad esempio: `asazure://westcentralus.asazure.windows.net/advworks01`. Quando si sposta un server in un'area diversa, si crea in modo efficace una nuova risorsa server in un'area diversa, che avrà un'area diversa nell'URI del nome del server. Le applicazioni client e le stringhe di connessione usate negli script devono connettersi al nuovo server usando il nuovo URI del nome del server. L'uso [di un alias](analysis-services-server-alias.md) del nome del server può ridurre il numero di posizioni in cui l'URI del nome del server deve essere modificato, ma deve essere implementato prima dello spostamento di un'area.

> [!IMPORTANT]
> Le aree di Azure usano intervalli di indirizzi IP diversi. Se sono presenti eccezioni del firewall configurate per l'area in cui si trova il server e/o l'account di archiviazione, potrebbe essere necessario configurare un intervallo di indirizzi IP diverso. Per altre informazioni, vedere [Domande frequenti sulla connettività Analysis Services rete.](analysis-services-network-faq.md)

> [!NOTE]
> Questo articolo descrive il ripristino di un backup del database in un server di destinazione da un contenitore di archiviazione nell'area del server di origine. In alcuni casi, il ripristino dei backup da un'area diversa può avere prestazioni scarse, soprattutto per i database di grandi dimensioni. Per ottenere prestazioni ottimali durante il ripristino del database, eseguire la migrazione o creare un nuovo contenitore di archiviazione nell'area del server di destinazione. Copiare i file di backup con estensione abf dal contenitore di archiviazione dell'area di origine nel contenitore di archiviazione dell'area di destinazione prima di ripristinare il database nel server di destinazione. Anche se non sono disponibili nell'ambito di questo articolo, in alcuni casi, in particolare con database di dimensioni molto grandi, lo scripting di un database dal server di origine, la ricreazione e quindi l'elaborazione nel server di destinazione per caricare i dati del database può essere più conveniente rispetto all'uso di backup/ripristino.

> [!NOTE]
> Se si usa un gateway dati locale per connettersi alle origini dati, è necessario spostare anche la risorsa gateway nell'area del server di destinazione. Per altre informazioni, [vedere Installare e configurare un gateway dati locale.](analysis-services-gateway-install.md)

## <a name="prerequisites"></a>Prerequisiti

- **Account di archiviazione di Azure:** obbligatorio per archiviare un file di backup con estensione abf.
- **SQL Server Management Studio (SSMS):** necessario per eseguire il backup e il ripristino dei database modello.
- **Azure PowerShell**. Obbligatorio solo se si sceglie di completare questa attività tramite PowerShell.

## <a name="prepare"></a>Preparare

### <a name="backup-model-databases"></a>Eseguire il backup dei database modello

Se **le impostazioni di** archiviazione non sono già configurate per il server di origine, seguire la procedura descritta in Configurare le impostazioni di [archiviazione](analysis-services-backup.md#configure-storage-settings).

Quando sono configurate le impostazioni di archiviazione, seguire la procedura descritta in [Backup](analysis-services-backup.md#backup) per creare un backup con estensione abf del database modello nel contenitore di archiviazione. In seguito si ripristini il backup con estensione abf nel nuovo server di destinazione.


### <a name="export-template"></a>Esportare il modello

Il modello contiene le proprietà di configurazione del server di origine.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per esportare un modello con il portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **Tutte le** risorse e quindi selezionare il server Analysis Services servizio.

3. Selezionare > **Esporta**  >  **modello.**

4. Scegliere **Scarica** nel pannello **Esporta** modello.

5. Individuare il file ZIP scaricato dal portale e quindi decomprimerlo in una cartella.

   Il file ZIP contiene i file con estensione json che comprendono il modello e i parametri necessari per distribuire un nuovo server.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per esportare un modello tramite PowerShell:

1. Accedere alla propria sottoscrizione di Azure con il comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e seguire le istruzioni visualizzate:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Se l'identità è associata a più sottoscrizioni, impostare la sottoscrizione attiva sulla sottoscrizione della risorsa server che si vuole spostare.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Esportare il modello del server di origine. Questi comandi salvano un modello JSON con ResourceGroupName come nome file nella directory corrente.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <server-account-name> `
     -ResourceType Microsoft.AnalysisServices/servers
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```
---

### <a name="get-storage-shared-access-signature-sas"></a>Ottenere la firma di accesso condiviso di archiviazione

Quando si distribuisce un server di destinazione da un modello, è necessario un token di firma di accesso condiviso di delega utente (come URI) per specificare il contenitore di archiviazione contenente il backup del database.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per ottenere una firma di accesso condiviso tramite il portale:

1. Nel portale selezionare l'account di archiviazione usato per eseguire il backup del database del server.

2. Selezionare **Storage Explorer** e quindi espandere **CONTENITORI BLOB**. 

3. Fare clic con il pulsante destro del mouse sul contenitore di archiviazione e **quindi scegliere Ottieni firma di accesso condiviso.**

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="Ottenere una firma di accesso condiviso":::

4. In **Firma di accesso condiviso** selezionare **Crea.** Per impostazione predefinita, la firma di accesso condiviso scadrà tra 24 ore.

5. Copiare e salvare **l'URI**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per ottenere una firma di accesso condiviso tramite PowerShell, seguire la procedura descritta in Creare una firma di accesso condiviso di delega utente per un contenitore o un [BLOB con PowerShell.](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob)

---

### <a name="modify-the-template"></a>Modificare il modello

Usare un editor di testo per modificare il template.jsnel file esportato, modificando le proprietà dell'area e del contenitore BLOB. 

Per modificare il modello:

1. In un editor di testo, nella **proprietà location,** specificare la nuova area di destinazione. Nella proprietà **backupBlobContainerUri incollare** l'URI del contenitore di archiviazione con la chiave di firma di accesso condiviso. 

    L'esempio seguente imposta l'area di destinazione per il server advworks1 su e specifica l'URI del contenitore di archiviazione `South Central US` con firma di accesso condiviso: 

    ```json
    "resources": [
        {
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "name": "[parameters('servers_advworks1_name')]",
            "location": "South Central US",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "asadmins@adventure-works.com"
                    ]
                },
                "backupBlobContainerUri": "https://storagenorthcentralus.blob.core.windows.net/backup?sp=rl&st=2020-06-01T19:30:42Z&se=2020-06-02T19:30:42Z&sv=2019-10-10&sr=c&sig=PCQ4s9RujJkxu89gO4tiDTbE3%2BFECx6zAdcv8x0cVUQ%3D",
                "querypoolConnectionMode": "All"
            }
        }
    ]         
    ```
2. Salvare il modello.

#### <a name="regions"></a>Regioni

Per ottenere le aree di Azure, vedere Località [di Azure.](https://azure.microsoft.com/global-infrastructure/locations/) Per ottenere le aree usando PowerShell, eseguire il [comando Get-AzLocation.](/powershell/module/az.resources/get-azlocation)

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>Sposta

Per distribuire una nuova risorsa server in un'area diversa, si userà iltemplate.jsnel **file** esportato e modificato nelle sezioni precedenti.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel portale selezionare **Crea una risorsa.**

2. In **Cerca nel Marketplace** digitare distribuzione del **modello** e quindi premere **INVIO.**

3. Selezionare **Distribuzione modelli**.

4. Selezionare **Crea**.

5. Selezionare **Creare un modello personalizzato nell'editor**.

6. Selezionare **Carica file** e quindi seguire le istruzioni per caricare iltemplate.js **file** esportato e modificato.

7. Verificare che l'editor dei modelli mostra le proprietà corrette per il nuovo server di destinazione.

8. Selezionare **Salva**.

9. Immettere o selezionare i valori delle proprietà:

    - **Sottoscrizione**: selezionare la sottoscrizione di Azure.
    
    - **Gruppo di risorse:** selezionare **Crea nuovo** e quindi immettere il nome di un gruppo di risorse. È possibile selezionare un gruppo di risorse esistente purché non contenga già un server Analysis Services con lo stesso nome.
    
    - **Località:** selezionare la stessa area specificata nel modello.

10. Selezionare **Rivedi e crea**.

11. Esaminare i termini e le nozioni di base e quindi selezionare **Crea**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Usare questi comandi per distribuire il modello:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>Ottenere l'URI del server di destinazione

Per connettersi al nuovo server di destinazione da SSMS per ripristinare il database modello, è necessario ottenere il nuovo URI del server di destinazione.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per ottenere l'URI del server nel portale:

1. Nel portale passare alla nuova risorsa del server di destinazione.

2. Nella pagina **Panoramica** copiare l'URI **del nome del** server.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per ottenere l'URI del server tramite PowerShell, usare i comandi seguenti:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
Copiare **ServerFullName** dall'output.

---

### <a name="restore-model-database"></a>Ripristinare il database modello

Seguire i passaggi descritti in [Ripristinare](analysis-services-backup.md#restore) per ripristinare il backup con estensione abf del database modello nel nuovo server di destinazione.

Facoltativo: dopo il ripristino del database modello, elaborare il modello e le tabelle per aggiornare i dati dalle origini dati. Per elaborare il modello e la tabella tramite SSMS:

1. In SSMS fare clic con il pulsante destro del mouse sul database modello > **Elabora database**.

2. Espandere **Tabelle**, fare clic con il pulsante destro del mouse su una tabella. In **Elabora tabelle selezionare** tutte le tabelle e quindi fare clic su **OK.**

## <a name="verify"></a>Verifica

1. Nel portale passare al nuovo server di destinazione.

2. Nella pagina Panoramica, in **Modelli nel server Analysis Services** verificare che i modelli ripristinati siano visualizzati.

3. Usare un'applicazione client come Power BI o Excel per connettersi al modello nel nuovo server. Verificare che vengano visualizzati oggetti del modello, ad esempio tabelle, misure e gerarchie. 

4. Eseguire gli script di automazione. Verificare che siano stati eseguiti correttamente.

Facoltativo: [ALM Toolkit è](http://alm-toolkit.com/) uno *strumento open source* per confrontare e  gestire set di Power BI e Analysis Services di modelli tabulari. Usare il toolkit per connettersi ai database del server di origine e di destinazione e confrontarli. Se la migrazione del database ha esito positivo, gli oggetti modello avranno la stessa definizione. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM Toolkit":::

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver verificato che le applicazioni client possano connettersi al nuovo server e che gli script di automazione vengano eseguiti correttamente, eliminare il server di origine. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per eliminare il server di origine dal portale:

Nella pagina Panoramica del server **di** origine selezionare **Elimina.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eliminare il server di origine usando PowerShell, usare il Remove-AzAnalysisServicesServer comando .

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> Dopo aver completato lo spostamento di un'area, è consigliabile che il nuovo server di destinazione usi un contenitore di archiviazione nella stessa area per i backup, anziché il contenitore di archiviazione nell'area del server di origine.
