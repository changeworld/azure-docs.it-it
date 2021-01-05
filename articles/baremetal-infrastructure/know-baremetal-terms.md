---
title: Informazioni sulle condizioni dell'infrastruttura BareMetal di Azure
description: Informazioni sulle condizioni dell'infrastruttura BareMetal di Azure.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: f11bc4cfdd463623010ed7b6677235344ec7cd62
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829153"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Informazioni sulle condizioni per l'infrastruttura BareMetal

In questo articolo verranno illustrate alcune importanti condizioni di BareMetal.

- **Revisione**: sono disponibili due revisioni dei timbri per gli indicatori di istanza di Baremetal. Ogni versione è diversa nell'architettura e nelle vicinanze degli host di macchine virtuali di Azure:
    - **Revisione 3** (Rev 3): è la progettazione originale.
    - **Revisione 4** (Rev 4): è una nuova progettazione che fornisce una prossimità più vicina agli host della macchina virtuale (VM) di Azure e riduce la latenza tra le macchine virtuali di Azure e le unità di istanza di Baremetal. 
    - **Revisione 4,2** (Rev 4,2): è la versione più recente dell'infrastruttura Baremetal che usa l'architettura Rev 4 esistente. È possibile accedere alle istanze di BareMetal e gestirle tramite il portale di Azure.  

- **Timbro**: definisce la dimensione di distribuzione interna Microsoft delle istanze di Baremetal. Prima di poter distribuire le unità di istanza, è necessario distribuire un timbro dell'istanza di BareMetal costituito da rack di calcolo, rete e archiviazione in una posizione del Data Center. Una distribuzione di questo tipo è detta timbro dell'istanza di BareMetal o dalla revisione 4,2.

- **Tenant**: un cliente distribuito nel timbro dell'istanza di Baremetal viene isolato in un *tenant.* Un tenant è isolato a livello di rete, archiviazione e calcolo dagli altri tenant, Le unità di archiviazione e di calcolo assegnate ai diversi tenant non possono vedersi reciprocamente o comunicare tra loro a livello di timbro dell'istanza di BareMetal. Un cliente può scegliere di avere distribuzioni in tenant diversi. Anche in questo caso, non esiste alcuna comunicazione tra i tenant sul livello di timbro dell'istanza di BareMetal.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come identificare e interagire con le unità di istanza di BareMetal tramite il [portale di Azure](workloads/sap/baremetal-infrastructure-portal.md).


 