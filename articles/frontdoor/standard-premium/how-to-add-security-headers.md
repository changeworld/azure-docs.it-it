---
title: Configurare le intestazioni di sicurezza con il set di regole standard/Premium (anteprima) di Azure front door
description: Questo articolo fornisce indicazioni su come usare il set di regole per configurare le intestazioni di sicurezza.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c08ba57f43969bb2f0ee9c66b6cb4e92879ed258
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099899"
---
# <a name="configure-security-headers-with-azure-front-door-standardpremium-preview-rule-set"></a>Configurare le intestazioni di sicurezza con il set di regole standard/Premium (anteprima) di Azure front door

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Questo articolo illustra come implementare le intestazioni di sicurezza per evitare vulnerabilità basate su browser come HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy o X-frame-options. Gli attributi basati sulla sicurezza possono anche essere definiti con i cookie.

Nell'esempio seguente viene illustrato come aggiungere un'intestazione Content-Security-Policy a tutte le richieste in ingresso che corrispondono al percorso nella route. In questo caso, nell'applicazione è consentita l'esecuzione di script solo dal sito attendibile **https://apiphany.portal.azure-api.net** .

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

* Prima di poter configurare le intestazioni di sicurezza, è necessario creare prima di tutto una porta anteriore. Per altre informazioni, vedere [Avvio rapido: Creare una frontdoor](create-front-door-portal.md).
* Esaminare la procedura di [configurazione di un set di regole](how-to-configure-rule-set.md) se la funzionalità set di regole non è stata usata in precedenza.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Aggiungere un'intestazione Content-Security-Policy nel portale di Azure

1. Passare al profilo standard/Premium di Azure front door e selezionare **set di regole** in **Settings (impostazioni).**

1. Selezionare **Aggiungi** per aggiungere un nuovo set di regole. Assegnare un **nome** al set di regole e quindi specificare un **nome** per la regola. Selezionare **Aggiungi un'azione** e quindi selezionare **intestazione risposta**.

1. Impostare l'operatore su **Accoda** per aggiungere questa intestazione come risposta a tutte le richieste in ingresso per questa route.

1. Aggiungere il nome dell'intestazione: **Content-Security-Policy** e definire i valori che verranno accettati da questa intestazione. In questo scenario si sceglie *"script-src ' self ' https://apiphany.portal.azure-api.net "*.

1. Dopo aver aggiunto tutte le regole desiderate alla configurazione, non dimenticare di associare il set di regole a una route. Questo passaggio è *necessario* per consentire al set di regole di agire. 

> [!NOTE]
> In questo scenario non sono state aggiunte [condizioni di corrispondenza](concept-rule-set-match-conditions.md) alla regola. A tutte le richieste in ingresso che corrispondono al percorso definito nella route associata verrà applicata questa regola. Per applicarla solo a un sottoinsieme di queste richieste, aggiungere le **condizioni di corrispondenza** specifiche alla regola.

## <a name="clean-up-resources"></a>Pulire le risorse

### <a name="deleting-a-rule"></a>Eliminazione di una regola

Nei passaggi precedenti è stata configurata l'intestazione Content-Security-Policy con il set di regole. Se non si desidera più una regola, è possibile selezionare il nome del set di regole e quindi selezionare Elimina regola. 

### <a name="deleting-a-rule-set"></a>Eliminazione di un set di regole

Se si desidera eliminare un set di regole, assicurarsi di annullarne l'associazione da tutte le route prima di eliminarlo. Per istruzioni dettagliate sull'eliminazione di un set di regole, vedere [configurare il set di regole](how-to-configure-rule-set.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare un Web Application Firewall per la porta anteriore, vedere [Web Application Firewall e sportello anteriore](../../web-application-firewall/afds/afds-overview.md).
