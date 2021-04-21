---
title: Azure AD'accesso ibrido sicuro con F5 deployment guide | Microsoft Docs
description: Esercitazione per distribuire una macchina virtuale F5 BIG-IP Virtual Edition (VE) in IaaS di Azure per l'accesso ibrido sicuro
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 139009c55573e1e115a22069671f66e93695a635
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783324"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Esercitazione per distribuire una macchina virtuale F5 BIG-IP Virtual Edition in IaaS di Azure per l'accesso ibrido sicuro

Questa esercitazione illustra il processo end-to-end di distribuzione di BIG-IP Vitural Edition (VE) in IaaS di Azure. Al termine di questa esercitazione si dovrebbero avere:

- Una macchina virtuale (VM) BIG-IP completamente preparata per la modellazione di un modello di verifica dell'accesso ibrido sicuro (SHA, Secure Hybrid Access)

- Un'istanza di gestione temporanea da usare per testare nuovi aggiornamenti e hotfix del sistema BIG-IP

## <a name="prerequisites"></a>Prerequisiti

L'esperienza o le conoscenze precedenti di F5 BIG-IP non sono necessarie, ma è consigliabile acquisire familiarità con la [terminologia di F5 BIG-IP.](https://www.f5.com/services/resources/glossary) La distribuzione di big-IP in Azure per SHA richiede:

