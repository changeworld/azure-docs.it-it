---
title: Provisioning su richiesta Azure AD Connect sincronizzazione cloud
description: Questo articolo descrive la funzionalità di provisioning su richiesta.
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
ms.openlocfilehash: 6ac186d4b460165605ccf0fc53bdb0b691348bf3
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622525"
---
# <a name="azure-ad-connect-cloud-sync-on-demand-provisioning"></a>Provisioning su richiesta Azure AD Connect sincronizzazione cloud

In Azure AD Connect Cloud Sync è stata introdotta una nuova funzionalità che consente di testare le modifiche apportate alla configurazione, applicando le modifiche a un singolo utente.  È possibile usarlo per convalidare e verificare che le modifiche apportate alla configurazione siano state applicate correttamente e siano sincronizzate correttamente con Azure AD.  

> [!IMPORTANT] 
> Quando si usa il provisioning su richiesta, i filtri di ambito non vengono applicati all'utente selezionato.  Ciò significa che è possibile usare il provisioning su richiesta sugli utenti esterni alle unità organizzative specificate.


## <a name="using-on-demand-provisioning"></a>Uso del provisioning su richiesta
Per usare la nuova funzionalità, attenersi alla procedura riportata di seguito.


1.  Nel portale di Azure selezionare **Azure Active Directory**.
2.  Selezionare **Azure AD Connect**.
3.  Selezionare **Gestisci sincronizzazione cloud**.

    ![Gestire il provisioning](media/how-to-install/install-6.png)
4. In **configurazione** selezionare la configurazione.
5. In **convalida** fare clic sul pulsante Esegui il **provisioning di un utente** . 

 ![Effettuare il provisioning di un utente](media/how-to-on-demand-provision/on-demand-2.png)

6. Nella schermata di provisioning su richiesta.  Immettere il **nome distinto** di un utente e fare clic sul pulsante **provision (provisioning** ).  
 
 ![Provisioning su richiesta](media/how-to-on-demand-provision/on-demand-3.png)
7. Al termine, verrà visualizzata una schermata operazione completata e 4 caselle di controllo verde che indicano che è stato eseguito correttamente il provisioning.  Tutti gli errori verranno visualizzati a sinistra.

  ![Operazione riuscita](media/how-to-on-demand-provision/on-demand-4.png)

A questo punto è possibile esaminare l'utente e determinare se sono state applicate le modifiche apportate alla configurazione.  Nella parte restante di questo documento vengono descritte le singole sezioni visualizzate nei dettagli di un utente sincronizzato correttamente.

## <a name="import-user-details"></a>Importa dettagli utente
In questa sezione vengono fornite informazioni sull'utente importato da Active Directory.  Questo è l'aspetto dell'utente prima che venga eseguito il provisioning in Azure AD.  Fare clic sul collegamento **Visualizza dettagli** per visualizzare queste informazioni.

![Importa utente](media/how-to-on-demand-provision/on-demand-5.png)

Utilizzando queste informazioni, è possibile visualizzare i vari attributi e i rispettivi valori importati.  Se è stato creato un mapping di attributi personalizzato, sarà possibile visualizzare il valore qui.
![Importa dettagli utente](media/how-to-on-demand-provision/on-demand-6.png)

## <a name="determine-if-user-is-in-scope-details"></a>Determinare se l'utente è nei dettagli dell'ambito
In questa sezione vengono fornite informazioni sul fatto che l'utente importato in Azure AD sia nell'ambito.  Fare clic sul collegamento **Visualizza dettagli** per visualizzare queste informazioni.

![Ambito user](media/how-to-on-demand-provision/on-demand-7.png)

Utilizzando queste informazioni, è possibile visualizzare informazioni aggiuntive sull'ambito degli utenti.

![Dettagli ambito utente](media/how-to-on-demand-provision/on-demand-10a.png)

## <a name="match-user-between-source-and-target-system-details"></a>Corrisponde all'utente tra i dettagli del sistema di origine e di destinazione
In questa sezione vengono fornite informazioni sul fatto che l'utente esista già nel Azure AD e che venga generato un join anziché eseguire il provisioning di un nuovo utente.  Fare clic sul collegamento **Visualizza dettagli** per visualizzare queste informazioni.
![Visualizzare i dettagli](media/how-to-on-demand-provision/on-demand-8.png)

Utilizzando queste informazioni, è possibile verificare se è stata trovata una corrispondenza o se è in corso la creazione di un nuovo utente.

![Informazioni utente](media/how-to-on-demand-provision/on-demand-11.png)

Nei dettagli corrispondenti viene visualizzato un messaggio con una delle tre operazioni seguenti.  Ad esempio:
- Crea: viene creato un utente in Azure AD
- Aggiornamento: un utente viene aggiornato in base a una modifica apportata alla configurazione
- Delete: un utente viene rimosso dal Azure AD.

Il messaggio può variare a seconda del tipo di operazione eseguita.

## <a name="perform-action-details"></a>Esegui dettagli azione
In questa sezione vengono fornite informazioni sull'utente di cui è stato effettuato il provisioning o che è stato esportato in Azure AD dopo l'applicazione della configurazione.  Si tratta di un aspetto simile a quello dell'utente dopo il provisioning in Azure AD.  Fare clic sul collegamento **Visualizza dettagli** per visualizzare queste informazioni.
![Esegui dettagli azione](media/how-to-on-demand-provision/on-demand-9.png)

Utilizzando queste informazioni, è possibile visualizzare i valori degli attributi dopo l'applicazione della configurazione.  Hanno un aspetto simile a quello che è stato importato o sono i diversi?  La configurazione si applica correttamente?  

Questo processo consente di tracciare la trasformazione dell'attributo mentre viene spostata nel cloud e nel tenant Azure AD.

![Trace (attributo)](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>Passaggi successivi 

- [Che cos'è Azure AD Connect sincronizzazione cloud?](what-is-cloud-sync.md)
- [Come installare Azure AD Connect sincronizzazione cloud](how-to-install.md)
 