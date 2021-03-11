---
title: Spostare le risorse tra aree usando PowerShell in Azure Resource Mover
description: Informazioni su come spostare le risorse tra aree usando PowerShell in Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/21/2021
ms.author: raynew
ms.openlocfilehash: b728170521570ff0d83b67671109e82adb7fa7b0
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584344"
---
# <a name="move-resources-across-regions-in-powershell"></a>Spostare le risorse tra le aree in PowerShell

Questo articolo illustra come spostare le risorse di Azure in un'area di Azure diversa usando PowerShell in [Azure Resource Mover](overview.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Verificare prerequisiti e requisiti.
> * Configurare la raccolta di spostamento.
> * Aggiungere risorse alla raccolta di spostamento e risolvere le dipendenze.
> * Preparare e spostare il gruppo di risorse di origine. 
> * Preparare e spostare le altre risorse.
> * Decidere se si vuole rimuovere lo spostamento o eseguirne il commit. 
> * Facoltativamente, rimuovere le risorse nell'area di origine dopo lo spostamento.

> [!NOTE]
> Le esercitazioni illustrano il percorso più rapido per provare uno scenario e prevedono l'uso delle opzioni predefinite. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare. Accedere quindi al [portale di Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Prerequisiti
**Requisito** | **Descrizione**
--- | ---
**Autorizzazioni di sottoscrizione** | Verificare di disporre dell'accesso *proprietario* per la sottoscrizione contenente le risorse che si desidera spostare<br/><br/> **Perché è necessario l'accesso del proprietario?** La prima volta che si aggiunge una risorsa per una coppia di origine e destinazione specifica in una sottoscrizione di Azure, Spostamento risorse crea un'[identità gestita assegnata dal sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (nota in precedenza come identità del servizio gestita) che viene considerata attendibile dalla sottoscrizione. Per creare l'identità e assegnarle il ruolo richiesto (Collaboratore o Amministratore Accesso utenti nella sottoscrizione di origine), l'account usato per aggiungere le risorse deve avere le autorizzazioni di *Proprietario* nella sottoscrizione. [Altre informazioni](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sui ruoli di Azure.
**Supporto di Resource Mover** | [Esaminare](common-questions.md) le aree supportate e altre domande comuni.
**Supporto per macchine virtuali** |  Verificare che le macchine virtuali che si desidera spostare siano supportate.<br/><br/> - [Verificare](support-matrix-move-region-azure-vm.md#windows-vm-support) le VM Windows supportate.<br/><br/> - [Verificare](support-matrix-move-region-azure-vm.md#linux-vm-support) le VM Linux e le versioni del kernel supportate.<br/><br/> -Controllare le impostazioni di [calcolo](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [archiviazione](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)e [rete](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) supportate.
**Supporto di SQL** | Se si desidera spostare le risorse SQL, esaminare l' [elenco dei requisiti di SQL](tutorial-move-region-sql.md#check-sql-requirements).
**Sottoscrizione di destinazione** | La sottoscrizione nell'area di destinazione richiede una quota sufficiente per creare le risorse che si stanno muovendo nell'area di destinazione. Se non è disponibile alcuna quota, [richiedere limiti aggiuntivi](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Addebiti per l'area di destinazione** | Verificare i prezzi e gli addebiti associati all'area di destinazione in cui si intende spostare le macchine virtuali. Per facilitare l'operazione, usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/).

### <a name="review-powershell-requirements"></a>Esaminare i requisiti di PowerShell

La maggior parte delle operazioni di spostamento delle risorse è la stessa se si usa il portale di Azure o PowerShell, con due eccezioni.

**Operazione** | **PowerShell** | **Portale**
--- | --- | ---
**Creare una raccolta di spostamento** | Una raccolta di spostamento (un elenco di tutte le risorse che si sta spostando) viene creata automaticamente. Le autorizzazioni di identità richieste vengono assegnate nel back-end dal portale. | Usare i cmdlet di PowerShell per:<br/><br/> -Creare un gruppo di risorse per la raccolta di spostamento e specificarne il percorso.<br/><br/> -Assegnare un'identità gestita alla raccolta.<br/><br/> -Aggiungere risorse alla raccolta.
**Rimuovere una raccolta di spostamento** | Non è possibile rimuovere direttamente una raccolta di spostamento nel portale. | Per rimuovere una raccolta di spostamento, usare un cmdlet di PowerShell.
**Operazioni di spostamento delle risorse**<br/><br/> (Preparare, avviare lo spostamento, eseguire il commit e così via).| Singoli passaggi con la convalida automatica da Resource Mover. | Cmdlet di PowerShell per:<br/><br/> 1) convalidare le dipendenze.<br/><br/> 2) eseguire lo spostamento.
**Elimina risorse di origine** | Direttamente nel portale di Resource Mover. | Cmdlet di PowerShell a livello di tipo di risorsa.


