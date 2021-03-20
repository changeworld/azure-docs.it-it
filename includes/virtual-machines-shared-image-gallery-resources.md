---
title: includere file
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bf01df2307796831dee602dad30455a4922ef90b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98873188"
---
| Risorsa | Descrizione|
|----------|------------|
| **Origine immagine** | Si tratta di una risorsa che è possibile usare per creare una **versione di immagine** in una raccolta di immagini. Un'origine dell'immagine può essere una macchina virtuale di Azure esistente [generalizzata o specializzata](../articles/virtual-machines/shared-image-galleries.md#generalized-and-specialized-images), un'immagine gestita, uno snapshot o una versione di immagine di un'altra raccolta immagini. |
| **Raccolta di immagini** | Come in Azure Marketplace, una **raccolta di immagini** è un repository per la gestione e la condivisione delle immagini, ma è possibile controllare chi ha accesso. |
| **Definizione delle immagini** | Le definizioni di immagini vengono create all'interno di una raccolta e forniscono informazioni sull'immagine e sui requisiti per il relativo uso interno. Questa include il fatto che l'immagine è per Windows o Linux, le note sulla versione e i requisiti minimi e massimi di memoria. Si tratta della definizione di un tipo di immagine. |
| **Versione dell'immagine** | La **versione dell'immagine** è ciò che si usa per creare una macchina virtuale quando si usa una raccolta. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Come un'immagine gestita, quando si usa una **versione dell'immagine** per creare una macchina virtuale, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale. Le versioni delle immagini possono essere usate più volte. |