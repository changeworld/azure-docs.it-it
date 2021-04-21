---
title: Accedere alla macchina virtuale Windows in Azure usando Azure Active Directory (anteprima)
description: Azure AD accedere a una macchina virtuale di Azure che esegue Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 418741c10dfe5f0678d7771d046781697512bafe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776502"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Accedere alla macchina virtuale Windows in Azure usando Azure Active Directory autenticazione (anteprima)

Le organizzazioni possono ora usare Azure Active Directory (AD) per le macchine virtuali di Azure che eseguono **Windows Server 2019 Datacenter Edition** o Windows 10 **1809** e versioni successive. L Azure AD per l'autenticazione alle macchine virtuali consente di controllare e applicare i criteri in modo centralizzato. Strumenti come il controllo degli accessi in base al ruolo di Azure e Azure AD l'accesso condizionale consentono di controllare chi può accedere a una macchina virtuale. Questo articolo illustra come creare e configurare una macchina virtuale Windows Server 2019 per usare Azure AD autenticazione.

> [!NOTE]
> Azure AD'accesso per le macchine virtuali Windows di Azure è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere Condizioni per l'utilizzo supplementari  [per Microsoft Azure anteprime](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L'uso dell'autenticazione Azure AD per accedere alle macchine virtuali Windows in Azure offre numerosi vantaggi, tra cui:

- Usare le stesse credenziali federate o Azure AD usate normalmente.
- Non è più necessario gestire gli account di amministratore locale.
- Il controllo degli accessi in base al ruolo di Azure consente di concedere l'accesso appropriato alle macchine virtuali in base alle esigenze e di rimuoverlo quando non è più necessario.
- Prima di consentire l'accesso a una macchina virtuale, Azure AD'accesso condizionale può applicare requisiti aggiuntivi, ad esempio: 
   - Autenticazione a più fattori
   - Controllo dei rischi di accesso
- Automatizzare e ridimensionare Azure AD'aggiunta di macchine virtuali Windows di Azure che fanno parte delle distribuzioni VDI.

> [!NOTE]
> Dopo aver abilitato questa funzionalità, le macchine virtuali Windows in Azure verranno Azure AD unite. Non è possibile aggiungerlo ad altri domini, ad esempio AD locale o Azure AD DS. Se è necessario, è necessario disconnettere la macchina virtuale dal tenant Azure AD disinstallando l'estensione.

## <a name="requirements"></a>Requisiti

### <a name="supported-azure-regions-and-windows-distributions"></a>Aree di Azure supportate e distribuzioni di Windows

Durante l'anteprima di questa funzionalità sono attualmente supportate le distribuzioni di Windows seguenti:

- Windows Server 2019 Datacenter
- Windows 10 1809 e versioni successive

> [!IMPORTANT]
> La connessione remota alle macchine virtuali unite a Azure AD è consentita solo da PC Windows 10 registrati Azure AD (a partire da Windows 10 20H1), Azure AD aggiunti Azure AD o Azure AD ibridi aggiunti alla stessa **directory** della macchina virtuale. 

Durante l'anteprima di questa funzionalità sono attualmente supportate le aree di Azure seguenti:

- Tutte le aree globali di Azure

> [!IMPORTANT]
> Per usare questa funzionalità di anteprima, distribuire solo una distribuzione di Windows supportata e in un'area di Azure supportata. La funzionalità non è attualmente supportata nei cloud Azure per enti pubblici o sovrani.

### <a name="network-requirements"></a>Requisiti di rete

Per abilitare Azure AD per le macchine virtuali Windows in Azure, è necessario assicurarsi che la configurazione di rete delle macchine virtuali consenta l'accesso in uscita agli endpoint seguenti sulla porta TCP 443:

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://pas.windows.net`

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Abilitazione Azure AD'accesso per una macchina virtuale Windows in Azure

Per usare l'accesso Azure AD per una macchina virtuale Windows in Azure, è prima necessario abilitare l'opzione di accesso Azure AD per la macchina virtuale Windows e quindi configurare le assegnazioni di ruolo di Azure per gli utenti autorizzati ad accedere alla macchina virtuale.
È possibile abilitare l'accesso Azure AD per la macchina virtuale Windows in diversi modi:

- Uso dell'esperienza portale di Azure durante la creazione di una macchina virtuale Windows
- Uso dell'Azure Cloud Shell quando si crea una macchina virtuale Windows **o per una macchina virtuale Windows esistente**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Uso portale di Azure'esperienza di creazione di macchine virtuali per abilitare l Azure AD di accesso

È possibile abilitare l Azure AD di accesso per Windows Server 2019 Datacenter o Windows 10 immagini di macchine virtuali 1809 e versioni successive. 

Per creare una macchina virtuale Windows Server 2019 Datacenter in Azure con Azure AD accesso: 

1. Accedere al portale di Azure [,](https://portal.azure.com)con un account che abbia accesso per creare macchine virtuali e selezionare + Crea una **risorsa.**
1. Digitare **Windows Server** nella barra di ricerca del Marketplace.
   1. Fare **clic su Windows Server** e scegliere Windows Server **2019 Datacenter dall'elenco** a discesa Selezionare un piano software.
   1. Fare clic su **Crea**.
1. Nella scheda "Gestione" abilitare l'opzione Accedi con le credenziali **di AAD (anteprima)** nella sezione Azure Active Directory da Disattivato a **Attivato.**
1. Assicurarsi che **l'identità gestita assegnata dal** sistema nella sezione Identità sia impostata su **Sì.** Questa azione dovrebbe essere eseguita automaticamente dopo aver abilitato l'account di accesso Azure AD credenziali.
1. Passare attraverso il resto dell'esperienza di creazione di una macchina virtuale. Durante questa anteprima, sarà necessario creare un nome utente e una password amministratore per la macchina virtuale.

![Accedere con credenziali Azure AD creare una macchina virtuale](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Per accedere alla macchina virtuale usando le credenziali Azure AD, è prima necessario configurare le assegnazioni di ruolo per la macchina virtuale, come descritto in una delle sezioni seguenti.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Uso dell'Azure Cloud Shell per abilitare l'Azure AD di accesso

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Gli strumenti comuni di Azure sono preinstallati e configurati in Cloud Shell per l'uso con l'account. È sufficiente selezionare il pulsante Copia per copiare il codice, incollarlo in Cloud Shell e quindi premere Invio per eseguirlo. Esistono alcuni modi per aprire Cloud Shell:

- Selezionare **Prova** nell'angolo superiore destro di un blocco di codice.
- Aprire Cloud Shell nel browser.
- Selezionare il Cloud Shell nel menu nell'angolo superiore destro del portale di Azure [.](https://portal.azure.com)

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede che sia in esecuzione l'interfaccia della riga di comando di Azure versione 2.0.31 o successiva. Eseguire az --version per trovare la versione. Se è necessario installare o aggiornare, vedere l'articolo Installare l'interfaccia [della riga di comando di Azure.](/cli/azure/install-azure-cli)

1. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#az_group_create). 
1. Creare una macchina virtuale [con az vm create](/cli/azure/vm#az_vm_create) usando una distribuzione supportata in un'area supportata. 
1. Installare l'estensione Azure AD macchina virtuale di accesso. 

L'esempio seguente distribuisce una macchina virtuale denominata myVM che usa Win2019Datacenter in un gruppo di risorse denominato myResourceGroup, nell'area southcentralus. Negli esempi seguenti è possibile specificare i nomi del proprio gruppo di risorse e della macchina virtuale in base alle esigenze.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> È necessario abilitare l'identità gestita assegnata dal sistema nella macchina virtuale prima di installare l'estensione della macchina Azure AD di accesso.

La creazione della macchina virtuale e delle risorse di supporto richiede alcuni minuti.

Infine, installare l'estensione Azure AD macchina virtuale di accesso per abilitare Azure AD per la macchina virtuale Windows. Le estensioni della macchina virtuale sono piccole applicazioni che eseguono attività di configurazione e automazione post-distribuzione nelle macchine virtuali di Azure. Usare [az vm extension](/cli/azure/vm/extension#az_vm_extension_set) set per installare l'estensione AADLoginForWindows nella macchina virtuale denominata nel gruppo di `myVM` `myResourceGroup` risorse:

> [!NOTE]
> È possibile installare l'estensione AADLoginForWindows in una macchina virtuale esistente di Windows Server 2019 o Windows 10 1809 e versioni successive per abilitarla per l Azure AD authentication. Di seguito è riportato un esempio dell'interfaccia della riga di comando di AZ.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Viene `provisioningState` visualizzato il valore di dopo `Succeeded` l'installazione dell'estensione nella macchina virtuale.

## <a name="configure-role-assignments-for-the-vm"></a>Configurare le assegnazioni di ruolo per la macchina virtuale

Dopo aver creato la macchina virtuale, è necessario configurare i criteri di Controllo degli accessi in base al ruolo di Azure per determinare chi può accedere alla macchina virtuale. Per autorizzare l'accesso alla macchina virtuale vengono usati due ruoli di Azure:

- **Accesso amministratore macchina virtuale:** gli utenti con questo ruolo assegnato possono accedere a una macchina virtuale di Azure con privilegi di amministratore.
- **Accesso utente alle macchine virtuali**: gli utenti a cui è stato assegnato questo ruolo possono accedere a una macchina virtuale di Azure con i privilegi di utente normale.

> [!NOTE]
> Per consentire a un utente di accedere alla macchina virtuale tramite RDP, è necessario assegnare il ruolo Accesso amministratore macchina virtuale o Accesso utente macchina virtuale. Un utente di Azure con i ruoli Proprietario o Collaboratore assegnati per una macchina virtuale non ha automaticamente i privilegi per accedere alla macchina virtuale tramite RDP. Ciò consente di garantire la separazione controllata tra il set di persone che controllano le macchine virtuali e il set di persone che possono accedere alle macchine virtuali.

Esistono diversi modi per configurare le assegnazioni di ruolo per la macchina virtuale:

- Uso dell'esperienza Azure AD portale
- Uso dell'Azure Cloud Shell utente

> [!NOTE]
> I ruoli Accesso amministratore macchina virtuale e Accesso utente macchina virtuale usano dataActions e pertanto non possono essere assegnati nell'ambito del gruppo di gestione. Attualmente questi ruoli possono essere assegnati solo nell'ambito della sottoscrizione, del gruppo di risorse o della risorsa.

### <a name="using-azure-ad-portal-experience"></a>Uso Azure AD portale

Per configurare le assegnazioni di ruolo per Azure AD macchine virtuali Windows Server 2019 Datacenter abilitate:

1. Passare alla pagina di panoramica della macchina virtuale specifica
1. Selezionare **Controllo di accesso (IAM)** dalle opzioni di menu
1. Selezionare **Aggiungi**, Aggiungi **assegnazione di ruolo per** aprire il riquadro Aggiungi assegnazione di ruolo.
1. **Nell'elenco a** discesa Ruolo selezionare un ruolo, ad esempio Account di accesso amministratore macchina virtuale **o** Accesso utente **macchina virtuale**.
1. Nel campo **Seleziona** selezionare un utente, un gruppo, un'entità servizio o un'identità gestita. Se l'entità di sicurezza non è visualizzata nell'elenco, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati, gli indirizzi e-mail e gli identificatori di oggetto.
1. Selezionare **Salva** per assegnare il ruolo.

Dopo qualche istante, all'entità di sicurezza verrà assegnato il ruolo per l'ambito selezionato.

![Assegnare ruoli agli utenti che accederanno alla macchina virtuale](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Uso dell'Azure Cloud Shell utente

L'esempio seguente usa [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) per assegnare il ruolo Accesso amministratore alle macchine virtuali alla macchina virtuale per l'utente di Azure corrente. Il nome utente dell'account di Azure attivo si ottiene con [az account show](/cli/azure/account#az_account_show) e viene impostato l'ambito per la macchina virtuale creata in un passaggio precedente con [az vm show](/cli/azure/vm#az_vm_show). L'ambito può anche essere assegnato a livello di gruppo di risorse o sottoscrizione e si applicano normali autorizzazioni di ereditarietà del controllo degli accessi in base al ruolo di Azure. Per altre informazioni, vedere [Accedere a una macchina virtuale Linux in Azure usando Azure Active Directory autenticazione .](../../virtual-machines/linux/login-using-aad.md)

```   AzureCLI
$username=$(az account show --query user.name --output tsv)
$vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Se il dominio AAD e il dominio del nome utente di accesso non corrispondono, è necessario specificare l'ID oggetto dell'account utente con , non solo il `--assignee-object-id` nome utente per `--assignee` . È possibile ottenere l'ID oggetto per l'account utente con [az ad user list](/cli/azure/ad/user#az_ad_user_list).

Per altre informazioni su come usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso alle risorse della sottoscrizione di Azure, vedere gli articoli seguenti:

- [Assegnare ruoli di Azure tramite l'interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md)
- [Assegnare ruoli di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md)
- [Assegnare ruoli di Azure usando Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Uso dell'accesso condizionale

È possibile applicare criteri di accesso condizionale, ad esempio l'autenticazione a più fattori o il controllo dei rischi di accesso utente prima di autorizzare l'accesso alle macchine virtuali Windows in Azure abilitate con Azure AD accesso. Per applicare i criteri di accesso condizionale, è necessario selezionare l'app "Accesso alle macchine virtuali Windows di Azure" dall'opzione di assegnazione delle app cloud o delle azioni e quindi usare il rischio di accesso come condizione e/o richiedere l'autenticazione a più fattori come controllo di accesso con concessione. 

> [!NOTE]
> Se si usa "Richiedi autenticazione a più fattori" come controllo di accesso di concessione per richiedere l'accesso all'app "Accesso alle macchine virtuali Windows di Azure", è necessario fornire l'attestazione di autenticazione a più fattori come parte del client che avvia la sessione RDP alla macchina virtuale Windows di destinazione in Azure. L'unico modo per ottenere questo risultato in un client Windows 10 client è usare il PIN Windows Hello for Business o l'autenticazione biometrica con il client RDP. Il supporto per l'autenticazione biometrica è stato aggiunto al client RDP Windows 10 versione 1809. Desktop remoto che usa Windows Hello for Business'autenticazione è disponibile solo per le distribuzioni che usano il modello di attendibilità certificato e attualmente non sono disponibili per il modello di attendibilità delle chiavi.

> [!WARNING]
> Per utente abilitato/applicato Azure AD Multi-Factor Authentication non è supportato per l'accesso alla macchina virtuale.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Accedere usando le Azure AD credenziali a una macchina virtuale Windows

> [!IMPORTANT]
> La connessione remota alle macchine virtuali unite a Azure AD è consentita solo da PC Windows 10 registrati Azure AD (la build minima richiesta è 20H1) o da un Azure AD aggiunto Azure AD o ibrido alla stessa **directory** della macchina virtuale. Inoltre, per usare RDP usando Azure AD credenziali utente, l'utente deve appartenere a uno dei due ruoli di Azure: Accesso amministratore macchina virtuale o Accesso utente macchina virtuale. Se si usa Azure AD pc Windows 10, è necessario immettere le credenziali nel `AzureAD\UPN` formato ( ad esempio, `AzureAD\john@contoso.com` ). A questo punto, Azure Bastion non può essere usato per accedere usando l'autenticazione Azure Active Directory con l'estensione AADLoginForWindows. è supportato solo RDP diretto.

Per accedere alla macchina virtuale Windows Server 2019 usando Azure AD: 

1. Passare alla pagina di panoramica della macchina virtuale abilitata con Azure AD accesso.
1. Selezionare **Connetti** per aprire il pannello Connetti alla macchina virtuale.
1. Selezionare **Scarica file RDP**.
1. Selezionare **Apri** per avviare il client Connessione Desktop remoto client.
1. Selezionare **Connetti per** avviare la finestra di dialogo di accesso di Windows.
1. Accedere usando le credenziali Azure AD utente.

A questo punto è stato eseguito l'accesso alla macchina virtuale windows server 2019 di Azure con le autorizzazioni del ruolo assegnate, ad esempio Utente macchina virtuale o Amministratore vm. 

> [!NOTE]
> È possibile salvare . File RDP locale nel computer per avviare future connessioni Desktop remoto alla macchina virtuale invece di passare alla pagina di panoramica della macchina virtuale nel portale di Azure e usando l'opzione di connessione.

## <a name="troubleshoot"></a>Risolvere problemi

### <a name="troubleshoot-deployment-issues"></a>Risolvere i problemi relativi alla distribuzione

L'estensione AADLoginForWindows deve essere installata correttamente per consentire alla macchina virtuale di completare il Azure AD join. Se l'estensione della macchina virtuale non viene installata correttamente, seguire questa procedura.

1. Eseguire RDP sulla macchina virtuale usando l'account amministratore locale ed esaminare il `CommandExecution.log` file in:
   
   `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0.`

   > [!NOTE]
   > Se l'estensione viene riavviata dopo l'errore iniziale, il log con l'errore di distribuzione verrà salvato come `CommandExecution_YYYYMMDDHHMMSSSSS.log` . "
1. Aprire un prompt dei comandi di PowerShell nella macchina virtuale e verificare che queste query sull'endpoint del servizio metadati dell'istanza in esecuzione nell'host di Azure restituisca:

   | Comando da eseguire | Output previsto |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Informazioni corrette sulla macchina virtuale di Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | ID tenant valido associato alla sottoscrizione di Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Token di accesso valido rilasciato dal Azure Active Directory per l'identità gestita assegnata a questa macchina virtuale |

   > [!NOTE]
   > Il token di accesso può essere decodificato usando uno strumento [come calebb.net](http://calebb.net/). Verificare che `appid` nel token di accesso corrisponda all'identità gestita assegnata alla macchina virtuale.

1. Assicurarsi che gli endpoint necessari siano accessibili dalla macchina virtuale tramite la riga di comando:
   
   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`

   > [!NOTE]
   > Sostituire `<TenantID>` con l Azure AD ID tenant associato alla sottoscrizione di Azure.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Lo stato del dispositivo può essere visualizzato eseguendo `dsregcmd /status` . L'obiettivo è che lo stato del dispositivo sia visualizzato come `AzureAdJoined : YES` .

   > [!NOTE]
   > Azure AD'attività di join viene acquisita nel Visualizzatore eventi nel `User Device Registration\Admin` log.

Se l'estensione AADLoginForWindows non riesce con un determinato codice di errore, è possibile seguire questa procedura:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problema 1: L'estensione AADLoginForWindows non viene installata con codice di errore terminale '1007' e codice di uscita: -2145648574.

Questo codice di uscita si traduce in perché l'estensione non è in grado di eseguire query sulle informazioni `DSREG_E_MSI_TENANTID_UNAVAILABLE` Azure AD tenant.

1. Verificare che la macchina virtuale di Azure possa recuperare tenantID dal servizio metadati dell'istanza.

   - RdP alla macchina virtuale come amministratore locale e verificare che l'endpoint restituisca un ID tenant valido eseguendo questo comando da una riga di comando con privilegi elevati nella macchina virtuale:
      
      - `curl -H Metadata:true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01`

1. L'amministratore della macchina virtuale tenta di installare l'estensione AADLoginForWindows, ma un'identità gestita assegnata dal sistema non ha abilitato prima la macchina virtuale. Passare al pannello Identità della macchina virtuale. Nella scheda Assegnato dal sistema verificare che Stato sia attivato.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problema 2: L'estensione AADLoginForWindows non viene installata con codice di uscita: -2145648607

Questo codice di uscita viene tradotto in `DSREG_AUTOJOIN_DISC_FAILED` perché l'estensione non è in grado di raggiungere `https://enterpriseregistration.windows.net` l'endpoint.

1. Verificare che gli endpoint necessari siano accessibili dalla macchina virtuale tramite la riga di comando:

   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`
   
   > [!NOTE]
   > Sostituire `<TenantID>` con l Azure AD ID tenant associato alla sottoscrizione di Azure. Se è necessario trovare l'ID tenant, è possibile passare il puntatore del mouse sul nome dell'account per ottenere la directory o l'ID tenant oppure selezionare proprietà **Azure Active Directory > > ID** directory nel portale di Azure.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Se uno dei comandi ha esito negativo con il messaggio "Impossibile risolvere l'host", provare a eseguire questo comando per determinare il server DNS usato `<URL>` dalla macchina virtuale.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Sostituire `<URL>` con i nomi di dominio completi usati dagli endpoint, ad esempio `login.microsoftonline.com` .

1. Verificare quindi se la specifica di un server DNS pubblico consente l'esito positivo del comando:

   `nslookup <URL> 208.67.222.222`

1. Se necessario, modificare il server DNS assegnato al gruppo di sicurezza di rete a cui appartiene la macchina virtuale di Azure.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problema 3: L'estensione AADLoginForWindows non viene installata con codice di uscita: 51

Il codice di uscita 51 viene tradotto in "Questa estensione non è supportata nel sistema operativo della macchina virtuale".

Nell'anteprima pubblica, l'estensione AADLoginForWindows deve essere installata solo in Windows Server 2019 o Windows 10 (build 1809 o successiva). Verificare che la versione di Windows sia supportata. Se la build di Windows non è supportata, disinstallare l'estensione vm.

### <a name="troubleshoot-sign-in-issues"></a>Risolvere i problemi di accesso

Alcuni errori comuni che si verificano quando si tenta di eseguire RDP con credenziali Azure AD includono l'assenza di ruoli di Azure assegnati, client non autorizzati o metodo di accesso 2FA obbligatorio. Usare le informazioni seguenti per risolvere questi problemi.

Il dispositivo e lo stato SSO possono essere visualizzati eseguendo `dsregcmd /status` . L'obiettivo è che lo stato del dispositivo sia `AzureAdJoined : YES` visualizzato come e `SSO State` `AzureAdPrt : YES` .

Inoltre, l'accesso RDP con Azure AD viene acquisito nel Visualizzatore eventi nei `AAD\Operational` registri eventi.

#### <a name="azure-role-not-assigned"></a>Ruolo di Azure non assegnato

Se viene visualizzato il messaggio di errore seguente quando si avvia una connessione desktop remoto alla macchina virtuale: 

- L'account è configurato per impedire l'uso di questo dispositivo. Per altre informazioni, contattare l'amministratore di sistema.

![L'account è configurato per impedire l'uso di questo dispositivo.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Verificare di aver configurato i [criteri di controllo degli](../../virtual-machines/linux/login-using-aad.md) accessi in base al ruolo di Azure per la macchina virtuale che concedono all'utente il ruolo Accesso amministratore macchina virtuale o Accesso utente macchina virtuale:

> [!NOTE]
> Se si verificano problemi con le assegnazioni di ruolo di Azure, vedere Risolvere i problemi relativi al [controllo degli accessi in base al ruolo di Azure.](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)
 
#### <a name="unauthorized-client"></a>Client non autorizzato

Se viene visualizzato il messaggio di errore seguente quando si avvia una connessione desktop remoto alla macchina virtuale: 

- le credenziali non funzionavano.

![Le credenziali non hanno funzionato](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Verificare che il PC Windows 10 in uso per avviare la connessione desktop remoto sia un computer aggiunto Azure AD o un Azure AD ibrido aggiunto alla stessa directory Azure AD a cui viene unita la macchina virtuale. Per altre informazioni sull'identità del dispositivo, vedere l'articolo [Informazioni sull'identità del dispositivo.](./overview.md)

> [!NOTE]
> Windows 10 Build 20H1 è stato aggiunto il supporto per un PC Azure AD registrato per avviare la connessione RDP alla macchina virtuale. Quando si usa un PC registrato Azure AD (non aggiunto Azure AD o Azure AD ibrido) come client RDP per avviare le connessioni alla macchina virtuale, è necessario immettere le credenziali nel formato `AzureAD\UPN` (ad esempio, `AzureAD\john@contoso.com` ).

Verificare che l'estensione AADLoginForWindows non sia stata disinstallata al termine dell Azure AD join.

Assicurarsi inoltre che i criteri di sicurezza "Sicurezza di rete: Consenti richieste di autenticazione PKU2U a questo computer di usare identità online" siano abilitati sia nel **server** che nel client.
 
#### <a name="mfa-sign-in-method-required"></a>Metodo di accesso MFA obbligatorio

Se viene visualizzato il messaggio di errore seguente quando si avvia una connessione desktop remoto alla macchina virtuale: 

- Il metodo di accesso che si sta tentando di usare non è consentito. Provare un metodo di accesso diverso o contattare l'amministratore di sistema.

![Il metodo di accesso che si sta tentando di usare non è consentito.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Se sono stati configurati criteri di accesso condizionale che richiedono l'autenticazione a più fattori (MFA) prima di poter accedere alla risorsa, è necessario assicurarsi che il PC Windows 10 che avvia la connessione Desktop remoto alla macchina virtuale acceda usando un metodo di autenticazione sicuro, ad esempio Windows Hello. Se non si usa un metodo di autenticazione avanzata per la connessione Desktop remoto, verrà visualizzato l'errore precedente.

Se non è stata distribuita Windows Hello for Business e questa non è un'opzione per il momento, è possibile escludere il requisito MFA configurando criteri di accesso condizionale che escludono l'app "Accesso alle macchine virtuali Windows di Azure" dall'elenco di app cloud che richiedono l'autenticazione a più fattori. Per altre informazioni sui Windows Hello for Business, vedere [panoramica Windows Hello for Business.](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

> [!NOTE]
> Windows Hello for Business'autenticazione PIN con RDP è stata supportata da Windows 10 per diverse versioni, ma il supporto per l'autenticazione biometrica con RDP è stato aggiunto Windows 10 versione 1809. L'Windows Hello for Business durante RDP è disponibile solo per le distribuzioni che usano il modello di attendibilità del certificato e attualmente non per il modello di attendibilità delle chiavi.
 
## <a name="preview-feedback"></a>Feedback sull'anteprima

Condividere commenti e suggerimenti su questa funzionalità di anteprima o segnalare i problemi usandola nel forum Azure AD [feedback.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui Azure Active Directory, vedere [Informazioni Azure Active Directory](../fundamentals/active-directory-whatis.md).
