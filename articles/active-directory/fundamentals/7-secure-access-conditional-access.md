---
title: Gestire l'accesso esterno con Azure Active Directory l'accesso condizionale
description: Come usare Azure Active Directory Criteri di accesso condizionale per proteggere l'accesso esterno alle risorse.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64209a4d9ca200c69783a4ae317b38beef8ded89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222310"
---
# <a name="manage-external-access-with-conditional-access-policies"></a>Gestire l'accesso esterno con i criteri di accesso condizionale 

L' [accesso condizionale](../conditional-access/overview.md) è lo strumento Azure ad USA per riunire i segnali, applicare i criteri e determinare se un utente deve essere autorizzato ad accedere alle risorse. Per informazioni dettagliate su come creare e usare i criteri di accesso condizionale (criteri di accesso condizionale), vedere [pianificare una distribuzione dell'accesso condizionale](../conditional-access/plan-conditional-access.md). 

![Diagramma dei segnali e delle decisioni di accesso condizionale](media/secure-external-access//7-conditional-access-signals.png)



Questo articolo illustra l'applicazione di criteri di accesso condizionale a utenti esterni e presuppone che non si abbia accesso alle funzionalità di [gestione dei diritti](../governance/entitlement-management-overview.md) . I criteri di accesso condizionale possono essere usati insieme alla gestione dei diritti.

In precedenza in questo set di documenti è [stato creato un piano di sicurezza](3-secure-access-plan.md) che descrive:

* Le applicazioni e le risorse hanno gli stessi requisiti di sicurezza e possono essere raggruppate per l'accesso.

* Requisiti di accesso per gli utenti esterni.

Questo piano viene usato per creare i criteri di accesso condizionale per l'accesso esterno. 

> [!IMPORTANT]
> Creare alcuni account di test utente esterni per poter testare i criteri creati prima di applicarli a tutti gli utenti esterni.

## <a name="conditional-access-policies-for-external-access"></a>Criteri di accesso condizionale per l'accesso esterno

Di seguito sono illustrate le procedure consigliate relative alla gestione dell'accesso esterno con i criteri di accesso condizionale.

* Se non è possibile usare le organizzazioni connesse nella gestione dei diritti, creare un gruppo di sicurezza Azure AD o un gruppo di Microsoft 365 per ogni organizzazione partner con cui si lavora. Assegnare tutti gli utenti del partner al gruppo. È quindi possibile utilizzare tali gruppi nei criteri di accesso condizionale.

* Creare il minor numero possibile di criteri di accesso condizionale. Per le applicazioni che hanno le stesse esigenze di accesso, aggiungerle tutte allo stesso criterio.  
‎ 
   > [!NOTE]
   > I criteri di accesso condizionale possono essere applicati a un massimo di 250 di applicazioni. Se più di 250 app hanno le stesse esigenze di accesso, creare criteri duplicati. Il criterio A si applica alle app 1-250, il criterio B si applica alle app 251-500 e così via.

* Denominare chiaramente i criteri specifici dell'accesso esterno con una convenzione di denominazione. Una convenzione di denominazione è *ExternalAccess_actiontaken_AppGroup*. Ad esempio ExternalAccess_Block_FinanceApps.

## <a name="block-all-external-users-from-resources"></a>Blocca tutti gli utenti esterni dalle risorse

È possibile impedire agli utenti esterni di accedere a set di risorse specifici con criteri di accesso condizionale. Una volta determinato il set di risorse a cui si vuole bloccare l'accesso, creare un criterio.

Per creare un criterio che blocca l'accesso per gli utenti esterni a un set di applicazioni:

1. Accedere al **portale di Azure**, selezionare **Azure Active Directory**, selezionare **sicurezza**, quindi selezionare **accesso condizionale**.

2. Selezionare **nuovo criterio** e immettere un **nome**, ad esempio ExternalAccess_Block_FinanceApps

3. Selezionare **gli utenti e i gruppi**. Nella scheda Includi scegliere **Seleziona utenti e gruppi**, quindi selezionare **tutti i guest e gli utenti esterni**. 

4. Selezionare **Escludi** e immettere i gruppi di amministratori e gli account di accesso di emergenza (Break-Glass).

5. Selezionare **app Cloud o azioni**, scegliere **Seleziona app**, selezionare tutte le app a cui si vuole bloccare l'accesso esterno, quindi scegliere **Seleziona**.

6. Selezionare **condizioni**, selezionare **località**, in Configura selezionare **Sì** e selezionare **qualsiasi percorso**.

7. In controlli di accesso selezionare **Concedi**, modificare l'interruttore in **blocco** e scegliere **Seleziona**.

8. Verificare che l'impostazione Abilita criterio sia impostata su **solo report**, quindi selezionare **Crea**.

## <a name="block-external-access-to-all-except-specific-external-users"></a>Blocca l'accesso esterno a tutti gli utenti esterni specifici

In alcuni casi è possibile che si desideri bloccare gli utenti esterni ad eccezione di un gruppo specifico. Ad esempio, potrebbe essere necessario bloccare tutti gli utenti esterni, ad eccezione di quelli che lavorano per il team Finance dalle applicazioni finanziarie. Per eseguire questa operazione:

1. Creare un gruppo di sicurezza che contenga gli utenti esterni che devono accedere al gruppo Finance.

2. Seguire i passaggi 1-3 in bloccare l'accesso esterno dalle risorse precedenti.

3. Nel passaggio 4 aggiungere il gruppo di sicurezza che si vuole escludere dal blocco delle app finanziarie.

4. Eseguire i passaggi rimanenti.

## <a name="implement-conditional-access"></a>Implementare l'accesso condizionale

Sono documentati molti criteri di accesso condizionale comuni. Vedere quanto segue, che può essere adattato a utenti esterni.

* [Richiedi Multi-Factor Authentication per tutti gli utenti](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

* [Accesso condizionale basato sul rischio per l'utente](../conditional-access/howto-conditional-access-policy-risk-user.md)

* [Richiedi Multi-Factor Authentication per l'accesso da reti non attendibili](../conditional-access/untrusted-networks.md) 

* [Richiedi le condizioni per l'utilizzo](../conditional-access/terms-of-use.md)

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti sulla protezione dell'accesso esterno alle risorse. Si consiglia di eseguire le azioni nell'ordine elencato.

1. [Determinare la postura di sicurezza desiderata per l'accesso esterno](1-secure-access-posture.md)

2. [Individua lo stato corrente](2-secure-access-current-state.md)

3. [Creazione di un piano di governance](3-secure-access-plan.md)

4. [Usare i gruppi per la sicurezza](4-secure-access-groups.md)

5. [Transizione a Azure AD B2B](5-secure-access-b2b.md)

6. [Accesso sicuro con la gestione dei diritti](6-secure-access-entitlement-managment.md)

7. [Accesso sicuro con i criteri di accesso condizionale](7-secure-access-conditional-access.md) (si trova qui)

8. [Proteggere l'accesso con le etichette di riservatezza](8-secure-access-sensitivity-labels.md)

9. [Accesso sicuro a Microsoft teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)