- Una sottoscrizione di Azure a pagamento o una sottoscrizione di valutazione gratuita di 12 [mesi.](https://azure.microsoft.com/free/)

- Uno degli SKU di licenza F5 BIG-IP seguenti

  - F5 BIG-IP® Best bundle

  - Licenza autonoma di F5 BIG-IP Access Policy Manager™ (APM)

  - Licenza del componente aggiuntivo F5 BIG-IP Access Policy Manager™ (APM) in un BIG-IP F5 BIG-IP esistente® Gestione traffico locale™ (LTM)
  
  - Licenza di valutazione completa per BIG-IP di 90 [giorni.](https://www.f5.com/trial/big-ip-trial.php)

- Un carattere jolly o un certificato SAN (Subject Alternative Name) per pubblicare applicazioni Web tramite SECURE Socket Layer (SSL). [Let's encrypt offre](https://letsencrypt.org/) un certificato gratuito di 90 giorni per il test.

- Un certificato SSL per la protezione dell'BIG-IPs di gestione. È possibile usare un certificato usato per pubblicare app Web, se il relativo soggetto corrisponde al nome di dominio completo (FQDN) di BIG-IP. Ad esempio, un certificato con caratteri jolly definito con un oggetto *.contoso.com è adatto per `https://big-ip-vm.contoso.com:8443`

La distribuzione della macchina virtuale e le impostazioni di configurazione del sistema di base possono richiedere circa 30 minuti. A questo punto la piattaforma BIG-IP sarà pronta per l'implementazione di uno degli scenari SHA elencati [qui.](f5-aad-integration.md)

Per testare gli scenari, questa esercitazione presuppone che BIG-IP verrà distribuito in un gruppo di risorse di Azure contenente un ambiente Active Directory (AD). L'ambiente deve essere costituito da un controller di dominio (DC) e da vm host Web (IIS). È anche possibile avere questi server in altre posizioni per la macchina virtuale BIG-IP, a garantire che BIG-IP abbia una linea di vista su ognuno dei ruoli necessari per supportare uno scenario specifico. Sono supportati anche gli scenari in cui la macchina virtuale BIG-IP è connessa a un altro ambiente tramite una connessione VPN.

Se non si dispone degli elementi indicati qui per il test, è possibile distribuire un intero ambiente di dominio AD in Azure, usando questo [script](https://github.com/Rainier-MSFT/Cloud_Identity_Lab). Una raccolta di applicazioni di test di esempio può anche essere distribuita a livello di codice in un host Web IIS usando questa [automazione tramite script.](https://github.com/jeevanbisht/DemoSuite)

>[!NOTE]
>Il [portale di Azure](https://portal.azure.com/#home) è in continua evoluzione, quindi alcuni dei passaggi di questa esercitazione possono differire dal layout effettivo osservato nel portale di Azure.

## <a name="azure-deployment"></a>Distribuzione di Azure

Un BIG-IP può essere distribuito in topologie diverse. Questa guida è incentrata su una singola distribuzione di interfaccia di rete (NIC). Tuttavia, se la distribuzione BIG-IP richiede più interfacce di rete per la disponibilità elevata, la separazione della rete o una velocità effettiva superiore a 1 GB, è consigliabile usare i modelli [di Azure Resource Manager (ARM)](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)precompilato di F5.

Completare le attività seguenti per distribuire BIG-IP VE dal [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

1. Accedere al [portale di Azure](https://portal.azure.com/#home) con un account che abbia le autorizzazioni per creare macchine virtuali. Ad esempio, Collaboratore

2. Nella casella di ricerca nella barra multifunzione superiore digitare **marketplace,** seguito da **INVIO**

3. Digitare **F5** nel filtro marketplace, seguito da **INVIO**

4. Selezionare **+ Aggiungi** nella barra multifunzione superiore e digitare **F5** nel filtro del marketplace, seguito da **INVIO**

5. Selezionare **F5 BIG-IP Virtual Edition (BYOL) Selezionare** un piano  >  **software**  >  **F5 BIG-IP VE - ALL (BYOL, 2 percorsi di avvio)**

6. Selezionare **Crea**.

![L'immagine mostra i passaggi per selezionare un piano software](./media/f5ve-deployment-plan/software-plan.png)

7. Eseguire il menu **Informazioni di base** e usare le impostazioni seguenti

 |  Dettagli del progetto     |  Valore     |
 |:-------|:--------|
 |Subscription|Sottoscrizione di destinazione per la distribuzione della macchina virtuale BIG-IP|
 |Resource group | Gruppo di risorse di Azure esistente in cui verrà distribuita o creata la macchina virtuale BIG-IP. Deve essere lo stesso gruppo di risorse del controller di dominio e delle macchine virtuali IIS|
 | **Dettagli istanza**|  |
 |Nome macchina virtuale| Esempio di BIG-IP-VM |
 |Region | Area geografica di Azure di destinazione per BIG-IP-VM |
 |Opzioni di disponibilità| Abilita solo se si usa una macchina virtuale nell'ambiente di produzione|
 |Immagine| F5 BIG-IP VE - ALL (BYOL, 2 percorsi di avvio)|
 |Istanza Spot di Azure| No, ma è possibile abilitarla se appropriato |
 |Dimensione| La specifica minima deve essere 2 vCCU e 8 GB di memoria|
 |**Account amministratore**|  |
 |Tipo di autenticazione|Selezionare la password per il momento. È possibile passare a una coppia di chiavi in un secondo momento |
 |Username|Identità che verrà creata come account locale BIG-IP per l'accesso alle interfacce di gestione. Il nome utente fa distinzione tra maiuscole e minuscole.|
 |Password|Proteggere l'accesso amministratore con una password complessa|
 |**Regole porta in ingresso**|  |
 |Porte in ingresso pubbliche|Nessuno|

8. Selezionare **Avanti: Dischi lasciando** tutte le impostazioni predefinite e selezionare **Avanti: Rete.**

9. Nel menu **Rete** completare queste impostazioni.

 |Interfaccia di rete|      valore |
 |:--------------|:----------------|
 |Rete virtuale|Stessa rete virtuale di Azure usata dal controller di dominio e dalle macchine virtuali IIS oppure crearne una|
 |Subnet| Stessa subnet interna di Azure del controller di dominio e delle macchine virtuali IIS oppure crearne una|
 |IP pubblico |  Nessuno|
 |Gruppo di sicurezza di rete NIC| Selezionare Nessuno se la subnet di Azure selezionata nei passaggi precedenti è già associata a un gruppo di sicurezza di rete(NSG). in caso contrario, selezionare Basic|
 |Accelerare la rete| Off |
 |**Bilanciamento del carico**|     |
 |Bilanciare il carico della macchina virtuale| No|

10. Selezionare **Avanti: Gestione** e completare queste impostazioni.

 |Monitoraggio|    Valore |
 |:---------|:-----|
 |Monitoraggio dettagliato| Off|
 |Diagnostica di avvio|Abilitare con un account di archiviazione personalizzato. Consente la connessione all'interfaccia BIG-IP Secure Shell (SSH) tramite l'opzione Console seriale nel portale di Azure. Selezionare qualsiasi account di archiviazione di Azure disponibile|
 |**Identità**|  |
 |Identità gestita assegnata dal sistema|Off|
 |Azure Active Directory|BIG-IP attualmente non supporta questa opzione|
 |**Autoshutdown**|    |
 |Abilitare l'arresto automatico| In caso di test, valutare la possibilità di impostare BIG-IP-VM per l'arresto giornaliero|

11. Selezionare **Avanti: Avanzate** lasciando tutte le impostazioni predefinite e selezionare **Avanti: Tag**.

12. Selezionare **Avanti: Rivedi e crea** per esaminare le configurazioni BIG-IP-VM, prima di selezionare **Crea** per avviare la distribuzione.

13. Il tempo necessario per distribuire completamente una macchina virtuale BIG-IP è in genere di 5 minuti. Al termine, non selezionare Vai alla risorsa, espandere il menu a sinistra  del portale di Azure e selezionare Gruppi di risorse per passare alla nuova MACCHINA VIRTUALE BIG-IP. Se la creazione della macchina virtuale ha esito negativo, **selezionare Indietro** e **Avanti.**

## <a name="network-configuration"></a>Configurazione di rete

Al primo avvio della macchina virtuale BIG-IP,  verrà effettuato il provisioning della scheda di interfaccia di rete con un indirizzo IP privato primario emesso dal servizio Dynamic Host Configuration Protocol (DHCP) della subnet di Azure a cui è connessa. Questo indirizzo IP verrà usato dal sistema operativo di gestione del traffico (TMOS) di BIG-IP per comunicare con:

- Comunicazione con altri host e servizi

- Accesso in uscita a Internet pubblico

- Accesso in ingresso all'BIG-IPs web config e alle interfacce di gestione SSH

L'esposizione di una di queste interfacce di gestione BIG-IPs Internet aumenta la superficie di attacco BIG-IPs, motivo per cui non è stato effettuato il provisioning dell'INDIRIZZO IP primario BIG-IPs con un indirizzo IP pubblico durante la distribuzione. Verrà invece effettuato il provisioning di un INDIRIZZO IP interno secondario e dell'indirizzo IP pubblico associato per i servizi di pubblicazione.
Questo mapping da 1 a 1 tra un INDIRIZZO IP pubblico e un INDIRIZZO IP privato della macchina virtuale consente al traffico esterno di raggiungere una macchina virtuale. Tuttavia, è necessaria anche una regola del gruppo di sicurezza di rete di Azure per consentire il traffico, in modo molto identico a un firewall.

Il diagramma mostra una singola distribuzione nic di un'interfaccia di rete BIG-IP in Azure, configurata con un indirizzo IP primario per operazioni e gestione generali e un indirizzo IP del server virtuale separato per la pubblicazione dei servizi.
In questa disposizione, una regola del gruppo di sicurezza di rete consente il traffico remoto destinato a essere indirizzato all'indirizzo IP pubblico per il servizio pubblicato, prima di essere inoltrato al `intranet.contoso.com` server virtuale BIG-IP.

![L'immagine mostra la distribuzione a scheda singola Per impostazione predefinita, gli indirizzi IP privati e pubblici emessi alle macchine virtuali di Azure sono sempre dinamici, quindi probabilmente cambieranno a ogni riavvio ](./media/f5ve-deployment-plan/single-nic-deployment.png) di una macchina virtuale. Evitare problemi di connettività imprevisti modificando l'indirizzo IP di gestione BIG-IPs in statico e facendo lo stesso con gli indirizzi IP secondari usati per i servizi di pubblicazione.

1. Dal menu della macchina virtuale BIG-IP passare a **Impostazioni rete**  >  

2. Nella visualizzazione rete selezionare il collegamento a destra di **Interfaccia di rete**

![L'immagine mostra la configurazione di rete](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>I nomi delle macchine virtuali vengono generati in modo casuale durante la distribuzione.

3. Nel riquadro a sinistra selezionare Configurazioni **IP** e quindi selezionare **la riga ipconfig1**

4. Impostare **l'opzione Assegnazione IP** su statico e, se necessario, modificare l'indirizzo IP primario delle macchine virtuali BIG-IP. Selezionare quindi **Salva** e chiudere il menu ipconfig1

>[!NOTE]
>Si userà questo indirizzo IP primario per connettere e gestire la MACCHINA VIRTUALE BIG-IP.

5. Selezionare **+ Aggiungi** sulla barra multifunzione superiore e specificare un nome per un INDIRIZZO IP privato secondario, ad esempio ipconfig2

6. Impostare **l'opzione Allocazione** delle impostazioni dell'indirizzo IP privato su **Statico.** Se si specifica l'indirizzo IP consecutivo successivo superiore o inferiore, è possibile mantenere gli elementi in ordine.

7. Impostare l'indirizzo IP pubblico **su Associa e** selezionare **Crea**

8. Specificare un nome per il nuovo indirizzo IP pubblico, ad esempio BIG-IP-VM_ipconfig2_Public

9. Se richiesto, impostare lo **SKU** su Standard

10. Se richiesto, impostare il **livello su** **Globale** e

11. Impostare **l'opzione** Assegnazione su **Statico** e quindi selezionare **OK due** volte

Big-IP-VM è ora pronto per la configurazione con:

- **IP privato primario:** dedicato alla gestione di BIG-IP-VM tramite l'utilità di configurazione Web e SSH. Verrà usato dal sistema BIG-IP come self-IP per connettersi ai servizi **back-end** pubblicati. Si connette anche a servizi esterni, ad esempio NTP, AD e LDAP.

- **IP privato secondario:** usato durante la creazione di un server virtuale BIG-IP APM per l'ascolto delle richieste in ingresso a uno o più servizi pubblicati.

- **IP pubblico:** associato all'IP privato secondario, consente al traffico client proveniente dalla rete Internet pubblica di raggiungere il server virtuale BIG-IP per i servizi pubblicati

L'esempio illustra la relazione 1-1 tra indirizzi IP pubblici e privati di una macchina virtuale. Una scheda di interfaccia di rete di una macchina virtuale di Azure può avere un solo indirizzo IP primario e tutti gli indirizzi IP creati vengono sempre definiti secondari.

>[!NOTE]
>Sono necessari i mapping IP secondari per la pubblicazione di servizi BIG-IP.

![Questa immagine mostra tutti gli IP secondari](./media/f5ve-deployment-plan/secondary-ips.png)

Per implementare SHA usando la configurazione guidata per l'accesso BIG-IP, ripetere i passaggi da 5 a 11 per creare coppie di indirizzi IP pubblici e privati aggiuntive per ogni servizio aggiuntivo che si prevede di pubblicare tramite BIG-IP APM. Lo stesso approccio può essere usato anche se si pubblicano servizi BIG-IPs **Configurazione avanzata**. Tuttavia, in questo scenario è possibile evitare sovraccarici ip pubblici usando una configurazione [SNI (Server Name Indicator).](https://support.f5.com/csp/#/article/K13452) In questa configurazione, un server virtuale BIG-IP accetterà tutto il traffico client ricevuto e lo instraderà verso la destinazione.

## <a name="dns-configuration"></a>Configurazione del DNS

È essenziale che dns sia configurato per consentire ai client di risolvere i servizi SHA pubblicati in ip pubblici della MACCHINA VIRTUALE BIG-IP.

I passaggi seguenti presuppongono che la zona DNS del dominio pubblico usato per i servizi SHA sia gestita in Azure. Tuttavia, gli stessi principi DNS per la creazione di record localizzatore si applicano comunque indipendentemente dalla posizione in cui viene gestita la zona DNS.

1. Se non è già aperto, espandere il menu a sinistra del portale e passare alla MACCHINA VIRTUALE BIG-IP tramite **l'opzione Gruppi di** risorse

2. Dal menu della macchina virtuale BIG-IP passare a **Impostazioni rete**  >  

3. Nella visualizzazione di rete BIG-IP-VM selezionare il primo IP secondario nell'elenco a discesa configurazione IP e selezionare il collegamento per l'indirizzo IP pubblico **della scheda di interfaccia di rete**

![Screenshot per visualizzare l'indirizzo IP pubblico della scheda di interfaccia di rete](./media/f5ve-deployment-plan/networking.png)

4. Sotto la **sezione** Impostazioni nel riquadro a sinistra selezionare **Configurazione** per visualizzare il menu delle proprietà IP pubblico e DNS per l'IP secondario selezionato

5. Selezionare e **Creare** un record alias e selezionare la **zona DNS** dall'elenco a discesa. Se una zona DNS non esiste già, potrebbe essere gestita all'esterno di Azure oppure è possibile crearne una per il suffisso di dominio verificato in Azure AD.

6. Usare i dettagli seguenti per creare il primo record alias DNS:

 | Campo | Valore |
 |:-------|:-----------|
 |Subscription| Stessa sottoscrizione della MACCHINA VIRTUALE BIG-IP|
 |Zona DNS| Zona DNS autorevole per il suffisso di dominio verificato che i siti Web pubblicati useranno, ad esempio, www.contoso.com |
 |Nome | Il nome host specificato verrà risolto nell'indirizzo IP pubblico associato all'INDIRIZZO IP secondario selezionato. Assicurarsi di definire i mapping DA DNS a IP corretti. Vedere l'ultima immagine nella sezione Configurazione di rete, ad esempio intranet.contoso.com > 13.77.148.215|
 | TTL | 1 |
 |Unità TTL | Ore |

7. Selezionare **Crea** per Azure per salvare queste impostazioni nel DNS pubblico.

8. Lasciare **l'etichetta del nome DNS (facoltativo)** e selezionare **Salva** prima di chiudere il menu IP pubblico.

9. Ripetere i passaggi da 1 a 6 per creare record DNS aggiuntivi per ogni servizio che si prevede di pubblicare usando la configurazione guidata di BIG-IP.

  Con i record DNS, è possibile usare uno degli strumenti online, ad esempio controllo [DNS,](https://dnschecker.org/) per verificare che un record creato sia stato propagato correttamente in tutti i server DNS pubblici globali.

  Se si gestisce lo spazio dei nomi di dominio DNS usando un provider esterno come [GoDaddy,](https://www.godaddy.com/)sarà necessario creare record usando la propria funzionalità di gestione DNS.

>[!NOTE]
>È anche possibile usare il file degli host locali di un PC se si testano e si cambia spesso record DNS. È possibile accedere al file localhosts **in** un PC Windows premendo Win + R sulla tastiera e inviando i driver di parola nella casella di esecuzione. Tenere presente che un record localhost fornirà solo la risoluzione DNS per il PC locale, non per altri client.

## <a name="client-traffic"></a>Traffico client

Per impostazione predefinita, le reti virtuali di Azure e le subnet associate sono reti private che non sono in grado di ricevere traffico Internet. La scheda di interfaccia di rete della MACCHINA VIRTUALE BIG-IP deve essere collegata al gruppo di protezione di rete specificato durante la distribuzione. Per consentire al traffico Web esterno di raggiungere la MACCHINA VIRTUALE BIG-IP, è necessario definire una regola NSG in ingresso per consentire le porte 443 (HTTPS) e 80 (HTTP) dalla rete Internet pubblica.

1. Nel **menu** Panoramica principale della macchina virtuale BIG-IP selezionare **Rete**

2. Selezionare **Aggiungi** regola in ingresso per immettere le proprietà della regola del gruppo di controllo di rete seguenti:

 |     Campo   |   Valore        |
 |:------------|:------------|
 |Source (Sorgente)| Qualsiasi|
 |Intervalli di porte di origine| *|
 |Indirizzi IP di destinazione|Elenco delimitato da virgole di tutti gli INDIRIZZI IP privati secondari BIG-IP-VM|
 |Porte di destinazione| 80,443|
 |Protocollo| TCP |
 |Azione| Allow|
 |Priorità|Valore minimo disponibile compreso tra 100 e 4096|
 |Nome | Nome descrittivo, ad esempio: `BIG-IP-VM_Web_Services_80_443`|

3. Selezionare **Aggiungi** per eseguire il commit delle modifiche e chiudere **il** menu Rete.

Il traffico HTTP e HTTPS da qualsiasi posizione sarà ora autorizzato a raggiungere tutte le interfacce secondarie BIG-IP-VMs. Consentire la porta 80 è utile per consentire all'APM BIG-IP di reindirizzare automaticamente gli utenti da HTTP a HTTPS. Questa regola può essere modificata per aggiungere o rimuovere gli IP di destinazione, quando necessario.

## <a name="manage-big-ip"></a>Gestire BIG-IP

Un sistema BIG-IP viene amministrato tramite la relativa interfaccia utente di configurazione Web, a cui è possibile accedere usando uno dei metodi consigliati seguenti:

- Da un computer all'interno della rete interna di BIG-IP

- Da un client VPN connesso alla rete interna della MACCHINA VIRTUALE BIG-IP

- Pubblicato tramite [Azure AD Application Proxy](./application-proxy-add-on-premises-application.md)

Prima di procedere con le configurazioni rimanenti, è necessario decidere il metodo più appropriato. Se necessario, è possibile connettersi direttamente alla configurazione Web da Internet configurando l'indirizzo IP primario di BIG-IP con un indirizzo IP pubblico. Aggiungere quindi una regola del gruppo di sicurezza di rete per consentire il traffico 8443 verso l'indirizzo IP primario. Assicurarsi di limitare l'origine al proprio indirizzo IP attendibile, altrimenti chiunque sarà in grado di connettersi.

Al termine, verificare che sia possibile connettersi alla configurazione Web della macchina virtuale BIG-IP e accedere con le credenziali specificate durante la distribuzione della macchina virtuale:

- Se ci si connette da una macchina virtuale nella rete interna o tramite VPN, connettersi direttamente alla porta BIG-IPs IP primario e alla porta di configurazione Web. Ad esempio: `https://<BIG-IP-VM_Primary_IP:8443`. Il browser chiederà se la connessione non è sicura, ma è possibile ignorarla fino a quando non viene configurato BIG-IP. Se il browser tenta di bloccare l'accesso, cancellare la cache e riprovare.

- Se la configurazione Web è stata pubblicata tramite Application Proxy, usare l'URL definito per accedere esternamente alla configurazione Web, senza aggiungere la porta, ad esempio `https://big-ip-vm.contoso.com` . L'URL interno deve essere definito usando la porta di configurazione Web, ad esempio `https://big-ip-vm.contoso.com:8443` 

Un sistema BIG-IP può essere gestito anche tramite l'ambiente SSH sottostante, che in genere viene usato per le attività della riga di comando e l'accesso a livello radice. Sono disponibili diverse opzioni per la connessione all'interfaccia della riga di comando, tra cui:

- [Azure Bastion:](../../bastion/bastion-overview.md)consente connessioni veloci e sicure a qualsiasi macchina virtuale all'interno di una rete virtuale, da qualsiasi posizione

- Connettersi direttamente tramite un client SSH come PuTTY tramite l'approccio JIT

- Console seriale: disponibile nella parte inferiore della sezione Supporto e risoluzione dei problemi del menu VM nel portale. Non supporta i trasferimenti di file.

- Come con la configurazione Web, è anche possibile connettersi direttamente all'interfaccia della riga di comando da Internet configurando l'indirizzo IP primario di BIG-IP con un indirizzo IP pubblico e aggiungendo una regola del gruppo di sicurezza di rete per consentire il traffico SSH. Anche in questo caso, assicurarsi di limitare l'origine al proprio indirizzo IP attendibile, se si usa questo metodo.  

## <a name="big-ip-license"></a>Licenza BIG-IP

Un sistema BIG-IP deve essere attivato e di cui è stato effettuato il provisioning con il modulo APM prima di poter essere configurato per i servizi di pubblicazione e SHA.

1. Accedere di nuovo alla configurazione Web e nella **pagina delle proprietà** Generale selezionare **Attiva**

2. Nel campo **Base Registration key (Chiave di registrazione** di base) immettere la chiave con distinzione tra maiuscole e minuscole fornita da F5

3. Lasciare **l'opzione**  Metodo di attivazione impostata su Automatico e selezionare **Avanti.** BIG-IP convaliderà la licenza e visualizza il contratto di licenza con l'utente finale.

4. Selezionare **Accept** and wait for activation to complete (Accetta e attende il completamento dell'attivazione) prima di **selezionare Continue (Continua).**

5. Accedere di nuovo e nella parte inferiore della pagina Riepilogo licenza selezionare **Avanti.** Big-IP visualizza ora un elenco di moduli che forniscono le varie funzionalità necessarie per SHA. Se non viene visualizzato, dalla scheda principale passare a **Provisioning risorse**  >  **di sistema.**

6. Controllare la colonna di provisioning per Criteri di accesso (APM)

![L'immagine mostra il provisioning dell'accesso](./media/f5ve-deployment-plan/access-provisioning.png)

7. Selezionare **Invia** e accettare l'avviso

8. Attendere che BIG-IP completi l'illuminazione delle nuove funzionalità. Può essere ciclo più volte prima di essere completamente inizializzato. 

9. Quando si è **pronti, selezionare Continua** e nella **scheda Informazioni** selezionare **Esegui l'utilità di installazione**

>[!IMPORTANT]
>Le licenze F5 sono vincolate all'uso da parte di una singola istanza VE BIG-IP in qualsiasi momento. Possono esserci motivi per cui si vuole eseguire la migrazione di una licenza [](https://support.f5.com/csp/article/K41458656) da un'istanza a un'altra e, in questo caso, assicurarsi di revocare la licenza di valutazione nell'istanza attiva prima di rimuovere le autorizzazioni. In caso contrario, la licenza andrà persa definitivamente.

## <a name="provision-big-ip"></a>Effettuare il provisioning di BIG-IP

La protezione del traffico di gestione da e verso BIG-IPs web config è fondamentale. Configurare un certificato di gestione dei dispositivi per proteggere il canale di configurazione Web da compromissione.

1. Dalla barra di spostamento a sinistra passare a Gestione certificati di sistema Traffico Gestione certificati Importazione elenco certificati  >    >    >  **SSL**  >  

2. **Nell'elenco a** discesa Tipo di importazione selezionare **PKCS 12(IIS)** e **scegliere File**. Individuare un certificato Web SSL con un nome soggetto o una SAN che copre l'FQDN che verrà assegnato alla MACCHINA VIRTUALE BIG-IP nei passaggi successivi

3. Specificare la password per il certificato e quindi selezionare **Importa**

4. Dalla barra di spostamento a sinistra passare a **Piattaforma di**  >  **sistema**

5. Configurare BIG-IP-VM con un nome host completo e il fuso orario per l'ambiente, seguito da **Update**

![L'immagine mostra le proprietà generali](./media/f5ve-deployment-plan/general-properties.png)

6. Dalla barra di spostamento a sinistra passare a **System**  >  **Configuration** Device  >    >  **NTP (NtP dispositivo configurazione sistema)**

7. Specificare un'origine NTP affidabile e selezionare **Aggiungi**, quindi **Aggiorna**. Ad esempio: `time.windows.com`

È ora necessario un record DNS per risolvere il BIG-IPs FQDN specificato nei passaggi precedenti nell'indirizzo IP privato primario. È necessario aggiungere un record al DNS interno dell'ambiente o al file localhost di un PC che verrà usato per connettersi alla configurazione Web di BIG-IP. In entrambi i modi, l'avviso del browser non dovrebbe più essere visualizzato quando ci si connette direttamente alla configurazione Web. In altre parole, non tramite Application Proxy o qualsiasi altro proxy inverso.

## <a name="ssl-profile"></a>Profilo SSL

Come proxy inverso, un sistema BIG-IP può fungere da semplice servizio di inoltro, altrimenti noto come proxy trasparente, o proxy completo che partecipa attivamente agli scambi tra client e server.
Un proxy completo crea due connessioni distinte: una connessione client TCP front-end insieme a una connessione server TCP back-end separata, abbinata a un soft gap nel mezzo. I client si connettono al listener proxy su un'estremità, altrimenti nota come **server virtuale,** e il proxy stabilisce una connessione separata e indipendente al server back-end. Si tratta di un'operazione bidirezionale su entrambi i lati.
In questa modalità proxy completa, il sistema BIG-IP F5 è in grado di controllare il traffico ed è quindi possibile interagire con richieste e risposte. Alcune funzioni, ad esempio il bilanciamento del carico e l'ottimizzazione delle prestazioni Web, nonché servizi di gestione del traffico più avanzati, ad esempio la sicurezza a livello di applicazione, l'accelerazione Web, il routing delle pagine e l'accesso remoto sicuro, si basano su questa funzionalità.
Quando si pubblicano servizi basati su SSL, il processo di decrittografia e crittografia del traffico tra i client e i servizi back-end viene gestito dai profili di IP SSL BIG.

Esistono due tipi di profili:

- **SSL client:** il modo più comune per configurare un sistema BIG-IP per pubblicare servizi interni con SSL è creare un profilo SSL client. Con un profilo SSL client, un sistema BIG-IP può decrittografare le richieste client in ingresso prima di inviarle a un servizio downstream. Crittografa quindi le risposte back-end in uscita prima di inviarle ai client.

- **SSL server:** per i servizi back-end configurati per HTTPS, è possibile configurare BIG-IP per l'uso di un profilo SSL del server. Con un profilo SSL del server, BIG-IP crittografa nuovamente la richiesta client prima di inviarla al servizio back-end di destinazione. Quando il server restituisce una risposta crittografata, il sistema BIG-IP decrittografa e crittografa nuovamente la risposta, prima di inviarla al client, tramite il profilo SSL client configurato.

Il provisioning di entrambi i profili SSL client e server avrà big-IP preconfigurato e pronto per tutti gli scenari SHA.

1. Dalla barra di spostamento a sinistra passare a System  >  **Certificate Management** Traffic Certificate  >  **Management** SSL Certificate List  >    >  **Import**

2. **Nell'elenco a** discesa Tipo di importazione selezionare **PKCS 12(IIS)**

3. Specificare un nome per il certificato importato, ad esempio  `ContosoWilcardCert`

4. Selezionare **Scegli file** per passare al certificato Web SSL il cui nome soggetto corrisponde al suffisso di dominio che si prevede di usare per i servizi pubblicati

5. Specificare la **password** per il certificato importato e quindi selezionare **Importa**

6. Dalla barra di spostamento a sinistra passare a **Local Traffic** Profiles SSL  >  Client (Client SSL **profili**  >  **traffico**  >   locale) e quindi selezionare **Create (Crea).**

7. Nella pagina **Nuovo profilo SSL** client specificare un nome descrittivo univoco per il nuovo profilo SSL client e verificare che profilo padre sia impostato su **clientsl**

![L'immagine mostra l'aggiornamento di big-ip](./media/f5ve-deployment-plan/client-ssl.png)

8. Selezionare la casella di controllo all'estrema destra nella **riga Certificate Key Chain (Catena di chiavi del** certificato) e selezionare Add **(Aggiungi)**

9. Nei tre elenchi a discesa selezionare il certificato con caratteri jolly importato senza passphrase, quindi **selezionare Aggiungi**  >  **completato.**

![L'immagine mostra l'aggiornamento del carattere jolly contoso big-ip](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Ripetere i passaggi da 6 a 9 per creare un **profilo certificato del server SSL.** Nella barra multifunzione superiore selezionare **SSL** Server Create  >  **(Crea server**  >  SSL).

11. Nella pagina **Nuovo profilo SSL** server specificare un nome descrittivo univoco per il nuovo profilo SSL del server e verificare che profilo padre sia impostato su **serversl**

12. Selezionare la casella di controllo all'estrema destra per le righe Certificato e Chiave e nell'elenco a discesa selezionare il certificato importato, seguito da **Completato.**

![L'immagine mostra l'aggiornamento di tutti i profili del server big-ip](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Se non si riesce ad ottenere un certificato SSL, è possibile usare il server BIG-IP autofirmato integrato e i certificati SSL client. Nel browser verrà semplicemente visualizzato un errore di certificato.

Un passaggio finale nella preparazione di un BIG-IP per SHA consiste nel verificare che sia in grado di individuare le risorse di pubblicazione e anche il servizio directory su cui si basa per l'accesso SSO. Un BIG-IP ha due origini di risoluzione dei nomi, a partire dal file locale/.../hosts oppure, se non viene trovato un record, il sistema BIG-IP usa qualsiasi servizio DNS con cui è stato configurato. Il metodo del file hosts non si applica ai nodi e ai pool APM che usano un nome di dominio completo.

1. Nella configurazione Web passare a **DNS** del dispositivo  >  **di Configurazione**  >    >  **sistema**

2. In **Elenco server di ricerca DNS** immettere l'indirizzo IP del server DNS degli ambienti

3. Selezionare **Aggiungi**  >  **aggiornamento**

Come passaggio separato e facoltativo, è possibile prendere in considerazione una configurazione [LDAP](https://somoit.net/f5-big-ip/authentication-using-active-directory) per autenticare gli amministratori di sistema BIG-IP in AD anziché gestire gli account BIG-IP locali.

## <a name="update-big-ip"></a>Aggiornare BIG-IP

La MACCHINA VIRTUALE BIG-IP deve essere aggiornata per sbloccare tutte le nuove funzionalità trattate nelle linee guida basate [su scenario.](f5-aad-integration.md) È possibile controllare la versione di TMOS del sistema passando il puntatore del mouse BIG-IPs nome host nella parte superiore sinistra della pagina principale. È consigliabile eseguire v15.x e versioni successive, scaricabile da [F5 download](https://downloads.f5.com/esd/productlines.jsp). Usare le [indicazioni](https://support.f5.com/csp/article/K34745165) per aggiornare il sistema operativo principale (TMOS).

Se l'aggiornamento del TMOS principale non è possibile, valutare almeno l'aggiornamento della sola configurazione guidata, usando questa procedura.

1. Dalla scheda principale della configurazione Web BIG-IP passare a **Configurazione**  >  **guidata di Accesso**

2. Nella pagina **Configurazione guidata** selezionare **Upgrade Guided Configuration (Configurazione guidata aggiornamento)**

![L'immagine mostra come aggiornare big-ip](./media/f5ve-deployment-plan/update-big-ip.png)

3. Nella finestra **di dialogo Upgrade Guided Configuration** (Configurazione guidata **aggiornamento) scegliere File** per caricare il pacchetto del caso d'uso scaricato e selezionare Carica e **installa**

4. Al termine dell'aggiornamento, selezionare **Continua.**

## <a name="backup-big-ip"></a>Backup BIG-IP

Con il provisioning completo del sistema BIG-IP, è consigliabile eseguire un backup completo della configurazione:

1. Passare a **System**  >  **Archives** Create (Crea archivi di  >  **sistema)**

2. Specificare un nome **file univoco** e abilitare **la crittografia** con una passphrase

3. Impostare **l'opzione Chiavi private** su **Includi per** eseguire anche il backup dei certificati SSL e del dispositivo

4. Selezionare **Completato** e attendere il completamento del processo

5. Verrà visualizzato uno stato dell'operazione che fornisce lo stato del risultato del backup. Selezionare **OK**.

6. Salvare l'archivio del set di configurazione utente (UCS) in locale scegliendo il collegamento del backup e selezionare **Scarica**.

Come passaggio facoltativo, è anche possibile eseguire un backup dell'intero disco di sistema usando gli snapshot di [Azure,](../../virtual-machines/windows/snapshot-copy-managed-disk.md)che a differenza del backup della configurazione Web offrono alcune opzioni di emergenza per il test tra le versioni di TMOS o il rollback in un sistema nuovo.

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>Ripristinare BIG-IP

Il ripristino di un BIG-IP segue una procedura simile al backup e può essere usato anche per la migrazione delle impostazioni di configurazione tra macchine virtuali BIG-IP. Prima di importare un backup, è necessario osservare i dettagli sui percorsi di aggiornamento supportati.

1. Passare ad **Archivi**  >  **di sistema**

2. Scegliere il collegamento di un backup da  ripristinare o selezionare il pulsante Carica per passare a un archivio UCS salvato in precedenza che non è presente nell'elenco

3. Specificare la passphrase per il backup e selezionare **Ripristina**

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>Al momento della scrittura, il cmdlet AzVmSnapshot è limitato al ripristino dello snapshot più recente, in base alla data. Gli snapshot vengono archiviati nella radice del gruppo di risorse della macchina virtuale. Tenere presente che il ripristino degli snapshot riavvia una macchina virtuale di Azure, quindi è necessario eseguire questa operazione con attenzione.

## <a name="additional-resources"></a>Risorse aggiuntive

-   [Reimpostare la password VE BIG-IP in Azure](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [Reimpostare la password senza usare il portale](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [Modificare la scheda di interfaccia di rete usata per la gestione ve BIG-IP](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [Informazioni sulle route in una singola configurazione della scheda di interfaccia di rete](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Passaggi successivi

Selezionare uno [scenario di distribuzione e](f5-aad-integration.md) avviare l'implementazione.
