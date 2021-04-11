---
title: Gestire i gruppi per il Marketplace commerciale-Azure Marketplace
description: Informazioni su come gestire i gruppi nel programma Commercial Marketplace nel centro per i partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: aa75082142aa5735350cd7fed30194314b6e7368
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108337"
---
# <a name="manage-groups-for-the-commercial-marketplace"></a>Gestire i gruppi per il Marketplace commerciale

I gruppi consentono di controllare contemporaneamente più ruoli utente e autorizzazioni.

## <a name="add-an-existing-group"></a>Aggiungere un gruppo esistente

Per aggiungere un gruppo già esistente nell'account aziendale dell'organizzazione (tenant di Azure AD) all'account del Centro per i partner:

1. Nella pagina **Utenti** (in **Impostazioni account**) selezionare **Aggiungi gruppi**.
1. Selezionare uno o più gruppi dall'elenco visualizzato. È possibile usare la casella di ricerca per cercare gruppi specifici. Se si seleziona più di un gruppo da aggiungere all'account del Centro per i partner, è necessario assegnare loro lo stesso ruolo o set di autorizzazioni personalizzate. Per aggiungere più gruppi con ruoli o autorizzazioni diversi, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
1. Al termine della scelta di gruppi, selezionare **Aggiungi selezionato**.
1. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per i gruppi selezionati. Tutti i membri del gruppo potranno accedere all'account del Centro per i partner con le autorizzazioni applicate al gruppo, indipendentemente dai ruoli e dalle autorizzazioni associati ai rispettivi account.
1. Selezionare **Salva**.

Quando si aggiunge un gruppo esistente, ogni utente membro di tale gruppo potrà accedere all'account del Centro per i partner, con le autorizzazioni associate al ruolo assegnato del gruppo.

## <a name="add-a-new-group"></a>Aggiungere un nuovo gruppo

Per aggiungere un nuovo gruppo all'account del Centro per i partner:

1. Nella pagina **Utenti** (in **Impostazioni account**) selezionare **Aggiungi gruppi**.
1. Nella pagina successiva selezionare **Nuovo gruppo**.
1. Immettere il nome visualizzato del nuovo gruppo.
1. Specificare i ruoli o le autorizzazioni personalizzate per il gruppo. Tutti i membri del gruppo potranno accedere all'account del Centro per i partner con le autorizzazioni applicate qui, indipendentemente dai ruoli o dalle autorizzazioni associati ai rispettivi account.
1. Selezionare gli utenti per il nuovo gruppo nell'elenco visualizzato. È possibile usare la casella di ricerca per cercare utenti specifici.
1. Al termine della selezione degli utenti, selezionare **Aggiungi selezionati** per aggiungerli al nuovo gruppo.
1. Selezionare **Salva**.

Questo nuovo gruppo verrà creato anche nell'account di lavoro dell'organizzazione (tenant Azure AD), non solo nell'account del centro per i partner.

## <a name="remove-a-group"></a>Rimuovere un gruppo

Per rimuovere un gruppo dal proprio account aziendale (Azure AD tenant):
1. Passare a **utenti** (in **Impostazioni account**).
1. Selezionare il gruppo che si vuole rimuovere usando la casella di controllo nella colonna all'estrema destra.
1. Scegliere **Rimuovi** tra le azioni disponibili. Verrà visualizzata una finestra popup in cui confermare che si vogliono rimuovere i gruppi selezionati.
