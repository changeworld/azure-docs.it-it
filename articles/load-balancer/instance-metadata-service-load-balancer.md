---
title: Recuperare le informazioni del servizio di bilanciamento del carico usando il servizio metadati dell'istanza di Azure
titleSuffix: Azure Load Balancer
description: Inizia a imparare a usare il servizio metadati dell'istanza di Azure per recuperare le informazioni del servizio di bilanciamento del carico.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 0d7d08eb5e77e744fb6ce0abefc550bc79de4c8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519083"
---
# <a name="retrieve-load-balancer-information-by-using-the-azure-instance-metadata-service"></a>Recuperare le informazioni del servizio di bilanciamento del carico usando il servizio metadati dell'istanza di Azure

IMDS (servizio metadati dell'istanza di Azure) fornisce informazioni sulle istanze di macchine virtuali attualmente in esecuzione. Il servizio è un'API REST disponibile in un indirizzo IP non instradabile noto (169.254.169.254). 

Quando si inseriscono le istanze di macchine virtuali o set di macchine virtuali dietro una Load Balancer Standard di Azure, usare IMDS per recuperare i metadati relativi al servizio di bilanciamento del carico e alle istanze.

I metadati includono le informazioni seguenti per le macchine virtuali o i set di scalabilità di macchine virtuali:

* IP pubblico dello SKU standard.
* Configurazioni delle regole in ingresso del servizio di bilanciamento del carico di ogni IP privato dell'interfaccia di rete.
* Configurazioni delle regole in uscita del servizio di bilanciamento del carico di ogni IP privato dell'interfaccia di rete.

## <a name="access-the-load-balancer-metadata-using-the-imds"></a>Accedere ai metadati del servizio di bilanciamento del carico usando IMDS

Per altre informazioni su come accedere ai metadati del servizio di bilanciamento del carico, vedere [usare il servizio metadati dell'istanza di Azure per accedere alle informazioni del servizio di bilanciamento del carico](howto-load-balancer-imds.md).

## <a name="troubleshoot-common-error-codes"></a>Risolvere i problemi relativi ai codici di errore comuni

Per altre informazioni sui codici di errore comuni e sui relativi metodi di mitigazione, vedere [risolvere i problemi relativi ai codici di errore comuni quando si usa IMDS](troubleshoot-load-balancer-imds.md). 

## <a name="support"></a>Supporto

Se non si è in grado di recuperare una risposta ai metadati dopo più tentativi, creare un problema di supporto nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sul [servizio metadati dell'istanza di Azure](../virtual-machines/windows/instance-metadata-service.md)

[Distribuire un servizio di bilanciamento del carico standard](quickstart-load-balancer-standard-public-portal.md)

