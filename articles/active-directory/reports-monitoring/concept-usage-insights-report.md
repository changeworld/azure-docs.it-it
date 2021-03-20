---
title: Report di utilizzo e informazioni dettagliate | Microsoft Docs
description: Introduzione ai report di utilizzo e informazioni dettagliate nel portale Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 54bce5e839786862a6dac9aeb685dd364547a09a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685030"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Report sull'utilizzo e le informazioni dettagliate nel portale Azure Active Directory

Con il report utilizzo e informazioni dettagliate è possibile ottenere una visualizzazione incentrata sull'applicazione dei dati di accesso. È possibile trovare risposte alle domande seguenti:

*   Quali sono le applicazioni principali usate nell'organizzazione?
*   Quali applicazioni hanno gli accessi più non riusciti? 
*   Quali sono gli errori di accesso principali per ogni applicazione?

## <a name="prerequisites"></a>Prerequisiti 

Per accedere ai dati dal report utilizzo e informazioni dettagliate, è necessario:

* Un tenant di Azure AD
* Una licenza di Azure AD Premium (P1/P2) per visualizzare i dati di accesso
* Utente nei ruoli amministratore globale, amministratore della sicurezza, lettore sicurezza o lettore report. Inoltre, qualsiasi utente (non amministratore) può accedere ai propri accessi. 

## <a name="access-the-usage-and-insights-report"></a>Accedere al report utilizzo e informazioni dettagliate

1. Passare al [portale di Azure](https://portal.azure.com).
2. Selezionare la directory giusta, quindi selezionare **Azure Active Directory** e scegliere **applicazioni aziendali**.
3. Nella sezione **Activity** selezionare **Usage & Insights** per aprire il report. 

![Screenshot mostra l'utilizzo & Insights selezionato dalla sezione Activity.](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Utilizzare il report

Il report utilizzo e informazioni dettagliate Mostra l'elenco di applicazioni con uno o più tentativi di accesso e consente di eseguire l'ordinamento in base al numero di accessi riusciti, gli accessi non riusciti e la percentuale di successo.

Facendo clic su **carica altro** nella parte inferiore dell'elenco è possibile visualizzare altre applicazioni nella pagina. È possibile selezionare l'intervallo di date per visualizzare tutte le applicazioni utilizzate nell'intervallo.

![Screenshot mostra informazioni sull'utilizzo & per le attività dell'applicazione in cui è possibile selezionare un intervallo e visualizzare l'attività di accesso per le diverse app.](./media/concept-usage-insights-report/usage-and-insights-report.png)

È inoltre possibile impostare lo stato attivo su un'applicazione specifica. Selezionare **Visualizza attività di accesso** per visualizzare l'attività di accesso nel tempo per l'applicazione e gli errori principali.  

Quando si seleziona un giorno nel grafico sull'utilizzo dell'applicazione, si ottiene un elenco dettagliato delle attività di accesso per l'applicazione.  

:::image type="content" source="./media/concept-usage-insights-report/usage-and-insights-application-report.png" alt-text="Screenshot mostra informazioni sull'utilizzo & per un'applicazione specifica in cui è possibile visualizzare un grafico per l'attività di accesso.":::

## <a name="next-steps"></a>Passaggi successivi

* [Report sugli accessi](concept-sign-ins.md)
