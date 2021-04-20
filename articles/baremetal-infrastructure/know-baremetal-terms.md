---
title: Conoscere i termini dell'infrastruttura BareMetal di Azure
description: Conoscere i termini dell'infrastruttura BareMetal di Azure.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 61ff958e75952f73efb222df3f0c4d5437668cd3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725459"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Conoscere i termini per l'infrastruttura BareMetal

Questo articolo illustra alcuni termini importanti relativi all'infrastruttura BareMetal.

- **Revisione:** sono disponibili due diverse revisioni dei timbri per l'infrastruttura BareMetal (istanze Large di HANA). Queste differenze si differenziano per l'architettura e la prossimità agli host delle macchine virtuali di Azure:
    - "Revisione 3" (versione 3): progettazione originale distribuita a metà 2016.
    - "Revisione 4.2" (versione 4.2): una nuova progettazione che offre una maggiore prossimità agli host delle macchine virtuali di Azure, con latenza di rete estremamente bassa tra macchine virtuali di Azure e istanze Large di HANA. Le risorse nel portale di Azure sono denominate "infrastruttura BareMetal" e i clienti possono accedere alle risorse come istanze bareMetal dal portale di Azure.

- **Stamp:** definisce le dimensioni della distribuzione interna Microsoft delle istanze BareMetal. Prima di poter distribuire le istanze, è necessario distribuire uno stamp di istanza BareMetal costituito da rack di calcolo, rete e archiviazione in una posizione del data center. Una distribuzione di questo tipo è detta indicatore di istanza BareMetal.

- **Tenant:** un cliente che distribuisce uno stamp di istanza BareMetal viene isolato come *tenant.* Un tenant è isolato a livello di rete, archiviazione e calcolo dagli altri tenant, Le unità di archiviazione e di calcolo assegnate ai diversi tenant non possono vedersi o comunicare tra loro a livello di stamp dell'istanza BareMetal. Un cliente può scegliere di avere distribuzioni in tenant diversi. Anche in questo caso, non esiste alcuna comunicazione tra tenant a livello di stamp dell'istanza BareMetal.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata introdotta la terminologia importante dell'infrastruttura BareMetal, è possibile ottenere informazioni su:
- Altri dettagli [dell'infrastruttura BareMetal.](concepts-baremetal-infrastructure-overview.md)
- Come connettere [le istanze dell'infrastruttura BareMetal in Azure.](connect-baremetal-infrastructure.md)

