---
title: Configurazione del nuovo agente di sincronizzazione Cloud Azure AD Connect
description: Questo articolo descrive come installare la sincronizzazione cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eacf3cad0a610ae75f38dbb6f1bd7bdab816d54
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613789"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>Crea una nuova configurazione per Azure AD Connect sincronizzazione cloud

Dopo aver installato l'agente di provisioning di Azure AD Connect, è necessario accedere al portale di Azure e configurarlo. Per abilitare l'agente, attenersi alla seguente procedura.

## <a name="configure-provisioning"></a>Configurare il provisioning
Per configurare il provisioning, attenersi alla seguente procedura.

 1. Nella portale di Azure selezionare **Azure Active Directory**
 2. Selezionare **Azure AD Connect**.
 3. Selezionare **Gestisci sincronizzazione cloud**.

 ![Gestire il provisioning](media/how-to-install/install-6.png)
 
 4. Selezionare **nuova configurazione**.
 5. Nella schermata Configurazione selezionare il dominio e se abilitare la sincronizzazione dell'hash delle password.  Fare clic su **Crea**.  
 
 ![Creare una nuova configurazione](media/how-to-configure/configure-1.png)


 6.  Viene visualizzata la schermata Modifica configurazione di provisioning.

   ![Modifica configurazione](media/how-to-configure/con-1.png)

 7. Immettere un **messaggio di posta elettronica di notifica**. Questo messaggio di posta elettronica riceverà una notifica quando il provisioning non è integro.  È consigliabile **evitare l'eliminazione accidentale** abilitata e impostare la **soglia di eliminazione accidentale** su un numero a cui si desidera ricevere una notifica.  Per ulteriori informazioni, vedere [eliminazioni accidentali](#accidental-deletions) di seguito.
 8. Spostare il selettore per abilitare e selezionare Salva.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Provisioning dell'ambito per utenti e gruppi specifici
È possibile definire l'ambito dell'agente per sincronizzare utenti e gruppi specifici usando gruppi di Active Directory locali o unità organizzative. Non è possibile configurare gruppi e unità organizzative all'interno di una configurazione. 

 1.  Nel portale di Azure selezionare **Azure Active Directory**.
 2. Selezionare **Azure AD Connect**.
 3. Selezionare **Gestisci sincronizzazione cloud**.
 4. In **configurazione** selezionare la configurazione.

 ![Sezione Configurazione](media/how-to-configure/scope-1.png)
 
 5. In **Configura** selezionare **Modifica filtri di ambito** per modificare l'ambito della regola di configurazione.
 ![Modifica ambito](media/how-to-configure/scope-3.png)
 7. A destra è possibile modificare l'ambito.  Al termine, fare clic su **fine**  e su **Salva** .
 8. Dopo aver modificato l'ambito, è necessario [riavviare il provisioning](#restart-provisioning) per avviare una sincronizzazione immediata delle modifiche.

## <a name="attribute-mapping"></a>Mapping degli attributi
Azure AD Connect sincronizzazione cloud consente di eseguire facilmente il mapping degli attributi tra gli oggetti utente/gruppo locali e gli oggetti in Azure AD.  È possibile personalizzare i mapping degli attributi predefiniti in base alle esigenze aziendali. Quindi è possibile modificare o eliminare i mapping degli attributi esistenti oppure crearne di nuovi.  Per ulteriori informazioni, vedere [mapping degli attributi](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Provisioning su richiesta
Azure AD Connect sincronizzazione cloud consente di testare le modifiche alla configurazione, applicando le modifiche a un singolo utente o gruppo.  È possibile usarlo per convalidare e verificare che le modifiche apportate alla configurazione siano state applicate correttamente e siano sincronizzate correttamente con Azure AD.  Per altre informazioni, vedere [provisioning su richiesta](how-to-on-demand-provision.md).

## <a name="accidental-deletions"></a>Eliminazioni accidentali
La funzionalità di eliminazione accidentale è progettata per proteggere le modifiche accidentali alla configurazione e le modifiche apportate alla directory locale che interessano molti utenti e gruppi.  Questa funzionalità consente di:

- configurare la possibilità di impedire eliminazioni accidentali automaticamente. 
- Imposta il numero di oggetti (soglia) oltre i quali la configurazione diverrà effettiva 
- consente di configurare un indirizzo di posta elettronica di notifica in modo da ricevere una notifica tramite posta elettronica quando il processo di sincronizzazione in questione viene messo in quarantena per questo scenario 

Per altre informazioni, vedere [eliminazioni accidentali](how-to-accidental-deletes.md)

## <a name="quarantines"></a>Mette
Cloud Sync monitora l'integrità della configurazione e inserisce oggetti non integri in uno stato di quarantena. Se la maggior parte o tutte le chiamate effettuate sul sistema di destinazione hanno esito negativo a causa di un errore, ad esempio, credenziali di amministratore non valide, il processo di sincronizzazione è contrassegnato come in quarantena.  Per ulteriori informazioni, vedere la sezione relativa alla risoluzione dei problemi in [quarantena](how-to-troubleshoot.md#provisioning-quarantined-problems).

## <a name="restart-provisioning"></a>Riavviare il provisioning 
Se non si vuole attendere la successiva esecuzione pianificata, attivare l'esecuzione del provisioning usando il pulsante **Riavvia provisioning** . 
 1.  Nel portale di Azure selezionare **Azure Active Directory**.
 2. Selezionare **Azure AD Connect**.
 3.  Selezionare **Gestisci sincronizzazione cloud**.
 4. In **configurazione** selezionare la configurazione.

   ![Selezione della configurazione per riavviare il provisioning](media/how-to-configure/scope-1.png)

 5. Nella parte superiore selezionare **Riavvia provisioning**.

## <a name="remove-a-configuration"></a>Rimuovere una configurazione
Per eliminare una configurazione, attenersi alla seguente procedura.

 1.  Nel portale di Azure selezionare **Azure Active Directory**.
 2. Selezionare **Azure AD Connect**.
 3. Selezionare **Gestisci sincronizzazione cloud**.
 4. In **configurazione** selezionare la configurazione.
   
   ![Selezione della configurazione per rimuovere la configurazione](media/how-to-configure/scope-1.png)

 5. Nella parte superiore della schermata di configurazione selezionare **Elimina**.

>[!IMPORTANT]
>Non vi è alcuna conferma prima di eliminare una configurazione. Assicurarsi che si tratta dell'azione che si desidera eseguire prima di selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è Azure AD Connect sincronizzazione cloud?](what-is-cloud-sync.md)
