---
title: Risolvere i problemi di autenticazione reciproca sul gateway applicazione Azure
description: Informazioni su come risolvere i problemi di autenticazione reciproca sul gateway applicazione
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 623addd253b3eb28bdf70db02ddfbe4b320cbae7
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231506"
---
# <a name="troubleshooting-mutual-authentication-errors-in-application-gateway-preview"></a>Risoluzione degli errori di autenticazione reciproca nel gateway applicazione (anteprima)

Informazioni su come risolvere i problemi relativi all'autenticazione reciproca quando si usa il gateway applicazione. 

## <a name="overview"></a>Panoramica 

Dopo aver configurato l'autenticazione reciproca in un gateway applicazione, è possibile che vengano visualizzati diversi errori quando si tenta di usare l'autenticazione reciproca. Di seguito sono riportate alcune cause comuni degli errori:

* Caricamento di un certificato o di una catena di certificati senza un certificato CA radice
* È stata caricata una catena di certificati con più certificati CA radice
* È stata caricata una catena di certificati che conteneva solo un certificato foglia senza un certificato CA 
* Errori di convalida dovuti alla mancata corrispondenza del DN dell'emittente  

Verranno esaminati diversi scenari in cui è possibile eseguire la risoluzione dei problemi e come risolverli. Si affronteranno quindi i codici di errore e si spiegheranno le possibili cause di determinati codici di errore che potrebbero essere visualizzati con l'autenticazione reciproca. 

## <a name="scenario-troubleshooting---configuration-problems"></a>Risoluzione dei problemi di scenario-problemi di configurazione
Esistono alcuni scenari che è possibile affrontare quando si tenta di configurare l'autenticazione reciproca. Verrà illustrata la procedura dettagliata per risolvere alcuni dei problemi più comuni. 

### <a name="self-signed-certificate"></a>Certificato autofirmato

#### <a name="problem"></a>Problema 

Il certificato client caricato è un certificato autofirmato e viene restituito il codice di errore ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate.

#### <a name="solution"></a>Soluzione 

Verificare che il certificato autofirmato in uso disponga dell'estensione *BasicConstraintsOid* = "2.5.29.19", che indica che l'oggetto può fungere da autorità di certificazione. In questo modo si garantisce che il certificato utilizzato sia un certificato della CA. Per ulteriori informazioni su come generare certificati client autofirmati, consultare [certificati client attendibili](./mutual-authentication-certificate-management.md).

## <a name="scenario-troubleshooting---connectivity-problems"></a>Risoluzione dei problemi di scenario-problemi di connettività

Potrebbe essere stato possibile configurare l'autenticazione reciproca senza problemi, ma si sono verificati problemi durante l'invio di richieste al gateway applicazione. Nella sezione seguente vengono affrontati alcuni problemi e soluzioni comuni. È possibile trovare la proprietà *sslClientVerify* nei log di accesso del gateway applicazione. 

### <a name="sslclientverify-is-none"></a>SslClientVerify è NONE

#### <a name="problem"></a>Problema 

La proprietà *sslClientVerify* viene visualizzata come "None" nei log di accesso. 

#### <a name="solution"></a>Soluzione 

Questa operazione viene visualizzata quando il client non invia un certificato client quando invia una richiesta al gateway applicazione. Questo problema può verificarsi se il client che invia la richiesta al gateway applicazione non è configurato correttamente per l'utilizzo di certificati client. Un modo per verificare che la configurazione dell'autenticazione client nel gateway applicazione funzioni come previsto è tramite il comando OpenSSL seguente:

```
openssl s_client -connect <hostname:port> -cert <path-to-certificate> -key <client-private-key-file> 
```

Il `-cert` flag è il certificato foglia, il `-key` flag è il file della chiave privata del client. 

