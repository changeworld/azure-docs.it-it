---
title: includere file
description: File di inclusione
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 1c2dec106ae72ddead7bda54792fa74e38eb6660
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081118"
---
| Risorsa                                                                                                           | Limite                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Numero massimo di cluster per sottoscrizione                                                                                  | 1000                                                                                                                                                                                                        |
| Numero massimo di nodi per cluster con set di disponibilità di macchine virtuali e SKU Basic di Load Balancer                       | 100                                                                                                                                                                                                         |
| Numero massimo di nodi per cluster con set di scalabilità di macchine virtuali e [SKU di Load Balancer Standard][standard-load-balancer] | 1000 (100 nodi per [pool di nodi][node-pool])                                                                                                                                                                 |
| Numero massimo di pod per nodo: [rete Basic][basic-networking] con Kubenet                                           | 110                                                                                                                                                                                                         |
| Numero massimo di pod per nodo: [rete Advanced][advanced-networking] con Azure Container Networking Interface        | Distribuzione dell'interfaccia della riga di comando di Azure: 30<sup>1</sup><br />Modello di Azure Resource Manager: 30<sup>1</sup><br />Distribuzione portale: 30                                                                                        |
| Apri la versione di anteprima del servizio di rete (OSM) AKS addon                                                                          | Versione del cluster Kubernetes: 1.19 +<sup>2</sup><br />Controller OSM per cluster: 1<sup>2</sup><br />Pod per controller OSM: 500<sup>2</sup><br />Account del servizio Kubernetes gestiti da OSM: 50<sup>2</sup> |

<sup>1</sup>Quando si distribuisce un cluster del servizio Azure Kubernetes con l'interfaccia della riga di comando di Azure o con un modello di Resource Manager, è possibile configurare questo valore fino a un massimo di 250 pod per nodo. Se un cluster del servizio Azure Kubernetes è già stato distribuito o si distribuisce un cluster con il portale di Azure, non è possibile configurare il numero massimo di pod per nodo.<br />

<sup>2</sup> Il componente aggiuntivo OSM per AKS è in uno stato di anteprima e sarà sottoposto a ulteriori miglioramenti prima della disponibilità generale (GA). Durante la fase di anteprima è consigliabile non superare i limiti indicati.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
