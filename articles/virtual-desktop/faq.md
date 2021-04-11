---
title: Domande frequenti sul desktop virtuale Windows-Azure
description: Domande frequenti e procedure consigliate per desktop virtuale di Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 1f5e4cb0d2db30c6b07370be137506f3fe26837f
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505298"
---
# <a name="windows-virtual-desktop-faq"></a>Domande frequenti su Desktop virtuale Windows

Questo articolo risponde alle domande frequenti e spiega le procedure consigliate per desktop virtuale di Windows.

## <a name="what-are-the-minimum-admin-permissions-i-need-to-manage-objects"></a>Quali sono le autorizzazioni di amministratore minime necessarie per gestire gli oggetti?

Se si desidera creare pool host e altri oggetti, è necessario disporre del ruolo Collaboratore nella sottoscrizione o nel gruppo di risorse che si sta utilizzando.

È necessario disporre del ruolo amministratore accesso utenti per un gruppo di app per pubblicare gruppi di app per utenti o gruppi di utenti.

Per impedire a un amministratore di gestire solo le sessioni utente, ad esempio l'invio di messaggi agli utenti, la disconnessione degli utenti e così via, è possibile creare ruoli personalizzati. Ad esempio:

```powershell
"actions": [
"Microsoft.Resources/deployments/operations/read",
"Microsoft.Resources/tags/read",
"Microsoft.Authorization/roleAssignments/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
],
"notActions": [],
"dataActions": [],
"notDataActions": []
}
```

## <a name="does-windows-virtual-desktop-support-split-azure-active-directory-models"></a>Windows Virtual Desktop supporta la suddivisione di modelli Azure Active Directory?

Quando un utente viene assegnato a un gruppo di app, il servizio esegue una semplice assegnazione di ruolo di Azure. Di conseguenza, l'Azure Active Directory dell'utente (AD) e il Azure AD del gruppo di app devono trovarsi nella stessa posizione. Tutti gli oggetti servizio, ad esempio i pool host, i gruppi di app e le aree di lavoro, devono trovarsi anche nello stesso Azure AD dell'utente.

È possibile creare macchine virtuali (VM) in un Azure AD diverso, purché la Active Directory venga sincronizzata con il Azure AD dell'utente nella stessa rete virtuale (VNET).

## <a name="what-are-location-restrictions"></a>Che cosa sono le restrizioni relative alla località?

A tutte le risorse del servizio è associata una località. Il percorso di un pool host determina la geografia in cui sono archiviati i metadati del servizio per il pool host. Un gruppo di app non può esistere senza un pool host. Se si aggiungono app a un gruppo di app RemoteApp, sarà necessario anche un host di sessione per determinare le app del menu Start. Per qualsiasi azione del gruppo di app, è necessario anche un accesso ai dati correlati nel pool host. Per assicurarsi che i dati non vengano trasferiti tra più posizioni, la posizione del gruppo di app deve essere identica a quella del pool host.

Anche le aree di lavoro devono trovarsi nella stessa posizione dei gruppi di app. Ogni volta che l'area di lavoro viene aggiornata, il gruppo di app correlato viene aggiornato insieme a esso. Come per i gruppi di app, il servizio richiede che tutte le aree di lavoro siano associate ai gruppi di app creati nella stessa posizione.

## <a name="how-do-you-expand-an-objects-properties-in-powershell"></a>Come si espandono le proprietà di un oggetto in PowerShell?

Quando si esegue un cmdlet di PowerShell, vengono visualizzati solo il nome e il percorso della risorsa.

Ad esempio:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg

Location Name   Type
-------- ----   ----
westus   0224hp Microsoft.DesktopVirtualization/hostpools
```

Per visualizzare tutte le proprietà di una risorsa, aggiungere `format-list` o `fl` alla fine del cmdlet.

Ad esempio:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg |fl
```

Per visualizzare proprietà specifiche, aggiungere i nomi di proprietà specifici dopo `format-list` o `fl` .

Ad esempio:

```powershell
Get-AzWvdHostPool -Name demohp -ResourceGroupName 0414rg |fl CustomRdpProperty

CustomRdpProperty : audiocapturemode:i:0;audiomode:i:0;drivestoredirect:s:;redirectclipboard:i:1;redirectcomports:i:0;redirectprinters:i:1;redirectsmartcards:i:1;screen modeid:i:2;
```

