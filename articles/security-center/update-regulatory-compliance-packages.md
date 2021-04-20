---
title: Uso del dashboard di conformità alle normative in Centro sicurezza di Azure
description: Informazioni su come aggiungere e rimuovere standard normativi dal dashboard di conformità alle normative nel Centro sicurezza
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
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: dab6b504fe026324251b7284fc3abdb52bb9911a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738955"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Personalizzare il set di standard nel dashboard di conformità alle normative

Il Centro sicurezza di Azure confronta continuamente la configurazione delle risorse con i requisiti di benchmark, normative e standard del settore. Il **dashboard di conformità** alle normative fornisce informazioni dettagliate sul proprio stato di conformità in base al modo in cui vengono soddisfatti requisiti di conformità specifici.


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Come vengono rappresentati gli standard di conformità alle normative nel Centro sicurezza?

Gli standard di settore, gli standard normativi e i benchmark sono rappresentati nel dashboard di conformità alle normative del Centro sicurezza. Ogni standard è un'iniziativa definita in Criteri di Azure.

Per visualizzare i dati di conformità mappati come valutazioni nel dashboard, aggiungere uno standard di conformità al gruppo di gestione o alla sottoscrizione dalla pagina **Criteri di** sicurezza. Per altre informazioni sulle Criteri di Azure e sulle iniziative, vedere [Uso dei criteri di sicurezza.](tutorial-security-policy.md)

Dopo aver assegnato uno standard o un benchmark all'ambito selezionato, lo standard viene visualizzato nel dashboard di conformità alle normative con tutti i dati di conformità associati mappati come valutazioni. È anche possibile scaricare i report di riepilogo per qualsiasi standard assegnato.

Microsoft tiene traccia degli standard normativi e migliora automaticamente la copertura in alcuni pacchetti nel tempo. Quando Microsoft rilascia nuovo contenuto per l'iniziativa, verrà visualizzato automaticamente nel dashboard come nuovi criteri mappati ai controlli nello standard.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Quali standard di conformità alle normative sono disponibili nel Centro sicurezza?

Per impostazione predefinita, a ogni sottoscrizione è **assegnato Azure Security Benchmark.** Si tratta delle linee guida specifiche di Azure, scritte da Microsoft, per le procedure consigliate per la sicurezza e la conformità basate su framework di conformità comuni. [Altre informazioni su Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction).

È anche possibile aggiungere standard come:

- NIST SP 800-53 R4
- SWIFT CSP CSCF-v2020
- UK Official e UK NHS
- Canada Federal PBMM
- Azure CIS 1.1.0

Gli standard vengono aggiunti al dashboard non appena diventano disponibili.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Aggiungere uno standard normativo al dashboard

La procedura seguente illustra come aggiungere un pacchetto per monitorare la conformità a uno degli standard normativi supportati.

> [!NOTE]
> Per aggiungere standard al dashboard, la sottoscrizione deve avere Azure Defender abilitata. Inoltre, solo gli utenti proprietari o collaboratori ai criteri hanno le autorizzazioni necessarie per aggiungere gli standard di conformità. 

1. Nella barra laterale del Centro sicurezza selezionare **Conformità con le normative** per aprire il dashboard corrispondente. Qui è possibile visualizzare gli standard di conformità attualmente assegnati alle sottoscrizioni selezionate.   

1. Nella parte superiore della pagina selezionare **Gestisci i criteri di conformità**. Verrà visualizzata la pagina Gestione dei criteri.

1. Selezionare la sottoscrizione o il gruppo di gestione per cui gestire la postura di conformità alle normative. 

    > [!TIP]
    > È consigliabile selezionare l'ambito più alto per cui è applicabile lo standard, in modo che i dati di conformità vengano aggregati e monitorati per tutte le risorse annidate. 

1. Per aggiungere gli standard pertinenti per l'organizzazione, fare clic su **Aggiungi altri standard**. 

1. Nella pagina **Aggiungi standard di conformità alle** normative è possibile cercare uno degli standard disponibili, tra cui:

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF v2020**
    - **UKO e UK NHS**
    - **Canada Federal PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.1.0**
    
    ![Aggiunta di standard normativi Centro sicurezza di Azure dashboard di conformità alle normative](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Selezionare **Aggiungi** e immettere tutti i dettagli necessari per l'iniziativa specifica, ad esempio ambito, parametri e correzione.

1. Nella barra laterale del Centro sicurezza selezionare di nuovo **Conformità con le normative** per tornare nel dashboard corrispondente.

    Il nuovo standard verrà visualizzato nell'elenco degli standard normativi e di settore. 

    > [!NOTE]
    > La visualizzazione di uno standard appena aggiunto nel dashboard di conformità potrebbe richiedere alcune ore.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard Conformità con le normative" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>Rimuovere uno standard dal dashboard

Se uno degli standard normativi forniti non è pertinente per l'organizzazione, è un semplice processo per rimuoverli dall'interfaccia utente. In questo modo è possibile personalizzare ulteriormente il dashboard di conformità alle normative e concentrarsi solo sugli standard applicabili.

Per rimuovere uno standard:

1. Dal menu del Centro sicurezza selezionare **Criteri di sicurezza.**

1. Selezionare la sottoscrizione pertinente da cui si vuole rimuovere uno standard.

    > [!NOTE]
    > È possibile rimuovere uno standard da una sottoscrizione, ma non da un gruppo di gestione. 

    Verrà visualizzata la pagina dei criteri di sicurezza. Per la sottoscrizione selezionata, vengono visualizzati i criteri predefiniti, gli standard di settore e normativi e tutte le iniziative personalizzate create.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Rimozione di uno standard normativo dal dashboard di conformità alle normative in Centro sicurezza di Azure":::

1. Per lo standard da rimuovere, selezionare **Disabilita**. Viene visualizzata una finestra di conferma.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Confermare che si vuole rimuovere lo standard normativo selezionato":::

1. Selezionare **Sì**. Lo standard verrà rimosso. 


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come aggiungere **standard di conformità per** monitorare la conformità con gli standard normativi e di settore.

Per il materiale correlato, vedere le pagine seguenti:

- [Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Dashboard di conformità alle normative del](security-center-compliance-dashboard.md) Centro sicurezza: informazioni su come tenere traccia ed esportare i dati di conformità alle normative con il Centro sicurezza e gli strumenti esterni
- [Uso dei criteri di sicurezza](tutorial-security-policy.md)