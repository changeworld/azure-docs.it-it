---
title: Consigli sulla configurazione e sulla sicurezza del firewall di Azure Percept
description: Altre informazioni sulla configurazione del firewall di Azure Percept e sulle raccomandazioni sulla sicurezza
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: cfc20a30104e24a3950c71bdd8377544803d2f25
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604414"
---
# <a name="azure-percept-firewall-configuration-and-security-recommendations"></a>Consigli sulla configurazione e sulla sicurezza del firewall di Azure Percept

Per informazioni sulla configurazione di firewall e procedure consigliate per la sicurezza generale con Azure Percept, vedere le linee guida riportate di seguito.

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Configurazione dei firewall per Azure Percept DK

Se per la configurazione di rete è necessario consentire esplicitamente le connessioni effettuate dai dispositivi Azure Percept DK, esaminare l'elenco di componenti seguente.

Questo elenco di controllo è un punto di partenza per le regole del firewall:

|URL (* = carattere jolly)|Porte TCP in uscita|Utilizzo|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|443|Autenticazione e autorizzazione di Azure DK SOM|
|*. auth.projectsantacruz.azure.net|443|Autenticazione e autorizzazione di Azure DK SOM|

Esaminare inoltre l'elenco delle [connessioni utilizzate da Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/production-checklist#allow-connections-from-iot-edge-devices).

## <a name="additional-recommendations-for-deployment-to-production"></a>Suggerimenti aggiuntivi per la distribuzione nell'ambiente di produzione

Azure Percept DK offre un'ampia gamma di funzionalità di sicurezza predefinite. Oltre alle potenti funzionalità di sicurezza incluse nella versione corrente, Microsoft suggerisce anche le linee guida seguenti quando si considerano le distribuzioni di produzione:

- Protezione fisica avanzata del dispositivo stesso
- Verificare che la crittografia dei dati inattivi sia abilitata
- Monitorare continuamente la postura del dispositivo e rispondere rapidamente agli avvisi
- Limitare il numero di amministratori che hanno accesso al dispositivo

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scopri di più sulla sicurezza di Azure Percept](./overview-percept-security.md)