---
title: Creare un'offerta di macchina virtuale di Azure Azure Marketplace usando la propria immagine
description: Informazioni su come pubblicare un'offerta di macchina virtuale Azure Marketplace usando la propria immagine.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: 47fe7b42b68ae42f74a74e5fc69c8d1041d3bf8d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727124"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Come creare una macchina virtuale usando la propria immagine

Questo articolo descrive come creare e distribuire un'immagine di macchina virtuale fornita dall'utente.

> [!NOTE]
> Prima di iniziare questa procedura, esaminare i [requisiti](marketplace-virtual-machines.md#technical-requirements) tecnici per le offerte di macchine virtuali di Azure, inclusi i requisiti del disco rigido virtuale (VHD).

Per usare invece un'immagine di base approvata, seguire le istruzioni in [Creare un'immagine di macchina virtuale da una base approvata.](azure-vm-create-using-approved-base.md)

## <a name="configure-the-vm"></a>Configurare la macchina virtuale

Questa sezione descrive come ridimensionare, aggiornare e generalizzare una macchina virtuale di Azure. Questa procedura è necessaria per preparare la macchina virtuale alla distribuzione in Azure Marketplace.

### <a name="size-the-vhds"></a>Ridimensionare i dischi rigidi virtuali

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Installare gli aggiornamenti più recenti

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>Eseguire altri controlli di sicurezza

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Eseguire una configurazione personalizzata e attività pianificate

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>Generalizzare l'immagine

Tutte le immagini in Azure Marketplace devono poter essere riutilizzate in modo generico. Per ottenere questo risultato, il disco rigido virtuale del sistema operativo deve essere generalizzato. La generalizzazione è un'operazione che rimuove tutti gli identificatori specifici di istanze e tutti i driver software da una macchina virtuale.

## <a name="bring-your-image-into-azure"></a>Portare l'immagine in Azure

> [!NOTE]
> La sottoscrizione di Azure che contiene il sig deve essere nello stesso tenant dell'account editore per poter pubblicare. Inoltre, l'account editore deve avere almeno l'accesso Collaboratore alla sottoscrizione contenente SIG.

Esistono tre modi per portare l'immagine in Azure:

1. Caricare il disco rigido virtuale in una raccolta di immagini condivise.
1. Caricare il disco rigido virtuale in un account di archiviazione di Azure.
1. Estrarre il disco rigido virtuale da un'immagine gestita (se si usano servizi di creazione di immagini).

Queste opzioni sono descritte nelle tre sezioni seguenti.

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>Opzione 1: Caricare il disco rigido virtuale come raccolta immagini condivise

1. Caricare i dischi rigidi virtuali nell'account di archiviazione.
2. Nella pagina portale di Azure cercare **Deploy a custom template (Distribuisci un modello personalizzato).**
3. Selezionare **Creare un modello personalizzato nell'editor**.
4. Copiare il modello Azure Resource Manager (ARM) seguente.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sourceStorageAccountResourceId": {
          "type": "string",
          "metadata": {
            "description": "Resource ID of the source storage account that the blob vhd resides in."
          }
        },
        "sourceBlobUri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk0Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk1Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "galleryName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Shared Image Gallery."
          }
        },
        "galleryImageDefinitionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Definition."
          }
        },
        "galleryImageVersionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Version - should follow <MajorVersion>.<MinorVersion>.<Patch>."
          }
        }
      },
      "resources": [
        {
          "type": "Microsoft.Compute/galleries/images/versions",
          "name": "[concat(parameters('galleryName'), '/', parameters('galleryImageDefinitionName'), '/', parameters('galleryImageVersionName'))]",
          "apiVersion": "2020-09-30",
          "location": "[resourceGroup().location]",
          "properties": {
            "storageProfile": {
              "osDiskImage": {
                "source": {
                  "id": "[parameters('sourceStorageAccountResourceId')]",
                  "uri": "[parameters('sourceBlobUri')]"
                }
              },
    
              "dataDiskImages": [
                {
                  "lun": 0,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk0Uri')]"
                  }
                },
                {
                  "lun": 1,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk1Uri')]"
                  }
                }
              ]
            }
          }
        }
      ]
    }
    
    ```

5. Incollare il modello nell'editor.

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="Schermata del codice di esempio per la macchina virtuale.":::

1. Selezionare **Salva**.
1. Usare i parametri in questa tabella per completare i campi nella schermata seguente.

| Parametri | Descrizione |
| --- | --- |
| sourceStorageAccountResourceId | ID risorsa dell'account di archiviazione di origine in cui si trova il disco rigido virtuale BLOB.<br><br>Per ottenere l'ID risorsa, passare **all'account** di archiviazione **portale di Azure,** passare **a** Proprietà e copiare il **valore ResourceID.** |
| sourceBlobUri | URI BLOB del BLOB vhd del disco del sistema operativo (deve essere nell'account di archiviazione fornito).<br><br>Per ottenere l'URL del BLOB, passare **all'account** di archiviazione **portale di Azure,** passare al **BLOB** e copiare il **valore dell'URL.** |
| sourceBlobDataDisk0Uri | URI BLOB del BLOB vhd del disco dati (deve essere nell'account di archiviazione fornito). Se non si dispone di un disco dati, rimuovere questo parametro dal modello.<br><br>Per ottenere l'URL del BLOB, passare **all'account** di archiviazione **portale di Azure,** passare al **BLOB** e copiare il **valore dell'URL.** |
| sourceBlobDataDisk1Uri | URI BLOB del BLOB vhd del disco dati aggiuntivo (deve essere nell'account di archiviazione fornito). Se non si dispone di un disco dati aggiuntivo, rimuovere questo parametro dal modello.<br><br>Per ottenere l'URL del BLOB, passare **all'account** di archiviazione **portale di Azure,** passare al **BLOB** e copiare il **valore dell'URL.** |
| galleryName | Nome della raccolta di immagini condivise |
| galleryImageDefinitionName | Nome della definizione dell'immagine |
| galleryImageVersionName | Nome della versione dell'immagine da creare, nel formato seguente: `<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="Mostra la finestra di distribuzione personalizzata.":::

