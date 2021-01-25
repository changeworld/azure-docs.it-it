---
title: Uso del dashboard conformità normativa nel centro sicurezza di Azure
description: Informazioni su come aggiungere e rimuovere gli standard normativi dal dashboard conformità normativa nel centro sicurezza
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 6fb2e5c0193bc4e66f8fb4215732a69c43731146
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756628"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Personalizzazione del set di standard nel dashboard di conformità alle normative

Il Centro sicurezza di Azure confronta continuamente la configurazione delle risorse con i requisiti di standard del settore, normative e benchmark. Il **Dashboard conformità normativa** fornisce informazioni approfondite sul comportamento di conformità in base al modo in cui si soddisfano i requisiti di conformità specifici.


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>In che modo vengono rappresentati gli standard di conformità normativa nel centro sicurezza?

Gli standard di settore, gli standard normativi e i benchmark sono rappresentati nel dashboard di conformità alle normative del Centro sicurezza. Ogni standard è un'iniziativa definita in criteri di Azure.

Per visualizzare i dati di conformità mappati come valutazioni nel dashboard, aggiungere uno standard di conformità al gruppo di gestione o alla sottoscrizione dall'interno della pagina dei **criteri di sicurezza** . Per altre informazioni su criteri e iniziative di Azure, vedere [utilizzo dei criteri di sicurezza](tutorial-security-policy.md).

Quando è stato assegnato uno standard o un benchmark per l'ambito selezionato, lo standard viene visualizzato nel dashboard conformità normativa con tutti i dati di conformità associati mappati come valutazioni. È anche possibile scaricare i report di riepilogo per uno degli standard assegnati.

Microsoft tiene traccia degli standard normativi e migliora automaticamente la copertura in alcuni pacchetti nel tempo. Quando Microsoft rilascia nuovo contenuto per l'iniziativa, viene visualizzato automaticamente nel dashboard Man seconda che i nuovi criteri siano mappati ai controlli nello standard.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Quali standard di conformità normativa sono disponibili nel centro sicurezza?

Per impostazione predefinita, a ogni sottoscrizione è assegnato il **benchmark di sicurezza di Azure** . Queste sono le linee guida specifiche di Azure per le procedure di sicurezza e conformità basate sui Framework di conformità comuni. [Altre informazioni su Azure Security Benchmark](../security/benchmarks/introduction.md).

È anche possibile aggiungere standard, ad esempio:

- NIST SP 800-53 R4
- SWIFT CSP CSCF-v2020
- Ufficiale del Regno Unito e NHS del Regno Unito
- Canada Federal PBMM
- Azure CIS 1.1.0

Gli standard vengono aggiunti al dashboard non appena diventano disponibili.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Aggiungere uno standard normativo al dashboard

La procedura seguente illustra come aggiungere un pacchetto per monitorare la conformità a uno degli standard normativi supportati.

> [!NOTE]
> Per aggiungere standard al dashboard, è necessario abilitare Azure Defender per la sottoscrizione. Inoltre, solo gli utenti proprietario o collaboratore di criteri hanno le autorizzazioni necessarie per aggiungere gli standard di conformità. 

1. Nella barra laterale del Centro sicurezza selezionare **Conformità con le normative** per aprire il dashboard corrispondente. Qui è possibile visualizzare gli standard di conformità attualmente assegnati alle sottoscrizioni selezionate.   

1. Nella parte superiore della pagina selezionare **Gestisci i criteri di conformità**. Verrà visualizzata la pagina Gestione dei criteri.

1. Selezionare la sottoscrizione o il gruppo di gestione per cui gestire la postura di conformità alle normative. 

    > [!TIP]
    > È consigliabile selezionare l'ambito più alto per cui è applicabile lo standard, in modo che i dati di conformità vengano aggregati e monitorati per tutte le risorse annidate. 

1. Per aggiungere gli standard pertinenti per l'organizzazione, fare clic su **Aggiungi altri standard**. 

1. Nella pagina **Aggiungi standard di conformità normativa** è possibile cercare uno degli standard disponibili, tra cui:

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF-v2020**
    - **UKO e UK NHS**
    - **Canada PBMM**
    
    ![Aggiunta di standard normativi al dashboard di conformità alle normative del Centro sicurezza di Azure](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Selezionare **Aggiungi** e immettere tutti i dettagli necessari per l'iniziativa specifica, ad esempio ambito, parametri e correzione.

1. Nella barra laterale del Centro sicurezza selezionare di nuovo **Conformità con le normative** per tornare nel dashboard corrispondente.

    Il nuovo standard verrà visualizzato nell'elenco degli standard normativi e di settore. 

    > [!NOTE]
    > La visualizzazione di uno standard appena aggiunto nel dashboard di conformità potrebbe richiedere alcune ore.

    [![Dashboard di conformità alle normative che mostra il vecchio e il nuovo standard Azure CIS](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)


## <a name="removing-a-standard-from-your-dashboard"></a>Rimozione di uno standard dal dashboard

Se uno degli standard normativi forniti non è pertinente per l'organizzazione, si tratta di un processo semplice per rimuoverli dall'interfaccia utente. In questo modo è possibile personalizzare ulteriormente il dashboard di conformità alle normative e concentrarsi solo sugli standard applicabili.

Per rimuovere uno standard:

1. Dal menu del Centro sicurezza selezionare **criteri di sicurezza**.

1. Selezionare la sottoscrizione pertinente da cui si desidera rimuovere uno standard.

    > [!NOTE]
    > È possibile rimuovere uno standard da una sottoscrizione, ma non da un gruppo di gestione. 

    Verrà visualizzata la pagina Criteri di sicurezza. Per la sottoscrizione selezionata, vengono visualizzati i criteri predefiniti, gli standard di settore e normativi e tutte le iniziative personalizzate create.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Rimozione di uno standard normativo dal dashboard di conformità normativa nel centro sicurezza di Azure":::

1. Per lo standard che si desidera rimuovere, selezionare **Disabilita**. Viene visualizzata una finestra di conferma.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Confermare che si desidera effettivamente rimuovere lo standard normativo selezionato":::

1. Selezionare **Sì**. Lo standard verrà rimosso. 


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come **aggiungere gli standard di conformità** per monitorare la conformità agli standard di settore e normativi.

Per materiale correlato, vedere le pagine seguenti:

- [Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Dashboard di conformità alle normative del Centro sicurezza](security-center-compliance-dashboard.md)
- [Uso dei criteri di sicurezza](tutorial-security-policy.md)