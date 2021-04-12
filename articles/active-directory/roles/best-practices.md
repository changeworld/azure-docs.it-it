---
title: Procedure consigliate per i ruoli di Azure AD-Azure Active Directory
description: Procedure consigliate per l'utilizzo dei ruoli Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 03/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78f4642b8f9f1ede65766a0026940c0af0f01ec2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111780"
---
# <a name="best-practices-for-azure-ad-roles"></a>Procedure consigliate per i ruoli di Azure AD

Questo articolo descrive alcune delle procedure consigliate per l'uso del controllo degli accessi in base al ruolo Azure Active Directory (Azure AD RBAC). Queste procedure consigliate sono derivate dalla nostra esperienza con Azure AD RBAC e dalle esperienze dei clienti come se stessi. Si consiglia di leggere anche le linee guida dettagliate sulla sicurezza per la protezione dell' [accesso con privilegi per le distribuzioni ibride e cloud in Azure ad](security-planning.md).

## <a name="1-manage-to-least-privilege"></a>1. gestire i privilegi minimi

Quando si pianifica la strategia di controllo di accesso, è consigliabile gestire i privilegi minimi. Con il privilegio minimo si concede agli amministratori esattamente le autorizzazioni necessarie per svolgere il proprio lavoro. Quando si assegna un ruolo agli amministratori, è necessario considerare tre aspetti: un set specifico di autorizzazioni per un ambito specifico, per un periodo di tempo specifico. Evitare di assegnare ruoli più ampi in ambiti più ampi anche se inizialmente sembra più pratico. Limitando i ruoli e gli ambiti, si limitano le risorse a rischio se l'entità di sicurezza non viene mai compromessa. Azure AD RBAC supporta oltre 65 [ruoli predefiniti](permissions-reference.md). Esistono Azure AD ruoli per gestire oggetti directory come utenti, gruppi e applicazioni, nonché per gestire Microsoft 365 servizi come Exchange, SharePoint e Intune. Per comprendere meglio Azure AD ruoli predefiniti, vedere [Understanding Roles in Azure Active Directory](concept-understand-roles.md). Se non è presente un ruolo incorporato che soddisfi le proprie esigenze, è possibile creare [ruoli personalizzati](custom-create.md).  
 
### <a name="finding-the-right-roles"></a>Ricerca dei ruoli corretti

Seguire questa procedura per trovare il ruolo appropriato.

1. Nella portale di Azure aprire [ruoli e amministratori](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) per visualizzare l'elenco dei ruoli di Azure ad.

1. Utilizzare il filtro del **servizio** per restringere l'elenco dei ruoli.

    ![Pagina ruoli e amministratori in Azure AD con filtro del servizio aperto](./media/best-practices/roles-administrators.png)

