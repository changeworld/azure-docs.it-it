---
title: Sincronizzazione dell'hash delle password selettiva per Azure AD Connect
description: Questo articolo descrive come configurare e configurare la sincronizzazione dell'hash delle password selettiva da usare con Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 248d5e163eb046edd130d69307a1c553d434b92d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604669"
---
# <a name="selective-password-hash-synchronization-configuration-for-azure-ad-connect"></a>Configurazione della sincronizzazione dell'hash delle password selettiva per Azure AD Connect

La [sincronizzazione dell'hash delle password](whatis-phs.md) è uno dei metodi di accesso usati per ottenere l'identità ibrida. Azure AD Connect consente di sincronizzare un hash della password di un utente da un'istanza di Active Directory locale a un'istanza di Azure AD basata sul cloud.  Per impostazione predefinita, dopo l'installazione, la sincronizzazione dell'hash delle password verrà eseguita su tutti gli utenti sincronizzati.

Se si vuole che un sottoinsieme di utenti esclusi dalla sincronizzazione dell'hash della password a Azure AD, è possibile configurare la sincronizzazione dell'hash delle password selettiva usando i passaggi guidati disponibili in questo articolo.

>[!Important]
> Microsoft non supporta la modifica o l'uso del servizio di sincronizzazione Azure AD Connect al di fuori delle configurazioni e delle azioni descritte in modo formale. Una di queste configurazioni o azioni può causare uno stato incoerente o non supportato della sincronizzazione Azure AD Connect. Di conseguenza, Microsoft non garantisce la possibilità di fornire un supporto tecnico efficiente per tali distribuzioni. 


## <a name="consider-your-implementation"></a>Prendere in considerazione l'implementazione  
Per ridurre il lavoro di amministrazione della configurazione, è necessario considerare prima di tutto il numero di oggetti utente da escludere dalla sincronizzazione dell'hash delle password. Verificare che gli scenari seguenti, che si escludono a vicenda, siano allineati alle proprie esigenze per selezionare l'opzione di configurazione corretta.
- Se il numero di utenti da **escludere** **è inferiore** al numero di utenti da **includere**, attenersi alla procedura descritta in questa [sezione](#excluded-users-is-smaller-than-included-users).
- Se il numero di utenti da **escludere** è **maggiore** del numero di utenti da **includere**, attenersi alla procedura descritta in questa [sezione](#excluded-users-is-larger-than-included-users).

> [!Important]
> Con entrambe le opzioni di configurazione scelte, una sincronizzazione iniziale obbligatoria (sincronizzazione completa) per applicare le modifiche, verrà eseguita automaticamente al successivo ciclo di sincronizzazione.

> [!Important]
> La configurazione della sincronizzazione dell'hash delle password selettiva influenza direttamente il writeback delle password. Le modifiche della password o le reimpostazioni della password avviate in Azure Active Directory eseguono il writeback nella Active Directory locale solo se l'utente rientra nell'ambito della sincronizzazione dell'hash delle password. 

### <a name="the-admindescription-attribute"></a>Attributo adminDescription
Entrambi gli scenari si basano sull'impostazione dell'attributo adminDescription degli utenti su un valore specifico.  Ciò consente di applicare le regole ed è ciò che rende il lavoro di pH selettivo.

|Scenario|valore adminDescription|
|-----|-----|
|Utenti esclusi più piccoli degli utenti inclusi|PHSFiltered|
|Utenti esclusi maggiori di quelli inclusi|PHSIncluded|

Questo attributo può essere impostato su uno di questi valori:

- uso dell'interfaccia utente Active Directory utenti e computer
- tramite il `Set-ADUser` cmdlet di PowerShell.  Per ulteriori informazioni, vedere [set-aduser](https://docs.microsoft.com/powershell/module/addsadministration/set-aduser).

 


### <a name="disable-the-synchronization-scheduler"></a>Disabilitare l'utilità di pianificazione della sincronizzazione:
Prima di avviare uno scenario di questo tipo, è necessario disabilitare l'utilità di pianificazione della sincronizzazione durante le modifiche alle regole di sincronizzazione.
 1. Avviare Windows PowerShell invio.

     ```Set-ADSyncScheduler -SyncCycleEnabled $false``` 
 
2.  Verificare che l'utilità di pianificazione sia disabilitata eseguendo il cmdlet seguente:
     
    ```Get-ADSyncScheduler```

Per ulteriori informazioni sull'utilità di pianificazione, vedere [Azure ad Connect utilità di pianificazione della sincronizzazione](how-to-connect-sync-feature-scheduler.md).




## <a name="excluded-users-is-smaller-than-included-users"></a>Utenti esclusi più piccoli degli utenti inclusi
Nella sezione **seguente viene descritto** come abilitare la sincronizzazione dell'hash delle password selettiva quando il numero di utenti da **escludere** è inferiore al numero di utenti da **includere**.

>[!Important]
> Prima di procedere, assicurarsi che l'utilità di pianificazione della sincronizzazione sia disabilitata come descritto in precedenza.

- Creare una copia modificabile di **in da ad – User AccountEnabled** con l'opzione per **abilitare la sincronizzazione dell'hash delle password non selezionata** e definirne il filtro di ambito 
- Creare un'altra copia modificabile di default **in from ad – User AccountEnabled** con l'opzione per **abilitare la sincronizzazione dell'hash delle password selezionata** e definirne il filtro di ambito 
- Abilitare di nuovo l'utilità di pianificazione della sincronizzazione 
- Impostare il valore dell'attributo in Active Directory definito come attributo di ambito per gli utenti che si desidera consentire nella sincronizzazione dell'hash delle password. 

>[!Important]
>I passaggi forniti per configurare la sincronizzazione dell'hash delle password selettiva avranno effetto solo sugli oggetti utente con l'attributo **adminDescription** popolato in Active Directory con il valore di **PHSFiltered**.
Se questo attributo non è popolato o se il valore è diverso da **PHSFiltered** , queste regole non verranno applicate agli oggetti utente.


### <a name="configure-the-necessary-synchronization-rules"></a>Configurare le regole di sincronizzazione necessarie:

 1. Avviare l'editor delle regole di sincronizzazione e impostare **la sincronizzazione della password** **su on** e il **tipo di regola** su **standard**.
     ![Avviare l'editor delle regole di sincronizzazione](media/how-to-connect-selective-password-hash-synchronization/exclude-1.png)
 2. Selezionare la regola **in from ad – User AccountEnabled** per il connettore della foresta Active Directory per cui si vuole configurare la password selettiva con la sincronizzazione dell'hash e fare clic su **Edit (modifica**). Selezionare **Sì** nella finestra di dialogo successiva per creare una copia modificabile della regola originale.
     ![Seleziona regola](media/how-to-connect-selective-password-hash-synchronization/exclude-2.png)
 3. La prima regola Disabilita la sincronizzazione degli hash delle password. Fornire il nome seguente alla nuova regola personalizzata: **in from ad-User AccountEnabled-Filter users from pH**.
 Modificare il valore di precedenza in un numero minore di 100 (ad esempio, **90** o a seconda del valore più basso disponibile nell'ambiente).
 Verificare che le caselle di controllo **Abilita sincronizzazione password** e **disabilitato** siano deselezionate e c.
 Fare clic su **Avanti**.
  ![Modifica in ingresso](media/how-to-connect-selective-password-hash-synchronization/exclude-3.png)
 4. In **filtro ambito** fare clic su **Aggiungi clausola**.
 Selezionare **adminDescription** nella colonna attributo, **uguale** a nella colonna operatore e immettere **PHSFiltered** come valore.
     ![Filtro di ambito](media/how-to-connect-selective-password-hash-synchronization/exclude-4.png)
 5. Non sono necessarie altre modifiche. Le **regole di join** e le **trasformazioni** devono essere lasciate con le impostazioni copiate predefinite, quindi è possibile fare clic su **Save** Now.
 Fare clic su **OK** nella finestra di dialogo di avviso per informare che una sincronizzazione completa verrà eseguita al successivo ciclo di sincronizzazione del connettore.
     ![Salva regola](media/how-to-connect-selective-password-hash-synchronization/exclude-5.png)
 6. Successivamente, creare un'altra regola personalizzata con la sincronizzazione dell'hash delle password abilitata. Selezionare di nuovo la regola predefinita **in from ad – User AccountEnabled** per la foresta Active Directory per cui si vuole configurare la password selettiva, quindi fare clic su **Edit (modifica**). Selezionare **Sì** nella finestra di dialogo successiva per creare una copia modificabile della regola originale.
     ![Regola personalizzata](media/how-to-connect-selective-password-hash-synchronization/exclude-6.png)
 7. Fornire il nome seguente alla nuova regola personalizzata: **in from ad-User AccountEnabled-Users included for pH**.
 Modificare il valore di precedenza in un numero inferiore alla regola creata in precedenza (in questo esempio sarà **89**).
 Verificare che la casella di controllo **Abilita sincronizzazione password** sia selezionata e che la casella di controllo **disabilitata** sia deselezionata.
 Fare clic su **Avanti**.  
     ![Modifica nuova regola](media/how-to-connect-selective-password-hash-synchronization/exclude-7.png)
 8. In **filtro ambito** fare clic su **Aggiungi clausola**.
 Selezionare **adminDescription** nella colonna attributo, **NOTEQUAL** nella colonna operatore e immettere **PHSFiltered** come valore.
     ![Regola ambito](media/how-to-connect-selective-password-hash-synchronization/exclude-8.png)
 9. Non sono necessarie altre modifiche. Le **regole di join** e le **trasformazioni** devono essere lasciate con le impostazioni copiate predefinite, quindi è possibile fare clic su **Save** Now.
 Fare clic su **OK** nella finestra di dialogo di avviso per informare che una sincronizzazione completa verrà eseguita al successivo ciclo di sincronizzazione del connettore.
     ![Regole di join](media/how-to-connect-selective-password-hash-synchronization/exclude-9.png)
 10. Confermare la creazione delle regole. Rimuovere la **sincronizzazione password filtri** **in** e **tipo di regola** **standard**. Verranno visualizzate entrambe le nuove regole appena create.
     ![Conferma regole](media/how-to-connect-selective-password-hash-synchronization/exclude-10.png) 


### <a name="re-enable-synchronization-scheduler"></a>Riabilitare l'utilità di pianificazione della sincronizzazione:  
Una volta completati i passaggi per configurare le regole di sincronizzazione necessarie, abilitare di nuovo l'utilità di pianificazione della sincronizzazione con i passaggi seguenti:
 1. In Windows PowerShell eseguire:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Quindi verificare che sia stata abilitata correttamente eseguendo:

     ```Get-ADSyncScheduler```

Per ulteriori informazioni sull'utilità di pianificazione, vedere [Azure ad Connect utilità di pianificazione della sincronizzazione](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Modifica attributo **adminDescription** utenti:
Una volta completate tutte le configurazioni, è necessario modificare l'attributo **adminDescription** per tutti gli utenti che si desidera **escludere** dalla sincronizzazione degli hash delle password in Active Directory e aggiungere la stringa utilizzata nel filtro di ambito: **PHSFiltered**.
   
  ![Modificare l'attributo](media/how-to-connect-selective-password-hash-synchronization/exclude-11.png)

È anche possibile usare il comando di PowerShell seguente per modificare l'attributo **adminDescription** di un utente:

```Set-ADUser myuser -Replace @{adminDescription="PHSFiltered"}```

## <a name="excluded-users-is-larger-than-included-users"></a>Utenti esclusi maggiori di quelli inclusi
Nella sezione seguente viene descritto come abilitare la sincronizzazione dell'hash delle password selettiva quando il numero di utenti da **escludere** è **maggiore** del numero di utenti da **includere**.

>[!Important]
> Prima di procedere, assicurarsi che l'utilità di pianificazione della sincronizzazione sia disabilitata come descritto in precedenza.

Di seguito è riportato un riepilogo delle azioni che verranno eseguite nei passaggi seguenti:

- Creare una copia modificabile di **in da ad – User AccountEnabled** con l'opzione per **abilitare la sincronizzazione dell'hash delle password non selezionata** e definirne il filtro di ambito 
- Creare un'altra copia modificabile di default **in from ad – User AccountEnabled** con l'opzione per **abilitare la sincronizzazione dell'hash delle password selezionata** e definirne il filtro di ambito 
- Abilitare di nuovo l'utilità di pianificazione della sincronizzazione 
- Impostare il valore dell'attributo in Active Directory definito come attributo di ambito per gli utenti che si desidera consentire nella sincronizzazione dell'hash delle password. 

>[!Important]
>I passaggi forniti per configurare la sincronizzazione dell'hash delle password selettiva avranno effetto solo sugli oggetti utente con l'attributo **adminDescription** popolato in Active Directory con il valore di **PHSIncluded**.
Se questo attributo non è popolato o se il valore è diverso da **PHSIncluded** , queste regole non verranno applicate agli oggetti utente.


### <a name="configure-the-necessary-synchronization-rules"></a>Configurare le regole di sincronizzazione necessarie:

 1. Avviare l'editor delle regole di sincronizzazione e impostare la **sincronizzazione password** e il tipo **di** **regola** **standard**.
     ![Tipo di regola](media/how-to-connect-selective-password-hash-synchronization/include-1.png)
 2. Selezionare la regola **in from ad – User AccountEnabled** per la foresta Active Directory per cui si vuole configurare la password selettiva e fare clic su **Edit (modifica**). Selezionare **Sì** nella finestra di dialogo successiva per creare una copia modificabile della regola originale.
     ![In da AD](media/how-to-connect-selective-password-hash-synchronization/include-2.png)
 3. La prima regola Disabilita la sincronizzazione degli hash delle password. Fornire il nome seguente alla nuova regola personalizzata: **in from ad-User AccountEnabled-Filter users from pH**.
 Modificare il valore di precedenza in un numero minore di 100 (ad esempio, **90** o a seconda del valore più basso disponibile nell'ambiente).
 Verificare che le caselle di controllo **Abilita sincronizzazione password** e **disabilitato** siano deselezionate.
 Fare clic su **Avanti**.
  ![Imposta precedenza](media/how-to-connect-selective-password-hash-synchronization/include-3.png)
 4. In **filtro ambito** fare clic su **Aggiungi clausola**.
Selezionare **adminDescription** nella colonna attributo, **NOTEQUAL** nella colonna operatore e immettere **PHSIncluded** come valore.
     ![Aggiungi clausola](media/how-to-connect-selective-password-hash-synchronization/include-4.png)
 5. Non sono necessarie altre modifiche. Le **regole di join** e le **trasformazioni** devono essere lasciate con le impostazioni copiate predefinite, quindi è possibile fare clic su **Save** Now.
 Fare clic su **OK** nella finestra di dialogo di avviso per informare che una sincronizzazione completa verrà eseguita al successivo ciclo di sincronizzazione del connettore.
     ![Trasformazione](media/how-to-connect-selective-password-hash-synchronization/include-5.png)
 6. Successivamente, creare un'altra regola personalizzata con la sincronizzazione dell'hash delle password abilitata. Selezionare di nuovo la regola predefinita **in from ad – User AccountEnabled** per la foresta Active Directory per cui si vuole configurare la password selettiva, quindi fare clic su **Edit (modifica**). Selezionare **Sì** nella finestra di dialogo successiva per creare una copia modificabile della regola originale.
     ![AccountEnabled utente](media/how-to-connect-selective-password-hash-synchronization/include-6.png)
 7. Fornire il nome seguente alla nuova regola personalizzata: **in from ad-User AccountEnabled-Users included for pH**.
 Modificare il valore di precedenza in un numero inferiore alla regola creata in precedenza (in questo esempio sarà **89**).
 Verificare che la casella di controllo **Abilita sincronizzazione password** sia selezionata e che la casella di controllo **disabilitata** sia deselezionata.
 Fare clic su **Avanti**.
     ![Abilita sincronizzazione password](media/how-to-connect-selective-password-hash-synchronization/include-7.png)
 8. In **filtro ambito** fare clic su **Aggiungi clausola**.
 Selezionare **adminDescription** nella colonna attributo, **uguale** a nella colonna operatore e immettere **PHSIncluded** come valore.
     ![PHSIncluded](media/how-to-connect-selective-password-hash-synchronization/include-8.png)
 9. Non sono necessarie altre modifiche. Le **regole di join** e le **trasformazioni** devono essere lasciate con le impostazioni copiate predefinite, quindi è possibile fare clic su **Save** Now.
 Fare clic su **OK** nella finestra di dialogo di avviso per informare che una sincronizzazione completa verrà eseguita al successivo ciclo di sincronizzazione del connettore.
     ![Salva ora](media/how-to-connect-selective-password-hash-synchronization/include-9.png)
 10.    Confermare la creazione delle regole. Rimuovere la **sincronizzazione password filtri** **in** e **tipo di regola** **standard**. Verranno visualizzate entrambe le nuove regole appena create.
     ![Sincronizza in](media/how-to-connect-selective-password-hash-synchronization/include-10.png)

### <a name="re-enable-synchronization-scheduler"></a>Riabilitare l'utilità di pianificazione della sincronizzazione:  
Una volta completati i passaggi per configurare le regole di sincronizzazione necessarie, abilitare di nuovo l'utilità di pianificazione della sincronizzazione con i passaggi seguenti:
 1. In Windows PowerShell eseguire:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Quindi verificare che sia stata abilitata correttamente eseguendo:

     ```Get-ADSyncScheduler```

Per ulteriori informazioni sull'utilità di pianificazione, vedere [Azure ad Connect utilità di pianificazione della sincronizzazione](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Modifica attributo **adminDescription** utenti:
Una volta completate tutte le configurazioni, è necessario modificare l'attributo **adminDescription** per tutti gli utenti che si desidera **includere** per la sincronizzazione dell'hash delle password in Active Directory e aggiungere la stringa utilizzata nel filtro di ambito: **PHSIncluded**.

  ![Modifica attributi](media/how-to-connect-selective-password-hash-synchronization/include-11.png)
 
 È anche possibile usare il comando di PowerShell seguente per modificare l'attributo **adminDescription** di un utente:

 ```Set-ADUser myuser -Replace @{adminDescription="PHSIncluded"}``` 

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sulla sincronizzazione dell'hash delle password](whatis-phs.md)
- [Funzionamento della sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md)
