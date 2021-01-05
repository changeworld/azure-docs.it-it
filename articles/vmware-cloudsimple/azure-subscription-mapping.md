---
title: Creare pool di risorse con il mapping della sottoscrizione di Azure
titleSuffix: Azure VMware Solution by CloudSimple
description: Viene descritto come creare pool di risorse per il cloud privato tramite il mapping delle sottoscrizioni di Azure
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7be4a4c601d3f33972c1e52596ef623116dcadd4
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97897077"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Creare pool di risorse per il cloud privato con mapping della sottoscrizione di Azure
Il mapping delle sottoscrizioni di Azure consente di creare pool di risorse per il cloud privato dai pool di risorse vSphere disponibili. Nel portale di CloudSimple è possibile visualizzare e gestire la sottoscrizione di Azure per i cloud privati.

> [!NOTE]
> Il mapping di un pool di risorse esegue anche il mapping dei pool di risorse figlio. Impossibile eseguire il mapping di un pool di risorse padre se è già stato eseguito il mapping di un pool di risorse figlio.

1. [Accedere al portale di CloudSimple](access-cloudsimple-portal.md).
2. Aprire la pagina **risorse** e selezionare **mapping sottoscrizioni di Azure**.  
3. Fare clic su **Modifica mapping della sottoscrizione di Azure**.  
4. Per mappare i pool di risorse disponibili, selezionarli a sinistra e fare clic sulla freccia rivolta verso destra. 
5. Per rimuovere i mapping, selezionarli a destra e fare clic sulla freccia rivolta verso sinistra. 

    ![Sottoscrizioni di Azure](media/resources-azure-mapping.png)

6. Fare clic su **OK**.