### <a name="sample-values"></a>Valori di esempio

Questi valori vengono utilizzati negli esempi di script:

**Impostazione** | **Valore** 
--- | ---
ID sottoscrizione | subscription-id
Area di origine |  Stati Uniti centrali
Area di destinazione | Stati Uniti centro-occidentali
Gruppo di risorse (contenente i metadati per la raccolta di spostamento) | RG-MoveCollection-demoRMS
Sposta nome raccolta | PS-centralus-westcentralus-demoRMS
Gruppo di risorse (area di origine) | PSDemoRM 
Gruppo di risorse (area di destinazione) | PSDemoRM-destinazione
Percorso del servizio di spostamento delle risorse | Stati Uniti orientali 2
IdentityType | SystemAssigned
VM da spostare | PSDemoVM


## <a name="sign-into-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il cmdlet Connect-AzAccount:

```azurepowershell-interactive
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="set-up-the-move-collection"></a>Configurare la raccolta di spostamento

L'oggetto MoveCollection archivia i metadati e le informazioni di configurazione sulle risorse che si desidera spostare. Per configurare una raccolta di spostamento, eseguire le operazioni seguenti:

- Creare un gruppo di risorse per la raccolta di spostamento.
- Registrare il provider di servizi nella sottoscrizione, in modo che sia possibile creare la risorsa MoveCollection.
- Creare l'oggetto MoveCollection con identità gestita. Per l'accesso dell'oggetto MoveCollection alla sottoscrizione in cui si trova il servizio Resource Mover, è necessaria un' [identità gestita assegnata dal sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (precedentemente nota come identità del servizio gestita (MSI)) considerata attendibile dalla sottoscrizione.
- Concedere l'accesso alla sottoscrizione di Resource Mover per l'identità gestita.

### <a name="create-the-resource-group"></a>Creare il gruppo di risorse

Creare un gruppo di risorse per i metadati della raccolta di spostamento e le informazioni di configurazione, come indicato di seguito:

```azurepowershell-interactive
New-AzResourceGroup -Name "RG-MoveCollection-demoRMS" -Location "East US 2"
```
**Output**:

![Testo di output dopo la creazione del gruppo di risorse](./media/tutorial-move-region-powershell/create-metadata-resource-group.png)

### <a name="register-the-resource-provider"></a>Registrare il provider di risorse

1. Registrare il provider di risorse Microsoft. migrate, in modo che sia possibile creare la risorsa MoveCollection, come indicato di seguito:

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate
    
2. Wait for registration:

    ```azurepowershell-interactive 
    While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
    {
        Start-Sleep -Seconds 5
        Write-Output "Waiting for registration to complete."
    }
    ```
### <a name="create-a-movecollection-object"></a>Creare un oggetto MoveCollection

Creare un oggetto MoveCollection e assegnarvi un'identità gestita, come indicato di seguito: 

```azurepowershell-interactive
New-AzResourceMoverMoveCollection -Name "PS-centralus-westcentralus-demoRMS"  -ResourceGroupName "RG-MoveCollection-demoRMS" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location "centraluseuap" -IdentityType "SystemAssigned"
```

**Output**:

![Testo di output dopo la creazione della raccolta di spostamento](./media/tutorial-move-region-powershell/output-move-collection.png)


### <a name="grant-access-to-the-managed-identity"></a>Concedere l'accesso all'identità gestita

Concedere l'accesso all'identità gestita alla sottoscrizione di Resource Mover come indicato di seguito. È necessario essere il proprietario della sottoscrizione.

1. Recuperare i dettagli dell'identità dall'oggetto MoveCollection.

    ```azurepowershell-interactive
    $moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PS-centralus-westcentralus-demoRMS"

    $identityPrincipalId = $moveCollection.IdentityPrincipalId   
    ``` 

2. Assegnare i ruoli necessari all'identità in modo che Azure Resource Mover possa accedere alla sottoscrizione per facilitare lo spostamento delle risorse.

    ```azurepowershell-interactive
    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"
    ``` 

## <a name="add-resources-to-the-move-collection"></a>Aggiungere risorse alla raccolta di spostamento

Recuperare gli ID per le risorse di origine esistenti che si desidera spostare. Creare l'oggetto impostazioni risorsa di destinazione, quindi aggiungere le risorse alla raccolta di spostamento.

> [!NOTE]
> Le risorse aggiunte a una raccolta di spostamento devono trovarsi nella stessa sottoscrizione, ma possono essere incluse in gruppi di risorse diversi.

Aggiungere le risorse come segue:

1. Ottenere l'ID risorsa di origine:

    ```azurepowershell-interactive
    Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
    ```

    **Output**

    ![Testo di output dopo il recupero dell'ID risorsa](./media/tutorial-move-region-powershell/output-retrieve-resource.png)

2. Creare l'oggetto impostazioni risorsa di destinazione in base alla risorsa che si sta muovendo. In questo caso si tratta di una macchina virtuale.

    ```azurepowershell-interactive
    $targetResourceSettingsObj = New-Object Microsoft.Azure.PowerShell.Cmdlets.ResourceMover.Models.Api202101.VirtualMachineResourceSettings
    ```

3. Impostare il tipo di risorsa e il nome della risorsa di destinazione per l'oggetto.

    ```azurepowershell-interactive
    $targetResourceSettingsObj.ResourceType = "Microsoft.Compute/virtualMachines"
    $targetResourceSettingsObj.TargetResourceName = "PSDemoVM"
    ```
    > [!NOTE]
    > La macchina virtuale di destinazione ha lo stesso nome della macchina virtuale nell'area di origine. È possibile scegliere un nome diverso.

4. Aggiungere le risorse di origine alla raccolta di spostamento usando l'ID risorsa e l'oggetto impostazioni di destinazione recuperato/creato.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx xxxxxxxxxxxx/resourceGroups/
    PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $targetResourceSettingsObj
    ```

    **Output** ![ di Testo di output dopo l'aggiunta della risorsa](./media/tutorial-move-region-powershell/output-add-resource.png)

## <a name="validate-and-add-dependencies"></a>Convalidare e aggiungere dipendenze

Verificare se le risorse aggiunte hanno dipendenze da altre risorse e aggiungerle in base alle esigenze. 

1. Convalidare le dipendenze come segue:

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
    **Output (quando esistono dipendenze)**

    ![Testo di output dopo la convalida delle dipendenze](./media/tutorial-move-region-powershell/dependency-output.png)

2. Dipendenze mancanti identità:

    - Per recuperare un elenco di tutte le dipendenze mancanti:

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Descendant"
        ```
        **Output** ![ di Testo di output dopo il recupero di un elenco di tutte le dipendenze](./media/tutorial-move-region-powershell/dependencies-list.png)  

    - Per recuperare solo le dipendenze di primo livello (dipendenze dirette per la risorsa):

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
        ```
        **Output** ![ di Testo di output dopo il recupero di un elenco di dipendenze di primo livello](./media/tutorial-move-region-powershell/dependencies-list-direct.png)  

3. Per aggiungere eventuali dipendenze mancanti in attesa, ripetere le istruzioni riportate sopra per [aggiungere risorse alla raccolta di spostamento](#add-resources-to-the-move-collection)e riconvalidare fino a quando non sono presenti risorse in attesa.

> [!NOTE]
> Se per qualsiasi motivo si vuole rimuovere risorse dalla raccolta di risorse, seguire le istruzioni riportate in [questo articolo](remove-move-resources.md).

## <a name="add-the-source-resource-group"></a>Aggiungere il gruppo di risorse di origine

Aggiungere il gruppo di risorse di origine che contiene le risorse che si desidera spostare nella raccolta di spostamento.

1. Recuperare l'ID del gruppo di risorse.

    ```azurepowershell-interactive
    Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
    ```
    **Output** ![ di Testo di output dopo il recupero dell'ID del gruppo di risorse di origine](./media/tutorial-move-region-powershell/source-resource-group-id.png)  

    > [!NOTE]
    > Si sta usando un gruppo di risorse già presente nell'area di destinazione.

 
2. Usare l'ID recuperato per aggiungere il gruppo di risorse alla raccolta.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS"  -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/psdemorm"  -Name "psdemorm"  -ExistingTargetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/PSDemoRM-target"
    ```
    **Output** ![ di Testo di output dopo l'aggiunta del gruppo di risorse di origine alla raccolta di spostamento](./media/tutorial-move-region-powershell/add-source-resource-group.png)

3. Verificare le dipendenze per assicurarsi di non aver perso nulla dopo l'aggiunta del gruppo di risorse.

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
4. Si noterà che non sono presenti dipendenze in attesa.

    **Output** ![ di Testo di output dopo il controllo delle dipendenze](./media/tutorial-move-region-powershell/all-dependencies-added.png)


## <a name="prepare-resources"></a>Preparare le risorse

È in genere necessario preparare le risorse nell'area di origine prima dello spostamento. Ad esempio:

- Per spostare risorse senza stato, ad esempio reti virtuali di Azure, schede di rete, bilanciamenti del carico e gruppi di sicurezza di rete, potrebbe essere necessario esportare un modello di Azure Resource Manager.
- Per spostare risorse con stato, ad esempio macchine virtuali di Azure e database SQL, potrebbe essere necessario avviare la replica delle risorse dall'area di origine all'area di destinazione.

In questa esercitazione, dal momento che si spostano le macchine virtuali, è necessario preparare il gruppo di risorse di origine e quindi avviare e confermare lo spostamento, prima di iniziare la preparazione delle macchine virtuali.

> [!NOTE]
> Se si dispone di un gruppo di risorse di destinazione esistente, è possibile eseguire direttamente il commit dello spostamento per il gruppo di risorse di origine e ignorare le fasi di spostamento per preparare e avviare.

  
### <a name="prepare-the-source-resource-group"></a>Preparare il gruppo di risorse di origine:

1. Preparare il gruppo di risorse:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Output**

    ![Testo di output dopo la preparazione del gruppo di risorse di origine](./media/tutorial-move-region-powershell/prepare-source-resource-group.png)

2. Avviare lo spostamento del gruppo di risorse di origine.

    ```azurepowershell-interactive
    "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    ![Testo di output dopo l'avvio dello spostamento del gruppo di risorse di origine](./media/tutorial-move-region-powershell/initiate-move-source-resource-group.png)

3. Eseguire il commit dello spostamento per il gruppo di risorse di origine.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Output**

    ![Testo di output dopo il commit del gruppo di risorse di origine](./media/tutorial-move-region-powershell/commit-move-source-resource-group.png)


### <a name="prepare-vm-resources"></a>Preparare le risorse VM

Dopo aver preparato e spostato il gruppo di risorse di origine, è possibile preparare le risorse della macchina virtuale per lo spostamento.

1. Convalidare le dipendenze prima di preparare le risorse della macchina virtuale.

    ```azurepowershell-interactive
    $resp = Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm') -ValidateOnly
    ```
    **Output**

    ![Testo di output dopo la convalida della macchina virtuale prima della preparazione](./media/tutorial-move-region-powershell/validate-vm-before-move.png)

2. Ottenere le risorse dipendenti che devono essere preparate insieme alla VM.

    ```azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```
    **Output**

    ![Testo di output dopo il recupero delle risorse della macchina virtuale dipendente](./media/tutorial-move-region-powershell/get-resources-before-prepare.png)

3. Avviare il processo di preparazione per tutte le risorse dipendenti.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```
    **Output**

    ![Testo di output dopo initating preparato per tutte le risorse](./media/tutorial-move-region-powershell/initiate-prepare-all.png)


    > [!NOTE]
    > È possibile specificare l'ID della risorsa di origine anziché il nome della risorsa come parametri di input per il cmdlet Prepare, oltre che nei cmdlet di avvio dello spostamento e del commit. A tale scopo, eseguire:


    ```azurepowershell-interactive
        Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRMS/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
    ```

## <a name="initiate-move-of-vm-resources"></a>Avviare lo spostamento delle risorse della macchina virtuale

1. Verificare che le risorse VM siano in uno stato di *spostamento iniziale in sospeso* :

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -SubscriptionId “ xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx “ -ResourceGroupName “RG-MoveCollection-demoRMS” -MoveCollectionName “PS-centralus-westcentralus-demoRMS ”   | Where-Object {  $_.MoveStatusMoveState -eq “InitiateMovePending” } | Select Name
    ```    

    **Output**

    ![Testo di output dopo il controllo dello stato di avvio](./media/tutorial-move-region-powershell/verify-initiate-move-pending.png)

2. Avviare lo spostamento:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverInitiateMove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```    

    **Output**

    ![Testo di output dopo l'avvio dello spostamento delle risorse](./media/tutorial-move-region-powershell/initiate-resources-move.png)


## <a name="discard-or-commit"></a>Rimozione o commit?

Dopo lo spostamento iniziale è possibile decidere se si vuole rimuovere lo spostamento o eseguirne il commit. 

- **Rimozione**: è possibile eliminare uno spostamento se si sta eseguendo un test e non si vuole realmente spostare la risorsa di origine. In seguito alla rimozione dello spostamento, la risorsa torna allo stato *Avvio spostamento in sospeso*. Se necessario, è possibile avviare nuovamente lo spostamento.
- **Commit**: il commit consente di completare lo spostamento nell'area di destinazione. Dopo il commit una risorsa di origine si troverà nello stato *Eliminazione origine in sospeso* e sarà possibile decidere se eliminarla.

### <a name="discard-the-move"></a>Rimuovere lo spostamento

Per annullare lo spostamento:

```azurepowershell-interactive
Invoke-AzResourceMoverDiscard -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
```
**Output**

![Testo di output dopo l'eliminazione dello spostamento](./media/tutorial-move-region-powershell/discard-move.png)


### <a name="commit-the-move"></a>Eseguire il commit dello spostamento

1. Eseguire il commit dello spostamento come indicato di seguito:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```
    **Output**

    ![Testo di output dopo il commit dello spostamento](./media/tutorial-move-region-powershell/commit-move.png)

2. Verificare che tutte le risorse siano state spostate nell'area di destinazione:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -ResourceGroupName “RG-MoveCollection-demoRMS ” -MoveCollectionName “PS-centralus-westcentralus-demoRMS”   
    ```
    Tutte le risorse sono ora in uno stato di *eliminazione origine in sospeso* nell'area di destinazione.

## <a name="delete-source-resources"></a>Elimina risorse di origine

Dopo aver eseguito il commit dello spostamento e aver verificato che le risorse funzionino come previsto nell'area di destinazione, è possibile eliminare ogni risorsa di origine nel [portale di Azure](../azure-resource-manager/management/manage-resources-portal.md#delete-resources), [usando PowerShell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources)o l'interfaccia della riga di comando di [Azure](../azure-resource-manager/management/manage-resources-cli.md#delete-resources).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * Spostare le macchine virtuali di Azure in un'altra area di Azure tramite PowerShell.
> * Le risorse associate alle macchine virtuali sono state spostate in un'altra area.

A questo punto, provare a migrare macchine virtuali di Azure tramite il portale

> [!div class="nextstepaction"]
> [Spostare le macchine virtuali di Azure nel portale](./tutorial-move-region-virtual-machines.md)


