---
title: Anteprima di Azure Firewall Premium nella portale di Azure
description: Per informazioni sull'anteprima di Azure Firewall Premium, vedere la portale di Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3d56fc73faeb0d48ba7e5b21449c61d6213afa40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100549697"
---
# <a name="azure-firewall-premium-preview-in-the-azure-portal"></a>Anteprima di Azure Firewall Premium nella portale di Azure

> [!IMPORTANT]
> Azure Firewall Premium è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Azure Firewall Premium Preview è un firewall di nuova generazione con funzionalità necessarie per ambienti altamente sensibili e regolamentati. Esso comprende le seguenti funzionalità:

- **Ispezione TLS** : consente di decrittografare il traffico in uscita, elaborare i dati e quindi crittografare i dati e inviarli alla destinazione.
- **IDP** : un sistema di rilevamento e prevenzione delle intrusioni nella rete consente di monitorare le attività di rete per attività dannose, registrare informazioni su questa attività, segnalarle e, facoltativamente, provare a bloccarlo.
- **Filtro URL** : estende la funzionalità di filtro FQDN del firewall di Azure per considerare un intero URL. Ad esempio, `www.contoso.com/a/c` anziché `www.contoso.com` .
- **Categorie Web** : gli amministratori possono consentire o negare l'accesso utente alle categorie di siti Web, ad esempio siti Web per il gioco d'azzardo, social media e altri.

Per altre informazioni, vedere [funzionalità Premium del firewall di Azure](premium-features.md).

## <a name="deploy-the-firewall"></a>Distribuire il firewall

La distribuzione di un'anteprima di Azure Firewall Premium è simile alla distribuzione di un firewall standard di Azure:

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="distribuzione del portale":::

Per **livello firewall** selezionare **Premium (anteprima)** e per **criterio firewall** selezionare un criterio Premium esistente o crearne uno nuovo.

## <a name="configure-the-premium-policy"></a>Configurare i criteri Premium

La configurazione di un criterio firewall Premium è simile alla configurazione di un criterio firewall standard. Con i criteri Premium è possibile configurare le funzionalità Premium:

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="Distribuzione dei criteri Premium":::

### <a name="rule-configuration"></a>Configurazione regola

Quando si configurano le regole dell'applicazione in un criterio Premium, è possibile configurare funzionalità Premium aggiuntive:

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="Regola Premium":::

## <a name="next-steps"></a>Passaggi successivi

Per visualizzare le funzionalità di anteprima di Azure Firewall Premium in azione, vedere [distribuire e configurare Azure Firewall Premium Preview](premium-deploy.md).