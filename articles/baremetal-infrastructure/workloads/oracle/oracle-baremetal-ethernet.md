---
title: Configurazione Ethernet di BareMetal per Oracle
description: Informazioni sulla configurazione delle interfacce Ethernet nelle istanze BareMetal per carichi di lavoro Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: c57cbc86d17090d6960a334c2790d80b43420aca
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588888"
---
# <a name="ethernet-configuration-of-baremetal-for-oracle"></a>Configurazione Ethernet di BareMetal per Oracle

In questo articolo verrà illustrata la configurazione delle interfacce Ethernet nelle istanze BareMetal per i carichi di lavoro Oracle.

Ogni istanza BareMetal di cui è stato effettuato il provisioning per Oracle è preconfigurata con set di interfacce Ethernet. Le interfacce Ethernet sono suddivise in quattro tipi:

- Utilizzato per o dall'accesso client.
- Usato per la comunicazione da nodo a nodo. Questa interfaccia è configurata in tutti i server indipendentemente dalla topologia richiesta. Viene usato solo per scenari di scalabilità orizzontale.
- Usato per la connettività da nodo a archiviazione.
- Usato per la configurazione del ripristino di emergenza e la connettività alla copertura globale per la connettività tra aree.

## <a name="architecture"></a>Architettura

Il diagramma seguente illustra l'architettura delle interfacce Ethernet preconfigurati dell'infrastruttura BareMetal. 

[![Diagramma che illustra l'architettura delle interfacce Ethernet preconfigurata per i carichi di lavoro Oracle.](media/oracle-baremetal-ethernet/architecture-ethernet.png)](media/oracle-baremetal-ethernet/architecture-ethernet.png#lightbox)

La configurazione predefinita include un'interfaccia IP client (eth1), che si connette dalla rete virtuale di Azure tramite la quale è possibile usare Secure Shell (SSH) per accedere a un'istanza di BareMetal.

> [!NOTE]
> Per un'altra interfaccia client (eth10) da una rete virtuale di Azure diversa, contattare l'amministratore del servizio clienti Microsoft per inviare una richiesta di servizio. Ad esempio, se si vogliono ambienti di sviluppo/test, nonché ambienti di produzione/ripristino di emergenza.

| **Interfaccia logica NIC** | **Nome con sistema operativo RHEL** | **Caso d'uso** |
| --- | --- | --- |
| Una | net1.tenant | Da client a istanza BareMetal |
| C | net2.tenant | Da nodo a archiviazione; supporta il coordinamento e l'accesso ai controller di archiviazione per la gestione dell'ambiente di archiviazione. |
| B | net3.tenant | Da nodo a nodo (interconnessione privata) |
| C | net4.tenant | Riservato/iSCSI |
| C | net5.tenant | Backup riservato/log |
| C | net6.tenant | Backup da nodo a storage_Data (RMAN, snapshot) |
| C | net7.tenant | Da nodo a storage_dNFS-Pri; fornisce connettività con l'array di archiviazione NetApp. |
| C | net8.tenant | Da nodo a storage_dNFS-sec; fornisce connettività con l'array di archiviazione NetApp. |
| D | net9.tenant | Connettività di ripristino di emergenza per la configurazione della copertura globale per l'accesso all'IMC in un'altra area. |
| Una | \*net10.tenant | \* Da client a istanza BareMetal
 |

Se necessario, è possibile definire più schede nic (Network Interface Controller) in modo personalizzato. Tuttavia, le configurazioni delle schede di interfaccia di rete esistenti *non possono* essere modificate.

## <a name="usage-rules"></a>Regole di utilizzo

Per le istanze BareMetal, l'impostazione predefinita avrà nove indirizzi IP assegnati nelle quattro schede di interfaccia di rete logiche. Si applicano le regole di utilizzo seguenti:

- Ethernet "A" deve avere un indirizzo IP assegnato non compreso nell'intervallo di indirizzi del pool DI INDIRIZZI IP del server inviato a Microsoft. Questo indirizzo IP non deve essere mantenuto nella directory etc/hosts del sistema operativo.
- Ethernet "B" deve essere gestita esclusivamente nella directory etc/hosts per la comunicazione tra le varie istanze. Mantenere questi indirizzi IP nelle configurazioni RAC (Real Application Cluster) Oracle con scalabilità orizzontale come indirizzi IP usati per la configurazione tra nodi.
- A Ethernet "C" deve essere assegnato un indirizzo IP usato per la comunicazione con l'archiviazione NFS. Questo tipo di indirizzo non deve essere mantenuto nella directory etc/hosts.
- Ethernet "D" deve essere usata esclusivamente per la configurazione della copertura globale per l'accesso alle istanze BareMetal nell'area di ripristino di emergenza.

## <a name="next-step"></a>Passaggio successivo

Altre informazioni sull'infrastruttura BareMetal per l'architettura Oracle.

> [!div class="nextstepaction"]
> [Architettura dell'infrastruttura BareMetal per Oracle](oracle-baremetal-architecture.md)
