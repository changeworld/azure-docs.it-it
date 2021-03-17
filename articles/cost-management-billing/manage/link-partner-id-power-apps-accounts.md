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
ms.openlocfilehash: adaff7a6b8559fe9604412a44eced6c490231e3c
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103603830"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>Collegare un ID partner agli account Power Apps

Questo articolo consente ai partner Microsoft, che sono provider di servizi Power Apps, di associare il servizio ai clienti di Microsoft Power Apps. Quando l'utente (partner Microsoft) gestisce, configura e supporta i servizi Power Apps per il cliente, è possibile accedere all'ambiente del cliente. È possibile usare le credenziali di Azure e un collegamento di amministratore partner (PAL) per associare l'ID di rete del partner con le credenziali dell'account usate per la distribuzione del servizio.

Il PAL consente a Microsoft di identificare e riconoscere i partner con clienti di Power Apps. Utilizzo degli attributi Microsoft per l'organizzazione di un partner in base alle autorizzazioni dell'account (ruolo Power Apps) e all'ambito (tenant, gruppo di risorse, risorsa).

## <a name="get-access-from-your-customer"></a>Ottenere l'accesso dal cliente

Prima di collegare l'ID partner, il cliente deve consentire l'accesso alle risorse di Power Apps usando una delle opzioni seguenti:

