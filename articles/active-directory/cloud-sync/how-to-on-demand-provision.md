---
title: Provisioning su richiesta in Azure AD Connect sincronizzazione cloud
description: Questo articolo descrive come usare la funzionalità di sincronizzazione cloud di Azure AD Connect per testare le modifiche alla configurazione.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048b78c7d59b3358dbffe2e3e6eedf41decabb8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554276"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Provisioning su richiesta in Azure AD Connect sincronizzazione cloud

È possibile usare la funzionalità di sincronizzazione cloud di Azure Active Directory (Azure AD) Connect per testare le modifiche della configurazione applicando queste modifiche a un singolo utente. Il provisioning su richiesta consente di convalidare e verificare che le modifiche apportate alla configurazione siano state applicate correttamente e siano sincronizzate correttamente con Azure AD.  

> [!IMPORTANT] 
> Quando si usa il provisioning su richiesta, i filtri di ambito non vengono applicati all'utente selezionato. È possibile usare il provisioning su richiesta su utenti esterni alle unità organizzative specificate.

## <a name="validate-a-user"></a>Convalidare un utente
Per usare il provisioning su richiesta, seguire questa procedura:

1.  Nel portale di Azure selezionare **Azure Active Directory**.
2.  Selezionare **Azure AD Connect**.
3.  Selezionare **Gestisci sincronizzazione cloud**.

    ![Screenshot che mostra il collegamento per la gestione della sincronizzazione cloud.](media/how-to-install/install-6.png)
4. In **configurazione** selezionare la configurazione.
5. In **convalida** selezionare il pulsante Esegui il **provisioning di un utente** . 

   ![Screenshot che mostra il pulsante per il provisioning di un utente.](media/how-to-on-demand-provision/on-demand-2.png)

6. Nella schermata **provisioning on demand** immettere il nome distinto di un utente e selezionare il pulsante **provision (provisioning** ).  
 
   ![Screenshot che mostra un nome utente e un pulsante di provisioning.](media/how-to-on-demand-provision/on-demand-3.png)
7. Al termine del provisioning, viene visualizzata una schermata operazione completata con quattro segni di spunta verdi. Tutti gli errori vengono visualizzati a sinistra.

   ![Screenshot che mostra il provisioning riuscito.](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>Ottenere informazioni dettagliate sul provisioning
A questo punto è possibile esaminare le informazioni dell'utente e determinare se le modifiche apportate nella configurazione sono state applicate. Nella parte restante di questo articolo vengono descritte le singole sezioni visualizzate nei dettagli di un utente sincronizzato correttamente.

### <a name="import-user"></a>Importa utente
La sezione **Importa utente** fornisce informazioni sull'utente importato da Active Directory. Si tratta di un aspetto simile a quello dell'utente prima del provisioning in Azure AD. Selezionare il collegamento **Visualizza dettagli** per visualizzare queste informazioni.

![Screenshot del pulsante per visualizzare i dettagli relativi a un utente importato.](media/how-to-on-demand-provision/on-demand-5.png)

Utilizzando queste informazioni, è possibile visualizzare i vari attributi (e i relativi valori) che sono stati importati. Se è stato creato un mapping di attributi personalizzati, è possibile visualizzare il valore qui.

![Screenshot che mostra i dettagli dell'utente.](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>Determinare se l'utente è nell'ambito
La sezione **determinare se l'utente è incluso nell'ambito** fornisce informazioni sul fatto che l'utente che è stato importato in Azure ad sia incluso nell'ambito. Selezionare il collegamento **Visualizza dettagli** per visualizzare queste informazioni.

![Screenshot del pulsante per visualizzare i dettagli sull'ambito utente.](media/how-to-on-demand-provision/on-demand-7.png)

Utilizzando queste informazioni, è possibile verificare se l'utente è nell'ambito.

![Screenshot che mostra i dettagli dell'ambito dell'utente.](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>Corrisponde all'utente tra il sistema di origine e quello di destinazione
La sezione **utente corrispondente tra il sistema di origine e** quello di destinazione fornisce informazioni sul fatto che l'utente esista già nel Azure ad e se deve verificarsi un join anziché eseguire il provisioning di un nuovo utente. Selezionare il collegamento **Visualizza dettagli** per visualizzare queste informazioni.

![Screenshot del pulsante per visualizzare i dettagli relativi a un utente corrispondente.](media/how-to-on-demand-provision/on-demand-8.png)

Utilizzando queste informazioni, è possibile verificare se è stata trovata una corrispondenza o se è in corso la creazione di un nuovo utente.

![Screenshot che mostra le informazioni utente.](media/how-to-on-demand-provision/on-demand-11.png)

I dettagli corrispondenti mostrano un messaggio con una delle tre operazioni seguenti:
- **Crea**: viene creato un utente in Azure ad.
- **Aggiornamento**: un utente viene aggiornato in base a una modifica apportata alla configurazione.
- **Delete**: un utente viene rimosso dal Azure ad.

A seconda del tipo di operazione eseguita, il messaggio può variare.

### <a name="perform-action"></a>Azione da eseguire
La sezione **Esegui azione** fornisce informazioni sull'utente di cui è stato effettuato il provisioning o l'esportazione in Azure ad dopo l'applicazione della configurazione. Questo è l'aspetto dell'utente dopo il provisioning in Azure AD. Selezionare il collegamento **Visualizza dettagli** per visualizzare queste informazioni.

![Screenshot del pulsante per visualizzare i dettagli relativi a un'azione eseguita.](media/how-to-on-demand-provision/on-demand-9.png)

Utilizzando queste informazioni, è possibile visualizzare i valori degli attributi dopo l'applicazione della configurazione. Hanno un aspetto simile a quello che è stato importato o sono diversi? La configurazione è stata applicata correttamente?  

Questo processo consente di tracciare la trasformazione dell'attributo mentre viene spostata nel cloud e nel tenant del Azure AD.

![Screenshot che mostra i dettagli degli attributi tracciati.](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>Passaggi successivi 

- [Che cos'è Azure AD Connect sincronizzazione cloud?](what-is-cloud-sync.md)
- [Installare Azure AD Connect sincronizzazione cloud](how-to-install.md)
 