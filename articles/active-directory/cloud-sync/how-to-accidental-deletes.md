---
title: Eliminazioni accidentali Azure AD Connect sincronizzazione cloud
description: In questo argomento viene descritto come utilizzare la funzionalità di eliminazione accidentale per impedire le eliminazioni.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ef7b6d9495b1431e03808b830671e839b90d436
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613800"
---
# <a name="accidental-delete-prevention"></a>Prevenzione eliminazioni accidentali

Nel documento seguente viene descritta la funzionalità di eliminazione accidentale per Azure AD Connect sincronizzazione cloud.  La funzionalità di eliminazione accidentale è progettata per proteggere le modifiche accidentali alla configurazione e le modifiche apportate alla directory locale che interessano molti utenti e gruppi.  Questa funzionalità consente di:

- configurare la possibilità di impedire eliminazioni accidentali automaticamente. 
- Imposta il numero di oggetti (soglia) oltre i quali la configurazione diverrà effettiva 
- consente di configurare un indirizzo di posta elettronica di notifica in modo da ricevere una notifica tramite posta elettronica quando il processo di sincronizzazione in questione viene messo in quarantena per questo scenario 

Per utilizzare questa funzionalità, è necessario impostare la soglia per il numero di oggetti che, se eliminati, la sincronizzazione dovrebbe arrestarsi.  Se quindi si raggiunge questo numero, la sincronizzazione verrà arrestata e verrà inviata una notifica al messaggio di posta elettronica specificato.  In questo modo è possibile analizzare le attività in corso.


## <a name="configure-accidental-delete-prevention"></a>Configurare la prevenzione accidentale dell'eliminazione
Per usare la nuova funzionalità, attenersi alla procedura riportata di seguito.


1.  Nel portale di Azure selezionare **Azure Active Directory**.
2.  Selezionare **Azure AD Connect**.
3.  Selezionare **Gestisci sincronizzazione cloud**.
4. In **configurazione** selezionare la configurazione.
5. In **Impostazioni** compilare quanto segue:
    - **Posta elettronica di notifica** -posta elettronica usata per le notifiche
    - **Impedisci eliminazioni accidentali** -selezionare questa casella per abilitare la funzionalità
    - **Soglia di eliminazione accidentale** : immettere un numero di oggetti per attivare l'arresto e la notifica della sincronizzazione

![Eliminazioni accidentali](media/how-to-accidental-deletes/accident-1.png)

## <a name="next-steps"></a>Passaggi successivi 

- [Che cos'è Azure AD Connect sincronizzazione cloud?](what-is-cloud-sync.md)
- [Come installare Azure AD Connect sincronizzazione cloud](how-to-install.md)
 

