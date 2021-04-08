---
title: "Esercitazione: Integrare l'accesso Single Sign-On (SSO) di Azure Active Directory con Maverics Identity Orchestrator SAML Connector | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Maverics Identity Orchestrator SAML Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 19f6b0601afe9ad84f02c93d7f6e1ae3a71a06a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585095"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Integrare Azure AD Single Sign-On con il connettore SAML dell'agente di orchestrazione delle identità Maverics

L'agente di orchestrazione delle identità Maverics di strati fornisce un modo semplice per integrare le applicazioni locali con Azure Active Directory (Azure AD) per l'autenticazione e il controllo di accesso. L'agente di orchestrazione Maverics è in grado di modernizzare l'autenticazione e l'autorizzazione per le app che attualmente si basano su intestazioni, cookie e altri metodi di autenticazione proprietari. Le istanze dell'agente di orchestrazione Maverics possono essere distribuite in locale o nel cloud. 

Questa esercitazione sull'accesso ibrido illustra come eseguire la migrazione di un'applicazione Web locale attualmente protetta da un prodotto di gestione di accesso Web legacy per usare Azure AD per l'autenticazione e il controllo di accesso. I passaggi principali sono indicati di seguito.

1. Configurare l'agente di orchestrazione Maverics
1. Proxy di un'applicazione
1. Registrare un'applicazione aziendale in Azure AD
1. Eseguire l'autenticazione tramite Azure e autorizzare l'accesso all'applicazione
1. Aggiungere intestazioni per l'accesso trasparente alle applicazioni
1. Usare più applicazioni

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Maverics Identity Orchestrator SAML Connector abilitata per l'accesso SSO. Per ottenere il software Maverics, contattare [Strata Sales](mailto:sales@strata.io).
* Almeno un'applicazione che utilizza l'autenticazione basata su intestazione. Gli esempi funzionano con un'applicazione denominata Sonar, ospitata in https://app.sonarsystems.com e un'applicazione denominata Connectulum, ospitata in https://app.connectulum.com .
* Un computer Linux per ospitare l'agente di orchestrazione Maverics
  * Sistema operativo: RHEL 7,7 o versione successiva, CentOS 7 +
  * Disco: >= 10 GB
  * Memoria: >= 4 GB
  * Porte: 22 (SSH/SCP), 443, 7474
  * Accesso radice per le attività di installazione/amministrazione
  * Rete in uscita dal server che ospita l'agente di orchestrazione delle identità Maverics all'applicazione protetta

## <a name="step-1-set-up-the-maverics-orchestrator"></a>Passaggio 1: configurare l'agente di orchestrazione Maverics

### <a name="install-maverics"></a>Installare Maverics

1. Ottenere la versione più recente di Maverics RPM. Copiare il pacchetto nel sistema in cui si vuole installare il software Maverics.

1. Installare il pacchetto Maverics, sostituendo `maverics.rpm` con il nome del file personale.

   `sudo rpm -Uvf maverics.rpm`

   Dopo l'installazione, Maverics verrà eseguito come servizio in `systemd`. Per verificare che il servizio sia in esecuzione, eseguire il comando seguente:

   `sudo systemctl status maverics`

1. Per riavviare l'agente di orchestrazione e seguire i log, è possibile eseguire il comando seguente:

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

Dopo aver installato Maverics, il `maverics.yaml` file predefinito viene creato nella `/etc/maverics` Directory. Prima di modificare la configurazione in modo da includere `appgateways` e `connectors` , il file di configurazione sarà simile a questo z:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>Configurare DNS

Il DNS sarà utile per evitare di dover ricordare l'indirizzo IP del server dell'agente di orchestrazione.

Modificare il file host del computer del browser (il computer portatile), usando un ipotetico IP dell'agente di orchestrazione di 12.34.56.78. Nei sistemi operativi basati su Linux, questo file si trova in `/etc/hosts` . In Windows si trova in `C:\windows\system32\drivers\etc` .

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

Per verificare che il DNS sia configurato come previsto, è possibile eseguire una richiesta all'endpoint di stato dell'agente di orchestrazione. Dal browser, richiedere http://sonar.maverics.com:7474/status .

### <a name="configure-tls"></a>Configurare TLS

La comunicazione tramite canali sicuri per comunicare con l'agente di orchestrazione è essenziale per mantenere la sicurezza. Per ottenere questo risultato, è possibile aggiungere una coppia di certificati/chiavi nella `tls` sezione.

