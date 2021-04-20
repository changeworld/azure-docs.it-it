---
title: Usare i widget della community nel portale per sviluppatori
titleSuffix: Azure API Management
description: Informazioni sui widget della community per il API Management per sviluppatori e su come inserire e usare i widget nel codice.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: de6160aa2e556297287ae9e9ecd58a93e54d863f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741677"
---
# <a name="use-community-widgets-in-the-developer-portal"></a>Usare i widget della community nel portale per sviluppatori

Tutti gli sviluppatori posizionano i widget della community nella cartella del `/community/widgets/` [repository GitHub](https://github.com/Azure/api-management-developer-portal)API Management portale per sviluppatori. Ognuna è stata accettata dal team del portale per sviluppatori. È possibile usare i widget inseriscendoli nella [versione self-hosted](developer-portal-self-host.md) del portale. La versione gestita del portale per sviluppatori attualmente non supporta i widget della community.

> [!NOTE]
> Il team del portale per sviluppatori esamina accuratamente i widget con contributi e le relative dipendenze. Tuttavia, il team non può garantire che sia sicuro caricare i widget. Usare il proprio giudizio quando si decide di usare un widget contributo dalla community. Per informazioni sulle misure preventive, vedere le linee guida per i contributi dei [widget.](developer-portal-widget-contribution-guidelines.md#contribution-guidelines)

## <a name="inject-and-use-external-widgets"></a>Inserire e usare widget esterni

1. Configurare un ambiente [locale per](developer-portal-self-host.md#step-1-set-up-local-environment) la versione più recente del portale per sviluppatori.

1. Passare alla cartella del widget nella `/community/widgets` directory . Leggere la descrizione del widget nel `readme.md` file .

1. Registrare il widget nei moduli del portale:

    1. `src/apim.design.module.ts` : modulo che registra le dipendenze in fase di progettazione.
    
        ```typescript
        import { WidgetNameDesignModule } from "../community/widgets/<widget-name>/widget.design.module";
    
        ...
    
            injector.bindModule(new WidgetNameDesignModule());
        ```
    
    1. `src/apim.publish.module.ts` : modulo che registra le dipendenze in fase di pubblicazione.
    
        ```typescript
        import { WidgetNamePublishModule } from "../community/widgets/<widget-name>/widget.publish.module";
    
        ...
    
            injector.bindModule(new WidgetNamePublishModule());
        ```
    
    1. `src/apim.runtime.module.ts` : modulo che registra le dipendenze di run-time.
    
        ```typescript
        import { WidgetNameRuntimeModule } from "../community/widgets/<widget-name>/widget.runtime.module";
    
        ...
    
            injector.bindModule(new WidgetNameRuntimeModule());
        ```

1. Controllare se il widget contiene un `npm_dependencies` file.

1. In tal caso, copiare i comandi dal file ed eseguirli nella directory principale del repository.

    In questo modo verranno installate le dipendenze del widget.

1. Eseguire `npm start`.

È possibile visualizzare il widget nella **categoria Community** nel selettore del widget.

:::image type="content" source="media/developer-portal-use-community-widgets/widget-selector.png" alt-text="Screenshot del selettore di widget":::


## <a name="next-steps"></a>Passaggi successivi


Altre informazioni sul portale per sviluppatori:

- [Panoramica del portale per sviluppatori di Gestione API](api-management-howto-developer-portal.md)

- [Widget di Contribute](developer-portal-widget-contribution-guidelines.md)
