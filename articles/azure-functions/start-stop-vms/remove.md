---
title: Rimuovi Panoramica di avvio/arresto di macchine virtuali V2 (anteprima)
description: Questo articolo descrive come rimuovere la funzionalità di avvio/arresto di macchine virtuali V2 (anteprima).
services: azure-functions
ms.subservice: ''
ms.date: 03/30/2021
ms.topic: conceptual
ms.openlocfilehash: b4308be8f7494c1cb6f6b4839fc5a2e9717668e3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111804"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>Come rimuovere le VM di avvio/arresto V2 (anteprima)

Dopo aver abilitato la funzionalità avvia/arresta VM V2 (anteprima) per gestire lo stato di esecuzione delle macchine virtuali di Azure, è possibile decidere di interrompere l'uso. La rimozione di questa funzionalità può essere eseguita eliminando il gruppo di risorse dedicato per archiviare le risorse seguenti per supportare le VM di avvio/arresto V2 (anteprima):

- Applicazioni di funzioni di Azure
- Pianificazioni in app per la logica di Azure
- Istanza di Application Insights
- Account di archiviazione di Azure

## <a name="delete-the-dedicated-resource-group"></a>Eliminare il gruppo di risorse dedicato

Per eliminare il gruppo di risorse, seguire i passaggi descritti nell'articolo [Azure Resource Manager gruppo di risorse e sull'eliminazione di risorse](../../azure-resource-manager/management/delete-resource-group.md) .

## <a name="next-steps"></a>Passaggi successivi

Per distribuire nuovamente questa funzionalità, vedere [distribuire start/stop V2](deploy.md) (anteprima).