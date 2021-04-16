---
title: Usare i gruppi cloud per gestire le assegnazioni di ruolo in Azure Active Directory | Microsoft Docs
description: Anteprima dei ruoli di Azure AD personalizzati per la delega della gestione delle identità. Gestire le assegnazioni di ruolo di Azure portale di Azure, PowerShell o API Graph.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1fea64305d4735c5bf1bf59b86ae5283600e622
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502199"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>Usare i gruppi cloud per gestire le assegnazioni di ruolo Azure Active Directory (anteprima)

Azure Active Directory (Azure AD) sta introducendo un'anteprima pubblica in cui è possibile assegnare un gruppo cloud Azure AD ruoli predefiniti. Con questa funzionalità, è possibile usare i gruppi per concedere l'accesso amministratore in Azure AD con il minimo sforzo da parte degli amministratori dei ruoli globali e con privilegi.

Si consideri questo esempio: Contoso ha assunto persone in aree geografiche diverse per gestire e reimpostare le password per i dipendenti dell'organizzazione Azure AD lavoro. Invece di chiedere a un amministratore del ruolo con privilegi o a un amministratore globale di assegnare il ruolo di amministratore del supporto helpdesk a ogni persona singolarmente, può creare un gruppo di Contoso_Helpdesk_Administrators e assegnarlo al ruolo. Quando gli utenti si uniscono al gruppo, il ruolo viene assegnato indirettamente. Il flusso di lavoro di governance esistente può quindi occuparsi del processo di approvazione e del controllo dell'appartenenza del gruppo per garantire che solo gli utenti legittimi siano membri del gruppo e siano quindi assegnati al ruolo di amministratore del supporto.

## <a name="how-this-feature-works"></a>Funzionamento di questa funzionalità

Creare un nuovo Microsoft 365 o un gruppo di sicurezza con la proprietà 'isAssignableToRole' impostata su 'true'. È anche possibile abilitare questa proprietà quando si crea un gruppo nel portale di Azure attivando Azure AD i ruoli **possono essere assegnati al gruppo**. In entrambi i modi, è quindi possibile assegnare il gruppo a uno o Azure AD ruoli nello stesso modo in cui si assegnano i ruoli agli utenti. È possibile creare un massimo di 250 gruppi assegnabili al ruolo in una singola Azure AD (tenant).

Se non si vuole che i membri del gruppo possano accedere in modo permanente al ruolo, è possibile usare Azure AD Privileged Identity Management. Assegnare un gruppo come membro idoneo di un Azure AD ruolo. Ogni membro del gruppo è quindi idoneo per l'attivazione dell'assegnazione per il ruolo a cui è assegnato il gruppo. Possono quindi attivare l'assegnazione di ruolo per una durata temporale fissa.

> [!Note]
> Per poter assegnare un gruppo al ruolo Azure AD tramite PIM, è necessario essere Privileged Identity Management versione aggiornata di . Si potrebbe usare una versione precedente di PIM perché l'organizzazione Azure AD usa l'API Privileged Identity Management precedente. Contattare l'alias per pim_preview@microsoft.com spostare l'organizzazione e aggiornare l'API. Per altre [informazioni, vedere Azure AD e funzionalità in PIM.](../privileged-identity-management/azure-ad-roles-features.md)

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>Perché viene applicata la creazione di un gruppo speciale per l'assegnazione a un ruolo

Se a un gruppo viene assegnato un ruolo, anche qualsiasi amministratore IT in grado di gestire l'appartenenza a un gruppo può gestire indirettamente l'appartenenza a tale ruolo. Si supponga, ad esempio, che un gruppo Contoso_User_Administrators assegnato al ruolo di amministratore dell'account utente. Un amministratore di Exchange che può modificare l'appartenenza ai gruppi potrebbe aggiungersi al gruppo Contoso_User_Administrators e diventare in questo modo un amministratore dell'account utente. Come si può vedere, un amministratore potrebbe elevare il proprio privilegio in modo non previsto.

Azure AD consente di proteggere un gruppo assegnato a un ruolo usando una nuova proprietà denominata isAssignableToRole per i gruppi. Solo i gruppi cloud con la proprietà isAssignableToRole impostata su 'true' in fase di creazione possono essere assegnati a un ruolo. Questa proprietà non è modificabile. Dopo aver creato un gruppo con questa proprietà impostata su 'true', non può essere modificato. Non è possibile impostare la proprietà su un gruppo esistente.
È stato progettato il modo in cui i gruppi vengono assegnati ai ruoli per evitare questo tipo di potenziale violazione:

