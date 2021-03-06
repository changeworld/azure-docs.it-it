---
title: Rotazione dei certificati per database di Azure per MySQL
description: Informazioni sulle modifiche imminenti delle modifiche apportate al certificato radice che avranno effetto sul database di Azure per MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 4b218a9481fdd5970fd6fc8fa6a1d071161e5b58
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313365"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql-single-server"></a>Informazioni sulle modifiche apportate alla modifica della CA radice per il server singolo database di Azure per MySQL

Il server singolo del database di Azure per MySQL ha completato la modifica del certificato radice il **15 febbraio 2021 (02/15/2021)** come parte delle procedure consigliate per la manutenzione e la sicurezza standard. Questo articolo fornisce ulteriori informazioni sulle modifiche, sulle risorse interessate e sui passaggi necessari per garantire che l'applicazione mantenga la connettività al server di database.

> [!NOTE]
> Questo articolo si applica solo al [database di Azure per MySQL-singolo server](single-server-overview.md) . Per [database di Azure per MySQL-server flessibile](flexible-server/overview.md), il certificato necessario per comunicare tramite SSL è la [CA radice globale DigiCert](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)
>
> Questo articolo contiene riferimenti al termine _slave_, un termine che Microsoft non usa più. Quando il termine verrà rimosso dal software, verrà rimosso anche dall'articolo.
>

#### <a name="why-is-a-root-certificate-update-required"></a>Perché è necessario un aggiornamento del certificato radice?

