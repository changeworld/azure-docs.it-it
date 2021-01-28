---
title: 'Esercitazione: prerequisiti per servizi cognitivi in Azure sinapsi Analytics'
description: Informazioni su come configurare i prerequisiti per l'uso di servizi cognitivi in sinapsi di Azure.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 3ab861caca0ef6f58c2c1bc722412774deb725ce
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936684"
---
# <a name="tutorial-prerequisites-for-using-cognitive-services-in-azure-synapse-analytics"></a>Esercitazione: prerequisiti per l'uso di servizi cognitivi in Azure sinapsi Analytics

Questa esercitazione illustra come configurare i prerequisiti per l'uso sicuro dei servizi cognitivi di Azure in Azure sinapsi Analytics.

Contenuto dell'esercitazione:
> [!div class="checklist"]
> - Creare una risorsa di servizi cognitivi come Analisi del testo o il rilevatore di anomalie.
> - Archiviare una chiave di autenticazione per le risorse di servizi cognitivi come segreti in Azure Key Vault e configurare l'accesso per un'area di lavoro di Azure sinapsi Analytics.
> - Creare un servizio collegato Azure Key Vault nell'area di lavoro di Azure sinapsi Analytics.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- [Area di lavoro di Azure sinapsi Analytics](../get-started-create-workspace.md) con un Azure Data Lake storage Gen2 account di archiviazione configurato come risorsa di archiviazione predefinita. È necessario essere il *collaboratore dati BLOB di archiviazione* del Azure Data Lake storage Gen2 file System con cui si lavora.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

[Servizi cognitivi di Azure](../../cognitive-services/index.yml) include molti tipi di servizi. Analisi del testo e il rilevatore di anomalie sono due esempi nelle esercitazioni sulle sinapsi di Azure.

È possibile creare una risorsa [analisi del testo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) nel portale di Azure:

![Screenshot che mostra Analisi del testo nel portale, con il pulsante Crea.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

È possibile creare una risorsa del [rilevatore di anomalie](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) nel portale di Azure:

![Screenshot che mostra il rilevatore di anomalie nel portale, con il pulsante Crea.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

## <a name="create-a-key-vault-and-configure-secrets-and-access"></a>Creare un insieme di credenziali delle chiavi e configurare segreti e accesso

1. Creare un insieme di credenziali delle [chiavi](https://ms.portal.azure.com/#create/Microsoft.KeyVault) nel portale di Azure.
2. Passare a **Key Vault**  >  **criteri di accesso** e concedere all' [area di lavoro di Azure sinapsi](../security/synapse-workspace-managed-identity.md) le autorizzazioni MSI per leggere i segreti da Azure Key Vault.

   > [!NOTE]
   > Assicurarsi che le modifiche dei criteri vengano salvate. Questo passaggio è facile da ignorare.

   ![Screenshot che mostra le selezioni per l'aggiunta di un criterio di accesso.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Passare alla risorsa Servizi cognitivi. Ad esempio, passare a **anomalie rilevamento**  >  **chiavi ed endpoint**. Quindi copiare uno dei due tasti negli Appunti.

4. Passare a **Key Vault**  >  **Secret** per creare un nuovo segreto. Specificare il nome del segreto, quindi incollare la chiave dal passaggio precedente nel campo **valore** . Infine, selezionare **Crea**.

   ![Screenshot che mostra le selezioni per la creazione di un segreto.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

   > [!IMPORTANT]
   > Assicurarsi di ricordare o annotare il nome del segreto. Verrà usato in un secondo momento quando ci si connette a servizi cognitivi da Azure sinapsi Studio.

## <a name="create-an-azure-key-vault-linked-service-in-azure-synapse"></a>Creare un servizio collegato Azure Key Vault in sinapsi di Azure

1. Aprire l'area di lavoro in Azure Synapse Studio. 
2. Passare a **Gestisci**  >  **servizi collegati**. Creare un servizio collegato **Azure Key Vault** puntando all'insieme di credenziali delle chiavi appena creato. 
3. Verificare la connessione selezionando il pulsante **Test connessione** . Se la connessione è verde, selezionare **Crea** e quindi selezionare **pubblica tutto** per salvare la modifica.

![Screenshot che mostra Azure Key Vault come nuovo servizio collegato.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

A questo punto si è pronti per continuare con una delle esercitazioni per l'uso dell'esperienza servizi cognitivi di Azure in Azure sinapsi Studio.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Analisi del sentiment con Servizi cognitivi di Azure](tutorial-cognitive-services-sentiment.md)
- [Esercitazione: Rilevamento anomalie con Servizi cognitivi](tutorial-cognitive-services-sentiment.md)
- [Esercitazione: Assegnazione di punteggi al modello di Machine Learning nei pool SQL dedicati di Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Funzionalità di Machine Learning in Azure Synapse Analytics](what-is-machine-learning.md)