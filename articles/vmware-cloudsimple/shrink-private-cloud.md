---
title: Compatta la soluzione VMware di Azure di CloudSimple cloud privato
description: Informazioni su come compattare dinamicamente un cloud privato in CloudSimple rimuovendo un nodo da un cluster vSphere esistente o rimuovendo un intero cluster.
author: Ajayan1008
ms.author: v-hborys
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a99b9b56f17b78a98f37d47dcefab26dd9c859de
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899134"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Compattare un cloud privato CloudSimple

CloudSimple offre la flessibilità necessaria per compattare dinamicamente un cloud privato.  Un cloud privato è costituito da uno o più cluster vSphere. Ogni cluster può avere da 3 a 16 nodi. Quando si compatta un cloud privato, si rimuove un nodo dal cluster esistente o si elimina un intero cluster. 

## <a name="before-you-begin"></a>Prima di iniziare

Per la compattazione di un cloud privato devono essere soddisfatte le condizioni seguenti.  Non è possibile eliminare il cluster di gestione (primo cluster) creato quando è stato creato un cloud privato.

* Un cluster vSphere deve avere tre nodi.  Un cluster con solo tre nodi non può essere compattato.
* Lo spazio di archiviazione totale utilizzato non deve superare la capacità totale dopo la compattazione del cluster.
* Controllare se le regole DRS (Distributed Resource Scheduler) impediscono vMotion di una macchina virtuale.  Se sono presenti regole, disabilitare o eliminare le regole.  Le regole DRS includono le regole di affinità da macchina virtuale a host.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Ridurre un cloud privato

1. [Accedere al portale di CloudSimple](access-cloudsimple-portal.md).

2. Aprire la pagina **risorse** .

3. Fare clic sul cloud privato che si vuole compattare

4. Nella pagina Riepilogo fare clic su **compatta**.

    ![Compatta cloud privato](media/shrink-private-cloud.png)

5. Selezionare il cluster che si desidera compattare o eliminare. 

    ![Compattazione del cloud privato: selezionare un cluster](media/shrink-private-cloud-select-cluster.png)

6. Selezionare **Rimuovi un nodo** o **Elimina l'intero cluster**. 

7. Verificare la capacità del cluster

8. Fare clic su **Invia** per compattare il cloud privato.

Viene avviata la compattazione del cloud privato.  È possibile monitorare lo stato di avanzamento nelle attività.  Il processo di compattazione può richiedere alcune ore a seconda dei dati, che devono essere risincronizzati in rete VSAN.

> [!NOTE]
> 1. Se si riduce un cloud privato eliminando l'ultimo o l'unico cluster nel Data Center, il Data Center non verrà eliminato.
> 2. Se si verifica una violazione della regola DRS, il nodo non verrà rimosso dal cluster e la descrizione dell'attività indica che la rimozione di un nodo violerà le regole DRS sul cluster.    


## <a name="next-steps"></a>Passaggi successivi

* [Usare VM di VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Vedere le informazioni sui [cloud privati](cloudsimple-private-cloud.md)
