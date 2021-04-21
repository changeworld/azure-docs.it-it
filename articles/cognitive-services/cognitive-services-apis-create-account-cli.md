---
title: Creare una risorsa di Servizi cognitivi con l'interfaccia della riga di comando di Azure
titleSuffix: Azure Cognitive Services
description: Per iniziare a Servizi cognitivi di Azure, creare e sottoscrivere una risorsa usando l'interfaccia della riga di comando di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: servizi cognitivi, intelligenza cognitiva, soluzioni cognitive, servizi di intelligenza artificiale
ms.topic: quickstart
ms.date: 3/22/2021
ms.author: aahi
ms.openlocfilehash: 26e3b264b7268f7a9ffdb592beef7d76844646f5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789142"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Guida introduttiva: Creare una risorsa Servizi cognitivi usando l'interfaccia della Command-Line di Azure

Usare questa guida introduttiva per iniziare a usare Servizi cognitivi di Azure'interfaccia della riga di comando [di Azure.](/cli/azure/install-azure-cli)

I Servizi cognitivi di Azure sono servizi basati sul cloud con API REST ed SDK delle librerie client che consentono agli sviluppatori di integrare l'intelligenza cognitiva nelle applicazioni senza la necessità di competenze o conoscenze dirette di intelligenza artificiale e data science. I Servizi cognitivi di Azure consentono agli sviluppatori di aggiungere facilmente funzionalità cognitive alle applicazioni con soluzioni cognitive in grado di vedere, ascoltare, parlare, comprendere e persino di iniziare a ragionare.

Servizi cognitivi sono rappresentati [dalle risorse](../azure-resource-manager/management/manage-resources-portal.md) di Azure create nella sottoscrizione di Azure. Dopo aver creato la risorsa, usare le chiavi e l'endpoint generati per autenticare le applicazioni.

In questa guida introduttiva si apprenderà come iscriversi a Servizi cognitivi di Azure e creare un account con una sottoscrizione a servizio singolo o multi-servizio, usando l'interfaccia della riga di comando di [Azure.](/cli/azure/install-azure-cli) Questi servizi sono rappresentati dalle [risorse di](../azure-resource-manager/management/manage-resources-portal.md)Azure, che consentono di connettersi a una o più API Servizi cognitivi di Azure servizio.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure valida: [crearne una](https://azure.microsoft.com/free/cognitive-services) gratuitamente.
* Interfaccia [della riga di comando di Azure](/cli/azure/install-azure-cli)

