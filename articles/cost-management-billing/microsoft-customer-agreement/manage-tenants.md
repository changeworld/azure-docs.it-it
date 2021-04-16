---
title: Gestire i tenant nell'account Contratto del cliente Microsoft fatturazione - Azure
description: L'articolo consente di comprendere e gestire i tenant associati all'account Contratto del cliente Microsoft fatturazione.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: dc34d0f12430838be29897ccc5cbeee382ecaa2b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107493127"
---
# <a name="manage-tenants-in-your-microsoft-customer-agreement-billing-account"></a>Gestire i tenant nell'account Contratto del cliente Microsoft fatturazione

L'articolo consente di comprendere e gestire i tenant associati all'account Contratto del cliente Microsoft fatturazione. Usare le informazioni per gestire i tenant, trasferire sottoscrizioni e amministrare la proprietà della fatturazione assicurando l'accesso sicuro all'ambiente di fatturazione.

## <a name="whats-a-tenant"></a>Che cos'è un tenant?

Un tenant è una rappresentazione digitale dell'organizzazione ed è principalmente associato a un dominio, ad esempio Microsoft.com. Si tratta di un ambiente gestito tramite Azure Active Directory che consente di assegnare agli utenti le autorizzazioni per gestire le risorse di Azure e la fatturazione.

Ogni tenant è distinto e separato dagli altri tenant, ma è possibile consentire agli utenti guest di altri tenant di accedere al tenant per tenere traccia dei costi e gestire la fatturazione.

## <a name="how-tenants-and-subscriptions-relate-to-billing-account"></a>Relazione tra tenant e sottoscrizioni con l'account di fatturazione

È possibile usare il Contratto del cliente Microsoft (account di fatturazione) per tenere traccia dei costi e gestire la fatturazione. Ogni account di fatturazione ha almeno un profilo di fatturazione. Il profilo di fatturazione consente di gestire la fattura e il metodo di pagamento. Ogni profilo di fatturazione include una sezione della fattura, per impostazione predefinita. È possibile creare altre sezioni della fattura per raggruppare, tenere traccia e gestire i costi a un livello più granulare, se necessario.

- L'account di fatturazione è associato a un singolo tenant. Significa che solo gli utenti che fanno parte del tenant possono accedere all'account di fatturazione.
- Quando si crea una nuova sottoscrizione di Azure per l'account di fatturazione, viene sempre creata nel tenant dell'account di fatturazione. Tuttavia, è possibile spostare le sottoscrizioni in altri tenant. È anche possibile collegare le sottoscrizioni esistenti di altri tenant all'account di fatturazione. Consente di gestire centralmente la fatturazione tramite un tenant mantenendo le risorse e le sottoscrizioni in altri tenant in base alle esigenze.

Il diagramma seguente illustra come l'account di fatturazione e le sottoscrizioni sono collegati ai tenant. L'account di fatturazione del Cliente Microsoft Contoso è associato al tenant 1, mentre l'account Contoso con pagamento in base al prezzo è associato al tenant 2. Si supponga che Contoso voglia pagare la sottoscrizione con pagamento in base al pagamento tramite l'account di fatturazione del McA e che possa usare un trasferimento della proprietà della fatturazione per collegare la sottoscrizione al proprio account di fatturazione del McA. La sottoscrizione e le relative risorse verranno comunque associate al tenant 2, ma vengono pagate per l'uso dell'account di fatturazione del Cliente Microsoft.

:::image type="content" source="./media/manage-tenants/billing-hierarchy-example.png" alt-text="Diagramma che mostra una gerarchia di fatturazione di esempio." border="false" lightbox="./media/manage-tenants/billing-hierarchy-example.png":::

## <a name="manage-subscriptions-under-multiple-tenants-in-a-single-microsoft-customer-agreement"></a>Gestire le sottoscrizioni in più tenant in un singolo Contratto del cliente Microsoft