## <a name="does-windows-virtual-desktop-support-guest-users"></a>Il desktop virtuale Windows supporta gli utenti Guest?

Desktop virtuale di Windows non supporta Azure AD account utente Guest. Si immagini, ad esempio, che un gruppo di utenti Guest abbia Microsoft 365 le licenze E3 per utente, Windows E3 per utente o WIN VDA nella propria azienda, ma siano utenti guest in una Azure AD aziendale diversa. L'altra società gestirebbe gli oggetti utente guest in Azure AD e Active Directory come gli account locali.

Non è possibile usare le proprie licenze per il vantaggio di terze parti. Inoltre, il desktop virtuale Windows attualmente non supporta l'account Microsoft (MSA).

## <a name="why-dont-i-see-the-client-ip-address-in-the-wvdconnections-table"></a>Perché l'indirizzo IP del client non viene visualizzato nella tabella WVDConnections?

Attualmente non è disponibile un modo affidabile per raccogliere gli indirizzi IP del client Web, pertanto non è necessario includere tale valore nella tabella.

## <a name="how-does-windows-virtual-desktop-handle-backups"></a>In che modo il desktop virtuale Windows gestisce i backup?

Sono disponibili più opzioni in Azure per la gestione del backup. È possibile usare backup di Azure, Site Recovery e snapshot.

## <a name="does-windows-virtual-desktop-support-third-party-collaboration-apps"></a>Il desktop virtuale Windows supporta le app di collaborazione di terze parti?

Desktop virtuale Windows è attualmente ottimizzato per i team. Microsoft non supporta attualmente app di collaborazione di terze parti, ad esempio zoom. Le organizzazioni di terze parti sono responsabili di fornire linee guida per la compatibilità ai clienti. Anche il desktop virtuale Windows non supporta Skype for business.

## <a name="can-i-change-from-pooled-to-personal-host-pools"></a>È possibile passare da pool a pool di host personali?

Dopo aver creato un pool di host, non è possibile modificarne il tipo. Tuttavia, è possibile spostare le macchine virtuali registrate in un pool host in un tipo diverso di pool host.

## <a name="whats-the-largest-profile-size-fslogix-can-handle"></a>Quali sono le dimensioni massime del profilo che FSLogix può gestire?

Limitazioni o quote in FSLogix dipendono dall'infrastruttura di archiviazione usata per archiviare i file VHD del profilo utente (X).

Nella tabella seguente viene illustrato un esempio del numero di IOPS che un profilo FSLogix deve supportare per ogni utente. I requisiti possono variare notevolmente a seconda dell'utente, delle applicazioni e dell'attività per ogni profilo.

| Risorsa | Requisito |
|---|---|
| IOPS a stato stazionario | 10 |
| IOPS di accesso/disconnessione | 50 |

L'esempio in questa tabella è di un singolo utente, ma può essere usato per stimare i requisiti per il numero totale di utenti nell'ambiente. Ad esempio, sono necessari circa 1.000 IOPS per utenti 100 e circa 5.000 IOPS durante l'accesso e la disconnessione.

## <a name="is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal"></a>Esiste un limite di scalabilità per i pool host creati nella portale di Azure?

Questi fattori possono influenzare il limite di scalabilità per i pool host:

- Il modello di Azure è limitato a 800 oggetti. Per altre informazioni, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md#template-limits). Ogni VM crea anche circa sei oggetti, quindi è possibile creare circa 132 VM ogni volta che si esegue il modello.

- Esistono restrizioni sul numero di core che è possibile creare per area e per sottoscrizione. Ad esempio, se si dispone di una sottoscrizione di Contratto Enterprise, è possibile creare 350 core. È necessario dividere 350 per il numero predefinito di core per macchina virtuale o per il limite di core per determinare il numero di macchine virtuali che è possibile creare ogni volta che si esegue il modello. Per altre informazioni [, vedere limiti delle macchine virtuali-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits---azure-resource-manager).

