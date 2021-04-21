---
title: Azure Key Vault nozioni fondamentali dell'autenticazione
description: Informazioni sul funzionamento del modello di autenticazione dell'insieme di credenziali delle chiavi
author: msmbaldwin
ms.author: mbaldwin
ms.date: 04/15/2021
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: f0cd2fb341dd790a7628cc3cf6a5bdd87d7c3687
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753417"
---
# <a name="key-vault-authentication-fundamentals"></a>Nozioni fondamentali sull'autenticazione di Key Vault

Azure Key Vault consente di archiviare e gestire in modo sicuro le credenziali dell'applicazione, ad esempio segreti, chiavi e certificati, in un repository cloud centrale e sicuro. Key Vault elimina la necessità di archiviare le credenziali nelle applicazioni. Le applicazioni possono eseguire l'autenticazione Key Vault in fase di esecuzione per recuperare le credenziali.

L'amministratore può controllare strettamente quali utenti e applicazioni possono accedere all'insieme di credenziali delle chiavi ed è possibile limitare e controllare le operazioni che eseguono. Questo documento illustra i concetti fondamentali del modello di accesso dell'insieme di credenziali delle chiavi. Offre un livello di conoscenza introduttivo e illustra come autenticare un utente o un'applicazione nell'insieme di credenziali delle chiavi dall'inizio alla fine.

## <a name="required-knowledge"></a>Conoscenze necessarie

Questo documento presuppone che l'utente abbia familiarità con i concetti seguenti. Se non si ha familiarità con questi concetti, seguire i collegamenti alla Guida prima di procedere.

