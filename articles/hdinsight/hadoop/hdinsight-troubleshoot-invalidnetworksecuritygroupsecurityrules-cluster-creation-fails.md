---
title: Errore InvalidNetworkSecurityGroupSecurityRules-Azure HDInsight
description: La creazione del cluster non riesce con il codice ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 7e0b984b3ec4a203f8a1118c0e6a166c5a9e1125
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944380"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scenario: InvalidNetworkSecurityGroupSecurityRules-la creazione del cluster non riesce in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Il codice di errore viene visualizzato `InvalidNetworkSecurityGroupSecurityRules` con una descrizione simile a "le regole di sicurezza nel gruppo di sicurezza di rete configurato con la subnet non consentono la connettività in ingresso e/o in uscita".

## <a name="cause"></a>Causa

Probabilmente un problema con le regole del [gruppo di sicurezza di rete](../../virtual-network/virtual-network-vnet-plan-design-arm.md) in ingresso configurate per il cluster.

## <a name="resolution"></a>Soluzione

Passare alla portale di Azure e identificare il NSG associato alla subnet in cui viene distribuito il cluster. Nella sezione **regole di sicurezza in ingresso** verificare che le regole consentano l'accesso in ingresso alla porta 443 per gli indirizzi IP indicati [qui](../control-network-traffic.md).

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]