## <a name="install-the-azure-cli-and-sign-in"></a>Installare l'interfaccia della riga di comando di Azure ed eseguire l'accesso

Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Per accedere all'installazione locale dell'interfaccia della riga di comando, eseguire il [comando az login:](/cli/azure/reference-index#az_login)

```azurecli-interactive
az login
```

È anche possibile usare il pulsante **verde Prova** per eseguire questi comandi nel browser.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Creare un nuovo gruppo Servizi cognitivi di Azure risorse

Prima di creare una risorsa di Servizi cognitivi, è necessario disporre di un gruppo di risorse di Azure per contenere la risorsa. Quando si crea una nuova risorsa, è possibile creare un nuovo gruppo di risorse o usarne uno esistente. Questo articolo illustra come creare un nuovo gruppo di risorse.

### <a name="choose-your-resource-group-location"></a>Scegliere la località del gruppo di risorse

Per creare una risorsa, è necessaria una delle località di Azure disponibili per la sottoscrizione. È possibile recuperare un elenco di località disponibili con il [comando az account list-locations.](/cli/azure/account#az_account_list_locations) È possibile accedere alla maggior parte dei Servizi cognitivi da diverse posizioni. Scegliere quella più vicina o visualizzare le località disponibili per il servizio.

> [!IMPORTANT]
> * Ricordare la località di Azure, perché sarà necessaria quando si chiama il Servizi cognitivi di Azure.
> * La disponibilità di alcuni Servizi cognitivi può variare in base all'area. Per altre informazioni, vedere [Prodotti di Azure per area.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Dopo aver creato la posizione di Azure, creare un nuovo gruppo di risorse nell'interfaccia della riga di comando di Azure usando [il comando az group create.](/cli/azure/group#az_group_create)

Nell'esempio seguente sostituire la località di Azure `westus2` con una delle località di Azure disponibili per la sottoscrizione.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Scegliere un servizio cognitivo e un piano tariffario

Quando si crea una nuova risorsa, è necessario conoscere il "tipo" di servizio che si vuole usare, insieme al piano tariffario [(o](https://azure.microsoft.com/pricing/details/cognitive-services/) sku) desiderato. Queste e altre informazioni verranno usate come parametri durante la creazione della risorsa.

### <a name="multi-service"></a>Multi-servizio

| Servizio                    | Tipo                      |
|----------------------------|---------------------------|
| Più servizi. Per altri [dettagli,](https://azure.microsoft.com/pricing/details/cognitive-services/) vedere la pagina dei prezzi.            | `CognitiveServices`     |


> [!NOTE]
> Molti dei Servizi cognitivi seguenti hanno un livello gratuito che è possibile usare per provare il servizio. Per usare il livello gratuito, `F0` usare come sku per la risorsa.

### <a name="vision"></a>Visione

| Servizio                    | Tipo                      |
|----------------------------|---------------------------|
| Visione artificiale            | `ComputerVision`          |
| Visione personalizzata - Previsione | `CustomVision.Prediction` |
| Visione personalizzata - Training   | `CustomVision.Training`   |
| Viso                       | `Face`                    |
| Riconoscimento modulo            | `FormRecognizer`          |
| Riconoscimento input penna             | `InkRecognizer`           |

### <a name="speech"></a>Voce

| Servizio            | Tipo                 |
|--------------------|----------------------|
| Servizi Voce    | `SpeechServices`     |
| Riconoscimento vocale | `SpeakerRecognition` |

### <a name="language"></a>Linguaggio

| Servizio            | Tipo                |
|--------------------|---------------------|
| Comprensione modulo | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Text Analytics     | `TextAnalytics`     |
| Traduzione testuale   | `TextTranslation`   |

### <a name="decision"></a>Decisione

| Servizio           | Tipo               |
|-------------------|--------------------|
| Rilevamento anomalie  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizza esperienze      | `Personalizer`     |

È possibile trovare un elenco dei "tipi" del servizio cognitivo disponibili con [il comando az cognitiveservices account list-kinds:](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_kinds)

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Aggiungere una nuova risorsa al gruppo di risorse

Per creare e sottoscrivere una nuova risorsa di Servizi cognitivi, usare [il comando az cognitiveservices account create.](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_create) Questo comando aggiunge una nuova risorsa fatturabile al gruppo di risorse creato in precedenza. Quando si crea la nuova risorsa, è necessario conoscere il "tipo" di servizio che si vuole usare, insieme al relativo piano tariffario (o sku) e a una località di Azure:

È possibile creare una risorsa F0 (gratuita) per Rilevamento anomalie, denominata `anomaly-detector-resource` con il comando seguente.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Ottenere le chiavi per la risorsa

Per accedere all'installazione locale dell'interfaccia Command-Line(CLI), usare [il comando az login.](/cli/azure/reference-index#az_login)

```azurecli-interactive
az login
```

Usare il [comando az cognitiveservices account keys list](/cli/azure/cognitiveservices/account/keys#az_cognitiveservices_account_keys_list) per ottenere le chiavi per la risorsa servizio cognitivo.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Piani tariffari e fatturazione

I piani tariffari (e l'importo fatturato) si basano sul numero di transazioni inviate usando le informazioni di autenticazione. Ogni piano tariffario specifica:
* Il numero massimo di transazioni consentite al secondo.
* Le funzionalità del servizio abilitate all'interno del piano tariffario.
* Costo per un numero predefinito di transazioni. Se si va oltre questo importo, verrà addebitato un costo aggiuntivo, come specificato nei dettagli [dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) per il servizio.

## <a name="get-current-quota-usage-for-your-resource"></a>Ottenere l'utilizzo corrente della quota per la risorsa

Usare il [comando az cognitiveservices account list-usage](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_usage) per ottenere l'utilizzo per la risorsa di Servizi cognitivi.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una risorsa di Servizi cognitivi, è possibile eliminarla o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse al suo interno.

Per rimuovere il gruppo di risorse e le risorse associate, usare il comando az group delete.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Vedi anche

* Vedere **[Autenticare le richieste Servizi cognitivi di Azure](authentication.md)** su come usare in modo sicuro Servizi cognitivi.
* Vedere **[Quali sono le Servizi cognitivi di Azure?](./what-are-cognitive-services.md)** per ottenere un elenco di categorie diverse all'interno di Servizi cognitivi.
* Vedere **[Supporto del linguaggio](language-support.md)** naturale per visualizzare l'elenco dei linguaggi naturali supportati da Servizi cognitivi.
* Vedere **[Usare Servizi cognitivi come contenitori](cognitive-services-container-support.md)** per informazioni su come usare Servizi cognitivi in modalità remota.
* Vedere **[Pianificare e gestire i costi per Servizi cognitivi per](plan-manage-costs.md)** stimare il costo dell'uso di Servizi cognitivi.
