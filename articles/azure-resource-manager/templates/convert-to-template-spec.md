---
title: Converti modello di portale in Specifica modello
description: Viene descritto come convertire un modello esistente nella raccolta portale di Azure in una specifica del modello.
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 8fe02f55348f2cdcabb43e05bb547819d4b51228
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739108"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>Convertire la raccolta di modelli nel portale in specifiche di modello

Il portale di Azure consente di archiviare modelli di Azure Resource Manager (modelli ARM) nell'account. **Questa funzionalità è in fase di deprecazione.** Per continuare a usare i modelli in questa raccolta, convertirli in [specifiche del modello](template-specs.md).

Questo articolo illustra come convertire i modelli esistenti nella raccolta di modelli in specifiche del modello.

Nel portale la funzionalità deprecata è chiamata **Templates (anteprima)**. Per verificare se sono presenti modelli da convertire, visualizzare la [raccolta di modelli nel portale](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems). Questi modelli hanno il tipo di risorsa `Microsoft.Gallery/myareas/galleryitems` .

## <a name="deprecation-of-portal-feature"></a>Funzionalità del portale deprecate

La raccolta di modelli nel portale verrà deprecata il 21 gennaio 2021. È possibile continuare a usarlo fino al 21 febbraio. A partire dal 22 febbraio, non è possibile creare nuovi modelli nella raccolta del portale, ma è comunque possibile visualizzare e distribuire i modelli esistenti.

Il 22 giugno la funzionalità verrà rimossa dal portale e tutte le operazioni API verranno bloccate. Non sarà possibile visualizzare o distribuire modelli dalla raccolta.

Prima del 22 giugno è necessario eseguire la migrazione di tutti i modelli che si desidera continuare a utilizzare. Per eseguire la migrazione dei modelli, è possibile usare uno dei metodi illustrati in questo articolo. Una volta rimossa la funzionalità, sarà necessario aprire un caso di supporto per ottenere i modelli di cui non è stata eseguita la migrazione.

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
