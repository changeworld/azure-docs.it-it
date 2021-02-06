---
title: Ottimizzazione di Visual Studio per Azure Service Fabric mesh
description: Questo articolo illustra come ottimizzare le prestazioni di Visual Studio per i progetti Service Fabric Mesh in modo che l'esecuzione del primo debug (F5) sia molto più rapida.
author: georgewallace
ms.author: gwallace
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: e676286dca852c94f94e8bd8ff2ab73ee92b1412
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625753"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Ottimizzare le prestazioni di Visual Studio per i progetti Azure Service Fabric Mesh

> [!IMPORTANT]
> L'anteprima di Azure Service Fabric mesh è stata ritirata. Le nuove distribuzioni non saranno più consentite tramite l'API Service Fabric mesh. Il supporto per le distribuzioni esistenti continuerà fino al 28 aprile 2021.
> 
> Per informazioni dettagliate, vedere il [ritiro anteprima di Azure Service Fabric mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Questo articolo illustra come ottimizzare le prestazioni di Visual Studio per i progetti Service Fabric Mesh in modo che l'esecuzione del primo debug (F5) sia molto più rapida.  

## <a name="change-visual-studio-settings"></a>Cambiare le impostazioni di Visual Studio
 
In Visual Studio, in **strumenti**  >  **Opzioni**   >  **Service Fabric Mesh Tools**  >  **generale**, è possibile modificare le impostazioni seguenti:

- **Pull required Docker images on project open** (Esegui il pull delle immagini Docker necessarie all'apertura del progetto) velocizza la prima esecuzione del debug (F5) avviando il processo di download delle immagini durante il caricamento del progetto.  
- **Deploy application on project open** (Distribuisci l'applicazione all'apertura del progetto) può velocizzare la prima esecuzione del debug (F5) avviando il processo di distribuzione dopo l'apertura del progetto.  
- **Remove application on project close** (Rimuovi l'applicazione alla chiusura del progetto) recupera le risorse (CPU, RAM) allocate all'app rimuovendo l'app Mesh quando il progetto viene chiuso.  

Se nella finestra di output di Strumenti di Service Fabric vengono visualizzati messaggi che indicano che Visual Studio sta eseguendo il pull delle immagini, si sta preparando o sta rimuovendo l'applicazione, si riferiscono alle impostazioni descritte sopra. È possibile disattivare queste impostazioni.

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: eseguire il debug di un'applicazione Azure Service Fabric Mesh in esecuzione nel cluster di sviluppo locale](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)