1. Vedere la documentazione relativa ai [ruoli predefiniti Azure ad](permissions-reference.md) . Le autorizzazioni associate a ogni ruolo sono elencate insieme per migliorare la leggibilità. Per comprendere la struttura e il significato delle autorizzazioni per i ruoli, vedere [come comprendere le autorizzazioni](permissions-reference.md#how-to-understand-role-permissions)dei ruoli.

1. Per la documentazione sulle attività, vedere il [ruolo con privilegi minimi](delegate-by-task.md) .

## <a name="2-use-privileged-identity-management-to-grant-just-in-time-access"></a>2. utilizzare Privileged Identity Management per concedere l'accesso just-in-Time

Uno dei principi del privilegio minimo è che l'accesso deve essere concesso solo per un periodo di tempo specifico. [Azure ad Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) consente di concedere l'accesso just-in-time agli amministratori. Microsoft consiglia di abilitare PIM in Azure AD. Utilizzando PIM, un utente può essere membro idoneo di un ruolo Azure AD. Il può quindi attivare il proprio ruolo per un intervallo di tempo limitato ogni volta che è necessario utilizzarlo. L'accesso con privilegi viene rimosso automaticamente alla scadenza dell'intervallo di tempo. È anche possibile [configurare le impostazioni PIM](../privileged-identity-management/pim-how-to-change-default-settings.md) per richiedere l'approvazione o ricevere messaggi di notifica quando un utente attiva l'assegnazione di ruolo. Le notifiche forniscono un avviso quando vengono aggiunti nuovi utenti ai ruoli con privilegi elevati. 

## <a name="3-turn-on-multi-factor-authentication-for-all-your-administrator-accounts"></a>3. attivare la funzionalità di autenticazione a più fattori per tutti gli account amministratore

[In base ai nostri studi](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984), il tuo account è 99,9% meno probabile che venga compromesso se usi multi-factor authentication. 
 
È possibile abilitare l'autenticazione a più fattori nei ruoli Azure AD usando due metodi:
- [Impostazioni del ruolo](../privileged-identity-management/pim-how-to-change-default-settings.md) in Privileged Identity Management
- [Accesso condizionale](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

## <a name="4-configure-recurring-access-reviews-to-revoke-unneeded-permissions-over-time"></a>4. configurare le verifiche di accesso periodiche per revocare le autorizzazioni non necessarie nel tempo

Le verifiche di accesso consentono alle organizzazioni di controllare regolarmente l'accesso degli amministratori per assicurarsi che solo le persone giuste abbiano accesso continuo. Il controllo regolare degli amministratori è cruciale per i motivi seguenti:
- Un attore malintenzionato può compromettere un account.
- Le persone spostano i team all'interno di una società. Se non è presente alcun controllo, è possibile accumulare un accesso non necessario nel tempo.
 
Per informazioni sulle verifiche di accesso per i ruoli, vedere [creare una verifica di accesso dei ruoli Azure ad in PIM](../privileged-identity-management/pim-how-to-start-security-review.md). Per informazioni sulle verifiche di accesso dei gruppi a cui sono assegnati ruoli, vedere [creare una verifica di accesso dei gruppi e delle applicazioni nelle verifiche di accesso Azure ad](../governance/create-access-review.md).

## <a name="5-limit-the-number-of-global-administrators-to-less-than-5"></a>5. limitare il numero di amministratori globali a meno di 5

Come procedura consigliata, Microsoft consiglia di assegnare il ruolo di amministratore globale a **meno di cinque** utenti nell'organizzazione. Gli amministratori globali contengono chiavi per il Regno ed è il suo interesse per mantenere bassa la superficie di attacco. Come indicato in precedenza, tutti questi account devono essere protetti con l'autenticazione a più fattori.

Per impostazione predefinita, quando un utente si iscrive a un servizio cloud Microsoft, viene creato un tenant di Azure AD e l'utente viene reso membro del ruolo di amministratore globale. Gli utenti a cui viene assegnato il ruolo di amministratore globale possono leggere e modificare tutte le impostazioni amministrative nell'organizzazione Azure AD. Con alcune eccezioni, gli amministratori globali possono anche leggere e modificare tutte le impostazioni di configurazione dell'organizzazione Microsoft 365. Gli amministratori globali hanno anche la possibilità di elevare l'accesso per leggere i dati.

Microsoft consiglia di gestire due account break Glass che vengono assegnati in modo permanente al ruolo di amministratore globale. Assicurarsi che questi account non richiedano lo stesso meccanismo di autenticazione a più fattori dei normali account amministrativi per l'accesso, come descritto in [gestire gli account di accesso di emergenza in Azure ad](../roles/security-emergency-access.md). 

## <a name="6-use-groups-for-azure-ad-role-assignments-and-delegate-the-role-assignment"></a>6. utilizzare i gruppi per Azure AD assegnazioni di ruolo e delegare l'assegnazione di ruolo

Se si dispone di un sistema di governance esterno che sfrutta i vantaggi dei gruppi, è consigliabile assegnare i ruoli a gruppi di Azure AD, anziché a singoli utenti. È anche possibile gestire i gruppi assegnabili ai ruoli in PIM per assicurarsi che non siano presenti proprietari o membri permanenti in questi gruppi privilegiati. Per ulteriori informazioni, vedere la pagina relativa alle [funzionalità di gestione per l'accesso con privilegi Azure ad gruppi](../privileged-identity-management/groups-features.md).

È possibile assegnare un proprietario a gruppi assegnabili al ruolo. Il proprietario decide chi è stato aggiunto o rimosso dal gruppo, quindi indirettamente decide chi ottiene l'assegnazione di ruolo. In questo modo, un amministratore globale o un amministratore del ruolo con privilegi può delegare la gestione dei ruoli in base al ruolo usando i gruppi. Per altre informazioni, vedere [usare i gruppi di cloud per gestire le assegnazioni di ruolo in Azure Active Directory](groups-concept.md).

## <a name="7-activate-multiple-roles-at-once-using-privileged-access-groups"></a>7. attivare più ruoli contemporaneamente usando gruppi di accesso con privilegi

Può trattarsi del caso in cui un utente ha cinque o sei assegnazioni idonee per Azure AD ruoli attraverso PIM. Dovranno attivare ogni singolo ruolo, che può ridurre la produttività. Peggio ancora, possono anche avere decine o centinaia di risorse di Azure assegnate, aggravando il problema.
 
In questo caso, è consigliabile usare i [gruppi di accesso con privilegi](../privileged-identity-management/groups-features.md). Creare un gruppo di accesso con privilegi e concedergli l'accesso permanente a più ruoli (Azure AD e/o Azure). Rendere tale utente un membro o un proprietario idoneo di questo gruppo. Con una sola attivazione, avranno accesso a tutte le risorse collegate.

![Diagramma del gruppo di accesso con privilegi che mostra l'attivazione di più ruoli contemporaneamente](./media/best-practices/privileged-access-group.png)

## <a name="8-use-cloud-native-accounts-for-azure-ad-roles"></a>8. utilizzare account nativi del cloud per i ruoli di Azure AD

Evitare di usare account sincronizzati locali per Azure AD assegnazioni di ruolo. Se l'account locale è compromesso, può compromettere anche le risorse Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](security-planning.md)