Per generare un certificato autofirmato e una chiave per il server dell'agente di orchestrazione, eseguire il comando seguente dall'interno della `/etc/maverics` Directory:

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> Per gli ambienti di produzione, è probabile che si voglia usare un certificato firmato da una CA nota per evitare gli avvisi nel browser. La [crittografia](https://letsencrypt.org/) è una soluzione efficace e gratuita se si sta cercando una CA attendibile.

A questo punto, usare il certificato e la chiave appena generati per l'agente di orchestrazione. Il file di configurazione dovrebbe ora contenere questo codice:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

Per verificare che TLS sia configurato come previsto, riavviare il servizio Maverics ed effettuare una richiesta all'endpoint di stato. Dal browser, richiedere https://sonar.maverics.com/status .

## <a name="step-2-proxy-an-application"></a>Passaggio 2: proxy di un'applicazione

Configurare quindi il proxy di base nell'agente di orchestrazione usando `appgateways` . Questo passaggio consente di verificare che l'agente di orchestrazione disponga della connettività necessaria all'applicazione protetta.

Il file di configurazione dovrebbe ora contenere questo codice:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com
```

Per verificare che l'inoltro funzioni come previsto, riavviare il servizio Maverics ed effettuare una richiesta all'applicazione tramite il proxy Maverics. Dal browser, richiedere https://sonar.maverics.com . Facoltativamente, è possibile effettuare una richiesta a risorse specifiche dell'applicazione, ad esempio, `https://sonar.maverics.com/RESOURCE` dove `RESOURCE` è una risorsa applicazione valida dell'app upstream protetta.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>Passaggio 3: registrare un'applicazione aziendale in Azure AD

A questo punto, creare una nuova applicazione aziendale in Azure AD che verrà usata per l'autenticazione degli utenti finali.

> [!NOTE]
> Quando si usano Azure AD funzionalità come l'accesso condizionale, è importante creare un'applicazione aziendale per ogni applicazione locale. Questo consente l'accesso condizionale per app, la valutazione del rischio per app, le autorizzazioni assegnate per ogni app e così via. In genere, un'applicazione aziendale in Azure AD viene mappata a un connettore di Azure in Maverics.

Per registrare un'applicazione aziendale in Azure AD:

1. Nel tenant di Azure AD passare ad **applicazioni aziendali** e quindi selezionare **nuova applicazione**. Nella raccolta di Azure AD cercare il connettore SAML dell'agente di **orchestrazione delle identità Maverics**, quindi selezionarlo.

1. Nel riquadro delle **Proprietà** del connettore SAML dell'agente di orchestrazione identità Maverics impostare **assegnazione utente obbligatoria?** su **No** per consentire l'utilizzo dell'applicazione per tutti gli utenti nella directory.

1. Nel riquadro **Panoramica** di Maverics Identity Orchestrator SAML Connector selezionare **Configura l'accesso Single Sign-On**  e quindi selezionare **SAML**.

1. Nel riquadro **SAML-based sign on** (Accesso basato su SAML) di Maverics Identity Orchestrator SAML Connector modificare **Configurazione SAML di base** selezionando il pulsante **Modifica** (icona a forma di matita).

   ![Screenshot del pulsante Modifica per "Configurazione SAML di base".](common/edit-urls.png)

1. Immettere un **ID entità** `https://sonar.maverics.com` . L'ID entità deve essere univoco tra le app nel tenant e può essere un valore arbitrario. Questo valore verrà usato quando si definisce il `samlEntityID` campo per il connettore di Azure nella sezione successiva.

1. Immettere un **URL di risposta** di `https://sonar.maverics.com/acs` . Questo valore verrà usato quando si definisce il `samlConsumerServiceURL` campo per il connettore di Azure nella sezione successiva.

1. Immettere un **URL di accesso** di `https://sonar.maverics.com/` . Questo campo non verrà usato da Maverics, ma è necessario in Azure AD per consentire agli utenti di ottenere l'accesso all'applicazione tramite il portale Azure AD app personali.

1. Selezionare **Salva**.

1. Nella sezione **certificato di firma SAML** selezionare il pulsante **copia** per copiare il valore dell' **URL dei metadati di Federazione dell'app** e quindi salvarlo nel computer.

   ![Screenshot del pulsante Copia per "Certificato di firma SAML".](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>Passaggio 4: eseguire l'autenticazione tramite Azure e autorizzare l'accesso all'applicazione

Quindi, inserire l'applicazione aziendale appena creata per usare configurando il connettore di Azure in Maverics. Questa `connectors` configurazione abbinata al `idps` blocco consente all'agente di orchestrazione di autenticare gli utenti.

Il file di configurazione dovrebbe ora contenere il codice seguente. Assicurarsi di sostituire `METADATA_URL` con il valore dell'URL dei metadati di Federazione dell'app nel passaggio precedente.

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Per verificare che l'autenticazione funzioni come previsto, riavviare il servizio Maverics ed effettuare una richiesta a una risorsa dell'applicazione tramite il proxy Maverics. Prima di accedere alla risorsa, è necessario essere reindirizzati ad Azure per l'autenticazione.

## <a name="step-5-add-headers-for-seamless-application-access"></a>Passaggio 5: aggiungere intestazioni per l'accesso trasparente alle applicazioni

Non si stanno ancora inviando intestazioni all'applicazione upstream. Si aggiungerà `headers` alla richiesta mentre passa attraverso il proxy Maverics per consentire all'applicazione upstream di identificare l'utente.

Il file di configurazione dovrebbe ora contenere questo codice:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Per verificare che l'autenticazione funzioni come previsto, effettuare una richiesta a una risorsa dell'applicazione tramite il proxy Maverics. L'applicazione protetta dovrebbe ora ricevere le intestazioni nella richiesta. 

È possibile modificare le chiavi di intestazione se l'applicazione prevede intestazioni diverse. Tutte le attestazioni restituite da Azure AD come parte del flusso SAML sono disponibili per l'uso nelle intestazioni. È ad esempio possibile includere un'altra intestazione di `secondary_email: azureSonarApp.email` , dove `azureSonarApp` è il nome del connettore ed `email` è un'attestazione restituita da Azure ad. 

## <a name="step-6-work-with-multiple-applications"></a>Passaggio 6: usare più applicazioni

Esaminiamo ora gli elementi necessari per eseguire il proxy per più applicazioni che si trovano in host diversi. Per ottenere questo passaggio, configurare un altro gateway app, un'altra applicazione aziendale in Azure AD e un altro connettore.

Il file di configurazione dovrebbe ora contenere questo codice:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp
  - name: azureConnectulumApp

appgateways:
  - name: sonar
    host: sonar.maverics.com
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

  - name: connectulum
    host: connectulum.maverics.com
    location: /
    # Replace https://app.connectulum.com with the address of your protected application
    upstream: https://app.connectulum.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureConnectulumApp.authenticated}}", "true"]

    headers:
      email: azureConnectulumApp.name
      firstname: azureConnectulumApp.givenname
      lastname: azureConnectulumApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com

  - name: azureConnectulumApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://connectulum.maverics.com/acs
    samlEntityID: https://connectulum.maverics.com
```

È possibile notare che il codice aggiunge un `host` campo alle definizioni del gateway applicazione. Il `host` campo consente all'agente di orchestrazione Maverics di distinguere tra l'host upstream e il traffico proxy.

Per verificare che il gateway app appena aggiunto funzioni come previsto, effettuare una richiesta a https://connectulum.maverics.com .

## <a name="advanced-scenarios"></a>Scenari avanzati

### <a name="identity-migration"></a>Migrazione di identità

Non è possibile mettere in funzione lo strumento di gestione dell'accesso al Web, ma non è possibile eseguire la migrazione degli utenti senza reimpostazioni della password di massa? L'agente di orchestrazione Maverics supporta la migrazione delle identità tramite `migrationgateways` .

### <a name="web-server-gateways"></a>Gateway server Web

Non si vuole rielaborare la rete e il traffico proxy tramite l'agente di orchestrazione Maverics? Non è un problema. L'agente di orchestrazione Maverics può essere associato a gateway server Web (moduli) per offrire le stesse soluzioni senza proxy.

## <a name="wrap-up"></a>Riepilogo

A questo punto, è stato installato l'agente di orchestrazione Maverics, è stata creata e configurata un'applicazione aziendale in Azure AD e l'agente di orchestrazione è stato configurato per il proxy in un'applicazione protetta, richiedendo l'autenticazione e applicando i criteri. Per altre informazioni su come usare l'agente di orchestrazione Maverics per i casi d'uso di gestione delle identità distribuite, [contattare strati](mailto:sales@strata.io).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)
