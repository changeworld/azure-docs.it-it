---
title: Applicazione di esempio per l'accesso ai dati di analisi del Marketplace commerciale
description: Usare l'applicazione di esempio per creare un'applicazione di analisi del Marketplace commerciale personalizzata.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8fe2301c4d4ed2a582774c65d5dbe68bab416aa3
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584079"
---
# <a name="sample-application-for-accessing-commercial-marketplace-analytics-data"></a>Applicazione di esempio per l'accesso ai dati di analisi del Marketplace commerciale

Le applicazioni di esempio vengono create in linguaggi C# e JAVA e sono disponibili in [GitHub](https://github.com/partneranalytics).

- [Applicazione di esempio C#](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV)
- [Applicazione di esempio JAVA](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV_Java)

È possibile scegliere di prendere spunto dall'applicazione di esempio e creare un'applicazione personalizzata in qualsiasi linguaggio.

L'applicazione di esempio raggiunge gli obiettivi seguenti:

- Genera un token di Azure Active Directory (Azure AD).
- Ottiene i set di impostazioni disponibili.
- Crea query definite dall'utente.
- Ottiene le query di sistema e definite dall'utente.
- Pianifica un report.

L'applicazione di esempio non copre il metodo di chiamata delle API per altre funzionalità. Tuttavia, il processo di chiamata di altre API rimane uguale a quello descritto in precedenza.

## <a name="how-to-run-the-application"></a>Come eseguire l'applicazione

1. Clonare il repository in un sistema locale usando questo comando:

    `git clone https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git`

    > [!NOTE]
    > Per altre istruzioni, vedere il `ProgrammaticExportSampleAppISV/README.md` file nel [repository](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git)github.

1. Per eseguire rapidamente l'app, aggiornare l'ID client e il segreto client nell' **appsettings.Development.js**

    :::image type="content" source="./media/analytics-programmatic-access/appsettings-development-json.png" alt-text="Viene illustrato un frammento di appsettings.Development.jssu file.":::

Quando si esegue l'app, viene avviato un server Web locale e viene aperta una pagina ( `https://localhost:44365/ProgrammaticExportSampleApp/sample` ).

[![Viene illustrata la pagina pianificazione del report.](./media/analytics-programmatic-access/schedule-report.png)](./media/analytics-programmatic-access/schedule-report.png#lightbox)

Questa pagina effettuerà chiamate API al server Web in esecuzione nel computer locale, che a sua volta effettuerà le chiamate API di accesso a livello di codice effettive.

## <a name="code-snippets"></a>Frammenti di codice

La struttura di base del codice C# per le chiamate API di accesso programmatico è la seguente:

:::image type="content" source="./media/analytics-programmatic-access/code-snippets.png" alt-text="Screenshot delle chiamate API.":::

## <a name="next-steps"></a>Passaggi successivi

- [API per l'accesso ai dati di analisi del Marketplace commerciale](analytics-available-apis.md)
