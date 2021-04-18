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
ms.openlocfilehash: 402f6cd6961108cdf1e9c94fb4f93309fbf15ead
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599027"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Integrare Azure AD Single Sign-On con Maverics Identity Orchestrator SAML Connector

Maverics Identity Orchestrator di Strata offre un modo semplice per integrare le applicazioni locali con Azure Active Directory (Azure AD) per l'autenticazione e il controllo di accesso. Maverics Orchestrator è in grado di modernizzare l'autenticazione e l'autorizzazione per le app che attualmente si basano su intestazioni, cookie e altri metodi di autenticazione proprietari. Le istanze di Maverics Orchestrator possono essere distribuite in locale o nel cloud. 

Questa esercitazione sull'accesso ibrido illustra come eseguire la migrazione di un'applicazione Web locale attualmente protetta da un prodotto legacy di gestione degli accessi Web per usare Azure AD per l'autenticazione e il controllo di accesso. I passaggi principali sono indicati di seguito.

1. Configurare Maverics Orchestrator
1. Proxy di un'applicazione
1. Registrare un'applicazione aziendale in Azure AD
1. Eseguire l'autenticazione tramite Azure e autorizzare l'accesso all'applicazione
1. Aggiungere intestazioni per un accesso trasparente alle applicazioni
1. Usare più applicazioni

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Maverics Identity Orchestrator SAML Connector abilitata per l'accesso SSO. Per ottenere il software Maverics, contattare [Le vendite di Strata.](mailto:sales@strata.io)
* Almeno un'applicazione che usa l'autenticazione basata su intestazione. Gli esempi funzionano con un'applicazione denominata Connectulum, ospitata in `https://app.connectulum.com` .
* Un computer Linux per ospitare Maverics Orchestrator
  * Sistema operativo: RHEL 7.7 o versione successiva, CentOS 7+
  * Disco: >= 10 GB
  * Memoria: >= 4 GB
  * Porte: 22 (SSH/SCP), 443, 7474
  * Accesso radice per le attività di installazione/amministrazione
  * Uscita di rete dal server che ospita Maverics Identity Orchestrator all'applicazione protetta

## <a name="step-1-set-up-the-maverics-orchestrator"></a>Passaggio 1: Configurare Maverics Orchestrator

### <a name="install-maverics"></a>Installare Maverics

1. Ottenere la versione più recente di Maverics RPM. Copiare il pacchetto nel sistema in cui si vuole installare il software Maverics.

1. Installare il pacchetto Maverics, sostituendo `maverics.rpm` con il nome del file personale.

   `sudo rpm -Uvf maverics.rpm`

   Dopo l'installazione, Maverics verrà eseguito come servizio in `systemd`. Per verificare che il servizio sia in esecuzione, eseguire il comando seguente:

   `sudo systemctl status maverics`

1. Per riavviare Orchestrator e seguire i log, è possibile eseguire il comando seguente:

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

Dopo aver installato Maverics, il file predefinito `maverics.yaml` viene creato nella directory `/etc/maverics` . Prima di modificare la configurazione per includere `appgateways` e , il file di configurazione sarà simile al seguente `connectors` z:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>Configurare DNS

DNS sarà utile in modo che non sia necessario ricordare l'INDIRIZZO IP del server Orchestrator.

Modificare il file host del computer browser (del computer portatile), usando un ipotetico IP di Orchestrator 12.34.56.78. Nei sistemi operativi basati su Linux questo file si trova in `/etc/hosts` . In Windows si trova in `C:\windows\system32\drivers\etc` .

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

Per verificare che DNS sia configurato come previsto, è possibile effettuare una richiesta all'endpoint di stato dell'agente di orchestrazione. Dal browser richiedere http://sonar.maverics.com:7474/status .

### <a name="configure-tls"></a>Configurare TLS

La comunicazione tramite canali sicuri per comunicare con Orchestrator è fondamentale per mantenere la sicurezza. A tale scopo, è possibile aggiungere una coppia di `tls` certificati/chiavi nella sezione .

