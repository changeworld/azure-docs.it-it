---
title: Usare i filtri funzionalità per abilitare i flag delle funzionalità condizionali
titleSuffix: Azure App Configuration
description: Informazioni su come usare i filtri funzionalità per abilitare i flag delle funzionalità condizionali
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 39455c4bc193cce036bd169c702b5c020d53d2f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98602246"
---
# <a name="use-feature-filters-to-enable-conditional-feature-flags"></a>Usare i filtri funzionalità per abilitare i flag delle funzionalità condizionali

I flag funzionalità consentono di attivare o disattivare la funzionalità nell'applicazione. Un flag di funzionalità semplice può essere acceso o disattivato. L'applicazione si comporta sempre allo stesso modo. Ad esempio, è possibile implementare una nuova funzionalità dietro un flag funzionalità. Quando il flag funzionalità è abilitato, tutti gli utenti visualizzano la nuova funzionalità. La disabilitazione del flag funzionalità consente di nascondere la nuova funzionalità.

Al contrario, un _flag della funzionalità condizionale_ consente l'abilitazione o la disabilitazione dinamica del flag funzionalità. L'applicazione può comportarsi in modo diverso, a seconda dei criteri del flag funzionalità. Si supponga di voler visualizzare la nuova funzionalità per un piccolo subset di utenti. Un flag di funzionalità condizionale consente di abilitare il flag funzionalità per alcuni utenti e di disabilitarlo per altri utenti. I _filtri delle funzionalità_ determinano lo stato del flag di funzionalità ogni volta che viene valutato.

La `Microsoft.FeatureManagement` libreria include tre filtri funzionalità:

- `PercentageFilter` Abilita il flag di funzionalità in base a una percentuale.
- `TimeWindowFilter` Abilita il flag di funzionalità durante un intervallo di tempo specificato.
- `TargetingFilter` Abilita il flag funzionalità per gli utenti e i gruppi specificati.

È anche possibile creare un filtro funzionalità personalizzato che implementi l' [interfaccia Microsoft. FeatureManagement. IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Registrazione di un filtro funzionalità

È possibile registrare un filtro funzionalità chiamando il `AddFeatureFilter` metodo, specificando il nome del tipo del filtro funzionalità desiderato. Il codice seguente, ad esempio, registra `PercentageFilter` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Configurazione di un filtro funzionalità nella configurazione app Azure

Per alcuni filtri funzionalità sono disponibili impostazioni aggiuntive. Ad esempio, `PercentageFilter` attiva una funzionalità basata su una percentuale. Dispone di un'impostazione che definisce la percentuale da usare.

È possibile configurare queste impostazioni per i flag di funzionalità definiti nella configurazione app Azure. Ad esempio, seguire questa procedura per `PercentageFilter` abilitare il flag funzionalità per il 50% delle richieste a un'app Web:

1. Per creare un'app Web con un flag funzionalità, seguire le istruzioni riportate nella [Guida introduttiva: aggiungere flag funzionalità a un'app ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) .

1. Nella portale di Azure passare all'archivio di configurazione e fare clic su **Gestione funzionalità**.

1. Fare clic sul menu di scelta rapida per il flag della funzionalità *beta* creato nella Guida introduttiva. Fare clic su **Modifica**.

    > [!div class="mx-imgBorder"]
    > ![Modifica flag funzionalità beta](./media/edit-beta-feature-flag.png)

1. Nella schermata di **modifica** selezionare la casella di controllo **Abilita flag funzionalità** se non è già abilitata. Selezionare quindi la casella di controllo **Usa filtro funzionalità** e selezionare **personalizzata**. 

1. Nel campo **nome** selezionare *Microsoft. percentuale*.

    > [!div class="mx-imgBorder"]
    > ![Aggiungi filtro funzionalità](./media/feature-flag-add-filter.png)

1. Fare clic sul menu di scelta rapida accanto al nome del filtro funzionalità. Fare clic su **modifica parametri filtro**.

    > [!div class="mx-imgBorder"]
    > ![Modifica parametri filtro funzionalità](./media/feature-flags-edit-filter-parameters.png)

1. Immettere un **nome** e *un* **valore** di 50. Il campo **valore** indica la percentuale di richieste per le quali abilitare il filtro funzionalità.

    > [!div class="mx-imgBorder"]
    > ![Imposta parametri filtro funzionalità](./media/feature-flag-set-filter-parameters.png)

1. Fare clic su **applica** per tornare alla schermata **modifica flag funzionalità** . Quindi fare di nuovo clic su **applica** per salvare le impostazioni del flag funzionalità.

1. Nella pagina **Gestione funzionalità** il flag funzionalità dispone ora di un valore di **filtro funzionalità** *personalizzato*. 

    > [!div class="mx-imgBorder"]
    > ![Flag funzionalità elencato con un valore di filtro funzionalità "Custom"](./media/feature-flag-filter-custom.png)

## <a name="feature-filters-in-action"></a>Filtri delle funzionalità in azione

Per visualizzare gli effetti di questo flag di funzionalità, avviare l'applicazione e fare clic sul pulsante **Aggiorna** nel browser più volte. Si noterà che l'elemento *beta* viene visualizzato sulla barra degli strumenti circa il 50% del tempo. Il resto del tempo è nascosto perché `PercentageFilter` Disattiva la funzionalità *beta* per un subset di richieste. Il video seguente mostra questo comportamento in azione.

> [!div class="mx-imgBorder"]
> ![TargetingFilter in azione](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Abilitare l'implementazione a fasi di funzionalità per destinatari specifici](./howto-targetingfilter-aspnet-core.md)
