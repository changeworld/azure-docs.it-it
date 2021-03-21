---
title: Aggiornare un cluster Azure Red Hat OpenShift
description: Informazioni su come aggiornare un cluster Azure Red Hat OpenShift che esegue OpenShift 4
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 1/10/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, red hat, cli
ms.openlocfilehash: 742da12bd3a10cd1f541e9c43f654cfe7df04340
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720886"
---
# <a name="upgrade-an-azure-red-hat-openshift-aro-cluster"></a>Aggiornare un cluster di Azure Red Hat OpenShift (ARO)

Parte del ciclo di vita del cluster ARO comporta l'esecuzione di aggiornamenti periodici alla versione più recente di OpenShift. È importante applicare le versioni di sicurezza più recenti o eseguire l'aggiornamento per ottenere le funzionalità più recenti. Questo articolo illustra come aggiornare tutti i componenti in un cluster OpenShift usando la console Web di OpenShift.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede la versione dell'interfaccia della riga di comando di Azure 2.0.65 di seguito. Eseguire `az --version` per trovare la versione corrente. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Questo articolo presuppone che l'utente abbia accesso a un cluster Azure Red Hat OpenShift esistente come utente con `admin` privilegi.

## <a name="check-for-available-aro-cluster-upgrades"></a>Verifica disponibilità aggiornamenti cluster ARO

Dalla console Web di OpenShift selezionare **Amministrazione**  >  **Impostazioni cluster** e aprire la scheda **Dettagli** .

Se lo **stato di aggiornamento** per il cluster riflette **gli aggiornamenti disponibili**, è possibile aggiornare il cluster.

## <a name="upgrade-your-aro-cluster"></a>Aggiornare il cluster ARO

Dalla console Web nel passaggio precedente, impostare il **canale** sul canale corretto per la versione di cui si vuole eseguire l'aggiornamento, ad esempio `stable-4.5` .

Selezionare una versione di cui eseguire l'aggiornamento e selezionare **Aggiorna**. Si noterà che lo stato dell'aggiornamento è stato modificato in: `Update to <product-version> in progress` . È possibile esaminare lo stato di avanzamento dell'aggiornamento del cluster osservando gli indicatori di stato per gli operatori e i nodi.

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni su come aggiornare un cluster ARO usando l'interfaccia della riga di comando OC](https://docs.openshift.com/container-platform/4.6/updating/updating-cluster-between-minor.html)
- Per informazioni sugli avvisi e sugli aggiornamenti della piattaforma OpenShift container disponibili, vedere la [sezione relativa agli errori](https://access.redhat.com/downloads/content/290/ver=4.6/rhel---8/4.6.0/x86_64/product-errata) nel portale per i clienti.
