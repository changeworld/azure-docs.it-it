---
title: Rotazione dei certificati per database di Azure per MariaDB
description: Informazioni sulle modifiche imminenti delle modifiche apportate al certificato radice che influiranno sul database di Azure per MariaDB
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 105bc7f14f9ddcc4a64564edc1eebcd17b898bc6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698995"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>Informazioni sulle modifiche apportate alla modifica della CA radice per il database di Azure per MariaDB

Il database di Azure per MariaDB ha completato la modifica del certificato radice il **15 febbraio 2021 (02/15/2021)** come parte delle procedure consigliate per la manutenzione e la sicurezza standard. Questo articolo fornisce ulteriori informazioni sulle modifiche, sulle risorse interessate e sui passaggi necessari per garantire che l'applicazione mantenga la connettività al server di database.

> [!NOTE]
> Questo articolo contiene riferimenti al termine _slave_, un termine che Microsoft non usa più. Quando il termine verrà rimosso dal software, verrà rimosso anche dall'articolo.
>

## <a name="why-root-certificate-update-is-required"></a>Perché è necessario l'aggiornamento del certificato radice?

Gli utenti del database di Azure per MariaDB possono usare solo il certificato predefinito per connettersi a un database di Azure per il server MariaDB, disponibile [qui](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). Tuttavia, il [Forum del browser dell'autorità di certificazione (CA)](https://cabforum.org/)ha pubblicato di   recente report di più certificati rilasciati dai fornitori di CA come non conformi.

In base ai requisiti di conformità del settore, i fornitori di CA hanno iniziato a revocare i certificati della CA per le CA non conformi, che richiedono che i server usino certificati rilasciati da CA conformi e firmati da certificati della CA da tali CA conformi. Poiché il database di Azure per MariaDB ha usato uno di questi certificati non conformi, è necessario ruotare il certificato nella versione conforme per ridurre al minimo le potenziali minacce per i server MySQL.

Il nuovo certificato viene implementato e in vigore a partire dal 15 febbraio 2021 (02/15/2021). 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Quale modifica è stata eseguita il 15 febbraio 2021 (02/15/2021)?

Il 15 febbraio 2021 il [certificato radice BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) è stato sostituito con una **versione conforme** dello stesso [certificato radice BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) per garantire che i clienti esistenti non debbano apportare modifiche e che non vi sia alcun effetto sulle connessioni al server. Durante questa modifica, il [certificato radice BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) non è stato **sostituito** con [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) e tale modifica viene posticipata per consentire ai clienti di apportare la modifica in modo più tempo.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>È necessario apportare modifiche nel client per mantenere la connettività?

Non sono necessarie modifiche sul lato client. Se è stata seguita la raccomandazione precedente, sarà comunque possibile continuare a connettersi fino a quando il **certificato BaltimoreCyberTrustRoot non viene rimosso** dal certificato della CA combinato. **Si consiglia di non rimuovere il BaltimoreCyberTrustRoot dal certificato della CA combinato fino a quando non si notano altre informazioni per mantenere la connettività.**

### <a name="previous-recommendation"></a>Raccomandazione precedente

- Scaricare la CA **BaltimoreCyberTrustRoot**  &  **DigiCertGlobalRootG2** dai collegamenti seguenti:

  - [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  - [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

- Sono inclusi un archivio certificati CA combinato con certificati **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** .

  - Per gli utenti Java (MariaDB Connector/J), eseguire:

    ```console
    keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
    ```

    Sostituire quindi il file dell'archivio chiavi originale con quello nuovo generato:

    - System. SetProperty ("javax. NET. SSL. trustStore", "path_to_truststore_file");
    - System. SetProperty ("javax. NET. SSL. trustStorePassword", "password");

  - Per gli utenti .NET (MariaDB Connector/NET, MariaDBConnector), verificare che **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** siano presenti nell'archivio certificati di Windows, in autorità di certificazione radice attendibili. Se non esistono certificati, importare il certificato mancante.

    [![Certificato .NET per database di Azure per MariaDB](media/overview/netconnecter-cert.png)](media/overview/netconnecter-cert.png#lightbox)

  - Per gli utenti .NET in Linux che usano SSL_CERT_DIR, assicurarsi che **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** siano presenti nella directory indicata da SSL_CERT_DIR. Se non esistono certificati, creare il file del certificato mancante.

  - Per altri utenti (MariaDB client/MariaDB Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift), è possibile unire due file di certificato CA come questo formato

   ```
   -----BEGIN CERTIFICATE-----
   (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
   -----END CERTIFICATE-----
   -----BEGIN CERTIFICATE-----
   (Root CA2: DigiCertGlobalRootG2.crt.pem)
   -----END CERTIFICATE-----
   ```

- Sostituire il file PEM CA radice originale con il file CA radice combinato e riavviare l'applicazione/client.
- In futuro, dopo la distribuzione del nuovo certificato sul lato server, è possibile modificare il file di CA PEM in DigiCertGlobalRootG2. CRT. pem.

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Perché il certificato BaltimoreCyberTrustRoot non è stato sostituito in DigiCertGlobalRootG2 durante questa modifica il 15 febbraio 2021?

Abbiamo valutato la preparazione del cliente per questa modifica e abbiamo realizzato che molti clienti stavano cercando ulteriori lead time per gestire questa modifica. Per garantire la conformità a più lead time ai clienti, abbiamo deciso di rinviare la modifica del certificato a DigiCertGlobalRootG2 per almeno un anno garantendo una lead time sufficiente ai clienti e agli utenti finali. 

Le indicazioni per gli utenti sono: usare i passaggi indicati in questo documento per creare un certificato combinato e connettersi al server, ma non rimuovere il certificato BaltimoreCyberTrustRoot fino a quando non viene inviata una comunicazione per rimuoverlo. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Cosa accade se è stato rimosso il certificato BaltimoreCyberTrustRoot?

Si inizierà a errori di connettività durante la connessione al database di Azure per il server MariaDB. Per mantenere la connettività, sarà necessario configurare di nuovo [SSL](howto-configure-ssl.md) con il certificato [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) .

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. se non si usa SSL/TLS, è ancora necessario aggiornare la CA radice?

Non sono necessarie azioni se non si usa SSL/TLS.

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Se si usa SSL/TLS, è necessario riavviare il server di database per aggiornare la CA radice?

No, non è necessario riavviare il server di database per iniziare a usare il nuovo certificato. L'aggiornamento del certificato è una modifica sul lato client e le connessioni client in ingresso devono usare il nuovo certificato per assicurarsi che possano connettersi al server di database.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. Ricerca per categorie verificare se si usa SSL/TLS con la verifica del certificato radice?

È possibile stabilire se le connessioni verificano il certificato radice rivedendo la stringa di connessione.

- Se la stringa di connessione include `sslmode=verify-ca` o `sslmode=verify-identity` , è necessario aggiornare il certificato.
- Se la stringa di connessione include `sslmode=disable` ,, `sslmode=allow` `sslmode=prefer` o `sslmode=require` , non è necessario aggiornare i certificati.
- Se la stringa di connessione non specifica sslmode, non è necessario aggiornare i certificati.

Se si usa un client che astrae la stringa di connessione, esaminare la documentazione del client per capire se i certificati sono verificati.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mariadb"></a>4. quali sono le conseguenze dell'uso del servizio app con il database di Azure per MariaDB?

Per i servizi app di Azure che si connettono al database di Azure per MariaDB, esistono due possibili scenari, a seconda di come si usa SSL con l'applicazione.

- Questo nuovo certificato è stato aggiunto al servizio app a livello di piattaforma. Se si usano i certificati SSL inclusi nella piattaforma del servizio app nell'applicazione, non è necessaria alcuna azione. Questo è lo scenario più comune. 
- Se si include in modo esplicito il percorso del file del certificato SSL nel codice, è necessario scaricare il nuovo certificato e aggiornare il codice per usare il nuovo certificato. Un esempio valido di questo scenario è quando si usano contenitori personalizzati nel servizio app come condivisi nella [documentazione del servizio app](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress). Si tratta di uno scenario insolito, ma alcuni utenti lo usano.

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mariadb"></a>5. quale è l'effetto se si usa Azure Kubernetes Services (AKS) con database di Azure per MariaDB?

Se si sta provando a connettersi al database di Azure per MariaDB usando Azure Kubernetes Services (AKS), è simile all'accesso da un ambiente host dedicato ai clienti. Fare riferimento ai passaggi [qui](../aks/ingress-own-tls.md).

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mariadb"></a>6. quali sono le conseguenze dell'uso di Azure Data Factory per connettersi al database di Azure per MariaDB?

Per il connettore che usa Azure Integration Runtime, il connettore usa i certificati nell'archivio certificati di Windows nell'ambiente ospitato da Azure. Questi certificati sono già compatibili con i nuovi certificati applicati e pertanto non è necessaria alcuna azione.

Per il connettore che usa Integration Runtime self-hosted in cui si include in modo esplicito il percorso del file del certificato SSL nella stringa di connessione, è necessario scaricare il [nuovo certificato](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) e aggiornare la stringa di connessione per usarlo.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. è necessario pianificare un tempo di inattività per la manutenzione del server di database per questa modifica?

No. Poiché la modifica è solo sul lato client per la connessione al server di database, non è necessario alcun tempo di inattività di manutenzione per il server di database per questa modifica.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. Se si crea un nuovo server dopo il 15 febbraio 2021 (02/15/2021), si avrà un effetto?

Per i server creati dopo il 15 febbraio 2021 (02/15/2021), si continuerà a usare [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) per le applicazioni per la connessione tramite SSL.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. con quale frequenza Microsoft aggiorna i certificati o quali sono i criteri di scadenza?

Questi certificati usati da database di Azure per MariaDB sono forniti da autorità di certificazione (CA) attendibili. Il supporto di questi certificati è quindi associato al supporto di questi certificati da parte della CA. Il certificato [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) è pianificato per scadere nel 2025, pertanto Microsoft dovrà eseguire una modifica del certificato prima della scadenza. Inoltre, nel caso in cui siano presenti bug non previsti in questi certificati predefiniti, è necessario che Microsoft effettui la rotazione del certificato nel primo momento simile alla modifica eseguita il 15 febbraio 2021 per garantire che il servizio sia sempre protetto e conforme.

### <a name="10-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>10. Se si usano le repliche di lettura, è necessario eseguire questo aggiornamento solo sul server di origine o sulle repliche di lettura?

Poiché questo aggiornamento è una modifica lato client, se il client utilizzato per leggere i dati dal server di replica, è necessario applicare anche le modifiche per tali client.

### <a name="11-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>11. Se si usa la replica dati, è necessario eseguire qualsiasi azione?

- Se la replica dei dati viene eseguita da una macchina virtuale (locale o da una macchina virtuale di Azure) al database di Azure per MySQL, è necessario verificare se SSL è usato per creare la replica. Eseguire **show slave status** e verificare l'impostazione seguente.

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

Se si usa la [replica dei dati](concepts-data-in-replication.md) per connettersi a database di Azure per MySQL, è necessario prendere in considerazione due aspetti:

- Se la replica dei dati viene eseguita da una macchina virtuale (locale o da una macchina virtuale di Azure) al database di Azure per MySQL, è necessario verificare se SSL è usato per creare la replica. Eseguire **show slave status** e verificare l'impostazione seguente. 

  ```azurecli-interactive
  Master_SSL_Allowed            : Yes
  Master_SSL_CA_File            : ~\azure_mysqlservice.pem
  Master_SSL_CA_Path            :
  Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
  Master_SSL_Cipher             :
  Master_SSL_Key                : ~\azure_mysqlclient_key.pem
  ```

  Se viene visualizzato il certificato fornito per il CA_file, SSL_Cert e SSL_Key, sarà necessario aggiornare il file aggiungendo il [nuovo certificato](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) e creare un file di certificato combinato.

- Se la replica dei dati è tra due database di Azure per MySQL, sarà necessario reimpostare la replica eseguendo **CALL MySQL.az_replication_change_master** e fornire il nuovo certificato radice duale come ultimo parametro [master_ssl_ca](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication).

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. è presente una query sul lato server per verificare se è in uso SSL?

Per verificare se si sta usando la connessione SSL per la connessione al server, fare riferimento alla [verifica SSL](howto-configure-ssl.md#verify-the-ssl-connection).

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13. esiste un'azione necessaria se si dispone già di DigiCertGlobalRootG2 nel file di certificato?

No. Non è necessario alcun intervento se il file del certificato dispone già del **DigiCertGlobalRootG2**.

### <a name="14-what-if-i-have-further-questions"></a>14. cosa accade se si hanno altre domande?

In caso di domande, ottenere le risposte dagli esperti della community in [Microsoft Q&a](mailto:AzureDatabaseformariadb@service.microsoft.com). Se si dispone di un piano di supporto ed è necessario assistenza tecnica, [contattare](mailto:AzureDatabaseformariadb@service.microsoft.com)Microsoft.