I proprietari della fatturazione possono creare sottoscrizioni quando hanno [le autorizzazioni appropriate](../manage/understand-mca-roles.md#subscription-billing-roles-and-tasks) per l'account di fatturazione. Per impostazione predefinita, tutte le nuove sottoscrizioni create nel Contratto del cliente Microsoft si Contratto del cliente Microsoft tenant.

- È possibile collegare sottoscrizioni da altri tenant all'account Contratto del cliente Microsoft di fatturazione. La proprietà della fatturazione di una sottoscrizione modifica solo la disposizione della fatturazione. Non influisce sul tenant del servizio o sui ruoli controllo degli accessi in base al ruolo di Azure.
- Per modificare il proprietario della sottoscrizione nel tenant del servizio, è necessario trasferire la [sottoscrizione a una directory Azure Active Directory directory](../../role-based-access-control/transfer-subscription.md).

## <a name="add-guest-users-to-your-microsoft-customer-agreement-tenant"></a>Aggiungere utenti guest al tenant Contratto del cliente Microsoft

Gli utenti aggiunti al tenant Contratto del cliente Microsoft fatturazione, per gestire le responsabilità di fatturazione da un tenant diverso, devono essere invitati come guest.

Per invitare un utente come guest, l'utente deve avere un indirizzo di posta elettronica esistente con un dominio diverso dal dominio Azure Active Directory (AD). Azure AD invia all'utente guest un messaggio di posta elettronica con un collegamento per l'autenticazione.

:::image type="content" source="./media/manage-tenants/guest-invitation-email.png" alt-text="Screenshot che mostra un invito tramite posta elettronica di esempio." lightbox="./media/manage-tenants/guest-invitation-email.png" :::

Quando un utente viene aggiunto al tenant Contratto del cliente Microsoft, deve [accettare l'invito](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation).

Quando selezionano il **collegamento Accetta invito,** viene richiesto di eseguire l'autenticazione con Azure.

:::image type="content" source="./media/manage-tenants/authenticate.png" alt-text="Screenshot che mostra una richiesta di autenticazione con Azure." lightbox="./media/manage-tenants/authenticate.png" :::

Quindi selezionano **Accetta**.

:::image type="content" source="./media/manage-tenants/accept-invitation.png" alt-text="Screenshot che mostra una richiesta di accettazione dell'invito." lightbox="./media/manage-tenants/accept-invitation.png" :::

Dopo l'accettazione, possono [visualizzare l'account Contratto del cliente Microsoft fatturazione in Gestione costi e fatturazione](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement).

:::image type="content" source="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" alt-text="Screenshot che mostra il Contratto del cliente Microsoft nell'elenco degli account di fatturazione." lightbox="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" :::

L'autorizzazione per invitare utenti guest è controllata dalle impostazioni Azure AD guest. Il valore delle impostazioni viene visualizzato in **Impostazioni nella** pagina **Relazioni organizzative.** Assicurarsi che l'impostazione sia selezionata, in caso contrario l'invito ha esito negativo. Per altre informazioni, vedere [Limitare le autorizzazioni di accesso degli utenti guest.](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)

:::image type="content" source="./media/manage-tenants/external-collaboration-settings.png" alt-text="Screenshot che mostra le impostazioni di collaborazione esterna." lightbox="./media/manage-tenants/external-collaboration-settings.png" :::

> [!IMPORTANT]
> Gli utenti guest ottengono l'accesso Contratto del cliente Microsoft tenant, che può potenzialmente costituire un problema di sicurezza. Per altre informazioni, vedere [Informazioni su come limitare le autorizzazioni predefinite degli utenti guest.](../../active-directory/fundamentals/users-default-permissions.md#restrict-member-users-default-permissions)

## <a name="manage-multiple-microsoft-cloud-services-under-an-azure-ad-tenant"></a>Gestire più servizi cloud Microsoft in un tenant Azure AD microsoft

È possibile gestire più servizi cloud per l'organizzazione in un singolo tenant Azure AD cloud. Gli account utente per tutte le offerte cloud di Microsoft vengono archiviati nel tenant Azure AD, che contiene account utente e gruppi. Il diagramma seguente illustra un esempio di un'organizzazione con più servizi che usa un tenant Azure AD che contiene gli account. Ogni servizio ha il proprio portale, in testo blu, in cui gli utenti gestiscono i propri servizi.

:::image type="content" source="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png" alt-text="Diagramma che mostra un esempio di un'organizzazione con più servizi che usa un tenant Azure AD tenant contenente gli account." border="false" lightbox="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png":::

## <a name="next-steps"></a>Passaggi successivi

Leggere gli articoli seguenti per informazioni su come amministrare la proprietà della fatturazione flessibile e garantire l'accesso sicuro alle Contratto del cliente Microsoft.

- [Come configurare un tenant](../../active-directory/develop/quickstart-create-new-tenant.md)
- [Ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md)
- [Trasferire una sottoscrizione di Azure in una directory Azure AD diversa](../../role-based-access-control/transfer-subscription.md)
- [Limitare le autorizzazioni di accesso guest (anteprima) in Azure Active Directory](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)
- [Aggiungere utenti guest alla directory nel portale di Azure](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Autorizzazioni utente predefinite in Azure Active Directory](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Che cos'è Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)