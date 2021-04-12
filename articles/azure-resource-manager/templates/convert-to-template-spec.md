---
title: Converti modello di portale in Specifica modello
description: Viene descritto come convertire un modello esistente nella raccolta portale di Azure in una specifica del modello.
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: c59275fca1eb3037b48b7293fc9e507df46b7fcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99555938"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>Convertire la raccolta di modelli nel portale in specifiche di modello

Il portale di Azure consente di archiviare modelli di Azure Resource Manager (modelli ARM) nell'account. Tuttavia, le [specifiche del modello](template-specs.md) offrono un modo più semplice per condividere i modelli con gli utenti dell'organizzazione e collegarsi ad altri modelli. Questo articolo illustra come convertire i modelli esistenti nella raccolta di modelli in specifiche del modello.

Per verificare se sono presenti modelli da convertire, visualizzare la [raccolta di modelli nel portale](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems). Questi modelli hanno il tipo di risorsa `Microsoft.Gallery/myareas/galleryitems` .

## <a name="convert-with-powershell-script"></a>Converti con script di PowerShell

Per semplificare la conversione dei modelli nella raccolta di modelli, usare uno script di PowerShell nel repository di modelli di avvio rapido di Azure. Quando si esegue lo script, è possibile creare una nuova specifica del modello per ogni modello o scaricare un modello che crea la specifica del modello. Lo script non elimina il modello dalla raccolta di modelli.

1. Copiare lo [script di migrazione](https://github.com/Azure/azure-quickstart-templates/blob/master/201-templatespec-migrate-create/Migrate-GalleryItems.ps1). Salvare una copia locale con il nome *Migrate-GalleryItems.ps1*.
1. Per creare nuove specifiche del modello, fornire i valori per `-ResourceGroupName` i `-Location` parametri e. 

   Impostare `ItemsToExport` su `MyGalleryItems` per esportare i modelli. Impostarlo su `AllGalleryItems` per esportare tutti i modelli a cui si ha accesso.

   Nell'esempio seguente vengono create nuove specifiche del modello per ogni modello in un gruppo di risorse denominato **migratedRG**. Se non esiste, lo script crea il gruppo di risorse.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. Per scaricare i modelli che è possibile usare per creare le specifiche del modello, non specificare i valori per il gruppo di risorse o la località. In alternativa, specificare `-ExportToFile` . Il modello non è uguale a quello della raccolta. Contiene invece una risorsa specifica del modello che consente di creare la specifica del modello per il modello.

   Nell'esempio seguente vengono scaricati i modelli senza creare specifiche del modello.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   Per informazioni su come distribuire il modello che crea la specifica del modello, vedere [Guida introduttiva: creare e distribuire una specifica del modello (anteprima)](quickstart-create-template-specs.md).

Per altre informazioni sullo script e sui relativi parametri, vedere [creare TemplateSpecs da modelli della raccolta di](https://github.com/Azure/azure-quickstart-templates/tree/master/201-templatespec-migrate-create)modelli.

## <a name="manually-convert-through-portal"></a>Conversione manuale tramite il portale

È possibile copiare manualmente i modelli dalla raccolta in una nuova specifica del modello.

1. Aprire i [modelli (anteprima)](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems) nel portale.
1. Selezionare il modello da migrare.
1. Selezionare **Visualizza modello**.
1. Copiare il contenuto del modello.
1. Nella barra di ricerca del portale cercare le **specifiche del modello**. Selezionare tale opzione.
1. Selezionare **Crea specifica di modello**.
1. Specificare i valori per nome, sottoscrizione, gruppo di risorse, posizione e versione.
1. Selezionare **Avanti: modifica modello**.
1. Per il contenuto del modello, incollare il modello copiato dalla raccolta di modelli.
1. Selezionare **Rivedi e crea**.
1. Una volta completata la convalida, selezionare **Crea**.

Se è necessario condividere la specifica del modello con altri utenti dell'organizzazione, [impostare il controllo degli accessi in base al ruolo](../../role-based-access-control/tutorial-role-assignments-group-powershell.md) per il gruppo o gli utenti che richiedono l'accesso.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle specifiche del modello, vedere [creare e distribuire specifiche di modelli](template-specs.md).
