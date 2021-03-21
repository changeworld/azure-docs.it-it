---
title: Eliminazioni accidentali Azure AD Connect sincronizzazione cloud
description: In questo argomento viene descritto come utilizzare la funzionalità di eliminazione accidentale per impedire le eliminazioni.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da54bd28c1d9ea933e88b6c86cf6092c10d036a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785186"
---
# <a name="accidental-delete-prevention"></a>Prevenzione eliminazioni accidentali

Nel documento seguente viene descritta la funzionalità di eliminazione accidentale per Azure AD Connect sincronizzazione cloud.  La funzionalità di eliminazione accidentale è progettata per proteggere le modifiche accidentali alla configurazione e le modifiche apportate alla directory locale che interessano molti utenti e gruppi.  Questa funzionalità consente di:

- configurare la possibilità di impedire eliminazioni accidentali automaticamente. 
- Imposta il numero di oggetti (soglia) oltre i quali la configurazione diverrà effettiva 
- configurare un indirizzo di posta elettronica di notifica in modo che possano ricevere una notifica tramite posta elettronica quando il processo di sincronizzazione in questione viene messo in quarantena per questo scenario 

Per utilizzare questa funzionalità, è necessario impostare la soglia per il numero di oggetti che, se eliminati, la sincronizzazione dovrebbe arrestarsi.  Se quindi si raggiunge questo numero, la sincronizzazione verrà arrestata e verrà inviata una notifica al messaggio di posta elettronica specificato.  Questa notifica consentirà di analizzare le attività in corso.


## <a name="configure-accidental-delete-prevention"></a>Configurare la prevenzione accidentale dell'eliminazione
Per usare la nuova funzionalità, attenersi alla procedura riportata di seguito.


1.  Nel portale di Azure selezionare **Azure Active Directory**.
2.  Selezionare **Azure AD Connect**.
3.  Selezionare **Gestisci sincronizzazione cloud**.
4. In **configurazione** selezionare la configurazione.
5. In **Impostazioni** compilare quanto segue:
    - **Posta elettronica di notifica** -posta elettronica usata per le notifiche
    - **Impedisci eliminazioni accidentali** -selezionare questa casella per abilitare la funzionalità
    - **Soglia di eliminazione accidentale** : immettere il numero di oggetti per arrestare la sincronizzazione e inviare una notifica

![Eliminazioni accidentali](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>Ripristino da un'istanza Delete accidentale
Se si verifica un'eliminazione accidentale, questo verrà visualizzato sullo stato della configurazione dell'agente di provisioning.  Si dirà che la soglia di eliminazione è stata **superata**.
 
![Stato di eliminazione accidentale](media/how-to-accidental-deletes/delete-1.png)

Se si fa clic su **Elimina soglia superata**, vengono visualizzate le informazioni sullo stato di sincronizzazione.  Questo fornirà ulteriori dettagli. 
 
 ![Stato di sincronizzazione](media/how-to-accidental-deletes/delete-2.png)

Facendo clic con il pulsante destro del mouse sui puntini di sospensione, si otterranno le opzioni seguenti:
 - Visualizza log di provisioning
 - Visualizzare Agent
 - Consenti eliminazioni

 ![Fare clic](media/how-to-accidental-deletes/delete-3.png)

Utilizzando **Visualizza log di provisioning**, è possibile visualizzare le voci **StagedDelete** ed esaminare le informazioni fornite sugli utenti che sono stati eliminati.
 
 ![Log di provisioning](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>Consentire le eliminazioni

L'azione **Consenti eliminazioni** eliminerà gli oggetti che hanno attivato la soglia di eliminazione accidentale.  Utilizzare la procedura seguente per accettare le eliminazioni.  

1. Fare clic con il pulsante destro del mouse sui puntini di sospensione e selezionare **Consenti eliminazioni**.
2. Fare clic su **Sì** nella conferma per consentire le eliminazioni.
 
 ![Sì alla conferma](media/how-to-accidental-deletes/delete-4.png)

3. Verrà visualizzata la conferma che le eliminazioni sono state accettate e lo stato tornerà a integro con il ciclo successivo. 
 
 ![Accetta eliminazioni](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>Rifiuto delle eliminazioni

Se non si desidera consentire le eliminazioni, è necessario eseguire le operazioni seguenti:
- esaminare l'origine delle eliminazioni
- risolvere il problema (ad esempio, l'unità organizzativa è stata spostata fuori dall'ambito accidentalmente ed è stata nuovamente aggiunta all'ambito)
- Esegui **riavvio sincronizzazione** sulla configurazione dell'agente

## <a name="next-steps"></a>Passaggi successivi 

- [Azure AD Connect la risoluzione dei problemi di sincronizzazione cloud?](how-to-troubleshoot.md)
- [Codici di errore Azure AD Connect Cloud Sync](reference-error-codes.md)
 