Gli utenti del database di Azure per MySQL possono usare solo il certificato predefinito per connettersi al proprio server MySQL, disponibile [qui](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). Tuttavia, il [Forum del browser dell'autorità di certificazione (CA)](https://cabforum.org/)ha pubblicato di   recente report di più certificati rilasciati dai fornitori di CA come non conformi.

In base ai requisiti di conformità del settore, i fornitori di autorità di certificazione hanno iniziato a revocare i certificati della CA per le CA non conformi, che richiedono che i server usino certificati rilasciati da CA conformi e firmati da certificati CA da tali CA conformi. Poiché il database di Azure per MySQL usava uno di questi certificati non conformi, era necessario ruotare il certificato nella versione conforme per ridurre al minimo le potenziali minacce per i server MySQL.

Il nuovo certificato viene implementato in vigore a partire dal 15 febbraio 2021 (02/15/2021).

#### <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Quale modifica è stata eseguita il 15 febbraio 2021 (02/15/2021)?

Il 15 febbraio 2021, il [certificato radice BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) è stato sostituito con una **versione conforme** dello stesso [certificato radice BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) per garantire che i clienti esistenti non debbano modificare nulla e non vi sia alcun effetto sulle connessioni al server. Durante questa modifica, il [certificato radice BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) non è stato **sostituito** con [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) e tale modifica viene posticipata per consentire ai clienti di apportare la modifica in modo più tempo.

#### <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>È necessario apportare modifiche nel client per mantenere la connettività?

Sul lato client non è richiesta alcuna modifica. Se è stata seguita la raccomandazione precedente riportata di seguito, è possibile continuare a connettersi fino a quando il **certificato BaltimoreCyberTrustRoot non viene rimosso** dal certificato della CA combinato. **Per mantenere la connettività, è consigliabile mantenere il BaltimoreCyberTrustRoot nel certificato della CA combinato fino a quando non si notano altre informazioni.**

###### <a name="previous-recommendation"></a>Raccomandazione precedente

Per evitare interruzioni della disponibilità dell'applicazione in seguito alla revoca imprevista dei certificati o all'aggiornamento di un certificato revocato, attenersi alla procedura seguente. L'idea è creare un nuovo file con *estensione PEM* , che combina il certificato corrente e quello nuovo e durante la convalida del certificato SSL, verrà usato uno dei valori consentiti. Vedere i passaggi seguenti:

1. Scaricare BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 CA radice dai collegamenti seguenti:

    * [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
    * [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

2. Sono inclusi un archivio certificati CA combinato con certificati **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** .

    * Per gli utenti Java (MySQL Connector/J), eseguire:

      ```console
      keytool -importcert -alias MySQLServerCACert -file D:\BaltimoreCyberTrustRoot.crt.pem -keystore truststore -storepass password -noprompt
      ```

      ```console
      keytool -importcert -alias MySQLServerCACert2 -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password -noprompt
      ```

      Sostituire quindi il file dell'archivio chiavi originale con quello nuovo generato:

      * System. SetProperty ("javax. NET. SSL. trustStore", "path_to_truststore_file");
      * System. SetProperty ("javax. NET. SSL. trustStorePassword", "password");

    * Per gli utenti di .NET (MySQL Connector/NET, MySQLConnector), assicurarsi che **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** siano presenti nell'archivio certificati di Windows, autorità di certificazione radice attendibili. Se non esistono certificati, importare il certificato mancante.

      :::image type="content" source="media/overview/netconnecter-cert.png" alt-text="Diagramma dei certificati .NET per database di Azure per MySQL":::

    * Per gli utenti .NET in Linux che usano SSL_CERT_DIR, assicurarsi che **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** siano presenti nella directory indicata da SSL_CERT_DIR. Se non esistono certificati, creare il file del certificato mancante.

    * Per altri utenti (client MySQL/MySQL Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift), è possibile unire due file di certificato CA nel formato seguente:

      ```
      -----BEGIN CERTIFICATE-----
      (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
      -----END CERTIFICATE-----
      -----BEGIN CERTIFICATE-----
      (Root CA2: DigiCertGlobalRootG2.crt.pem)
      -----END CERTIFICATE-----
      ```

3. Sostituire il file PEM CA radice originale con il file CA radice combinato e riavviare l'applicazione/client.

   In futuro, dopo la distribuzione del nuovo certificato sul lato server, è possibile modificare il file di CA PEM in DigiCertGlobalRootG2. CRT. pem.

> [!NOTE]
> Non eliminare o modificare il **certificato Baltimore** fino a quando non viene apportata la modifica del certificato. Una volta apportata la modifica, si invierà una comunicazione, che sarà quindi sicura per eliminare il **certificato Baltimore**.

#### <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Perché il certificato BaltimoreCyberTrustRoot non è stato sostituito in DigiCertGlobalRootG2 durante questa modifica il 15 febbraio 2021?

È stata valutata la preparazione del cliente per questa modifica e si è reso conto che molti clienti stavano cercando lead time aggiuntive per gestire la modifica. Per fornire più lead time ai clienti per prepararsi, si è deciso di rinviare la modifica del certificato a DigiCertGlobalRootG2 per almeno un anno, fornendo lead time sufficienti per i clienti e gli utenti finali.

Si consiglia agli utenti di usare i passaggi descritti in questo documento per creare un certificato combinato e connettersi al server, ma non rimuovere il certificato BaltimoreCyberTrustRoot fino a quando non viene inviata una comunicazione per rimuoverlo.

#### <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Cosa accade se è stato rimosso il certificato BaltimoreCyberTrustRoot?

Si inizieranno a riscontrare errori di connettività durante la connessione al database di Azure per il server MySQL. Per mantenere la connettività, è necessario configurare di nuovo [SSL](howto-configure-ssl.md) con il certificato [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) .

## <a name="frequently-asked-questions"></a>Domande frequenti

#### <a name="if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>Se non si usa SSL/TLS, è ancora necessario aggiornare la CA radice?

  Non sono necessarie azioni se non si usa SSL/TLS.

#### <a name="if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>Se si usa SSL/TLS, è necessario riavviare il server di database per aggiornare la CA radice?

No, non è necessario riavviare il server di database per iniziare a usare il nuovo certificato. Questo certificato radice è una modifica sul lato client e le connessioni client in ingresso devono usare il nuovo certificato per assicurarsi che possano connettersi al server di database.

#### <a name="how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>Ricerca per categorie sa se si usa SSL/TLS con la verifica del certificato radice?

È possibile stabilire se le connessioni verificano il certificato radice rivedendo la stringa di connessione.

* Se la stringa di connessione include `sslmode=verify-ca` o `sslmode=verify-identity` , è necessario aggiornare il certificato.
* Se la stringa di connessione include `sslmode=disable` ,, `sslmode=allow` `sslmode=prefer` o `sslmode=require` , non è necessario aggiornare i certificati.
* Se la stringa di connessione non specifica sslmode, non è necessario aggiornare i certificati.

Se si usa un client che astrae la stringa di connessione, esaminare la documentazione del client per capire se i certificati sono verificati.

#### <a name="what-is-the-impact-of-using-app-service-with-azure-database-for-mysql"></a>Qual è l'effetto dell'uso del servizio app con database di Azure per MySQL?

Per i servizi app di Azure che si connettono al database di Azure per MySQL, esistono due possibili scenari, a seconda di come si usa SSL con l'applicazione.

* Questo nuovo certificato è stato aggiunto al servizio app a livello di piattaforma. Se si usano i certificati SSL inclusi nella piattaforma del servizio app nell'applicazione, non è necessaria alcuna azione. Questo è lo scenario più comune.
* Se si include in modo esplicito il percorso del file del certificato SSL nel codice, è necessario scaricare il nuovo certificato e produrre un certificato combinato come indicato in precedenza e usare il file del certificato. Un esempio valido di questo scenario è quando si usano contenitori personalizzati nel servizio app come condivisi nella [documentazione del servizio app](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress). Si tratta di uno scenario insolito, ma alcuni utenti lo usano.

#### <a name="what-is-the-impact-of-using-azure-kubernetes-services-aks-with-azure-database-for-mysql"></a>Qual è l'effetto dell'uso di Azure Kubernetes Services (AKS) con database di Azure per MySQL?

Se si sta provando a connettersi al database di Azure per MySQL usando i servizi di Azure Kubernetes (AKS), è simile all'accesso da un ambiente host dedicato ai clienti. Fare riferimento ai passaggi [qui](../aks/ingress-own-tls.md).

#### <a name="what-is-the-impact-of-using-azure-data-factory-to-connect-to-azure-database-for-mysql"></a>Qual è l'effetto dell'uso di Azure Data Factory per connettersi a database di Azure per MySQL?

Per un connettore che usa Azure Integration Runtime, il connettore usa i certificati nell'archivio certificati di Windows nell'ambiente ospitato da Azure. Questi certificati sono già compatibili con i nuovi certificati applicati e pertanto non è necessaria alcuna azione.

Per un connettore che usa Integration Runtime self-hosted in cui si include in modo esplicito il percorso del file del certificato SSL nella stringa di connessione, è necessario scaricare il [nuovo certificato](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) e aggiornare la stringa di connessione per usarlo.

#### <a name="do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>È necessario pianificare un tempo di inattività per la manutenzione del server di database per questa modifica?

No. Poiché la modifica è solo sul lato client per la connessione al server di database, non è necessario alcun tempo di inattività di manutenzione per il server di database per questa modifica.

#### <a name="if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>Se si crea un nuovo server dopo il 15 febbraio 2021 (02/15/2021), si avrà un effetto?

Per i server creati dopo il 15 febbraio 2021 (02/15/2021), si continuerà a usare [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) per le applicazioni per la connessione tramite SSL.

#### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Con quale frequenza Microsoft aggiorna i certificati o quali sono i criteri di scadenza?

Questi certificati usati da database di Azure per MySQL sono forniti da autorità di certificazione (CA) attendibili. Il supporto di questi certificati è quindi associato al supporto di questi certificati da parte della CA. Il certificato [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) è pianificato per scadere nel 2025, pertanto Microsoft dovrà eseguire una modifica del certificato prima della scadenza. Inoltre, nel caso in cui siano presenti bug non previsti in questi certificati predefiniti, è necessario che Microsoft effettui la rotazione del certificato nel primo momento simile alla modifica eseguita il 15 febbraio 2021 per garantire che il servizio sia sempre protetto e conforme.

#### <a name="if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>Se si usano le repliche di lettura, è necessario eseguire questo aggiornamento solo sul server di origine o sulle repliche di lettura?

Poiché questo aggiornamento è una modifica lato client, se il client utilizzato per leggere i dati dal server di replica, è necessario applicare anche le modifiche per tali client.

#### <a name="if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>Se si usa la replica dati, è necessario eseguire qualsiasi azione?

Se si usa la [replica dei dati](concepts-data-in-replication.md) per connettersi a database di Azure per MySQL, è necessario prendere in considerazione due aspetti:

* Se la replica dei dati viene eseguita da una macchina virtuale (locale o da una macchina virtuale di Azure) al database di Azure per MySQL, è necessario verificare se SSL è usato per creare la replica. Eseguire **show slave status** e verificare l'impostazione seguente.  

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    Se si nota che il certificato viene fornito per il CA_file, SSL_Cert e SSL_Key, sarà necessario aggiornare il file aggiungendo il [nuovo certificato](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) e creare un file di certificato combinato.

* Se la replica dei dati è tra due server di database di Azure per MySQL, sarà necessario reimpostare la replica eseguendo **CALL MySQL.az_replication_change_master** e fornire il nuovo certificato radice duale come ultimo parametro [master_ssl_ca](howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication).

#### <a name="is-there-a-server-side-query-to-determine-whether-ssl-is-being-used"></a>Esiste una query sul lato server per determinare se è in uso SSL?

Per verificare se si sta usando la connessione SSL per la connessione al server, fare riferimento alla [verifica SSL](howto-configure-ssl.md#step-4-verify-the-ssl-connection).

#### <a name="is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>Esiste un'azione necessaria se si dispone già di DigiCertGlobalRootG2 nel file di certificato?

No. Non è necessario alcun intervento se il file del certificato dispone già del **DigiCertGlobalRootG2**.

#### <a name="what-if-i-have-further-questions"></a>Che cosa succede se ho altre domande?

Per domande, ottenere le risposte dagli esperti della community in [Microsoft Q&a](mailto:AzureDatabaseforMySQL@service.microsoft.com). Se si dispone di un piano di supporto ed è necessario assistenza tecnica, [contattare](mailto:AzureDatabaseforMySQL@service.microsoft.com)Microsoft.
