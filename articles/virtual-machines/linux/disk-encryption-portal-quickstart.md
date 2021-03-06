---
title: Creare e crittografare una macchina virtuale Linux con il portale di Azure
description: Questo argomento di avvio rapido illustra come usare il portale di Azure per creare e crittografare una macchina virtuale Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 188ba72f4da4e5a24554f895473f1c74e48d50fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558356"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Guida introduttiva: Creare e crittografare una macchina virtuale con il portale di Azure

È possibile creare macchine virtuali di Azure tramite il portale di Azure. Il portale di Azure è un'interfaccia utente basata su browser per creare le macchine virtuali e le risorse associate. Questo argomento di avvio rapido illustra come usare il portale di Azure per distribuire una macchina virtuale (VM) Linux che esegue Ubuntu 18.04 LTS, creare un insieme di credenziali delle chiavi per l'archiviazione delle chiavi di crittografia e crittografare la VM.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Scegliere **Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
1. Nella pagina Nuovo, in Più comuni, selezionare **Ubuntu Server 18.04 LTS**.
1. Nella scheda Informazioni di base verificare che sia selezionata la sottoscrizione corretta in Dettagli progetto.
1. Per "Gruppo di risorse" selezionare **Crea nuovo**. Immettere *myResourceGroup* come nome e scegliere **OK**.
1. Per **Nome macchina virtuale** immettere *MyVM*.
1. In **Area** selezionare *(Stati Uniti) Stati Uniti orientali*.
1. Assicurarsi che l'opzione **Dimensioni** sia impostata su *D2s Standard v3*.
1. In **Account amministratore** selezionare *Password* come **Tipo di autenticazione**. Immettere un nome utente e una password.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-linux-vm-creation.png" alt-text="Schermata relativa alla creazione della macchina virtuale Linux":::

    > [!WARNING]
    > La scheda "Dischi" include un campo "Tipo di crittografia" in **Opzioni disco**. Questo campo viene usato per specificare le opzioni di crittografia per [Managed Disks](../managed-disks-overview.md) + CMK, **non** per Crittografia dischi di Azure.
    >
    > Per evitare confusione, è consigliabile ignorare completamente la scheda *Dischi* durante il completamento dell'esercitazione.

1. Selezionare la scheda "Gestione" e verificare di avere un account di archiviazione di diagnostica. Se non sono presenti account di archiviazione, selezionare *Crea nuovo*, assegnare all'account di archiviazione il nome *myStorageAccount* e fare clic su "OK"

    :::image type="content" source="../media/disk-encryption/portal-quickstart-vm-creation-storage.png" alt-text="Schermata di creazione del gruppo di risorse":::

1. Fare clic su "Rivedi e crea".
1. Nella pagina **Crea macchina virtuale** è possibile visualizzare i dettagli sulla macchina virtuale che si sta creando. Quando si è pronti, selezionare **Crea**.

La distribuzione della macchina virtuale richiederà alcuni minuti. Al termine della distribuzione, passare alla sezione successiva.

## <a name="encrypt-the-virtual-machine"></a>Crittografare la macchina virtuale

1. Una volta completata la distribuzione della VM, selezionare **Vai alla risorsa**.
1. Sulla barra laterale sinistra selezionare **Dischi**.
1. Sulla barra superiore selezionare **Impostazioni aggiuntive**.
1. In **Impostazioni di crittografia** > **Dischi da crittografare** selezionare **Dischi dati e sistema operativo**.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-disks-to-encryption.png" alt-text="Screenshot che evidenzia il sistema operativo e i dischi dati.":::

1. In **Impostazioni di crittografia** scegliere **Selezionare l'insieme di credenziali delle chiavi e la chiave per la crittografia**.
1. Nella schermata **Selezionare chiave da Azure Key Vault** selezionare **Crea nuovo**.

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="Screenshot che evidenzia l'opzione Crea nuovo.":::

1. A sinistra di **Insieme di credenziali delle chiavi e chiave** selezionare **Fare clic per selezionare una chiave**.
1. In **Selezionare chiave da Azure Key Vault** selezionare **Crea nuovo** nel campo **Insieme di credenziali delle chiavi**.
1. Nella schermata **Crea un insieme di credenziali delle chiavi** assicurarsi che il valore di Gruppo di risorse sia *myResourceGroup* e assegnare un nome all'insieme di credenziali delle chiavi.  Ogni insieme di credenziali delle chiavi in Azure deve avere un nome univoco.
1. Nella scheda **Criteri di accesso** selezionare la casella **Crittografa dischi di Azure per la crittografia dei volumi**.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-keyvault-enable.png" alt-text="Selezione di dischi e crittografia":::

1. Selezionare **Rivedi e crea**.  
1. Dopo che l'insieme di credenziali delle chiavi ha superato la convalida, selezionare **Crea**. Si tornerà nella schermata **Selezionare chiave da Azure Key Vault**.
1. Lasciare vuoto il campo **Chiave** e scegliere **Seleziona**.
1. Nella parte superiore della schermata Crittografia fare clic su **Salva**. Viene visualizzato un avviso popup che informa che la VM verrà riavviata. Fare clic su **Sì**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare il gruppo di risorse, la macchina virtuale e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per la macchina virtuale, selezionare Elimina e quindi confermare il nome del gruppo di risorse da eliminare.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un Key Vault abilitato per le chiavi di crittografia, quindi è stata creata una macchina virtuale che è stata abilitata per la crittografia.  

> [!div class="nextstepaction"]
> [Informazioni su Crittografia dischi di Azure](disk-encryption-overview.md)