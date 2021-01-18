---
title: File di inclusione
description: File di inclusione
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 04/30/2020
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: c6352ee9d29e4e45aa4be449046a0715fee06047
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165910"
---
## <a name="create-a-new-cdn-profile"></a>Creare un nuovo profilo di rete CDN

Un profilo di rete CDN è un contenitore per endpoint della rete CDN e specifica un piano tariffario.

1. In alto a sinistra nel portale di Azure selezionare **Crea una risorsa**. Viene visualizzato il riquadro **Nuovo**.
   
1. Cercare e selezionare **Rete CDN**, quindi selezionare **Crea**:
   
    ![Selezionare una risorsa della rete CDN](./media/cdn-create-profile/cdn-new-resource.png)

    Viene visualizzato il riquadro **Profilo rete CDN**.

1. Immettere i valori seguenti:
   
    | Impostazione  | valore |
    | -------- | ----- |
    | **Nome** | Immettere *cdn-profile-123* come nome del profilo. Il nome deve essere univoco a livello globale. Se è già in uso, immettere un nome diverso. |
    | **Sottoscrizione** | Selezionare una sottoscrizione di Azure dall'elenco a discesa. |
    | **Gruppo di risorse** | Selezionare **Crea nuovo** e immettere *CDNQuickstart-rg* come nome del gruppo di risorse oppure selezionare **Usa esistente** e scegliere *CDNQuickstart-rg* se il gruppo è già disponibile. | 
    | **Località del gruppo di risorse** | Selezionare una località vicina nell'elenco a discesa. |
    | **Piano tariffario** | Selezionare l'opzione **Akamai standard** nell'elenco a discesa. Il tempo di distribuzione per il livello Akamai è di circa un minuto. Il livello Microsoft richiede circa 10 minuti e il livello Verizon circa 30 minuti. |
    | **Crea un nuovo endpoint rete CDN ora** | Non selezionare alcun elemento. |  
   
    ![Nuovo profilo di rete CDN](./media/cdn-create-profile/cdn-new-profile.png)

1. Selezionare **Crea** per creare il profilo.

