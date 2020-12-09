---
title: 'Esercitazione: Prerequisiti per Servizi cognitivi in Azure Synapse'
description: Esercitazione su come configurare i prerequisiti per usare Servizi cognitivi in Azure Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: cf76a3d09da83693791873feb9e6ab03c432e47f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464398"
---
# <a name="tutorial-pre-requisites-for-using-cognitive-services-in-azure-synapse"></a>Esercitazione: Prerequisiti per Servizi cognitivi in Azure Synapse

Questa esercitazione descrive come configurare i prerequisiti per usare in modo sicuro Servizi cognitivi in Azure Synapse.

Contenuto dell'esercitazione:
> [!div class="checklist"]
> - Creare una risorsa di Servizi cognitivi, ad esempio Analisi del testo o Rilevamento anomalie.
> - Archiviare la chiave di autenticazione per le risorse di Servizi cognitivi come segreti in Azure Key Vault e configurare l'accesso per l'area di lavoro di Azure Synapse.
> - Creare un servizio collegato Azure Key Vault nell'area di lavoro di Azure Synapse Analytics.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- [Area di lavoro di Azure Synapse Analytics](../get-started-create-workspace.md) con un account di archiviazione di ADLS Gen2 configurato come risorsa archiviazione predefinita. È necessario essere il **collaboratore dei dati del BLOB di archiviazione** del file system di ADLS Gen2 che si vuole usare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/)

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa di Servizi cognitivi

In [Servizi cognitivi di Azure](https://go.microsoft.com/fwlink/?linkid=2147492) sono inclusi molti tipi diversi di servizi. Di seguito sono riportati alcuni esempi usati nelle esercitazioni su Synapse.

### <a name="create-an-anomaly-detector-resource"></a>Creare una risorsa di Rilevamento anomalie
Creare una risorsa [Rilevamento anomalie](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) nel portale di Azure.

![Creare rilevamento anomalie](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

### <a name="create-a-text-analytics-resource"></a>Creare una risorsa Analisi del testo
Creare una risorsa [Analisi del testo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) nel portale di Azure.

![Creare analisi del testo](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

## <a name="create-key-vault-and-configure-secrets-and-access"></a>Creare un'istanza di Key Vault e configurare segreti e accesso

1. Creare un'istanza di [Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault) nel portale di Azure.
2. Passare a **Key Vault -> Criteri di accesso** e concedere all'[identità gestita dell'area di lavoro di Azure Synapse](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-identity) le autorizzazioni per leggere i segreti in Azure Key Vault.

>Assicurarsi che le modifiche dei criteri vengano salvate. Questo passaggio è facile da ignorare.

![Aggiungere un criterio di accesso](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Passare alla risorsa di Servizi cognitivi, ad esempio **Rilevamento anomalie -> Chiavi ed endpoint** e copiare una delle due chiavi negli Appunti.

4. Passare a **Key Vault -> Segreto** per creare un nuovo segreto. Specificare il nome del segreto e quindi incollare la chiave del passaggio precedente nel campo "Valore". Fare infine clic su **Crea**.

![Creare un segreto](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

> Assicurarsi di ricordare o annotare il nome di questo segreto. Verrà usato in seguito per connettersi a Servizi cognitivi da Azure Synapse Studio.

## <a name="create-azure-keyvault-linked-service-in-azure-synapse"></a>Creare un servizio collegato Azure Key Vault in Azure Synapse

1. Aprire l'area di lavoro in Azure Synapse Studio. Passare a **Gestisci -> Servizi collegati**. Creare un servizio collegato "Azure Key Vault" che punta all'istanza di Key Vault appena creata. Quindi, verificare la connessione facendo clic sul pulsante "Test connessione" e controllare se è verde. Se tutto funziona perfettamente, fare prima clic su "Crea" e quindi su "Pubblica tutto" per salvare la modifica.
![Servizio collegato](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

È ora possibile continuare con una delle esercitazioni sull'uso dell'esperienza di Servizi cognitivi in Azure Synapse Studio.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Analisi del sentiment con Servizi cognitivi di Azure](tutorial-cognitive-services-sentiment.md)
- [Esercitazione: Rilevamento anomalie con Servizi cognitivi](tutorial-cognitive-services-sentiment.md)
- [Esercitazione: Assegnazione di punteggi al modello di Machine Learning nei pool SQL dedicati di Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Funzionalità di Machine Learning in Azure Synapse Analytics](what-is-machine-learning.md)