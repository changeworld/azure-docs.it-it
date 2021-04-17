---
title: Disponibilità elevata e ripristino di emergenza per Oracle in BareMetal
description: Informazioni sulla disponibilità elevata e sul ripristino di emergenza per Oracle in Azure'infrastruttura BareMetal.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: ab0337622eaa8c1368760fcbcd28533dacb3adce
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590306"
---
# <a name="high-availability-and-disaster-recovery-for-oracle-on-baremetal"></a>Disponibilità elevata e ripristino di emergenza per Oracle in BareMetal

In questo articolo verranno trattate le nozioni di base sulla disponibilità elevata e sul ripristino di emergenza. Verrà quindi illustrato come ottenere disponibilità elevata e ripristino di emergenza in un ambiente Oracle nell'infrastruttura BareMetal.

## <a name="high-availability-vs-disaster-recovery"></a>Disponibilità elevata e ripristino di emergenza

Sia la disponibilità elevata che il ripristino di emergenza forniscono copertura, ma da diversi tipi di errori. Usano diverse funzionalità e opzioni del Oracle Database.

La disponibilità elevata consente a un sistema di risolvere più errori senza influire sull'esperienza utente dell'applicazione. Le caratteristiche comuni di un sistema a disponibilità elevata includono:

- Hardware ridondante che non presenta un singolo punto di errore.
- Ripristino automatico da errori non critici, ad esempio unità disco non riuscite o cavi di rete difettosi.
- Possibilità di eseguire il roll-on delle modifiche hardware e software senza alcun effetto evidente sull'elaborazione.
- Soddisfa o supera gli obiettivi per gli obiettivi del tempo di ripristino (RTO) e del punto di ripristino (RPO).

La funzionalità più comune di Oracle usata per la disponibilità elevata è [Oracle Real Application Clusters (RAC).](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92)

Il ripristino di emergenza protegge da errori localizzati irreversibili che danneggiano la strategia di disponibilità elevata primaria. Nell'ecosistema Oracle viene fornito tramite la replica di database, nota anche come [Oracle Data Guard.](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle funzionalità a disponibilità elevata per Oracle:

> [!div class="nextstepaction"]
> [Funzionalità a disponibilità elevata per Oracle nell'infrastruttura BareMetal](high-availability-features.md)
