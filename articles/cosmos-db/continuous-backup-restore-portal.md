---
title: Configurare il backup continuo e il ripristino temporizzato usando portale di Azure in Azure Cosmos DB.
description: Informazioni su come identificare il punto di ripristino e configurare il backup continuo con portale di Azure. Mostra come ripristinare un account Live ed eliminato.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ee6eedbc078e1b9c07ed00922ce1c37b38410128
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381869"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-portal"></a>Configurare e gestire il backup continuo e il ripristino temporizzato (anteprima)-uso di portale di Azure
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> La funzionalità di ripristino temporizzato (modalità di backup continuo) per Azure Cosmos DB è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La funzionalità di ripristino temporizzato di Azure Cosmos DB (anteprima) consente di eseguire il ripristino da una modifica accidentale all'interno di un contenitore, di ripristinare un account, un database o un contenitore eliminato o di eseguire il ripristino in qualsiasi area (in cui erano presenti backup). La modalità di backup continuo consente di eseguire il ripristino in qualsiasi momento negli ultimi 30 giorni.

Questo articolo descrive come identificare il punto di ripristino e configurare il backup continuo con portale di Azure.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Effettuare il provisioning di un account con backup continuo

Quando si crea un nuovo account di Azure Cosmos DB, per l'opzione **criteri di backup** scegliere modalità **continua** per abilitare la funzionalità di ripristino temporizzato per il nuovo account. Quando questa funzionalità è abilitata per l'account, tutti i database e i contenitori sono disponibili per il backup continuo. Con il ripristino temporizzato, i dati vengono sempre ripristinati in un nuovo account, attualmente non è possibile eseguire il ripristino in un account esistente.

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="Effettuare il provisioning di un account Azure Cosmos DB con la configurazione del backup continuo." border="true":::

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>Ripristinare un account Live da modifiche accidentali

È possibile usare portale di Azure per ripristinare un account Live o i database e i contenitori selezionati al suo interno. Per ripristinare i dati, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/)
1. Passare all'account di Azure Cosmos DB e aprire il riquadro **ripristino temporizzato** .

   > [!NOTE]
   > Il riquadro Restore in portale di Azure viene popolato solo se si dispone dell' `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` autorizzazione. Per altre informazioni su come impostare questa autorizzazione, vedere l'articolo relativo alle [autorizzazioni di backup e ripristino](continuous-backup-restore-permissions.md) .

