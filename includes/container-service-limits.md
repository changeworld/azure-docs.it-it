---
title: includere file
description: File di inclusione
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: da22991b9a1c4b69d3a3d6eb6f76b0925a6ad3d4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799981"
---
| Risorsa                                                                                                           | Limite                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Numero massimo di cluster per sottoscrizione                                                                                  | 5000                                                                                                                                                                                                        |
| Numero massimo di nodi per cluster con set di disponibilità di macchine virtuali e SKU Basic di Load Balancer                       | 100                                                                                                                                                                                                         |
| Numero massimo di nodi per cluster con set di scalabilità di macchine virtuali e [SKU di Load Balancer Standard][standard-load-balancer] | 1000 (in tutti i [pool di nodi)][node-pool]                                            |
| Numero massimo di pool di nodi per cluster                                                                                     | 100                                                                                  |
| Numero massimo di pod per nodo: [rete Basic][basic-networking] con Kubenet                                           | Massimo: 250 <br /> Impostazione predefinita dell'interfaccia della riga di comando di Azure: 110 <br /> Azure Resource Manager modello predefinito: 110 <br /> portale di Azure distribuzione predefinita: 30          |
| Numero massimo di pod per nodo: [rete Advanced][advanced-networking] con Azure Container Networking Interface        | Massimo: 250 <br /> Impostazione predefinita: 30                                                      |
| Aprire l'anteprima del componente aggiuntivo del servizio Web del servizio Web OSM (Open Service Mesh)                                                                          | Versione cluster Kubernetes: 1.19+<sup>1</sup><br />Controller OSM per cluster: 1<sup>1</sup><br />Pod per controller OSM: 500<sup>1</sup><br />Account del servizio Kubernetes gestiti da OSM: 50<sup>1</sup> |

<sup>1</sup> Il componente aggiuntivo OSM per il servizio Web Disaks è in uno stato di anteprima e verrà sottoposto a miglioramenti aggiuntivi prima della disponibilità generale. Durante la fase di anteprima è consigliabile non superare i limiti indicati.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
