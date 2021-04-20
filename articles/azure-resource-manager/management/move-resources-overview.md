---
title: Spostare le risorse di Azure tra gruppi di risorse, sottoscrizioni o aree.
description: Panoramica dei tipi di risorse di Azure che possono essere spostati tra gruppi di risorse, sottoscrizioni o aree.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 646c0892ac3609fc531e91f4bfb9c1de6a61bc38
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730506"
---
# <a name="move-azure-resources-across-resource-groups-subscriptions-or-regions"></a>Spostare le risorse di Azure tra gruppi di risorse, sottoscrizioni o aree

Le risorse di Azure possono essere spostate in un nuovo gruppo di risorse o sottoscrizione o in aree diverse.

## <a name="move-resources-across-resource-groups-or-subscriptions"></a>Spostare le risorse tra gruppi di risorse o sottoscrizioni

È possibile spostare le risorse di Azure in un'altra sottoscrizione di Azure o in un altro gruppo di risorse nella stessa sottoscrizione. Per spostare le risorse, è possibile usare il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Per altre informazioni, vedere [Spostare le risorse in un nuovo gruppo di risorse o sottoscrizione.](move-resource-group-and-subscription.md)

### <a name="upgrade-a-subscription"></a>Aggiornare una sottoscrizione

Se si desidera effettivamente aggiornare l'offerta della sottoscrizione di Azure (ad esempio passando da gratuito a pagamento a consumo) è necessario convertire la sottoscrizione.

- Per aggiornare una versione di valutazione gratuita, vedere Aggiornare la versione di valutazione gratuita o Microsoft Imagine sottoscrizione di Azure a con pagamento [in base al go.](../../cost-management-billing/manage/upgrade-azure-subscription.md)
- Per modificare un account con pagamento in base al go, vedere Modificare la sottoscrizione con pagamento in base al go di [Azure in un'offerta diversa.](../../cost-management-billing/manage/switch-azure-offer.md)

Se non è possibile convertire la sottoscrizione, [creare una richiesta di supporto tecnico di Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selezionare **Gestione delle sottoscrizioni** per il tipo di problema.

## <a name="move-resources-across-regions"></a>Spostare le risorse tra aree

Aree geografiche, aree e zone di disponibilità di Azure sono alla base dell'infrastruttura globale di Azure. Le [aree geografiche di](https://azure.microsoft.com/global-infrastructure/geographies/) Azure contengono in genere due o più aree di [Azure.](https://azure.microsoft.com/global-infrastructure/regions/) Un'area è un'area all'interno di un'area geografica, zone di disponibilità e più data center.

Dopo aver distribuito le risorse in un'area di Azure specifica, è possibile spostare le risorse in un'area diversa per diversi motivi.

- **Allineamento all'avvio di** un'area: spostare le risorse in un'area di Azure appena introdotta che non era disponibile in precedenza.
- **Usufruire di servizi/funzionalità**: spostare le risorse per sfruttare servizi o funzionalità disponibili in un'area specifica.
- **Rispondere a sviluppi aziendali**: spostare le risorse in un'area in risposta a modifiche aziendali, ad esempio fusioni o acquisizioni.
- **Allinearsi per prossimità**: spostare le risorse in un'area in cui si trova l'azienda.
- **Soddisfare requisiti relativi ai dati**: spostare le risorse per garantire l'allineamento ai requisiti di residenza dei dati o alle esigenze di classificazione dei dati. [Altre informazioni](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf)
- **Rispondere a requisiti di distribuzione**: spostare le risorse distribuite per errore o spostarle in risposta a esigenze di capacità.
- **Rispondere alla rimozione delle autorizzazioni:** spostare le risorse a causa di aree rimosse.

### <a name="move-resources-with-resource-mover"></a>Spostare le risorse con Lo spostamento risorse

È possibile spostare le risorse in un'area diversa con [Spostamento risorse di Azure](../../resource-mover/overview.md). Spostamento risorse offre quanto segue:

- Un singolo hub per lo spostamento di risorse tra aree.
- Riduzione dei tempi richiesti e della complessità degli spostamenti. Tutto il necessario in un'unica posizione.
- Un'esperienza semplice e coerente per spostare diversi tipi di risorse di Azure.
- Un modo semplice per identificare le dipendenze tra le risorse da spostare. Questa identificazione consente di spostare le risorse correlate insieme, in modo che tutto funzioni come previsto nell'area di destinazione, dopo lo spostamento.
- Pulizia automatica delle risorse nell'area di origine, se si preferisce eliminarle dopo lo spostamento.
- Test. È possibile provare a eseguire uno spostamento e quindi annullarlo se non si vuole procedere a uno spostamento completo.

È possibile spostare le risorse in un'altra area usando due metodi diversi:

- **Iniziare a spostare le risorse da un gruppo di risorse:** con questo metodo si avvia lo spostamento dell'area dall'interno di un gruppo di risorse. Dopo aver selezionato le risorse da spostare, il processo continua nell'hub Spostamento risorse per controllare le dipendenze delle risorse e orchestrare il processo di spostamento. [Altre informazioni](../../resource-mover/move-region-within-resource-group.md)
- **Iniziare a spostare le risorse direttamente dall'hub** spostamento risorse: con questo metodo si avvia il processo di spostamento dell'area direttamente nell'hub. [Altre informazioni](../../resource-mover/tutorial-move-region-virtual-machines.md)

## <a name="next-steps"></a>Passaggi successivi

- Per verificare se un tipo di risorsa supporta lo spostamento, vedere Supporto [delle operazioni di spostamento per le risorse.](move-support-resources.md)
- Per altre informazioni sul processo di spostamento dell'area, vedere [Informazioni sul processo di spostamento.](../../resource-mover/about-move-process.md)
