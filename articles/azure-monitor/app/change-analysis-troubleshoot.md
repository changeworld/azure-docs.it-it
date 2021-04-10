---
title: Risolvere i problemi relativi all'analisi delle modifiche dell'applicazione
description: Informazioni su come risolvere i problemi relativi all'analisi delle modifiche dell'applicazione.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 7200978ce31a47f7bd0d023cecf359b10a1c96de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100521086"
---
# <a name="troubleshoot-application-change-analysis-preview"></a>Risolvere i problemi di analisi delle modifiche dell'applicazione (anteprima)

## <a name="having-trouble-registering-microsoft-change-analysis-resource-provider-from-change-history-tab"></a>Problemi di registrazione di Microsoft. Modificare il provider di risorse di analisi dalla scheda cronologia modifiche

Se è la prima volta che si visualizza la cronologia delle modifiche dopo l'integrazione con l'analisi delle modifiche dell'applicazione, si noterà che il provider di risorse **Microsoft. ChangeAnalysis** viene registrato automaticamente. In rari casi potrebbe non riuscire per i motivi seguenti:

- **Non si dispone delle autorizzazioni sufficienti per registrare il provider di risorse Microsoft. ChangeAnalysis**. Questo messaggio di errore indica che il ruolo nella sottoscrizione corrente non ha l'ambito **Microsoft. support/register/Action** associato. Questo problema può verificarsi se non si è il proprietario di una sottoscrizione e si hanno le autorizzazioni di accesso condiviso tramite un collaboratore, ovvero visualizzare l'accesso a un gruppo di risorse. Per risolvere il problema, è possibile contattare il proprietario della sottoscrizione per registrare il provider di risorse **Microsoft. ChangeAnalysis** . Questa operazione può essere eseguita in portale di Azure tramite **sottoscrizioni | Provider di risorse** e ricerca ```Microsoft.ChangeAnalysis``` e registrazione nell'interfaccia utente o tramite Azure PowerShell o l'interfaccia della riga di comando di Azure.

    Registrare il provider di risorse tramite PowerShell:
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Non è stato possibile registrare il provider di risorse Microsoft. ChangeAnalysis**. Questo messaggio indica che si è verificato un errore immediatamente perché l'interfaccia utente ha inviato una richiesta di registrazione del provider di risorse e non è correlata al problema di autorizzazione. Probabilmente potrebbe trattarsi di un problema temporaneo di connettività Internet. Provare ad aggiornare la pagina e a controllare la connessione a Internet. Se l'errore è permanente, contattare changeanalysishelp@microsoft.com

- **Questa operazione richiede più tempo del previsto**. Questo messaggio indica che la registrazione richiede più di 2 minuti. Si tratta di un'operazione insolita, ma non implica necessariamente un errore. È possibile passare alle **sottoscrizioni | Provider di risorse** per verificare lo stato di registrazione del provider di risorse **Microsoft. ChangeAnalysis** . È possibile provare a usare l'interfaccia utente per annullare la registrazione, ripetere la registrazione o aggiornare per verificare se è utile. Se il problema persiste, contattare il changeanalysishelp@microsoft.com supporto tecnico.
    ![Risoluzione dei problemi di registrazione RP troppo lungo](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![Screenshot dello strumento diagnostica e Risolvi i problemi per una macchina virtuale con strumenti di risoluzione dei problemi selezionati.](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Screenshot del riquadro per lo strumento analizza la risoluzione dei problemi delle modifiche recenti per una macchina virtuale.](./media/change-analysis/analyze-recent-changes.png)

## <a name="azure-lighthouse-subscription-is-not-supported"></a>La sottoscrizione di Azure Lighthouse non è supportata

- Non è **stato possibile eseguire una query sul provider di risorse Microsoft. ChangeAnalysis** con messaggio *la sottoscrizione di Azure Lighthouse non è supportata. le modifiche sono disponibili solo nel tenant principale della sottoscrizione*. Attualmente esiste una limitazione per la registrazione del provider di risorse di analisi delle modifiche tramite la sottoscrizione di Azure Lighthouse per gli utenti che non si trova nel tenant Home. Microsoft sta lavorando per ovviare a questa limitazione. Se si tratta di un problema di blocco, esiste una soluzione alternativa che prevede la creazione di un'entità servizio e l'assegnazione esplicita del ruolo per consentire l'accesso.  Per ulteriori informazioni, contattare il contatto changeanalysishelp@microsoft.com .

## <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>Si è verificato un errore durante il recupero delle modifiche. Aggiornare questa pagina o tornare più tardi per visualizzare le modifiche

Questo è il messaggio di errore generale presentato dal servizio di analisi delle modifiche dell'applicazione quando non è stato possibile caricare le modifiche. Di seguito sono riportate alcune cause note:

- Errore di connettività Internet dal dispositivo client
- Il servizio di analisi delle modifiche temporaneamente non disponibile aggiorna la pagina dopo alcuni minuti in genere corregge questo problema. Se l'errore è permanente, contattare changeanalysishelp@micorosoft.com

## <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>Le autorizzazioni disponibili non sono sufficienti per visualizzare alcune modifiche. Contattare l'amministratore della sottoscrizione di Azure

Questo è il messaggio di errore generale non autorizzato, che indica che l'utente corrente non dispone di autorizzazioni sufficienti per visualizzare la modifica. Per visualizzare le modifiche dell'infrastruttura restituite da Azure Resource Graph e Azure Resource Manager è necessario almeno l'accesso in lettura per la risorsa. Per le modifiche apportate all'app Web in-Guest e le modifiche alla configurazione, è necessario almeno il ruolo Collaboratore.

## <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Non è stato possibile registrare il provider di risorse Microsoft. ChangeAnalysis

Questo messaggio indica che si è verificato un errore immediatamente perché l'interfaccia utente ha inviato una richiesta di registrazione del provider di risorse e non è correlata al problema di autorizzazione. Probabilmente potrebbe trattarsi di un problema temporaneo di connettività Internet. Provare ad aggiornare la pagina e a controllare la connessione a Internet. Se l'errore è permanente, contattare changeanalysishelp@microsoft.com

## <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>Non si dispone delle autorizzazioni sufficienti per registrare il provider di risorse Microsoft. ChangeAnalysis. Contattare l'amministratore della sottoscrizione di Azure

Questo messaggio di errore indica che il ruolo nella sottoscrizione corrente non ha l'ambito **Microsoft. support/register/Action** associato. Questo problema può verificarsi se non si è il proprietario di una sottoscrizione e si hanno le autorizzazioni di accesso condiviso tramite un collaboratore, ovvero visualizzare l'accesso a un gruppo di risorse. Per risolvere il problema, è possibile contattare il proprietario della sottoscrizione per registrare il provider di risorse **Microsoft. ChangeAnalysis** . Questa operazione può essere eseguita in portale di Azure tramite **sottoscrizioni | Provider di risorse** e ricerca ```Microsoft.ChangeAnalysis``` e registrazione nell'interfaccia utente o tramite Azure PowerShell o l'interfaccia della riga di comando di Azure.

Registrare il provider di risorse tramite PowerShell:

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più su [Azure Resource Graph](../../governance/resource-graph/overview.md), che consente di migliorare l'analisi del risparmio energia.