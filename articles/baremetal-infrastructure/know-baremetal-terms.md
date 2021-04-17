---
title: Conoscere le condizioni dell'infrastruttura BareMetal di Azure
description: Conoscere i termini dell'infrastruttura BareMetal di Azure.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: aa7d9693b3417ff0bb6c6a61800aee72cd416c48
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536766"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Conoscere i termini per l'infrastruttura BareMetal

Questo articolo illustra alcuni termini importanti relativi all'infrastruttura BareMetal.

- **Revisione:** è presente una revisione del timbro originale nota come Revisione 3 (Rev 3) e due revisioni aggiuntive del timbro per i timbri dell'istanza BareMetal. Ogni timbro è diverso per architettura e prossimità agli host di macchine virtuali di Azure:
    - **Revisione 4** (Versione 4): una progettazione più recente che offre una prossimità più vicina agli host delle macchine virtuali di Azure e riduce la latenza tra le macchine virtuali di Azure e le istanze SAP HANA azure. 
    - **Revisione 4.2** (Rev 4.2): l'ultima infrastruttura BareMetal ridefinito con l'architettura Rev 4 esistente. Rev 4 offre una prossimità più vicina agli host di macchine virtuali (VM) di Azure. Ha miglioramenti significativi nella latenza di rete tra le macchine virtuali di Azure e le istanze BareMetal distribuite in stamp o righe Rev 4. È possibile accedere e gestire le istanze di BareMetal tramite il portale di Azure.    

- **Stamp:** definisce le dimensioni di distribuzione interna Microsoft delle istanze BareMetal. Prima di poter distribuire le istanze, è necessario distribuire un stamp di istanza BareMetal costituito da rack di calcolo, rete e archiviazione in una posizione del data center. Una distribuzione di questo tipo è detta indicatore di istanza BareMetal.

- **Tenant:** un cliente che distribuisce un stamp di istanza BareMetal viene isolato come *tenant.* Un tenant è isolato a livello di rete, archiviazione e calcolo dagli altri tenant, Le unità di archiviazione e di calcolo assegnate ai diversi tenant non possono vedersi o comunicare tra loro a livello di stamp dell'istanza BareMetal. Un cliente può scegliere di avere distribuzioni in tenant diversi. Anche in questo caso, non esiste alcuna comunicazione tra tenant a livello di stamp dell'istanza BareMetal.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver introdotto l'importante terminologia dell'infrastruttura BareMetal, è possibile ottenere informazioni su:
- Altri dettagli [dell'infrastruttura BareMetal](concepts-baremetal-infrastructure-overview.md).
- Come connettere [istanze dell'infrastruttura BareMetal in Azure](connect-baremetal-infrastructure.md).