- Solo gli amministratori globali e gli amministratori del ruolo con privilegi possono creare un gruppo assegnabile al ruolo (con la proprietà "isAssignableToRole" abilitata).
- Non può essere un gruppo Azure AD dinamico. in altri, deve avere un tipo di appartenenza "Assegnato". Il popolamento automatico dei gruppi dinamici potrebbe causare l'aggiunta di un account indesiderato al gruppo e quindi l'assegnazione al ruolo.
- Per impostazione predefinita, solo gli amministratori globali e gli amministratori dei ruoli con privilegi possono gestire l'appartenenza a un gruppo assegnabile al ruolo, ma è possibile delegare la gestione dei gruppi assegnabili al ruolo aggiungendo i proprietari dei gruppi.
- Per impedire l'elevazione dei privilegi, le credenziali dei membri e dei proprietari di un gruppo assegnabile al ruolo possono essere modificate solo da un amministratore di Privileged Authentication o da un amministratore globale.
- Nessuna annidamento. Non è possibile aggiungere un gruppo come membro di un gruppo assegnabile al ruolo.

## <a name="limitations"></a>Limitazioni

Gli scenari seguenti non sono attualmente supportati:  

- Assegnare gruppi locali a Azure AD ruoli (predefiniti o personalizzati)

## <a name="known-issues"></a>Problemi noti

- Azure AD solo clienti con licenza *P2:* non assegnare un gruppo come Attivo a un ruolo tramite Azure AD e Privileged Identity Management (PIM). In particolare, non assegnare un ruolo a un gruppo assegnabile al ruolo quando viene creato e assegnare un ruolo al gruppo usando PIM in un secondo momento.  Ciò può causare problemi in cui gli utenti non possono visualizzare le assegnazioni di ruolo attive in PIM, nonché l'impossibilità di rimuovere tale assegnazione PIM. Le assegnazioni idonee non sono interessate in questo scenario. Se si tenta di eseguire questa assegnazione, è possibile che venga visualizzato un comportamento imprevisto, ad esempio:
  - L'ora di fine per l'assegnazione di ruolo potrebbe non essere visualizzata correttamente.
  - Nel portale PIM **i** ruoli personali possono visualizzare una sola assegnazione di ruolo indipendentemente dal numero di metodi con cui viene concessa l'assegnazione (tramite uno o più gruppi e direttamente).
- *Azure AD solo clienti con licenza P2* Anche dopo aver eliminato il gruppo, viene comunque visualizzato un membro idoneo del ruolo nell'interfaccia utente di PIM. Dal punto di vista funzionale non si verifica alcun problema. si tratta solo di un problema di cache nel portale di Azure.  
- Usare il nuovo [interfaccia di amministrazione di Exchange per](https://admin.exchange.microsoft.com/) le assegnazioni di ruolo tramite l'appartenenza a gruppi. L'interfaccia di amministrazione di Exchange precedente non supporta ancora questa funzionalità. I cmdlet di Exchange PowerShell funzionano come previsto.
- Azure Information Protection portal (portale classico) non riconosce ancora l'appartenenza al ruolo tramite il gruppo. È possibile [eseguire la migrazione](/azure/information-protection/configure-policy-migrate-labels) alla piattaforma di etichettatura di riservatezza unificata e quindi usare il Centro conformità di Office 365 Security & per usare le assegnazioni di gruppo per gestire i ruoli.
- [L'interfaccia di amministrazione](https://config.office.com/) delle app non supporta ancora questa funzionalità. Assegnare gli utenti direttamente al ruolo amministratore delle app di Office.
- [Il Centro conformità M365](https://compliance.microsoft.com/) non supporta ancora questa funzionalità. Assegnare gli utenti direttamente ai Azure AD appropriati per l'uso di questo portale.

Questi problemi sono stati risolti.

## <a name="required-license-plan"></a>Piano di licenza necessario

L'uso di questa funzionalità richiede la disponibilità di una licenza Azure AD Premium P1 nell'organizzazione Azure AD. Per usare anche Privileged Identity Management per l'attivazione del ruolo JUST-In-Time, è necessario disporre di una licenza Azure AD Premium P2. Per trovare la licenza più valida per i requisiti, vedere [Confronto delle funzionalità disponibili a livello generale dei piani Gratuito e Premium.](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses)

## <a name="next-steps"></a>Passaggi successivi

- [Creare un gruppo assegnabile a un ruolo](groups-create-eligible.md)
- [Assegnare un ruolo a un gruppo assegnabile al ruolo](groups-assign-role.md)
