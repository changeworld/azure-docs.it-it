---
title: Aggiornare una delega
description: Informazioni su come aggiornare una delega per un cliente caricato in precedenza in Azure Lighthouse.
ms.date: 12/03/2020
ms.topic: how-to
ms.openlocfilehash: eec5d1f2d34e8e3c9e0ded59cb111624b1981873
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791340"
---
# <a name="update-a-delegation"></a>Aggiornare una delega

Dopo aver caricato una sottoscrizione o un gruppo di risorse in Azure Lighthouse, potrebbe essere necessario apportare modifiche. È ad esempio possibile che il cliente desideri eseguire attività di gestione aggiuntive che richiedono un ruolo predefinito di Azure diverso o che sia necessario modificare il tenant a cui viene delegata una sottoscrizione del cliente.

> [!TIP]
> Anche se si fa riferimento a provider di servizi e clienti in questo argomento, le [aziende che gestiscono più tenant](../concepts/enterprise.md) possono usare lo stesso processo per configurare il faro di Azure e consolidare la loro esperienza di gestione.

Se il cliente è stato caricato [tramite modelli di Azure Resource Manager (modelli ARM)](onboard-customer.md), è necessario eseguire una nuova distribuzione per quel cliente. A seconda di ciò che si sta modificando, è consigliabile aggiornare l'offerta originale oppure rimuovere l'offerta originale e crearne una nuova.

- **Se si modificano solo le autorizzazioni**: è possibile aggiornare la delega cambiando solo la sezione **autorizzazioni** del modello ARM.
- **Se si modifica il tenant di gestione**: è necessario creare un nuovo modello ARM usando con un **mspOfferName** diverso rispetto all'offerta precedente.

## <a name="update-your-arm-template"></a>Aggiornare il modello ARM

Per aggiornare la delega, è necessario distribuire un modello ARM che includa le modifiche da apportare.

Se si aggiornano solo le autorizzazioni, ad esempio l'aggiunta di un nuovo gruppo di utenti con un ruolo non incluso in precedenza o la modifica del ruolo per un utente esistente, è possibile usare lo stesso **mspOfferName** del [modello ARM](onboard-customer.md#create-an-azure-resource-manager-template) usato per la delega precedente. È possibile usare il modello precedente come punto di partenza. Apportare quindi le modifiche necessarie, ad esempio sostituendo un ruolo predefinito di Azure con un altro o aggiungendo un'autorizzazione completamente nuova al modello.

Se si modifica il **mspOfferName**, questa sarà considerata una nuova offerta separata. Questa operazione è necessaria se si modifica il tenant di gestione.

Non è necessario modificare il **mspOfferName** se il tenant di gestione rimane invariato. Nella maggior parte dei casi, è consigliabile avere un solo **mspOfferName** usato dallo stesso cliente e gestire il tenant. Se si sceglie di modificarlo comunque, assicurarsi che la delega precedente del cliente venga rimossa prima di distribuirne una nuova.

## <a name="remove-the-previous-delegation"></a>Rimuovere la delega precedente

Prima di eseguire una nuova distribuzione, potrebbe essere necessario [rimuovere l'accesso alla delega precedente](remove-delegation.md). In questo modo si garantisce che tutte le autorizzazioni precedenti vengano rimosse, consentendo di iniziare a pulire con gli esatti utenti/gruppi e i ruoli da applicare in futuro.

> [!IMPORTANT]
> Se si usa un nuovo **mspOfferName** e si mantiene uno degli stessi valori di **PrincipalId** , è necessario rimuovere l'accesso alla delega precedente prima di distribuire la nuova offerta. Se non si rimuove prima l'offerta, gli utenti che hanno precedentemente concesso l'autorizzazione potrebbero perdere completamente l'accesso a causa di assegnazioni in conflitto.

Se si modifica il tenant di gestione, è possibile lasciare invariata l'offerta precedente se si vuole che entrambi i tenant continuino ad avere accesso. Se si desidera che il nuovo tenant di gestione disponga dell'accesso, è necessario rimuovere l'offerta precedente. Questa operazione può essere eseguita prima o dopo l'onboarding della nuova offerta.

Se si aggiorna l'offerta per modificare solo le autorizzazioni e si mantiene lo stesso **mspOfferName**, non è necessario rimuovere la delega precedente. Con la nuova distribuzione la delega precedente viene sostituita e verranno applicate solo le autorizzazioni nel modello più recente.

:::image type="content" source="../media/update-delegation.jpg" alt-text="Diagramma che mostra quando modificare mspOfferName e rimuovere una delega precedente.":::

La rimozione dell'accesso alla delega può essere eseguita da qualsiasi utente nel tenant di gestione a cui è stato concesso il [ruolo eliminazione di assegnazione di registrazione dei servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) nella delega originale. Se nessun utente nel tenant di gestione dispone di questo ruolo, è possibile richiedere al cliente di [rimuovere l'accesso all'offerta nell'portale di Azure](view-manage-service-providers.md#add-or-remove-service-provider-offers).

> [!TIP]
> Se la delega precedente è stata rimossa seguendo i passaggi precedenti e non è ancora possibile distribuire il nuovo modello ARM, potrebbe essere necessario [rimuovere completamente la definizione di registrazione](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition). Questa operazione può essere eseguita da qualsiasi utente con il ruolo proprietario nel tenant del cliente.  

## <a name="deploy-the-arm-template"></a>Distribuire il modello di Resource Manager

Il cliente può [distribuire il modello aggiornato](onboard-customer.md#deploy-the-azure-resource-manager-templates) in modo analogo a quanto avveniva in precedenza: nella portale di Azure, usando PowerShell o l'interfaccia della riga di comando di Azure.

Al termine della distribuzione, [verificare che sia stata eseguita correttamente](onboard-customer.md#confirm-successful-onboarding). Le autorizzazioni aggiornate saranno quindi valide per la sottoscrizione o i gruppi di risorse delegati dal cliente.

## <a name="updating-managed-service-offers"></a>Aggiornamento delle offerte del servizio gestito

Se il cliente è stato caricato tramite un'offerta di servizio gestito pubblicata in Azure Marketplace e si desidera aggiornare le autorizzazioni, è possibile aggiornare la delega [pubblicando una nuova versione dell'offerta](../../marketplace/partner-center-portal/update-existing-offer.md) con le [autorizzazioni](../../marketplace/plan-managed-service-offer.md) che si desidera utilizzare aggiornate nel piano del cliente. Il cliente sarà quindi in grado di eseguire l'aggiornamento alla versione più recente nel portale di Azure.

Se si desidera modificare il tenant di gestione, sarà necessario [creare e pubblicare una nuova offerta di servizio gestito](../../marketplace/plan-managed-service-offer.md) che il cliente possa accettare.

> [!TIP]
> Come indicato in precedenza, è consigliabile non usare più offerte diverse tra lo stesso cliente e la gestione del tenant. Se si pubblica una nuova offerta per lo stesso cliente che usa lo stesso tenant di gestione, assicurarsi che l'offerta precedente venga rimossa prima che il cliente accetti l'offerta più recente.

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare e gestire i clienti](view-manage-customers.md) passando a **Clienti personali** nel portale di Azure.
- Informazioni su come [rimuovere l'accesso a una delega](remove-delegation.md) di cui in precedenza è stato eseguito l'onboarding.