* Azure Active Directory [collegamento](../../active-directory/fundamentals/active-directory-whatis.md)
* Collegamento entità di [sicurezza](./authentication.md#app-identity-and-security-principals)

## <a name="key-vault-configuration-steps-summary"></a>Key Vault dei passaggi di configurazione

1. Registrare l'utente o l'applicazione Azure Active Directory come entità di sicurezza.
1. Configurare un'assegnazione di ruolo per l'entità di sicurezza in Azure Active Directory.
1. Configurare i criteri di accesso dell'insieme di credenziali delle chiavi per l'entità di sicurezza.
1. Configurare Key Vault'accesso del firewall all'insieme di credenziali delle chiavi (facoltativo).
1. Testare la capacità dell'entità di sicurezza di accedere all'insieme di credenziali delle chiavi.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Registrare un utente o un'applicazione in Azure Active Directory come entità di sicurezza

Quando un utente o un'applicazione effettua una richiesta all'insieme di credenziali delle chiavi, la richiesta deve essere prima autenticata Azure Active Directory. Per il funzionamento, l'utente o l'applicazione deve essere registrato in Azure Active Directory come entità di sicurezza.

Seguire i collegamenti alla documentazione seguenti per informazioni su come registrare un utente o un'applicazione in Azure Active Directory.
**Assicurarsi di creare una password per la registrazione utente e un segreto client o una credenziale del certificato client per le applicazioni.**

* Registrazione di un utente [](../../active-directory/fundamentals/add-users-azure-active-directory.md) nel collegamento Azure Active Directory
* Registrazione di un'applicazione in Azure Active Directory [collegamento](../../active-directory/develop/quickstart-register-app.md)

## <a name="assign-your-security-principal-a-role"></a>Assegnare un ruolo all'entità di sicurezza

È possibile usare il controllo degli accessi in base al ruolo di Azure per assegnare autorizzazioni alle entità di sicurezza. Queste autorizzazioni sono denominate assegnazioni di ruolo.

Nel contesto dell'insieme di credenziali delle chiavi, queste assegnazioni di ruolo determinano il livello di accesso di un'entità di sicurezza al piano di gestione (noto anche come piano di controllo) dell'insieme di credenziali delle chiavi. Queste assegnazioni di ruolo non forniscono direttamente l'accesso ai segreti del piano dati, ma forniscono l'accesso per gestire le proprietà dell'insieme di credenziali delle chiavi. Ad esempio, a un  utente o **a** un'applicazione a cui è stato assegnato un ruolo Lettore non sarà consentito apportare modifiche alle impostazioni del firewall dell'insieme di credenziali delle chiavi, mentre un utente o un'applicazione a cui è stato assegnato un ruolo Collaboratore può apportare modifiche. Nessuno dei due ruoli avrà accesso diretto per eseguire operazioni su segreti, chiavi e certificati, ad esempio la creazione o il recupero del relativo valore fino a quando non viene assegnato l'accesso al piano dati dell'insieme di credenziali delle chiavi. Questa operazione viene trattata nel passaggio successivo.

>[!IMPORTANT]
> Anche se gli utenti con il ruolo Collaboratore o Proprietario non hanno accesso per eseguire operazioni sui segreti archiviati nell'insieme di credenziali delle chiavi per impostazione predefinita, i ruoli Collaboratore e Proprietario forniscono le autorizzazioni per aggiungere o rimuovere i criteri di accesso ai segreti archiviati nell'insieme di credenziali delle chiavi. Un utente con queste assegnazioni di ruolo può quindi concedere a se stesso l'accesso ai segreti di accesso nell'insieme di credenziali delle chiavi. Per questo motivo, è consigliabile che solo gli amministratori abbia accesso ai ruoli Collaboratore o Proprietario. A utenti e applicazioni che devono solo recuperare segreti dall'insieme di credenziali delle chiavi deve essere concesso il ruolo Lettore. **Altri dettagli sono presenti nella sezione successiva.**

>[!NOTE]
> Quando si assegna un'assegnazione di ruolo a un utente a livello di tenant di Azure Active Directory, questo set di autorizzazioni verrà esteso a tutte le sottoscrizioni, i gruppi di risorse e le risorse all'interno dell'ambito dell'assegnazione. Per seguire l'entità di sicurezza dei privilegi minimi, è possibile eseguire questa assegnazione di ruolo in un ambito più granulare. Ad esempio, è possibile assegnare a un utente un ruolo Lettore a livello di sottoscrizione e un ruolo Proprietario per un singolo insieme di credenziali delle chiavi. Passare alle impostazioni di Identity Access Management (IAM) di una sottoscrizione, di un gruppo di risorse o di un insieme di credenziali delle chiavi per eseguire un'assegnazione di ruolo in un ambito più granulare.

* Per altre informazioni sul collegamento ai [ruoli](../../role-based-access-control/built-in-roles.md) di Azure
* Per altre informazioni sull'assegnazione o la rimozione di un collegamento alle assegnazioni di [ruolo](../../role-based-access-control/role-assignments-portal.md)

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>Configurare i criteri di accesso dell'insieme di credenziali delle chiavi per l'entità di sicurezza

Prima di concedere l'accesso a utenti e applicazioni per l'accesso all'insieme di credenziali delle chiavi, è importante comprendere i diversi tipi di operazioni che possono essere eseguite su un insieme di credenziali delle chiavi. Esistono due tipi principali di operazioni dell'insieme di credenziali delle chiavi, ovvero operazioni del piano di gestione (definite anche piano di controllo) e operazioni del piano dati.

Questa tabella mostra diversi esempi delle diverse operazioni controllate dal piano di gestione rispetto al piano dati. Le operazioni che modificano le proprietà dell'insieme di credenziali delle chiavi sono operazioni del piano di gestione. Le operazioni che modificano o recuperano il valore dei segreti archiviati nell'insieme di credenziali delle chiavi sono operazioni del piano dati.

|Operazioni del piano di gestione (esempi)|Operazioni del piano dati (esempi)|
| --- | --- |
| Creare un insieme di credenziali delle chiavi | Creare una chiave, un segreto, un certificato
| Eliminare Key Vault | Eliminare una chiave, un segreto, un certificato
| Aggiungere o rimuovere Key Vault di ruolo | Elencare e ottenere i valori di chiavi, segreti, certificati
| Aggiungere o rimuovere criteri Key Vault di accesso | Backup e ripristino di chiavi, segreti, certificati
| Modificare Key Vault impostazioni del firewall | Rinnovare chiavi, segreti, certificati
| Modificare Key Vault di ripristino | Ripulire o ripristinare chiavi, segreti e certificati eliminati in modo soft
| Modificare Key Vault impostazioni dei log di diagnostica

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>Assegnazioni di ruolo & Azure Active Directory di accesso al piano di gestione

Azure Active Directory assegnazioni di ruolo concedono l'accesso per eseguire operazioni del piano di gestione in un insieme di credenziali delle chiavi. Questo accesso viene in genere concesso agli utenti, non alle applicazioni. È possibile limitare le operazioni del piano di gestione che un utente può eseguire modificando l'assegnazione di ruolo di un utente.

Ad esempio, l'assegnazione di un ruolo lettore Key Vault a un utente consente di visualizzare le proprietà dell'insieme di credenziali delle chiavi, ad esempio i criteri di accesso, ma non di apportare modifiche. Assegnando un utente, un ruolo Proprietario consentirà l'accesso completo per modificare le impostazioni del piano di gestione dell'insieme di credenziali delle chiavi.

Le assegnazioni di ruolo vengono controllate nel pannello Controllo di accesso (IAM) dell'insieme di credenziali delle chiavi. Se si vuole che un utente specifico abbia accesso come lettore o amministratore di più risorse dell'insieme di credenziali delle chiavi, è possibile creare un'assegnazione di ruolo a livello di insieme di credenziali, gruppo di risorse o sottoscrizione e l'assegnazione di ruolo verrà aggiunta a tutte le risorse nell'ambito dell'assegnazione.

L'accesso al piano dati o l'accesso per eseguire operazioni su chiavi, segreti e certificati archiviati nell'insieme di credenziali delle chiavi possono essere aggiunti in uno dei due modi seguenti.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>Opzione di accesso al piano dati 1: criteri di Key Vault classici

I criteri di accesso dell'insieme di credenziali delle chiavi concedono a utenti e applicazioni l'accesso per eseguire operazioni sul piano dati in un insieme di credenziali delle chiavi.

> [!NOTE]
> Questo modello di accesso non è compatibile con il controllo degli accessi in base al ruolo di Azure per l'insieme di credenziali delle chiavi (opzione 2) documentato di seguito. È necessario sceglierne uno. È possibile effettuare questa selezione quando si fa clic sulla scheda Criteri di accesso dell'insieme di credenziali delle chiavi.

I criteri di accesso classici sono granulari, ovvero è possibile consentire o negare a ogni singolo utente o applicazione di eseguire singole operazioni all'interno di un insieme di credenziali delle chiavi. Ecco alcuni esempi:

* L'entità di sicurezza 1 può eseguire qualsiasi operazione sulla chiave, ma non può eseguire operazioni relative a segreti o certificati.
* L'entità di sicurezza 2 può elencare e leggere tutte le chiavi, i segreti e i certificati, ma non può eseguire operazioni di creazione, eliminazione o rinnovo.
* L'entità di sicurezza 3 può eseguire il backup e il ripristino di tutti i segreti, ma non può leggere il valore dei segreti stessi.

Tuttavia, i criteri di accesso classici non consentono autorizzazioni a livello di oggetto e le autorizzazioni assegnate vengono applicate all'ambito di un singolo insieme di credenziali delle chiavi. Ad esempio, se si concede l'autorizzazione dei criteri di accesso "Secret Get" a un'entità di sicurezza in un determinato insieme di credenziali delle chiavi, l'entità di sicurezza ha la possibilità di ottenere tutti i segreti all'interno di tale insieme di credenziali delle chiavi specifico. Tuttavia, questa autorizzazione "Ottieni segreto" non verrà estesa automaticamente ad altri insiemi di credenziali delle chiavi e deve essere assegnata in modo esplicito.

> [!IMPORTANT]
> I criteri di accesso dell'insieme di credenziali delle chiavi Azure Active Directory assegnazioni di ruolo sono indipendenti l'uno dall'altro. L'assegnazione di un ruolo "Collaboratore" a un'entità di sicurezza a livello di sottoscrizione non consentirà automaticamente all'entità di sicurezza di eseguire operazioni del piano dati in ogni insieme di credenziali delle chiavi all'interno dell'ambito della sottoscrizione. L'entità di sicurezza deve comunque essere concessa o concedere a se stessa le autorizzazioni dei criteri di accesso per eseguire operazioni del piano dati.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault"></a>Opzione di accesso al piano dati 2: Controllo degli accessi in base al ruolo di Azure per Key Vault

Un nuovo modo per concedere l'accesso al piano dati dell'insieme di credenziali delle chiavi è tramite il controllo degli accessi in base al ruolo di Azure per l'insieme di credenziali delle chiavi.

> [!NOTE]
> Questo modello di accesso non è compatibile con i criteri di accesso classici dell'insieme di credenziali delle chiavi illustrati in precedenza. È necessario sceglierne uno. Sarà possibile effettuare questa selezione quando si fa clic sulla scheda Criteri di accesso dell'insieme di credenziali delle chiavi.

Key Vault assegnazioni di ruolo sono un set di assegnazioni di ruolo predefinite di Azure che includono set comuni di autorizzazioni usati per accedere a chiavi, segreti e certificati. Questo modello di autorizzazione abilita anche funzionalità aggiuntive che non sono disponibili nel modello di criteri di accesso dell'insieme di credenziali delle chiavi classico.

* Le autorizzazioni del controllo degli accessi in base al ruolo di Azure possono essere gestite su larga scala consentendo agli utenti di assegnare questi ruoli a livello di sottoscrizione, gruppo di risorse o singolo insieme di credenziali delle chiavi. Un utente avrà le autorizzazioni del piano dati per tutti gli insiemi di credenziali delle chiavi nell'ambito dell'assegnazione del controllo degli accessi in base al ruolo di Azure. In questo modo si elimina la necessità di assegnare singole autorizzazioni per i criteri di accesso per utente/applicazione per ogni insieme di credenziali delle chiavi.

* Le autorizzazioni del controllo degli accessi in base al ruolo di Azure sono compatibili con Privileged Identity Management o PIM. In questo modo è possibile configurare i controlli di accesso JUST-In-Time per i ruoli con privilegi come Key Vault Administrator. Si tratta di una procedura consigliata per la sicurezza e segue l'entità con privilegi minimi eliminando l'accesso permanente agli insiemi di credenziali delle chiavi.

Per altre informazioni sul controllo degli accessi in base al ruolo di Azure Key Vault, vedere i documenti seguenti:

* Collegamento controllo degli accessi in base al Key Vault [di Azure](rbac-guide.md)
* Collegamento al controllo degli accessi in Key Vault di [Azure](../../role-based-access-control/built-in-roles.md#key-vault-administrator)

## <a name="configure-key-vault-firewall"></a>Configurare Key Vault firewall

Per impostazione predefinita, l'insieme di credenziali delle chiavi consente al traffico proveniente dalla rete Internet pubblica di inviare l'insieme di credenziali delle chiavi tramite un endpoint pubblico. Per un ulteriore livello di sicurezza, è possibile configurare il firewall Azure Key Vault per limitare l'accesso all'endpoint pubblico dell'insieme di credenziali delle chiavi.

Per abilitare il firewall dell'insieme di credenziali delle chiavi, fare clic sulla scheda Rete nel portale dell'insieme di credenziali delle chiavi e selezionare il pulsante di opzione Consenti accesso da: "Endpoint privato e reti selezionate". Se si sceglie di abilitare il firewall dell'insieme di credenziali delle chiavi, questi sono i modi in cui è possibile consentire il traffico attraverso il firewall dell'insieme di credenziali delle chiavi.

* Aggiungere indirizzi IPv4 all'elenco consenti firewall dell'insieme di credenziali delle chiavi. Questa opzione è ideale per le applicazioni con indirizzi IP statici.

* Aggiungere una rete virtuale al firewall dell'insieme di credenziali delle chiavi. Questa opzione è ideale per le risorse di Azure con indirizzi IP dinamici, ad esempio macchine virtuali. È possibile aggiungere risorse di Azure a una rete virtuale e aggiungere la rete virtuale all'elenco di elementi consentiti del firewall dell'insieme di credenziali delle chiavi. Questa opzione usa un endpoint di servizio che è un indirizzo IP privato all'interno della rete virtuale. In questo modo si offre un livello di protezione aggiuntivo, in modo che nessun traffico tra l'insieme di credenziali delle chiavi e la rete virtuale sia instradato sulla rete Internet pubblica. Per altre informazioni sull'endpoint di servizio, vedere la documentazione seguente. [Link](./network-security.md)

* Aggiungere una connessione di collegamento privato all'insieme di credenziali delle chiavi. Questa opzione connette la rete virtuale direttamente a una particolare istanza dell'insieme di credenziali delle chiavi, portando in modo efficace l'insieme di credenziali delle chiavi all'interno della rete virtuale. Per altre informazioni sulla configurazione di una connessione endpoint privato all'insieme di credenziali delle chiavi, vedere il collegamento [seguente](./private-link-service.md)

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>Testare la capacità dell'entità servizio di accedere all'insieme di credenziali delle chiavi

Dopo aver seguito tutti i passaggi precedenti, sarà possibile impostare e recuperare i segreti dall'insieme di credenziali delle chiavi.

### <a name="authentication-process-for-users-examples"></a>Processo di autenticazione per gli utenti (esempi)

* Gli utenti possono accedere al portale di Azure per usare l'insieme di credenziali delle chiavi. [Key Vault di avvio rapido del portale di Azure](./quick-create-portal.md)

* L'utente può usare l'interfaccia della riga di comando di Azure per usare l'insieme di credenziali delle chiavi. [Key Vault di avvio rapido dell'interfaccia della riga di comando di Azure](./quick-create-cli.md)

* L'utente può usare Azure PowerShell per usare l'insieme di credenziali delle chiavi. [Key Vault Azure PowerShell guida introduttiva](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Azure Active Directory processo di autenticazione per applicazioni o servizi (esempi)

* Un'applicazione fornisce un segreto client e un ID client in una funzione per ottenere un token Azure Active Directory client. 

* Un'applicazione fornisce un certificato per ottenere un token Azure Active Directory sicurezza. 

* Una risorsa di Azure usa l'autenticazione msi per ottenere un token Azure Active Directory servizio. 

* Altre informazioni sul collegamento di [autenticazione](../../active-directory/managed-identities-azure-resources/overview.md) msi

### <a name="authentication-process-for-application-python-example"></a>Processo di autenticazione per l'applicazione (esempio Python)

Usare l'esempio di codice seguente per verificare se l'applicazione è in grado di recuperare un segreto da Key Vault usando l'entità servizio configurata.

>[!NOTE]
>Questo esempio è solo a scopo dimostrativo e di test. **NON USARE L'AUTENTICAZIONE DEL SEGRETO CLIENT NELL'AMBIENTE DI PRODUZIONE** Questa non è una procedura di progettazione sicura. È consigliabile usare il certificato client o l'autenticazione msi come procedura consigliata.

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIENT SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni più dettagliate sull'autenticazione dell'insieme di credenziali delle chiavi, vedere il documento seguente. [Autenticazione dell'insieme di credenziali delle chiavi](./authentication.md)