1. Per eseguire il ripristino, compilare i dettagli seguenti:

   * **Punto di ripristino (UTC)** : un timestamp negli ultimi 30 giorni. L'account deve esistere in tale timestamp. È possibile specificare il punto di ripristino in formato UTC. Può essere il più vicino al secondo quando si desidera ripristinarlo. Selezionare il collegamento **fare clic qui** per ottenere informazioni sull' [Identificazione del punto di ripristino](#event-feed).

   * **Località** : area di destinazione in cui viene ripristinato l'account. L'account deve esistere in quest'area al timestamp specificato (ad esempio, Stati Uniti occidentali o Stati Uniti orientali. Un account può essere ripristinato solo nelle aree in cui è presente l'account di origine.

   * **Ripristina risorsa** : è possibile scegliere un **intero account** o un **database/contenitore selezionato** da ripristinare. I database e i contenitori devono esistere al timestamp specificato. In base al punto di ripristino e al percorso selezionati, le risorse di ripristino vengono popolate, consentendo all'utente di selezionare database o contenitori specifici che devono essere ripristinati.

   * **Gruppo** di risorse: gruppo di risorse in cui verrà creato e ripristinato l'account di destinazione. Il gruppo di risorse deve essere già esistente.

   * **Restore account di destinazione** : il nome dell'account di destinazione. Il nome dell'account di destinazione deve seguire le stesse linee guida per la creazione di un nuovo account. Questo account verrà creato dal processo di ripristino nella stessa area in cui risiede l'account di origine.
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="Ripristinare un account Live da modifiche accidentali portale di Azure." border="true":::

1. Dopo aver selezionato i parametri precedenti, selezionare il pulsante **Submit (Invia** ) per avviare un ripristino. Il costo di ripristino è un addebito di una sola volta, in base alla quantità di dati e agli addebiti per l'archiviazione nell'area specificata. Per ulteriori informazioni, vedere la sezione relativa ai [prezzi](continuous-backup-restore-introduction.md#continuous-backup-pricing) .

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>Usare il feed di eventi per identificare l'ora di ripristino

Quando si compila l'ora del punto di ripristino nel portale di Azure, se è necessaria assistenza per l'identificazione del punto di ripristino, selezionare il collegamento **fare clic qui** per passare al pannello Feed eventi. Il feed di eventi fornisce un elenco completo di fedeltà per gli eventi di creazione, sostituzione ed eliminazione nei database e nei contenitori dell'account di origine. 

Se, ad esempio, si desidera eseguire il ripristino fino al momento in cui un determinato contenitore è stato eliminato o aggiornato, controllare questo feed di eventi. Gli eventi vengono visualizzati in ordine cronologico decrescente in base al momento in cui si sono verificati, con gli eventi recenti nella parte superiore. È possibile esplorare i risultati e selezionare l'ora prima o dopo l'evento per restringere ulteriormente il tempo.

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="Usare il feed di eventi per identificare l'ora del punto di ripristino." border="true":::

> [!NOTE]
> Il feed di eventi non Visualizza le modifiche apportate alle risorse dell'elemento. È sempre possibile specificare manualmente qualsiasi timestamp negli ultimi 30 giorni (purché esista l'account in quel momento) per il ripristino.

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>Ripristinare un account eliminato

È possibile utilizzare portale di Azure per ripristinare completamente un account eliminato entro 30 giorni dall'eliminazione. Per ripristinare un account eliminato, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/)
1. Cercare *Azure Cosmos DB* risorse nella barra di ricerca globale. Elenca tutti gli account esistenti.
1. Selezionare quindi il pulsante **Ripristina** . Il riquadro ripristino Visualizza un elenco di account eliminati che è possibile ripristinare entro il periodo di memorizzazione, ovvero 30 giorni dall'ora di eliminazione.
1. Scegliere l'account che si desidera ripristinare.

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="Ripristinare un account eliminato da portale di Azure." border="true":::

   > [!NOTE]
   > Nota: il riquadro ripristino in portale di Azure viene popolato solo se si dispone dell' `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` autorizzazione. Per altre informazioni su come impostare questa autorizzazione, vedere l'articolo relativo alle [autorizzazioni di backup e ripristino](continuous-backup-restore-permissions.md) .

1. Selezionare un account da ripristinare e immettere i dettagli seguenti per ripristinare un account eliminato:

   * **Punto di ripristino (UTC)** : un timestamp negli ultimi 30 giorni. L'account dovrebbe esistere a tale timestamp. Specificare il punto di ripristino in formato UTC. Può essere il più vicino al secondo quando si desidera ripristinarlo.

   * **Località** : area di destinazione in cui deve essere ripristinato l'account. L'account di origine deve esistere in questa area al timestamp specificato. Esempi di Stati Uniti occidentali o Stati Uniti orientali.  

   * **Gruppo** di risorse: gruppo di risorse in cui verrà creato e ripristinato l'account di destinazione. Il gruppo di risorse deve essere già esistente.

   * **Ripristino dell'account di destinazione** : il nome dell'account di destinazione deve seguire le stesse linee guida per la creazione di un nuovo account. Questo account verrà creato dal processo di ripristino nella stessa area in cui risiede l'account di origine.

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>Tenere traccia dello stato dell'operazione di ripristino

Dopo l'avvio di un'operazione di ripristino, selezionare l'icona della campana di **notifica** nell'angolo superiore destro del portale. Fornisce un collegamento che visualizza lo stato dell'account da ripristinare. Durante l'esecuzione del ripristino, lo stato dell'account verrà *creato*, al termine dell'operazione di ripristino, lo stato dell'account verrà modificato in in *linea*.

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="Lo stato dell'account ripristinato passa da creazione a online al termine dell'operazione." border="true":::

## <a name="next-steps"></a>Passaggi successivi

* Configurare e gestire il backup continuo usando l'interfaccia della riga di comando di [Azure](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-powershell.md)o [Azure Resource Manager](continuous-backup-restore-template.md).
* [Modello di risorse della modalità di backup continuo](continuous-backup-restore-resource-model.md)
* [Gestire le autorizzazioni](continuous-backup-restore-permissions.md) necessarie per ripristinare i dati con la modalità di backup continuo.
