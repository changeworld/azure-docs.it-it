---
title: Estensioni per i servizi cloud (supporto esteso)
description: Estensioni per i servizi cloud (supporto esteso)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: f9029a36dc3b778e139b4553524e8e2ca6b4bbad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98757170"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Estensioni per i servizi cloud (supporto esteso)

Le estensioni sono piccole applicazioni che forniscono attività di configurazione e automazione post-distribuzione sui ruoli. Ad esempio, è possibile abilitare una connessione Desktop remoto nel ruolo durante la distribuzione del servizio cloud (supporto esteso) utilizzando Desktop remoto estensione.  

## <a name="remote-desktop-extension"></a>Estensione Desktop remoto

Desktop remoto consente di accedere al desktop di un ruolo in esecuzione in Azure. È possibile utilizzare una connessione Desktop remoto per risolvere i problemi e diagnosticare i problemi dell'applicazione mentre è in esecuzione.

È possibile abilitare una connessione Desktop remoto nel ruolo durante lo sviluppo includendo i moduli desktop remoto nella definizione del servizio o tramite l'estensione desktop remoto. 

Per ulteriori informazioni, vedere [configurare Desktop remoto dalla portale di Azure](enable-rdp.md)

## <a name="windows-azure-diagnostics-extension"></a>Estensione Diagnostica di Azure Windows

È possibile monitorare le metriche di prestazioni chiave per qualsiasi servizio cloud. Ogni ruolo del servizio cloud raccoglie dati minimi: utilizzo della CPU, utilizzo della rete e utilizzo del disco. Se il servizio cloud ha l'estensione Microsoft. Azure. Diagnostics applicata a un ruolo, tale ruolo può raccogliere ulteriori punti di dati. 

Con il monitoraggio di base, i dati dei contatori delle prestazioni provenienti dalle istanze del ruolo sono campionati e raccolti a intervalli di 3 minuti. Questi dati di monitoraggio di base non vengono salvati nell'account di archiviazione e non hanno alcun costo aggiuntivo. 

Con il monitoraggio avanzato, altre metriche vengono campionate e raccolte a intervalli di 5 minuti, 1 ora e 12 ore. I dati aggregati vengono salvati in un account di archiviazione all'interno di tabelle e vengono eliminati dopo 10 giorni. L'account di archiviazione usato è configurato per ruolo; è possibile usare account di archiviazione diversi per ruoli diversi. 

Per ulteriori informazioni, vedere [Apply the Windows Azure Diagnostics Extension in Cloud Services (Extended Support)](enable-wad.md)


## <a name="next-steps"></a>Passaggi successivi 
- Esaminare i [prerequisiti di distribuzione](deploy-prerequisite.md) per i servizi cloud (supporto esteso).
- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando il [portale di Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), il [modello](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