8. Selezionare **Rivedi e crea**. Al termine della convalida, selezionare **Crea.**

> [!TIP]
> L'account editore deve avere l'accesso "Proprietario" per pubblicare l'immagine SIG. Se necessario, seguire questa procedura per concedere l'accesso:
>
> 1. Passare a Raccolta immagini condivise.
> 2. Selezionare **Controllo di** accesso (IAM) nel pannello sinistro.
> 3. Selezionare **Aggiungi** e quindi Aggiungi **assegnazione di ruolo.**
> 4. Per **Ruolo** selezionare **Proprietario.**
> 5. Per **Assegna accesso a** selezionare **Utente, gruppo o entità servizio.**
> 6. Immettere l'indirizzo di posta elettronica di Azure della persona che pubblicherà l'immagine.
> 7. Selezionare **Salva**.<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="Viene visualizzata la finestra Aggiungi assegnazione di ruolo.":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>Opzione 2: Caricare il disco rigido virtuale in un account di archiviazione

Configurare e preparare la macchina virtuale per il caricamento come descritto in Preparare un disco rigido virtuale Windows o [VHDX](../virtual-machines/windows/prepare-for-upload-vhd-image.md) per il caricamento in Azure o Creare e caricare un [disco rigido virtuale Linux.](../virtual-machines/linux/create-upload-generic.md)

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>Opzione 3: Estrarre il disco rigido virtuale da un'immagine gestita (se si usano servizi di creazione di immagini)

Se si usa un servizio di creazione di immagini come [Packer,](https://www.packer.io/)potrebbe essere necessario estrarre il disco rigido virtuale dall'immagine. Non esiste un modo diretto per eseguire questa operazione. Sarà necessario creare una macchina virtuale ed estrarre il disco rigido virtuale dal disco della macchina virtuale.

## <a name="create-the-vm-on-the-azure-portal"></a>Creare la macchina virtuale nel portale di Azure

Seguire questa procedura per creare l'immagine della macchina virtuale di base nel [portale di Azure](https://ms.portal.azure.com/).

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).
2. Selezionare **Macchine virtuali**.
3. Selezionare **+ Aggiungi** per aprire la schermata Crea una **macchina** virtuale.
4. Selezionare l'immagine dall'elenco a discesa o selezionare Sfoglia tutte le immagini **pubbliche e** private per cercare o esplorare tutte le immagini di macchine virtuali disponibili.
5. Per creare una **macchina virtuale di generazione 2,** passare alla **scheda** Avanzate e selezionare l'opzione **Gen 2.**

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Selezionare Gen 1 o Gen 2.":::

6. Selezionare le dimensioni della macchina virtuale da distribuire.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Selezionare le dimensioni consigliate della macchina virtuale per l'immagine selezionata.":::

7. Indicare gli altri dettagli necessari per creare la macchina virtuale.
8. Selezionare **Review + create (Revisione e creazione)** per rivedere le scelte effettuate. Quando viene **visualizzato il messaggio** Convalida superata, selezionare **Crea**.

Azure avvia il provisioning della macchina virtuale specificata. Tenere traccia dello stato di avanzamento selezionando la **scheda Macchine** virtuali nel menu a sinistra. Dopo la creazione, lo stato della macchina virtuale cambia in **Esecuzione .**

## <a name="connect-to-your-vm"></a>Connettersi alla macchina virtuale

Fare riferimento alla documentazione seguente per connettersi alla macchina [virtuale Windows](../virtual-machines/windows/connect-logon.md) [o Linux.](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm)

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Testare l'immagine della](azure-vm-image-test.md) macchina virtuale per assicurarsi che soddisfi i Azure Marketplace di pubblicazione. Questo indirizzo è facoltativo.
- Se non si vuole testare l'immagine [](https://partner.microsoft.com/) della macchina virtuale, accedere a Partner Center pubblicare l'immagine SIG (opzione #1).
- Se è stata seguita l'opzione #2 o #3, [generare l'URI di firma di accesso condiviso](azure-vm-get-sas-uri.md).
- In caso di difficoltà durante la creazione del nuovo disco rigido virtuale basato su Azure, vedere Domande frequenti sulle macchine virtuali [per Azure Marketplace](azure-vm-create-faq.md).
