---
title: Abilitare le protezioni dei carichi di lavoro integrati del Centro sicurezza di Azure
description: Scopri come abilitare Azure Defender per estendere le protezioni del Centro sicurezza di Azure alle tue risorse ibride e multicloud
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/24/2021
ms.openlocfilehash: 6496b18c8c22cbbd4fd3344736a4b38b7a998890
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102108923"
---
# <a name="quickstart-enable-azure-defender"></a>Guida introduttiva: abilitare Azure Defender

Per informazioni sui vantaggi di Azure Defender, vedere la pagina relativa al [Centro sicurezza di Azure Defender abilitata](security-center-pricing.md).

## <a name="prerequisites"></a>Prerequisiti

Ai fini delle guide di avvio rapido e delle esercitazioni sul Centro sicurezza, è necessario abilitare Azure Defender. 

Con Azure Defender è possibile proteggere un'intera sottoscrizione di Azure e le misure di protezione verranno ereditate da tutte le risorse al suo interno.

È disponibile una versione di valutazione gratuita valida 30 giorni. Per informazioni dettagliate sui prezzi nella valuta scelta e in base alla propria area, vedere [prezzi del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender-from-the-azure-portal"></a>Abilitare Azure Defender dalla portale di Azure

Per abilitare le funzionalità del Centro sicurezza, incluse le funzionalità di protezione dalle minacce, è necessario abilitare Azure Defender nella sottoscrizione che contiene i carichi di lavoro applicabili. Se il servizio viene abilitato a livello di area di lavoro, non verranno abilitate funzionalità come l'accesso JIT alle VM, i controlli applicazioni adattivi e i rilevamenti di rete per le risorse di Azure. Inoltre, gli unici piani di Azure Defender disponibili a livello di area di lavoro sono Azure Defender per i server e Azure Defender per i server SQL nelle macchine virtuali.

- È possibile abilitare **Azure Defender per gli account di archiviazione** a livello di sottoscrizione o di risorsa
- È possibile abilitare **Azure Defender per SQL** a livello di sottoscrizione o di risorsa
- È possibile abilitare la protezione dalle minacce per il **database di Azure per MariaDB/MySQL/PostgreSQL** solo a livello di risorsa

### <a name="to-enable-azure-defender-on-your-subscriptions-and-workspaces"></a>Per abilitare Azure Defender sulle sottoscrizioni e le aree di lavoro:

- Per abilitare Azure Defender in una sottoscrizione:

    1. Scegliere **Prezzi e impostazioni** dal menu del Centro sicurezza.
    1. Selezionare la sottoscrizione o l'area di lavoro che si desidera proteggere.
    1. Selezionare **Azure Defender - On** per eseguire l'aggiornamento.
    1. Selezionare **Salva**.

    > [!TIP]
    > Si noterà che i prezzi vengono applicati separatamente per ogni piano di Azure Defender, che è possibile attivare o disattivare singolarmente. Ad esempio, potrebbe essere necessario disabilitare Azure Defender per il servizio app nelle sottoscrizioni che non dispongono di un piano di servizio app Azure associato. 

    :::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Pagina dei prezzi del Centro sicurezza nel portale":::

- Per abilitare Azure Defender su più sottoscrizioni o aree di lavoro:

    1. Nella barra laterale del Centro sicurezza selezionare **Attività iniziali**.

        La scheda **Aggiorna** contiene un elenco di sottoscrizioni e aree di lavoro idonee per l'onboarding.

        :::image type="content" source="./media/enable-azure-defender/get-started-upgrade-tab.png" alt-text="Scheda Aggiorna della pagina introduttiva"::: 

    1. Dall'elenco **selezionare le sottoscrizioni e le aree di lavoro per abilitare Azure Defender in** selezionare le sottoscrizioni e le aree di lavoro da aggiornare e selezionare **Aggiorna** per abilitare Azure Defender.

       - Se si selezionano sottoscrizioni e aree di lavoro non idonee per la versione di valutazione, con il passaggio successivo verranno aggiornate e inizieranno a essere applicati gli addebiti.
       - Se si seleziona un'area di lavoro idonea per una versione di valutazione gratuita, il passaggio successivo inizierà una versione di valutazione.

        :::image type="content" source="./media/enable-azure-defender/upgrade-selected-workspaces-and-subscriptions.png" alt-text="Aggiorna tutte le aree di lavoro e le sottoscrizioni selezionate dalla pagina di introduzione":::


## <a name="next-steps"></a>Passaggi successivi

Ora che è stato abilitato Azure Defender, abilitare la raccolta automatica dei dati tramite gli agenti e le estensioni necessari descritti in [provisioning di agenti ed estensioni dal centro sicurezza di Azure](security-center-enable-data-collection.md).