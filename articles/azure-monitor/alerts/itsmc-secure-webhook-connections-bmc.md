---
title: IT Service Management Connector-esportazione sicura in monitoraggio di Azure-configurazione con BMC
description: Questo articolo illustra come connettere i prodotti/servizi ITSM con BMC in un'esportazione sicura in monitoraggio di Azure.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 7c50d533f95e1131e26ddd808c77023ae4591a26
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041706"
---
# <a name="connect-bmc-helix-to-azure-monitor"></a>Connettere BMC Helix a monitoraggio di Azure

Le sezioni seguenti forniscono informazioni dettagliate su come connettere il prodotto BMC Helix ed esportare in modo sicuro in Azure.

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di aver soddisfatto i prerequisiti seguenti:

* Azure AD è registrato.
* Si dispone della versione supportata di BMC Helix multicloud Service Management (versione 19,08 o successiva).

## <a name="configure-the-bmc-helix-connection"></a>Configurare la connessione a BMC Helix

1. Per ottenere l'URI per l'esportazione protetta, attenersi alla procedura seguente nell'ambiente BMC Helix:

   1. Accedere a Integration Studio.
   1. Cercare il flusso di **avvisi Crea evento imprevisto da Azure** .
   1. Copiare l'URL del webhook.
   
   ![Screenshot del webhook U R L in Integration Studio.](media/itsmc-secure-webhook-connections-bmc/bmc-url.png)
   
2. Seguire le istruzioni in base alla versione:
   * [Abilitazione dell'integrazione predefinita con monitoraggio di Azure per la versione 20,02](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html).
   * [Abilitazione dell'integrazione predefinita con monitoraggio di Azure per la versione 19,11](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html).

3. Come parte della configurazione della connessione in BMC Helix, passare all'istanza di Integration BMC e seguire queste istruzioni:

   1. Selezionare **Catalogo**.
   2. Selezionare **avvisi di Azure**.
   3. Selezionare **connettori**.
   4. Selezionare **configurazione**.
   5. Selezionare la configurazione **Aggiungi nuova connessione** .
   6. Immettere le informazioni per la sezione di configurazione:
      - **Nome**: creare un proprio.
      - **Tipo di autorizzazione**: **nessuno**
      - **Descrizione**: crearne una personalizzata.
      - **Sito**: **cloud**
      - **Numero di istanze**: **2**, il valore predefinito.
      - **Check**: selezionato per impostazione predefinita per abilitare l'utilizzo.
      - L'ID tenant di Azure e l'ID dell'applicazione Azure vengono ricavati dall'applicazione definita in precedenza.

![Screenshot che mostra la configurazione BMC.](media/itsmc-secure-webhook-connections-bmc/bmc-configuration.png)
