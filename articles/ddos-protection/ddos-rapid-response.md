---
title: Risposta rapida DDoS di Azure
description: Scopri come coinvolgere gli esperti DDoS durante un attacco attivo per il supporto specializzato.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: f551f40a9bd70c4a3b54c1d2756cfb94caf79590
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103063"
---
# <a name="azure-ddos-rapid-response"></a>Risposta rapida DDoS di Azure

Durante un accesso attivo, i clienti standard di protezione DDoS di Azure hanno accesso al team DDoS Rapid Response (DRR), che può aiutare a esaminare gli attacchi durante un'analisi di attacco e post-attacco.

## <a name="prerequisites"></a>Prerequisiti

- Prima di eseguire i passaggi di questa esercitazione, è necessario creare prima di tutto un [piano di protezione standard DDoS di Azure](manage-ddos-protection.md).

## <a name="when-to-engage-drr"></a>Quando attivare DRR

È necessario coinvolgere DRR solo se: 

- Durante un attacco DDoS se si ritiene che le prestazioni della risorsa protetta siano gravemente degradate o che la risorsa non sia disponibile. 
- Si ritiene che la risorsa stia subendo un attacco DDoS, ma il servizio di protezione DDoS non mitiga l'attacco in modo efficace.
- Si prevede un evento virale che aumenterà significativamente il traffico di rete.
- Per gli attacchi che hanno un importante effetto aziendale.

## <a name="engage-drr-during-an-active-attack"></a>Coinvolgere DRR durante un attacco attivo

1. Da portale di Azure durante la creazione di una nuova richiesta di supporto, scegliere **tipo di problema** come tecnico.
2. Scegliere **servizio** come **protezione DDoS**.
3. Scegliere una risorsa dal menu a discesa risorsa. _Per coinvolgere DRR, è necessario selezionare un piano DDoS collegato alla rete virtuale protetta da protezione DDoS standard._

    ![Scegliere una risorsa](./media/ddos-rapid-response/choose-resource.png)

4. Nella pagina del **problema** successivo selezionare la **gravità** come un problema critico e un **tipo di problema** come "sotto attacco".

    ![PSeverity e tipo di problema](./media/ddos-rapid-response/severity-and-problem-type.png)

5. Completare i dettagli aggiuntivi e inviare la richiesta di supporto.

DRR segue il modello di supporto Azure Rapid Response. Per ulteriori informazioni su risposta rapida, vedere l' [ambito del supporto e](https://azure.microsoft.com/en-us/support/plans/response/) la velocità di risposta.

Per altre informazioni, vedere la [documentazione standard di protezione DDoS](./ddos-protection-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come eseguire [test tramite simulazioni](test-through-simulations.md).
- Informazioni su come [visualizzare e configurare la telemetria di protezione DDoS](telemetry.md).
- Informazioni su come [visualizzare e configurare la registrazione diagnostica DDoS](diagnostic-logging.md).
