---
title: Considerazioni sull'applicazione di patch per BareMetal per Oracle
description: Informazioni sulle considerazioni sull'applicazione di patch al sistema operativo/kernel per l'infrastruttura BareMetal per Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 5af713c776def8af558531c84013b221a8d30087
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559098"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Considerazioni sull'applicazione di patch per BareMetal per Oracle

In questo articolo verranno trattate importanti considerazioni sull'applicazione di patch al sistema operativo/kernel per l'infrastruttura BareMetal per Oracle.

Per garantire prestazioni di rete e stabilità del sistema appropriate, installare la versione specifica del sistema operativo dei driver eNIC e fNIC, come illustrato nella tabella di compatibilità seguente. 

I server vengono recapitati ai clienti con versioni compatibili. Durante l'applicazione di patch al sistema operativo o al kernel, tuttavia, è possibile eseguire il rollback dei driver alle versioni dei driver predefinite. Assicurarsi quindi che la versione del driver appropriata sia in esecuzione seguendo le operazioni di applicazione di patch al sistema operativo/kernel.

| Fornitore del sistema operativo | Versione del pacchetto del sistema operativo | Versione del firmware | Driver eNIC | Driver fNIC |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla configurazione Ethernet di BareMetal per Oracle.

> [!div class="nextstepaction"]
> [Configurazione Ethernet di BareMetal per Oracle](oracle-baremetal-ethernet.md)

