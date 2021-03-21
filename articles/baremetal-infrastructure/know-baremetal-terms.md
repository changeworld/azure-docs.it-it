---
title: Informazioni sulle condizioni dell'infrastruttura BareMetal di Azure
description: Informazioni sulle condizioni dell'infrastruttura BareMetal di Azure.
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: fd7a39854c86f728ef152f8e7d858157e1ad26f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861921"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Informazioni sulle condizioni per l'infrastruttura BareMetal

In questo articolo verranno illustrate alcune importanti condizioni di BareMetal.

- **Revisione**: è presente una revisione del timbro originale nota come revisione 3 (Rev 3) e due diverse revisioni di timbri per gli indicatori di istanza di Baremetal. Ogni timbro è diverso nell'architettura e nelle vicinanze degli host di macchine virtuali di Azure:
    - **Revisione 4** (Rev 4): progettazione più recente che consente di avvicinarsi più vicino agli host della macchina virtuale (VM) di Azure e di ridurre la latenza tra le macchine virtuali di Azure e le unità di istanza di Baremetal. 
    - **Revisione 4,2** (Rev 4,2): l'ultima infrastruttura Baremetal rimarchiata con l'architettura Rev 4 esistente. Il Rev 4 fornisce una prossimità più vicina agli host della macchina virtuale (VM) di Azure. Offre miglioramenti significativi nella latenza di rete tra le macchine virtuali di Azure e le unità di istanza BareMetal distribuite in indicatori o righe Rev 4. È possibile accedere alle istanze di BareMetal e gestirle tramite il portale di Azure.    

- **Timbro**: definisce la dimensione di distribuzione interna Microsoft delle istanze di Baremetal. Prima di poter distribuire le unità di istanza, è necessario distribuire un timbro dell'istanza di BareMetal costituito da rack di calcolo, rete e archiviazione in una posizione del Data Center. Una distribuzione di questo tipo è detta timbro dell'istanza di BareMetal o dalla revisione 4,2.

- **Tenant**: un cliente distribuito nel timbro dell'istanza di Baremetal viene isolato in un *tenant.* Un tenant è isolato a livello di rete, archiviazione e calcolo dagli altri tenant, Le unità di archiviazione e di calcolo assegnate ai diversi tenant non possono vedersi reciprocamente o comunicare tra loro a livello di timbro dell'istanza di BareMetal. Un cliente può scegliere di avere distribuzioni in tenant diversi. Anche in questo caso, non esiste alcuna comunicazione tra i tenant sul livello di timbro dell'istanza di BareMetal.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sull' [infrastruttura Baremetal](workloads/sap/baremetal-overview-architecture.md) o su come [identificare e interagire con le unità di istanza di Baremetal](workloads/sap/baremetal-infrastructure-portal.md). 