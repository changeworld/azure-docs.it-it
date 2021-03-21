---
title: Risoluzione dei problemi relativi al cloud Spring di Azure nella rete virtuale
description: Guida alla risoluzione dei problemi per la rete virtuale di Azure Spring cloud.
author: mikedodaro
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b2369a6380c7b74302d32366d0604fca616fc3ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698230"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>Risoluzione dei problemi relativi al cloud Spring di Azure nelle reti virtuali

Questo documento consente di risolvere vari problemi che possono verificarsi quando si usa il cloud Spring di Azure nelle reti virtuali.

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Si è verificato un problema durante la creazione di un'istanza del servizio cloud di Azure Spring

Per creare un'istanza di Azure Spring cloud, è necessario disporre di autorizzazioni sufficienti per distribuire l'istanza nella rete virtuale.  L'istanza del servizio cloud Spring deve [concedere l'autorizzazione del servizio cloud Spring di Azure alla rete virtuale](spring-cloud-tutorial-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network).

Se si usa il portale di Azure per configurare l'istanza del servizio cloud di Azure Spring, il portale di Azure convaliderà le autorizzazioni.

Per configurare l'istanza del servizio cloud Spring di Azure usando l' [interfaccia](/cli/azure/get-started-with-azure-cli)della riga di comando di Azure, verificare che:

- La sottoscrizione è attiva.
- Il percorso è supportato dal cloud Spring di Azure.
- Il gruppo di risorse per l'istanza è già stato creato.
- Il nome della risorsa è conforme alla regola di denominazione. Deve contenere solo lettere minuscole, numeri e trattini. Il primo carattere deve essere una lettera. L'ultimo carattere deve essere una lettera o un numero. Il valore deve contenere da 2 a 32 caratteri.

Per configurare l'istanza del servizio cloud Spring di Azure usando il modello di Gestione risorse, vedere [comprendere la struttura e la sintassi dei modelli Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

### <a name="common-creation-issues"></a>Problemi di creazione comuni

| Messaggio di errore | Procedura di correzione |
|------|------|
| Le risorse create da Azure Spring cloud non sono consentite dai criteri. | Le risorse di rete verranno create quando si distribuisce il cloud Spring di Azure nella propria rete virtuale. Controllare se sono stati definiti [criteri di Azure](../governance/policy/overview.md) per bloccare la creazione. Non è stato possibile creare le risorse. il messaggio di errore è stato trovato. |
| Le subnet specificate sono associate alle tabelle di route. disassociarle. | Attualmente non è supportata la distribuzione del cloud Spring di Azure nella subnet associata alle tabelle di route esistenti, dissociarle e riprovare. |
| Il traffico necessario non è allowlisted. | Per assicurarsi che il traffico richiesto sia allowlisted, vedere [responsabilità del cliente per l'esecuzione di Azure Spring cloud in VNET](spring-cloud-vnet-customer-responsibilities.md) . |

## <a name="my-application-cant-be-registered"></a>Non è possibile registrare l'applicazione

Questo problema si verifica se la rete virtuale è configurata con le impostazioni DNS personalizzate. In questo caso, la zona DNS privata usata da Azure Spring cloud è inefficace. Aggiungere il 168.63.129.16 IP DNS di Azure come server DNS upstream nel server DNS personalizzato.

## <a name="other-issues"></a>Altri problemi

[Risolvere i problemi comuni relativi al cloud Spring di Azure](./spring-cloud-troubleshoot.md).