Per altre informazioni su come usare il comando OpenSSL `s_client` , vedere la [pagina manuale](https://www.openssl.org/docs/man1.0.2/man1/openssl-s_client.html).

### <a name="sslclientverify-is-failed"></a>SslClientVerify non riuscito

#### <a name="problem"></a>Problema

Il *sslClientVerify* della proprietà viene visualizzato come "non riuscito" nei log di accesso. 

#### <a name="solution"></a>Soluzione

Esistono diverse possibili cause degli errori nei log di accesso. Di seguito è riportato un elenco di cause comuni di errore:
* **Non è possibile ottenere il certificato dell'autorità emittente:** Non è stato possibile trovare il certificato dell'autorità emittente del certificato client. Ciò significa in genere che la catena di certificati CA client attendibile non è completa nel gateway applicazione. Verificare che la catena di certificati CA client attendibile caricata nel gateway applicazione sia stata completata.  
* **Non è possibile ottenere il certificato dell'autorità emittente locale:** Analogamente a non è possibile ottenere il certificato dell'autorità emittente, non è stato possibile trovare il certificato dell'autorità emittente del certificato client. Ciò significa in genere che la catena di certificati CA client attendibile non è completa nel gateway applicazione. Verificare che la catena di certificati CA client attendibile caricata nel gateway applicazione sia stata completata.
* **Impossibile verificare il primo certificato:** Impossibile verificare il certificato client. Questo errore si verifica in modo specifico quando il client presenta solo il certificato foglia, il cui emittente non è attendibile. Verificare che la catena di certificati CA client attendibile caricata nel gateway applicazione sia stata completata.
* **Impossibile verificare l'autorità di certificazione del certificato client:** Questo errore si verifica quando la configurazione *VerifyClientCertIssuerDN* è impostata su true. Questa situazione si verifica in genere quando il DN dell'emittente del certificato client non corrisponde ai *ClientCertificateIssuerDN* estratti dalla catena di certificati della CA client attendibile caricata dal cliente. Per altre informazioni sul modo in cui il gateway applicazione estrae il *ClientCertificateIssuerDN*, vedere [gateway applicazione estrazione del DN dell'autorità emittente](./mutual-authentication-overview.md#verify-client-certificate-dn). Come procedura consigliata, assicurarsi che sia in corso il caricamento di una catena di certificati per ogni file nel gateway applicazione. 

Per altre informazioni su come estrarre l'intera catena di certificati CA client attendibili per caricare il gateway applicazione, vedere [come estrarre le catene di certificati CA client attendibili](./mutual-authentication-certificate-management.md).

## <a name="error-code-troubleshooting"></a>Risoluzione dei problemi del codice di errore
Se viene visualizzato uno dei seguenti codici di errore, sono disponibili alcune soluzioni consigliate che consentono di risolvere il problema che si potrebbe affrontare. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustspecifydata"></a>Codice di errore: ApplicationGatewayTrustedClientCertificateMustSpecifyData

#### <a name="cause"></a>Causa

Sono presenti dati del certificato mancanti. Il certificato caricato potrebbe essere un file vuoto senza dati del certificato. 

#### <a name="solution"></a>Soluzione

Verificare che il file di certificato caricato non includa dati mancanti. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustnothaveprivatekey"></a>Codice di errore: ApplicationGatewayTrustedClientCertificateMustNotHavePrivateKey

#### <a name="cause"></a>Causa

Nella catena di certificati è presente una chiave privata. Nella catena di certificati non dovrebbe essere presente una chiave privata. 

#### <a name="solution"></a>Soluzione

Controllare la catena di certificati che è stata caricata e rimuovere la chiave privata che faceva parte della catena. Ricaricare la catena senza la chiave privata. 

### <a name="error-code-applicationgatewaytrustedclientcertificateinvaliddata"></a>Codice di errore: ApplicationGatewayTrustedClientCertificateInvalidData

#### <a name="cause"></a>Causa

Questo codice di errore può avere due cause potenziali.
1. L'analisi non è riuscita perché la catena non è stata presentata nel formato corretto. Il gateway applicazione prevede che una catena di certificati sia in formato PEM e prevede che i singoli dati del certificato siano delimitati. 
2. Il parser non ha trovato alcun elemento da analizzare. Il file caricato potrebbe potenzialmente avere solo i delimitatori, ma non i dati del certificato. 

#### <a name="solution"></a>Soluzione

A seconda della cause di questo errore, esistono due possibili soluzioni. 
* Verificare che la catena di certificati caricata sia nel formato corretto (PEM) e che i dati del certificato siano delimitati correttamente. 
* Controllare che il file di certificato caricato contenga i dati del certificato oltre ai delimitatori. 

### <a name="error-code-applicationgatewaytrustedclientcertificatedoesnotcontainanycacertificate"></a>Codice di errore: ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate

#### <a name="cause"></a>Causa

Il certificato caricato contiene solo un certificato foglia senza un certificato CA. Il caricamento di una catena di certificati con certificati della CA e un certificato foglia è accettabile perché il certificato foglia viene semplicemente ignorato, ma un certificato deve disporre di una CA. 

#### <a name="solution"></a>Soluzione 

Verificare che la catena di certificati che è stata caricata sia contenuta in un solo certificato foglia. È necessario che l'estensione *BasicConstraintsOid* = "2.5.29.19" sia presente e indicare che l'oggetto può fungere da autorità di certificazione.

### <a name="error-code-applicationgatewayonlyonerootcaallowedintrustedclientcertificate"></a>Codice di errore: ApplicationGatewayOnlyOneRootCAAllowedInTrustedClientCertificate

#### <a name="cause"></a>Causa

La catena di certificati contiene più certificati CA radice *o* contiene certificati CA radice zero. 

#### <a name="solution"></a>Soluzione

I certificati caricati devono contenere esattamente un certificato CA radice (e, tuttavia, molti certificati CA intermedi necessari).