Per generare un certificato autofirmato e una chiave per il server Orchestrator, eseguire il comando seguente dalla `/etc/maverics` directory :

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> Per gli ambienti di produzione, è probabile che si voglia usare un certificato firmato da una CA nota per evitare avvisi nel browser. [Crittografare è](https://letsencrypt.org/) un'opzione valida e gratuita se si sta cercando una CA attendibile.

Usare ora il certificato e la chiave appena generati per Orchestrator. Il file di configurazione dovrebbe ora contenere questo codice:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

Per verificare che TLS sia configurato come previsto, riavviare il servizio Maverics ed effettuare una richiesta all'endpoint di stato.

## <a name="step-2-proxy-an-application"></a>Passaggio 2: Proxy di un'applicazione

Configurare quindi il proxying di base in Orchestrator usando `appgateways` . Questo passaggio consente di verificare che Orchestrator abbia la connettività necessaria all'applicazione protetta.

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

Per verificare che il proxy funzioni come previsto, riavviare il servizio Maverics ed effettuare una richiesta all'applicazione tramite il proxy Maverics. Facoltativamente, è possibile effettuare una richiesta a risorse dell'applicazione specifiche.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>Passaggio 3: Registrare un'applicazione aziendale in Azure AD

A questo punto, creare una nuova applicazione aziendale in Azure AD che verrà usata per l'autenticazione degli utenti finali.

> [!NOTE]
> Quando si usano Azure AD come l'accesso condizionale, è importante creare un'applicazione aziendale per ogni applicazione locale. Ciò consente l'accesso condizionale per app, la valutazione del rischio per app, le autorizzazioni assegnate per app e così via. In genere, un'applicazione aziendale in Azure AD viene mappata a un connettore di Azure in Maverics.

Per registrare un'applicazione aziendale in Azure AD:

1. Nel tenant Azure AD, passare **ad Applicazioni aziendali** e quindi selezionare Nuova **applicazione.** Nella raccolta Azure AD cercare **Maverics Identity Orchestrator SAML Connector** e quindi selezionarlo.

1. Nel riquadro Maverics Identity Orchestrator SAML Connector **Properties** (Proprietà del connettore SAML di Maverics Identity Orchestrator) impostare Assegnazione utente **obbligatoria?** su **No** per consentire all'applicazione di funzionare per tutti gli utenti nella directory.

1. Nel riquadro **Panoramica** di Maverics Identity Orchestrator SAML Connector selezionare **Configura l'accesso Single Sign-On**  e quindi selezionare **SAML**.

1. Nel riquadro **SAML-based sign on** (Accesso basato su SAML) di Maverics Identity Orchestrator SAML Connector modificare **Configurazione SAML di base** selezionando il pulsante **Modifica** (icona a forma di matita).

   ![Screenshot del pulsante Modifica per "Configurazione SAML di base".](common/edit-urls.png)

1. Immettere **l'ID entità** `https://sonar.maverics.com` . L'ID entità deve essere univoco tra le app nel tenant e può essere un valore arbitrario. Questo valore verrà utilizzato quando si definirà il `samlEntityID` campo per il connettore Azure nella sezione successiva.

1. Immettere **l'URL di risposta** `https://sonar.maverics.com/acs` . Questo valore verrà utilizzato quando si definisce il `samlConsumerServiceURL` campo per il connettore di Azure nella sezione successiva.

1. Immettere un **URL di accesso** di `https://sonar.maverics.com/` . Questo campo non verrà usato da Maverics, ma è necessario in Azure AD per consentire agli utenti di accedere all'applicazione tramite il portale Azure AD App personali.

1. Selezionare **Salva**.

1. Nella sezione Certificato di firma  **SAML** selezionare il pulsante Copia per copiare il valore di **URL** metadati federazione app e quindi salvarlo nel computer.

   ![Screenshot del pulsante Copia per "Certificato di firma SAML".](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>Passaggio 4: Eseguire l'autenticazione tramite Azure e autorizzare l'accesso all'applicazione

Inserire quindi l'applicazione aziendale appena creata per l'uso configurando il connettore di Azure in Maverics. Questa `connectors` configurazione abbinata al blocco `idps` consente all'agente di orchestrazione di autenticare gli utenti.

Il file di configurazione dovrebbe ora contenere il codice seguente. Assicurarsi di sostituire con il valore dell'URL dei metadati di federazione `METADATA_URL` dell'app del passaggio precedente.

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

Per verificare che l'autenticazione funzioni come previsto, riavviare il servizio Maverics ed effettuare una richiesta a una risorsa applicazione tramite il proxy Maverics. Prima di accedere alla risorsa, è necessario essere reindirizzati ad Azure per l'autenticazione.

## <a name="step-5-add-headers-for-seamless-application-access"></a>Passaggio 5: Aggiungere intestazioni per un accesso facile alle applicazioni

Non si inviano ancora intestazioni all'applicazione upstream. Si aggiungerà alla richiesta mentre passa attraverso il proxy Maverics per consentire `headers` all'applicazione upstream di identificare l'utente.

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

Per verificare che l'autenticazione funzioni come previsto, effettuare una richiesta a una risorsa applicazione tramite il proxy Maverics. L'applicazione protetta dovrebbe ora ricevere intestazioni nella richiesta. 

È possibile modificare le chiavi di intestazione se l'applicazione prevede intestazioni diverse. Tutte le attestazioni che vengono Azure AD come parte del flusso SAML sono disponibili per l'uso nelle intestazioni. Ad esempio, è possibile includere un'altra intestazione di , dove è il nome del `secondary_email: azureSonarApp.email` `azureSonarApp` connettore e `email` è un'attestazione restituita da Azure AD. 

## <a name="step-6-work-with-multiple-applications"></a>Passaggio 6: Usare più applicazioni

Si esaminino ora gli elementi necessari per eseguire il proxy a più applicazioni in host diversi. Per eseguire questo passaggio, configurare un altro gateway app, un'altra applicazione aziendale in Azure AD e un altro connettore.

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

Si sarà notato che il codice aggiunge un `host` campo alle definizioni del gateway app. Il `host` campo consente a Maverics Orchestrator di distinguere l'host upstream a cui eseguire il proxy del traffico.

Per verificare che il gateway app appena aggiunto funzioni come previsto, effettuare una richiesta a `https://connectulum.maverics.com` .

## <a name="advanced-scenarios"></a>Scenari avanzati

### <a name="identity-migration"></a>Migrazione delle identità

Non è possibile usare lo strumento di gestione degli accessi Web di fine vita, ma non è possibile eseguire la migrazione degli utenti senza reimpostazioni di massa delle password? Maverics Orchestrator supporta la migrazione delle identità tramite `migrationgateways` .

### <a name="web-server-gateways"></a>Gateway server Web

Non si vuole rielaborare il traffico di rete e proxy tramite Maverics Orchestrator? Non è un problema. Maverics Orchestrator può essere associato a gateway server Web (moduli) per offrire le stesse soluzioni senza proxy.

## <a name="wrap-up"></a>Conclusione

A questo punto, è stato installato Maverics Orchestrator, è stata creata e configurata un'applicazione aziendale in Azure AD ed è stato configurato Orchestrator per il proxy per un'applicazione protetta, richiedendo al tempo stesso l'autenticazione e l'applicazione dei criteri. Per altre informazioni su come usare Maverics Orchestrator per i casi d'uso della gestione delle identità distribuite, [contattare Strata.](mailto:sales@strata.io)

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)
