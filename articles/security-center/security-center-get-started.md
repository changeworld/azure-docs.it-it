---
title: Eseguire l'aggiornamento ad Azure Defender - Centro sicurezza di Azure
description: Questo argomento di avvio rapido illustra come eseguire l'aggiornamento ad Azure Defender del Centro sicurezza per aumentare la sicurezza.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f37a59d5db3883754b602b2b2525e07b57206b7
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099353"
---
# <a name="quickstart-set-up-azure-security-center"></a>Avvio rapido: Configurare il Centro sicurezza di Azure

Il Centro sicurezza di Azure offre la gestione unificata della sicurezza e la protezione dalle minacce nei carichi di lavoro ibridi e multicloud. Mentre le funzionalità gratuite offrono sicurezza limitata solo per le risorse di Azure, l'abilitazione di Azure Defender estende queste funzionalità all'ambiente locale e ad altri ambienti cloud. Azure Defender consente di individuare e risolvere le vulnerabilità di sicurezza, di applicare i controlli su applicazioni e accessi per bloccare le attività dannose, di rilevare le minacce usando funzioni di analisi e di intelligenza e di rispondere rapidamente in caso di attacco. È possibile provare Azure Defender gratuitamente. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

Questa sezione introduttiva illustra tutti i passaggi consigliati per abilitare il Centro sicurezza di Azure e Azure Defender. Una volta completati tutti i passaggi della Guida introduttiva, saranno disponibili:

> [!div class="checklist"]
> * Il Centro sicurezza è abilitato nelle sottoscrizioni di Azure
> * Azure Defender abilitato nelle sottoscrizioni di Azure
> * Configurazione automatica della raccolta dati
> * Notifiche di posta elettronica configurate per gli avvisi di sicurezza
> * Computer ibridi e multicloud connessi ad Azure

## <a name="prerequisites"></a>Prerequisiti
Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

Per abilitare Azure Defender in una sottoscrizione, è necessario avere il ruolo di proprietario della sottoscrizione, collaboratore della sottoscrizione o amministratore della sicurezza.

## <a name="enable-security-center-on-your-azure-subscription"></a>Abilitare Centro sicurezza nella sottoscrizione di Azure

> [!TIP]
> Per abilitare Centro sicurezza in tutte le sottoscrizioni in un gruppo di gestione, vedere [Abilitare Centro sicurezza in più sottoscrizioni di Azure](onboard-management-group.md).

1. Accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/).

1. Nel menu del portale selezionare **Centro sicurezza**. 

    Si aprirà la pagina di panoramica del Centro sicurezza.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Dashboard di panoramica del Centro sicurezza" lightbox="./media/security-center-get-started/overview.png":::

    **Centro di sicurezza - Panoramica** offre una visualizzazione unificata del comportamento di sicurezza dei carichi di lavoro cloud ibridi, consentendo all'utente di individuarne e valutarne la sicurezza e di identificare e ridurre i rischi. Il Centro sicurezza abilita automaticamente, senza alcun costo, una sottoscrizione di Azure di cui non sia stato eseguito l'onboarding in precedenza da uno degli utenti della sottoscrizione.

Per visualizzare e filtrare l'elenco delle sottoscrizioni, selezionare la voce di menu **Sottoscrizioni**. Il Centro sicurezza modificherà la visualizzazione in modo da riflettere il comportamento di sicurezza delle sottoscrizioni selezionate. 

Dopo pochi minuti dal primo avvio del Centro sicurezza, è possibile visualizzare quanto segue:

- **Suggerimenti** per individuare i modi per migliorare la sicurezza delle risorse connesse.
- Un inventario delle risorse attualmente valutate dal Centro sicurezza, con il comportamento di sicurezza di ognuna.

Per usufruire di tutti i vantaggi del Centro sicurezza, continuare con i passaggi successivi della sezione avvio rapido.



## <a name="next-steps"></a>Passaggi successivi
In questa Guida introduttiva è stato abilitato il Centro sicurezza di Azure. Il passaggio successivo consiste nell'abilitare Azure Defender per la gestione unificata della sicurezza e la protezione dalle minacce nei carichi di lavoro cloud ibridi.

> [!div class="nextstepaction"]
> [Guida introduttiva: abilitare Azure Defender](enable-azure-defender.md)