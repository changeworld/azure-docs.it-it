---
title: Gestire la struttura di backup con Centro backup
description: Informazioni su come gestire l'ambiente Azure per assicurarsi che tutte le risorse siano conformi da una prospettiva di backup con il centro di backup.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 283c99c4b17683850f71b25fb2006784e43f3b8f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506210"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Gestire la struttura di backup con Centro backup

Backup Center consente di gestire l'ambiente Azure per assicurarsi che tutte le risorse siano conformi dal punto di vista del backup. Di seguito sono riportate alcune delle funzionalità di governance di backup Center:

* Visualizzare e assegnare criteri di Azure per il backup

* Visualizzare la conformità delle risorse in tutti i criteri di Azure predefiniti per il backup.

* Visualizzare tutte le origini dati che non sono state configurate per il backup.

## <a name="supported-scenarios"></a>Scenari supportati

* Per un elenco dettagliato degli scenari supportati e non supportati, vedere la [matrice di supporto](backup-center-support-matrix.md) .

## <a name="azure-policies-for-backup"></a>Criteri di Azure per il backup

Per visualizzare tutti i [criteri di Azure](../governance/policy/overview.md) disponibili per il backup, selezionare la voce **di menu criteri di Azure per il backup** . Verranno visualizzate tutte le [definizioni di criteri di Azure](policy-reference.md) predefinite e personalizzate per il backup disponibili per l'assegnazione alle sottoscrizioni e ai gruppi di risorse.

Selezionando una delle definizioni è possibile [assegnare i criteri](../governance/policy/tutorials/create-and-manage.md#assign-a-policy) a un ambito.

![Selezionare le definizioni dei criteri di Azure](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>Conformità del backup

Facendo clic sulla voce di menu conformità backup è possibile visualizzare la [conformità](../governance/policy/how-to/get-compliance-data.md) delle risorse in base ai vari criteri predefiniti assegnati all'ambiente Azure. È possibile visualizzare la percentuale di risorse conformi a tutti i criteri, nonché i criteri che dispongono di una o più risorse non conformi.

![Visualizzare la conformità del backup](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>Origini dati da proteggere

Selezionando la voce di menu origini dati da **proteggere** , è possibile visualizzare tutte le origini dati che non sono state configurate per il backup. È possibile filtrare l'elenco in base alla sottoscrizione DataSource, al gruppo di risorse, alla località, al tipo e ai tag. Una volta identificata un'origine dati di cui è necessario eseguire il backup, è possibile fare clic con il pulsante destro del mouse sull'elemento della griglia corrispondente e selezionare **backup** per configurare il backup per la risorsa.

![Menu origini dati da proteggere](./media/backup-center-govern-environment/protectable-datasources.png)

> [!NOTE]
> Se si seleziona **SQL nella macchina virtuale di Azure** come tipo di origine dati, la vista origine dati da **proteggere** Visualizza l'elenco di tutte le macchine virtuali della raccolta che non dispongono di alcun database SQL configurato per il backup.
> Se si seleziona **archiviazione di Azure (file di Azure)** come tipo di origine dati, la vista origine dati da **proteggere** Visualizza l'elenco di tutti gli account di archiviazione (che supportano le condivisioni file) che non dispongono di alcuna condivisione file configurata per il backup.


## <a name="next-steps"></a>Passaggi successivi

* [Monitorare e gestire i backup](backup-center-monitor-operate.md)
* [Eseguire azioni con backup Center](backup-center-actions.md)
* [Ottenere informazioni dettagliate sui backup](backup-center-obtain-insights.md)