- Il nome del prefisso della macchina virtuale e il numero di macchine virtuali sono inferiori a 15 caratteri. Per altre informazioni, vedere [regole di denominazione e restrizioni per le risorse di Azure](../azure-resource-manager/management/resource-name-rules.md#microsoftcompute).

## <a name="can-i-manage-windows-virtual-desktop-environments-with-azure-lighthouse"></a>È possibile gestire gli ambienti desktop virtuali Windows con Azure Lighthouse?

Azure Lighthouse non supporta completamente la gestione di ambienti desktop virtuali Windows. Poiché Lighthouse non supporta attualmente la gestione degli utenti del tenant Cross-Azure AD, i clienti Lighthouse devono ancora accedere al Azure AD che i clienti usano per gestire gli utenti.

Non è inoltre possibile utilizzare le sottoscrizioni CSP sandbox con il servizio desktop virtuale di Windows. Per altre informazioni, vedere [account sandbox di integrazione](/partner-center/develop/set-up-api-access-in-partner-center#integration-sandbox-account).

Infine, se è stato abilitato il provider di risorse dall'account del proprietario CSP, gli account del cliente CSP non saranno in grado di modificare il provider di risorse.

## <a name="how-often-should-i-turn-my-vms-on-to-prevent-registration-issues"></a>Con quale frequenza è necessario attivare le VM per evitare problemi di registrazione?

Dopo aver registrato una macchina virtuale in un pool di host nel servizio desktop virtuale di Windows, l'agente aggiorna periodicamente il token della macchina virtuale ogni volta che la macchina virtuale è attiva. Il certificato per il token di registrazione è valido per 90 giorni. A causa di questo limite di 90 giorni, è consigliabile che le macchine virtuali siano online per 20 minuti ogni 90 giorni, in modo che il computer possa aggiornare i propri token e aggiornare l'agente e i componenti dello stack affiancato. Se la macchina virtuale viene riattivata entro questo limite di tempo, il token di registrazione non scadrà né diventerà non valido. Se la macchina virtuale è stata avviata dopo 90 giorni e si verificano problemi di registrazione, seguire le istruzioni riportate nella [Guida alla risoluzione dei problemi dell'agente desktop virtuale di Windows](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved) per rimuovere la macchina virtuale dal pool host, reinstallare l'agente e registrarla nuovamente nel pool.

## <a name="can-i-set-availability-options-when-creating-host-pools"></a>È possibile impostare le opzioni di disponibilità durante la creazione di pool host?

Sì. Quando si crea una macchina virtuale, i pool host di desktop virtuali di Windows hanno un'opzione per selezionare il set di disponibilità o le zone di disponibilità. Queste opzioni di disponibilità sono identiche a quelle usate da calcolo di Azure. Se si seleziona una zona per la macchina virtuale creata in un pool host, l'impostazione viene applicata automaticamente a tutte le macchine virtuali create in tale zona. Se si preferisce distribuire le VM del pool host in più zone, è necessario seguire le istruzioni in [aggiungere macchine virtuali con il portale di Azure](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal) per selezionare manualmente una nuova zona per ogni nuova macchina virtuale creata.

## <a name="which-availability-option-is-best-for-me"></a>Quale opzione di disponibilità è migliore per me?

L'opzione di disponibilità da usare per le VM dipende dalla posizione dell'immagine e dai relativi campi del disco gestito. Nella tabella seguente viene illustrata la relazione di ogni impostazione con queste variabili che consentono di individuare l'opzione più adatta alla distribuzione. 

| Opzione di disponibilità | Posizione dell'immagine | Pulsante di opzione Usa disco gestito (pulsante di opzione) |
|---|---|---|
| Nessuno | Raccolta | Disabilitato con "Sì" come predefinito |
| Nessuno | Archiviazione BLOB | Abilitato con "No" come predefinito |
| Zona di disponibilità | Raccolta (opzione di archiviazione BLOB disabilitata) | Disabilitato con "Sì" come predefinito |
| Set di disponibilità con SKU gestito (disco gestito) | Raccolta | Disabilitato con "Sì" come predefinito |
| Set di disponibilità con SKU gestito (disco gestito) | Archiviazione BLOB | Abilitato con "No" come predefinito |
| Set di disponibilità con SKU gestito (disco gestito) | Archiviazione BLOB (opzione raccolta disabilitata) | Disabilitato con "No" come predefinito |
| Set di disponibilità (appena creato dall'utente) | Raccolta | Disabilitato con "Sì" come predefinito |
| Set di disponibilità (appena creato dall'utente) | Archiviazione BLOB | Abilitato con "No" come predefinito |
