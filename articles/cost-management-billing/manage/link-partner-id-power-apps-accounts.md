---
title: Collegare un ID partner agli account Power Apps con le credenziali di Azure
description: Questo articolo aiuta i partner Microsoft a usare le credenziali di Azure per aiutare i clienti a usare Microsoft Power Apps.
author: bandersmsft
ms.reviewer: akangaw
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: banders
ms.openlocfilehash: acb22cc4b2a461e476131a83972db3e782425a39
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727709"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>Collegare un ID partner agli account Power Apps account

Questo articolo consente ai partner Microsoft, che sono Power Apps provider di servizi, di associare il servizio ai clienti in Microsoft Power Apps. Quando il partner Microsoft gestisce, configura e supporta i servizi Power Apps per il cliente, ha accesso all'ambiente del cliente. È possibile usare le credenziali di Azure e un collegamento di amministratore partner per associare l'ID della rete partner alle credenziali dell'account usate per la distribuzione del servizio.

L'elenco di accesso alla pubblicazione consente a Microsoft di identificare e riconoscere i partner che hanno Power Apps clienti. Microsoft attribirà l'utilizzo all'organizzazione di un partner in base alle autorizzazioni dell'account (ruolo Power Apps) e all'ambito (tenant, gruppo di risorse, risorsa).

## <a name="get-access-from-your-customer"></a>Ottenere l'accesso dal cliente

Prima di collegare l'ID partner, il cliente deve concedere l'accesso alle risorse Power Apps usando una delle opzioni seguenti:

- **Utente guest:** il cliente può aggiungere l'utente come utente guest e assegnare Power Apps ruolo. Per altre informazioni, vedere [Aggiungere utenti guest da un'altra directory](../../active-directory/external-identities/what-is-b2b.md).
- **Account directory:** il cliente può creare un account utente nella propria directory e assegnare qualsiasi ruolo Power Apps utente.
- **Entità servizio:** il cliente può aggiungere un'app o uno script dall'organizzazione nella propria directory e assegnare qualsiasi Power Apps ruolo. L'identità dell'app o dello script viene definita entità servizio.
- **Amministratore delegato:** il cliente può delegare un gruppo di risorse in modo che gli utenti possano lavorare su di esso dall'interno del tenant. Per altre informazioni, vedere [Per i partner: amministratore delegato.](/power-platform/admin/for-partners-delegated-administrator)

## <a name="link-customer-to-a-partner-id"></a>Collegare un cliente a un ID partner

Quando si ha accesso alle risorse del cliente, usare portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per collegare l'ID Microsoft Partner Network (ID MPN) all'ID utente o all'entità servizio. Collegare l'ID partner a ogni tenant del cliente.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Usare il portale di Azure per creare il collegamento a un nuovo ID partner

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a [Collega a un ID partner](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) nel portale di Azure.
1. Immettere [l'ID Microsoft Partner Network](https://partner.microsoft.com/) per l'organizzazione. Assicurarsi di usare  **l'ID MPN associato**  visualizzato nel profilo partner.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="Screenshot che mostra la finestra Collegamento a un ID partner." lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. Per collegare l'ID partner a un altro cliente, passare alla directory. In **Cambia directory** selezionare la directory appropriata.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="Screenshot che mostra la finestra Directory e sottoscrizione in cui è possibile cambiare directory." lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Usare PowerShell per creare il collegamento a un nuovo ID partner

Installare il [modulo Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) Azure PowerShell.

Accedere al tenant del cliente con l'account utente o l'entità servizio. Per altre informazioni, vedere [Sign in with PowerShell](/powershell/azure/authenticate-azureps) (Accedere con PowerShell).

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Creare il collegamento al nuovo ID partner. L'ID partner è l'ID [Microsoft Partner Network](https://partner.microsoft.com/) dell'organizzazione. Assicurarsi di usare **l'ID MPN associato**  visualizzato nel profilo partner.

```azurepowershell-interactive
new-AzManagementPartner -PartnerId 12345
```

Ottenere l'ID partner collegato

```azurepowershell-interactive
get-AzManagementPartner
```

Aggiornare l'ID partner collegato

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Eliminare l'ID partner collegato

```azurepowershell-interactive
remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Usare l'interfaccia della riga di comando di Azure per creare il collegamento a un nuovo ID partner

Installare prima di tutto l'estensione dell'interfaccia della riga di comando di Azure.

```azurecli-interactive
az extension add --name managementpartner
```

Accedere al tenant del cliente con l'account utente o l'entità servizio. Per altre informazioni, vedere [Accedere tramite l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli).

```azurecli-interactive
az login --tenant TenantName
```

Creare il collegamento al nuovo ID partner. L'ID partner è l'ID [Microsoft Partner Network](https://partner.microsoft.com/) dell'organizzazione.

```azurecli-interactive
az managementpartner create --partner-id 12345
```

Ottenere l'ID partner collegato

```azurecli-interactive
az managementpartner show
```

Aggiornare l'ID partner collegato

```azurecli-interactive
az managementpartner update --partner-id 12345
```

Eliminare l'ID partner collegato

```azurecli-interactive
az managementpartner delete --partner-id 12345
```

### <a name="next-steps"></a>Passaggi successivi

- Leggere le [domande frequenti su Gestione costi e fatturazione](../cost-management-billing-faq.yml) per domande e risposte sul collegamento di un ID partner Power Apps account.
- Partecipare alla discussione [Community Partner Microsoft](https://aka.ms/PALdiscussion) per ricevere aggiornamenti o inviare commenti.
- Leggere le domande [frequenti sulla specializzazione](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf) avanzata sviluppo di applicazioni a basso codice per l'associazione di Power Apps basata su PAL per la specializzazione avanzata per lo sviluppo di applicazioni a basso codice.
