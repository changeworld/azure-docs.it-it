---
title: Come contribuire ai widget per il portale per sviluppatori
titleSuffix: Azure API Management
description: Informazioni sulle linee guida consigliate da seguire quando si contribuisce a un widget nel repository del API Management per sviluppatori.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c4d3ed2aeaac57f721d23d7c7aa1c70ef14e4294
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741705"
---
# <a name="how-to-contribute-widgets-to-the-api-management-developer-portal"></a>Come contribuire ai widget nel portale per API Management per sviluppatori

Se si desidera aggiungere un widget al repository [GitHub](https://github.com/Azure/api-management-developer-portal)del portale per sviluppatori API Management, seguire questa procedura in tre passaggi:

1. Creare una fork del repository.

1. Implementare il widget.

1. Aprire una richiesta pull per includere il widget nel repository ufficiale.

Il widget erediterà la licenza del repository. Sarà disponibile per [l'installazione con consenso esplicito](developer-portal-use-community-widgets.md) nella versione self-hosted del portale. Il team del portale per sviluppatori può decidere di includerlo anche nella versione gestita del portale.

Per un esempio di come sviluppare [un widget](developer-portal-implement-widgets.md) personalizzato, vedere l'esercitazione sull'implementazione del widget.

## <a name="contribution-guidelines"></a>Linee guida per i contributi

Queste linee guida hanno lo scopo di garantire la sicurezza e la privacy dei clienti e dei visitatori dei portali. Seguire queste linee guida per assicurarsi che il contributo sia accettato:

1. Inserire il widget nella `community/widgets/<your-widget-name>` cartella .

1. Il nome del widget deve essere minuscolo e alfanumerico con trattini che separano le parole. Ad esempio: `my-new-widget`.

1. La cartella deve contenere uno screenshot del widget in un portale pubblicato.

1. La cartella deve contenere `readme.md` un file che segue il modello del `/scaffolds/widget/readme.md` file.

1. La cartella può contenere `npm_dependencies` un file con i comandi npm per installare o gestire le dipendenze del widget.

    Specificare in modo esplicito la versione di ogni dipendenza. Ad esempio:  

    ```console
    npm install azure-storage@2.10.3 axios@0.19.1
    ```

    Il widget deve richiedere dipendenze minime. Ogni dipendenza verrà esaminata attentamente dai revisori. In particolare, la logica di base del widget deve essere open source nella cartella del widget. Non eseguire il wrapping in un pacchetto npm.

1. Le modifiche ai file all'esterno della cartella del widget non sono consentite come parte del contributo di un widget. Ciò include, ma non è limitato, il `/package.json` file.

1. Non è consentito inserire script di rilevamento o inviare dati creati dal cliente a servizi personalizzati.

    > [!NOTE]
    > È possibile raccogliere dati creati dal cliente solo tramite `Logger` l'interfaccia .

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui contributi, vedere il [repository GitHub](https://github.com/Azure/api-management-developer-portal/)API Management portale per sviluppatori.

- Vedere [Implementare i widget](developer-portal-implement-widgets.md) per informazioni su come sviluppare un widget personalizzato, passo dopo passo.

- Vedere [Usare i widget della](developer-portal-use-community-widgets.md) community per informazioni su come usare i widget con contributi della community.