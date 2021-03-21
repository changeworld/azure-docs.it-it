---
title: Creare una risorsa per Translator
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come creare una risorsa di conversione di servizi cognitivi di Azure e come ottenere una chiave di sottoscrizione e un URL dell'endpoint.
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: a0d8532d19aff41bc5e7defb3b58462e81018749
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712930"
---
# <a name="create-a-translator-resource"></a>Creare una risorsa per Translator

In questo articolo si apprenderà come creare una risorsa di conversione nella portale di Azure. [Azure Translator](translator-info-overview.md) è un servizio di traduzione automatica basato sul cloud che fa parte della famiglia di [Servizi cognitivi di Azure](../what-are-cognitive-services.md) delle API REST. Le risorse di Azure sono istanze dei servizi creati dall'utente. Tutte le richieste API ai servizi di Azure richiedono un URL dell' **endpoint** e una **chiave di sottoscrizione** di sola lettura per l'autenticazione dell'accesso.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario un [**account Azure**](https://azure.microsoft.com/free/cognitive-services/)attivo.  Se non si dispone di un abbonamento, è possibile [**creare una sottoscrizione di 12 mesi gratuita**](https://azure.microsoft.com/free/).

## <a name="translator-resource-types"></a>Tipi di risorse di conversione

È possibile accedere al servizio di conversione tramite due tipi di risorse diversi:

* I tipi di risorse a **servizio singolo** abilitano l'accesso a una singola chiave API del servizio e a un endpoint.  

* I tipi di risorse **Multi-Service** consentono l'accesso a più servizi cognitivi usando una singola chiave API e un endpoint. La risorsa Servizi cognitivi è attualmente disponibile per i servizi seguenti:
  * Lingua ([Translator](../translator/translator-info-overview.md), [Language Understanding (LUIS)](../luis/what-is-luis.md), [analisi del testo](../text-analytics/overview.md))  
  * Visione ([visione artificiale](../computer-vision/overview.md)), ([Face](../face/overview.md))  
  * Decisione ([content moderator](../content-moderator/overview.md))  

## <a name="create-your-resource"></a>Creare la risorsa

* Passare direttamente alla pagina [**Crea conversione**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) nella portale di Azure per completare i dettagli del progetto.

* Passare direttamente alla pagina [**Crea servizi cognitivi**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) nella portale di Azure per completare i dettagli del progetto.

>[!TIP]
>Se si preferisce, è possibile avviare il portale di Azure home page per iniziare il processo di **creazione** nel modo seguente:
>
> 1. Passare al [**portale di Azure**](https://ms.portal.azure.com/#home) Home page.
> 1. Selezionare ➕**creare una risorsa**  dal menu Servizi di Azure.
>1. Nella casella di ricerca **Cerca nel Marketplace** immettere e selezionare **Translator** (risorsa a servizio singolo) o **Servizi cognitivi** (risorsa multiservizio).  *Vedere* [scegliere il tipo di risorsa](#create-your-resource)sopra riportato.
> 1. Selezionare **Crea** . verrà eseguita la pagina dei dettagli del progetto.
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>Completa il progetto e i dettagli dell'istanza

1. **Sottoscrizione**. Selezionare una delle sottoscrizioni di Azure disponibili.

1. **Gruppo di risorse**. Il gruppo di risorse di Azure scelto funge da contenitore virtuale per la nuova risorsa. È possibile creare un nuovo gruppo di risorse o aggiungere la risorsa a un gruppo di risorse preesistente che condivide lo stesso ciclo di vita, autorizzazioni e criteri.

1. **Area risorse**. Scegliere **globale** , a meno che l'azienda o l'applicazione non richieda un'area specifica. Translator è un servizio non a livello di area: non esiste alcuna dipendenza da una specifica area di Azure. *Vedere* [aree e zone di disponibilità in Azure](../../availability-zones/az-overview.md).

1. **Nome**. Immettere il nome scelto per la risorsa. Il nome scelto deve essere univoco all'interno di Azure.

> [!NOTE]
> Se si utilizza una funzionalità di conversione che richiede un endpoint di dominio personalizzato, il valore immesso nel campo nome sarà il parametro del nome di dominio personalizzato per l'endpoint.

5. **Piano tariffario**. Selezionare un piano [tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/translator) che soddisfi le proprie esigenze:

   * A ogni sottoscrizione è associato un piano gratuito.
   * Il livello gratuito presenta le stesse caratteristiche e funzionalità dei piani a pagamento e non scade.
   * È consentita una sola sottoscrizione gratuita per ogni account.</li></ul>

1. Se è stata creata una risorsa multiservizio, sarà necessario confermare ulteriori dettagli sull'utilizzo tramite le caselle di controllo.

1. Selezionare **Rivedi e crea**.

1. Esaminare le condizioni del servizio e selezionare **Crea** per distribuire la risorsa.

1. Dopo che la risorsa è stata distribuita correttamente, selezionare **Vai alla risorsa**.

### <a name="authentication-keys-and-endpoint-url"></a>Chiavi di autenticazione e URL dell'endpoint

Tutte le richieste API di servizi cognitivi richiedono un URL dell'endpoint e una chiave di sola lettura per l'autenticazione.

* **Chiavi di autenticazione**. La chiave è una stringa univoca che viene passata a ogni richiesta al servizio di traduzione. È possibile passare la chiave tramite un parametro della stringa di query o specificarla nell'intestazione della richiesta HTTP.

* **URL dell'endpoint**. Usare l'endpoint globale nella richiesta API, a meno che non sia necessaria un'area specifica di Azure. *Vedere* [URL di base](reference/v3-0-reference.md#base-urls). L'URL dell'endpoint globale è `api.cognitive.microsofttranslator.com` .

## <a name="get-your-authentication-keys-and-endpoint"></a>Ottenere le chiavi di autenticazione e l'endpoint

1. Dopo la distribuzione della nuova risorsa, selezionare **Vai alla risorsa** o passare direttamente alla pagina delle risorse.
1. Nella barra di sinistra fare clic su **chiavi ed endpoint** in *Gestione risorse*.
1. Copiare e incollare le chiavi di sottoscrizione e l'URL dell'endpoint in un percorso appropriato, ad esempio il *blocco note di Microsoft*.

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="Ottenere la chiave e l'endpoint.":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>Come eliminare una risorsa o un gruppo di risorse

> [!Warning]
> Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse contenute nel gruppo.

Per rimuovere una risorsa di servizi cognitivi o di conversione, è possibile **eliminare la risorsa** o **eliminare il gruppo di risorse**.

Per eliminare la risorsa:

1. Passare al gruppo di risorse nel portale di Azure.
1. Selezionare le risorse da eliminare selezionando la casella di controllo adiacente.
1. Selezionare **Elimina** dal menu superiore accanto al bordo destro.
1. Digitare *Yes* nella finestra di dialogo **risorse eliminate** .
1. Selezionare **Elimina**.

Per eliminare il gruppo di risorse:

1. Passare al gruppo di risorse nel portale di Azure.
1. Selezionare il **gruppo di risorse Elimina** dalla barra dei menu superiore accanto al bordo sinistro.
1. Confermare la richiesta di eliminazione immettendo il nome del gruppo di risorse e selezionando **Elimina**.

## <a name="how-to-get-started-with-translator"></a>Come iniziare a usare Translator

In questa Guida introduttiva si apprenderà come usare il servizio di conversione con le API REST.

> [!div class="nextstepaction"]
> [Introduzione a Translator](quickstart-translator.md)

## <a name="more-resources"></a>Altre risorse

* [Esempi di codice per Microsoft Translator](https://github.com/MicrosoftTranslator).  Esempi di codice per i traduttori multilingue sono disponibili su GitHub.
* [Forum di supporto su Microsoft Translator](https://www.aka.ms/TranslatorForum)
* [Introduzione ad Azure (video di 3 minuti)](https://azure.microsoft.com/get-started/?b=16.24)