- **Utente Guest** : il cliente può aggiungerlo come utente Guest e assegnare tutti i ruoli di Power Apps. Per altre informazioni, vedere [Aggiungere utenti guest da un'altra directory](../../active-directory/external-identities/what-is-b2b.md).
- **Account directory** : il cliente può creare un account utente nella propria directory e assegnare un ruolo Power Apps.
- **Entità servizio** : il cliente può aggiungere un'app o uno script dall'organizzazione nella propria directory e assegnare qualsiasi ruolo Power Apps. L'identità dell'app o dello script viene definita entità servizio.
- **Amministratore delegato** : il cliente può delegare un gruppo di risorse in modo che gli utenti possano lavorare su di esso dall'interno del tenant. Per ulteriori informazioni, vedere [per i partner: amministratore delegato](/power-platform/admin/for-partners-delegated-administrator).

## <a name="link-customer-to-a-partner-id"></a>Collegare il cliente a un ID partner

Quando si ha accesso alle risorse del cliente, usare la portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per collegare l'ID del Microsoft Partner Network (MPN ID) all'ID utente o all'entità servizio. Collegare l'ID partner a ogni tenant del cliente.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Usare il portale di Azure per creare il collegamento a un nuovo ID partner

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a [Collega a un ID partner](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) nel portale di Azure.
1. Immettere l'ID [Microsoft Partner Network](https://partner.microsoft.com/) per l'organizzazione. Assicurarsi di usare l'  **ID MPN associato**  indicato nel profilo partner.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="Screenshot che mostra il collegamento a una finestra ID partner." lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. Per collegare l'ID partner a un altro cliente, passare alla directory. In **Cambia directory** selezionare la directory appropriata.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="Screenshot che mostra la finestra Directory + sottoscrizione in cui è possibile cambiare la directory." lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Usare PowerShell per creare il collegamento a un nuovo ID partner

Installare il modulo [AZ. ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) Azure PowerShell.

Accedere al tenant del cliente con l'account utente o l'entità servizio. Per altre informazioni, vedere [Sign in with PowerShell](/powershell/azure/authenticate-azureps) (Accedere con PowerShell).

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Creare il collegamento al nuovo ID partner. L'ID partner è l'ID [Microsoft Partner Network](https://partner.microsoft.com/) dell'organizzazione. Assicurarsi di usare l' **ID MPN associato**  indicato nel profilo partner.

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

Innanzitutto, installare l'estensione dell'interfaccia della riga di comando di Azure.

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

## <a name="frequently-asked-questions-faq"></a>Domande frequenti

Le sezioni seguenti illustrano le domande frequenti relative al collegamento di un ID partner per gli account Power Apps.

### <a name="who-should-link-the-partner-id"></a>Chi deve collegare l'ID partner?

Tutti gli utenti dell'organizzazione partner che lavorano sulle risorse Power Apps di un cliente possono collegare l'ID partner all'account. Idealmente, l'associazione in PAL deve essere eseguita all'inizio del progetto. Tuttavia, può essere eseguita ogni volta che si dispone dell'accesso alla directory del cliente.

### <a name="can-a-partner-id-be-changed-after-its-linked"></a>Un ID partner può essere modificato dopo essere stato collegato?

Sì. Un ID partner collegato può essere modificato, aggiunto o rimosso. Un esempio di questa situazione potrebbe essere quando un dipendente dell'azienda lascia l'organizzazione. Un altro esempio potrebbe essere quello in cui termina un progetto o un contratto con il cliente.

### <a name="what-if-a-user-has-an-account-in-more-than-one-customer-tenant"></a>Cosa accade se un utente ha un account in più di un tenant del cliente?

Il collegamento tra l'ID partner e l'account viene eseguito per ogni tenant del cliente. Collegare l'ID partner in ogni tenant del cliente.

### <a name="can-other-partners-or-customers-edit-or-remove-the-link-to-the-partner-id"></a>Altri partner o clienti possono modificare o rimuovere il collegamento all'ID partner?

Il collegamento è associato a livello di account utente. Solo il titolare dell'account può modificare o rimuovere il collegamento all'ID partner. Il cliente e altri partner non possono modificare il collegamento all'ID partner.

### <a name="which-mpn-id-should-i-use-if-my-company-has-multiple"></a>Quale ID MPN è necessario usare se l'azienda ne ha più di uno?

Assicurarsi di usare l'**ID MPN associato** visualizzato nel profilo partner. Si tratta in genere dell'associazione di ID account locale con l'organizzazione.

### <a name="how-do-i-explain-pal-to-my-customer"></a>Ricerca per categorie spiegare a PAL il cliente?

PAL consente a Microsoft di identificare e riconoscere i partner che aiutano i clienti a raggiungere gli obiettivi aziendali e a realizzare un valore nel cloud. I clienti devono innanzitutto fornire un partner per accedere alla risorsa Power Apps. Una volta concesso l'accesso, viene associato l'ID Microsoft Partner Network del partner (ID MPN). Questa associazione aiuta Microsoft a comprendere i provider di servizi e a perfezionare gli strumenti e i programmi necessari per supportare al meglio i clienti.

### <a name="what-data-does-pal-collect"></a>Quali dati vengono raccolti dal collegamento amministrazione partner?

L'associazione del collegamento amministrazione partner alle credenziali esistenti non fornisce alcun nuovo dato del cliente a Microsoft. Fornisce le informazioni a Microsoft, in cui un partner è attivamente coinvolti negli ambienti Power Apps di un cliente. Microsoft può attribuire l'utilizzo e l'influenza dell'ambiente del cliente all'organizzazione partner in base alle autorizzazioni dell'account (ruolo Power Apps) e all'ambito (tenant, gruppo di risorse, risorsa) fornito al partner dal cliente.

### <a name="does-pal-association-affect-the-security-of-a-customers-power-apps-environment"></a>L'associazione PAL influisce sulla sicurezza dell'ambiente Power Apps di un cliente?

L'associazione PAL aggiunge solo l'ID MPN del partner alla credenziale già sottoposta a provisioning. Non modifica alcuna autorizzazione (ruolo Power Apps) o fornisce dati del servizio Power Apps aggiuntivi al partner o a Microsoft.

### <a name="next-steps"></a>Passaggi successivi

- Partecipare alla discussione [Community Partner Microsoft](https://aka.ms/PALdiscussion) per ricevere aggiornamenti o inviare commenti.
- Leggi le [domande frequenti sulla specializzazione avanzata per lo sviluppo di applicazioni con codice basso](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf) per l'associazione di app Power Apps basate su PAL per lo sviluppo di applicazioni con codice basso avanzata
