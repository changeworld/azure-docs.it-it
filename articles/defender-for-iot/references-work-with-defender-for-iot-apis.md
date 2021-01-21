---
title: Usare le API di Defender per IoT
description: Usare un'API REST esterna per accedere ai dati individuati da sensori e console di gestione ed eseguire azioni con tali dati.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: reference
ms.service: azure
ms.openlocfilehash: 44ea6e8343203a9cb18947f31f45aa0b023178b0
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624575"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>Defender per le API del sensore e della console di gestione

Usare un'API REST esterna per accedere ai dati individuati da sensori e console di gestione ed eseguire azioni con tali dati.

Le connessioni sono protette tramite SSL.

## <a name="getting-started"></a>Introduzione

In generale, quando si usa un'API esterna nel sensore di Azure Defender per l'it o nella console di gestione locale, è necessario generare un token di accesso. I token non sono necessari per le API di autenticazione usate nel sensore e nella console di gestione locale.

Per generare un token:

1. Nella finestra **Impostazioni sistema** selezionare token di **accesso**.
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="Screenshot delle impostazioni di sistema Windows evidenziando il pulsante token di accesso.":::

2. Selezionare **Genera nuovo token**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Selezionare il pulsante per generare un nuovo token.":::

3. Descrivere lo scopo del nuovo token e selezionare **Avanti**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Generare un nuovo token e immettere il nome dell'integrazione ad esso associato.":::

4. Viene visualizzato il token di accesso. Copiarlo perché non verrà più visualizzato.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Copiare il token di accesso per l'integrazione.":::

5. Selezionare **Fine**. I token creati vengono visualizzati nella finestra di dialogo **token di accesso** .
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="Screenshot della finestra di dialogo token dispositivo con token di riempimento":::

   **Usato** indica l'ultima volta che è stata ricevuta una chiamata esterna con questo token.

   Se **N/A** viene visualizzato nel campo **utilizzato** per questo token, la connessione tra il sensore e il server connesso non funziona.

6. Aggiungere un'intestazione HTTP denominata **authorization** alla richiesta e impostarne il valore sul token generato.

## <a name="sensor-api-specifications"></a>Specifiche dell'API del sensore

Questa sezione descrive le API del sensore seguenti:

- /api/v1/devices

- /api/v1/devices/connections

- /api/v1/devices/cves

- /api/v1/alerts

- /api/v1/events

- /api/v1/reports/vulnerabilities/devices

- /api/v1/reports/vulnerabilities/security

- /api/v1/reports/vulnerabilities/operational

- /api/external/authentication/validation

- set_password/External/Authentication/

- set_password_by_admin/External/Authentication/

### <a name="retrieve-device-information"></a>Recuperare le informazioni sul dispositivo

Usare questa API per richiedere un elenco di tutti i dispositivi rilevati da Defender per il sensore Internet.

#### <a name="apiv1devices"></a>/api/v1/devices

#### <a name="method"></a>Metodo

**GET**

Richiede un elenco di tutti i dispositivi rilevati da Defender per il sensore Internet.

#### <a name="query-parameters"></a>Parametri di query

- **autorizzato**: per filtrare solo i dispositivi autorizzati e non autorizzati.

  **Esempi**:

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano i dispositivi.

#### <a name="device-fields"></a>Campi dispositivo

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **id** | Numeric | No | - |
| **ipAddresses** | Matrice JSON | Sì | Indirizzi IP (può essere più di un indirizzo in caso di indirizzi Internet o un dispositivo con schede di rete doppie) |
| **nome** | string | No | - |
| **type** | string | No | Unknown, stazione ingegneristica, PLC, HMI, storico, controller di dominio, server di database, punto di accesso wireless, router, Switch, server, workstation, videocamera IP, stampante, firewall, stazione terminale, gateway VPN, Internet o multicast e broadcast |
| **macAddresses** | Matrice JSON | Sì | Indirizzi MAC (può essere più di un indirizzo nel caso di un dispositivo con schede di rete doppie) |
| **operatingSystem** | string | Sì | - |
| **engineeringStation** | Boolean | No | True o false |
| **scanner** | Boolean | No | True o false |
| **autorizzato** | Boolean | No | True o false |
| **fornitore** | string | Sì | - |
| **protocolli** | Matrice JSON | Sì | Oggetto protocollo |
| **firmware** | Matrice JSON | Sì | Oggetto firmware |

#### <a name="protocol-fields"></a>Campi di protocollo

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **Nome** | string | No |  |
| **Indirizzi** | Matrice JSON | Sì | Valori master o numerici |

#### <a name="firmware-fields"></a>Campi del firmware

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **serie** | string | No | N/A oppure il valore effettivo |
| **model** | string | No | N/A oppure il valore effettivo |
| **firmwareVersion** | Double | No | N/A oppure il valore effettivo |
| **additionalData** | string | No | N/A oppure il valore effettivo |
| **moduleAddress** | string | No | N/A oppure il valore effettivo |
| **rack** | string | No | N/A oppure il valore effettivo |
| **slot** | string | No | N/A oppure il valore effettivo |
| **address** | string | No | N/A oppure il valore effettivo |

#### <a name="response-example"></a>Esempio di risposta

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

### <a name="retrieve-device-connection-information"></a>Recuperare le informazioni di connessione del dispositivo

Usare questa API per richiedere un elenco di tutte le connessioni per ogni dispositivo.

#### <a name="apiv1devicesconnections"></a>/api/v1/devices/connections

#### <a name="method"></a>Metodo

**GET**

#### <a name="query-parameters"></a>Parametri di query

Se non si impostano i parametri della query, vengono restituite tutte le connessioni del dispositivo.

**Esempio**:

`/api/v1/devices/connections`

- **DeviceID**: filtrare in base a un ID dispositivo specifico per visualizzare le relative connessioni.

  **Esempio**:

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes**: intervallo di tempo da ora indietro, per minuto, durante il quale le connessioni erano attive.

  **Esempio**:

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore**: filtra solo le connessioni rilevate prima di un'ora specifica (in millisecondi, UTC).

  **Esempio**:

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter**: filtra solo le connessioni rilevate dopo un periodo di tempo specifico (in millisecondi, UTC).

  **Esempio**:

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano le connessioni del dispositivo.

#### <a name="fields"></a>Campi

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **firstDeviceId** | Numeric | No | - |
| **secondDeviceId** | Numeric | No | - |
| **lastSeen** | Numeric | No | Epoch (UTC) |
| **individuati** | Numeric | No | Epoch (UTC) |
| **porte** | Matrice di numeri | No | - |
| **protocolli** | Matrice JSON | No | Campo protocollo |

#### <a name="protocol-field"></a>Campo protocollo

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **nome** | string | No | - |
| **comandi** | Matrice di stringhe | No | - |

#### <a name="response-example"></a>Esempio di risposta

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

### <a name="retrieve-information-on-cves"></a>Recuperare informazioni su CVEs

Usare questa API per richiedere un elenco di tutti i CVEs noti individuati nei dispositivi della rete.

#### <a name="apiv1devicescves"></a>/api/v1/devices/cves

#### <a name="method"></a>Metodo

**GET**

#### <a name="query-parameters"></a>Parametri di query

Per impostazione predefinita, questa API fornisce l'elenco di tutti gli IP del dispositivo con CVEs, fino a 100 CVEs con punteggio superiore per ogni indirizzo IP.

**Esempio**:

`/api/v1/devices/cves`

- **DeviceID**: filtrare in base a un indirizzo IP del dispositivo specifico, per ottenere un massimo di 100 CVES con punteggio superiore identificato nel dispositivo specifico.

  **Esempio**:

  `/api/v1/devices/<ipAddress>/cves`

- **Top**: numero di CVES con punteggio superiore da recuperare per ogni indirizzo IP del dispositivo.

  **Esempio**:

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano CVEs identificati sugli indirizzi IP.

#### <a name="fields"></a>Campi

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **cveId** | string | No | - |
| **ipAddress** | string | No | Indirizzo IP |
| **Punteggio** | string | No | 0,0-10,0 |
| **attackVector** | string | No | Rete, rete adiacente, locale o fisica |
| **description** | string | No | - |

#### <a name="response-example"></a>Esempio di risposta

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

### <a name="retrieve-alert-information"></a>Recuperare le informazioni sugli avvisi

Usare questa API per richiedere un elenco di tutti gli avvisi rilevati da Defender per il sensore Internet.

#### <a name="apiv1alerts"></a>/api/v1/alerts

#### <a name="method"></a>Metodo

**GET**

#### <a name="query-parameters"></a>Parametri di query

- **stato**: per filtrare solo gli avvisi gestiti o non gestiti.

  **Esempio**:

  `/api/v1/alerts?state=handled`

- **fromTime**: consente di filtrare gli avvisi creati da un orario specifico (in millisecondi, UTC).

  **Esempio**:

  `/api/v1/alerts?fromTime=<epoch>`

- **ToTime**: per filtrare gli avvisi creati solo prima di un periodo di tempo specifico (in millisecondi, UTC).

  **Esempio**:

  `/api/v1/alerts?toTime=<epoch>`

- **tipo**: per filtrare gli avvisi in base a un tipo specifico. Tipi esistenti in base ai quali filtrare: nuovi dispositivi, disconnessioni imprevisti.

  **Esempio**:

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano gli avvisi.

#### <a name="alert-fields"></a>Campi avviso

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **ID** | Numeric | No | - |
| **time** | Numeric | No | Epoch (UTC) |
| **title** | string | No | - |
| **message** | string | No | - |
| **severity** | string | No | Avviso, secondario, principale o critico |
| **motore** | string | No | Violazione del protocollo, violazione dei criteri, malware, anomalie o funzionamento |
| **sourceDevice** | Numeric | Sì | ID dispositivo |
| **destinationDevice** | Numeric | Sì | ID dispositivo |
| **additionalInformation** | Oggetto informazioni aggiuntive | Sì | - |

#### <a name="additional-information-fields"></a>Campi informazioni aggiuntive

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **description** | string | No | - |
| **informazioni** | Matrice JSON | No | string |

#### <a name="response-example"></a>Esempio di risposta

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

### <a name="retrieve-timeline-events"></a>Recuperare gli eventi della sequenza temporale

Usare questa API per richiedere un elenco di eventi segnalati alla sequenza temporale dell'evento.

#### <a name="apiv1events"></a>/api/v1/events

#### <a name="method"></a>Metodo

**GET**

#### <a name="query-parameters"></a>Parametri di query

- **minutesTimeFrame**: intervallo di tempo a partire da ora indietro, per minuto, in cui sono stati segnalati gli eventi.

  **Esempio**:

  `/api/v1/events?minutesTimeFrame=20`

- **tipo**: per filtrare l'elenco di eventi in base a un tipo specifico.

  **Esempi**:

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano gli avvisi.

#### <a name="event-fields"></a>Campi dell'evento

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|--|
| **timestamp** | Numeric | No | Epoch (UTC) |
| **title** | string | No | - |
| **severity** | string | No | INFORMAZIONI, avviso o avviso |
| **proprietario** | string | Sì | Se l'evento è stato creato manualmente, questo campo includerà il nome utente che ha creato l'evento |
| **content** | string | No | - |

#### <a name="response-example"></a>Esempio di risposta

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

### <a name="retrieve-vulnerability-information"></a>Recuperare le informazioni sulla vulnerabilità

Usare questa API per richiedere i risultati della valutazione della vulnerabilità per ogni dispositivo.

#### <a name="apiv1reportsvulnerabilitiesdevices"></a>/api/v1/reports/vulnerabilities/devices

#### <a name="method"></a>Metodo

**GET**

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano i dispositivi valutati.

L'oggetto dispositivo contiene:

- Dati generali

- Punteggio valutazione

- Vulnerabilità

#### <a name="device-fields"></a>Campi dispositivo

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **nome** | string | No | - |
| **ipAddresses** | Matrice JSON | No | - |
| **securityScore** | Numeric | No | - |
| **fornitore** | string | Sì |  |
| **firmwareVersion** | string | Sì | - |
| **model** | string | Sì | - |
| **isWirelessAccessPoint** | Boolean | No | True o false |
| **operatingSystem** | Oggetto sistema operativo | Sì | - |
| **vulnerabilità** | Oggetto vulnerabilità | Sì | - |

#### <a name="operating-system-fields"></a>Campi del sistema operativo

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **Nome** | string | Sì | - |
| **Tipo** | string | Sì | - |
| **Versione** | string | Sì | - |
| **latestVersion** | string | Sì | - |

#### <a name="vulnerabilities-fields"></a>Campi vulnerabilità
 
| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **Antivirus** | Matrice JSON | Sì | Nomi antivirus |
| **plainTextPasswords** | Matrice JSON | Sì | Oggetti password |
| **remoteAccess** | Matrice JSON | Sì | Oggetti di accesso remoto |
| **isBackupServer** | Boolean | No | True o false |
| **openedPorts** | Matrice JSON | Sì | Oggetti porta aperti |
| **isEngineeringStation** | Boolean | No | True o false |
| **isKnownScanner** | Boolean | No | True o false |
| **cves** | Matrice JSON | Sì | Oggetti CVE |
| **isUnauthorized** | Boolean | No | True o false |
| **malwareIndicationsDetected** | Boolean | No | True o false |
| **weakAuthentication** | Matrice JSON | Sì | Applicazioni rilevate che usano l'autenticazione vulnerabile |

#### <a name="password-fields"></a>Campi password

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **password** | string | No | - |
| **protocol** | string | No | - |
| **forza** | string | No | Debole, debole, medio o forte |

#### <a name="remote-access-fields"></a>Campi di accesso remoto

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **port** | Numeric | No | - |
| **trasporto** | string | No | TCP o UDP |
| **client** | string | No | Indirizzo IP |
| **clientSoftware** | string | No | SSH, VNC, desktop remoto o Team Viewer |

#### <a name="open-port-fields"></a>Apri campi porta

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **port** | Numeric | No | - |
| **trasporto** | string | No | TCP o UDP |
| **protocol** | string | Sì | - |
| **isConflictingWithFirewall** | Boolean | No | True o false |

#### <a name="cve-fields"></a>Campi CVE

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **ID** | string | No | - |
| **Punteggio** | Numeric | No | Double |
| **description** | string | No | - |

#### <a name="response-example"></a>Esempio di risposta

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]

```

### <a name="retrieve-security-vulnerabilities"></a>Recuperare le vulnerabilità della sicurezza

Usare questa API per richiedere i risultati di una valutazione della vulnerabilità generale. Questa valutazione fornisce informazioni approfondite sul livello di sicurezza del sistema.

Questa valutazione si basa su informazioni generali sulla rete e sul sistema e non su una valutazione specifica del dispositivo.

#### <a name="apiv1reportsvulnerabilitiessecurity"></a>/api/v1/reports/vulnerabilities/security

#### <a name="method"></a>Metodo

**GET**

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Oggetto JSON che rappresenta i risultati valutati. Ogni chiave può ammettere i valori null. In caso contrario, conterrà un oggetto JSON con chiavi non nullable.

### <a name="result-fields"></a>Campi risultato

**Chiavi**

**unauthorizedDevices**

| Nome del campo | Type | Elenco di valori |
| ---------- | ---- | -------------- |
| **address** | string | Indirizzo IP |
| **nome** | string | - |
| **firstDetectionTime** | Numeric | Epoch (UTC) |
| lastSeen | Numeric | Epoch (UTC) |

**illegalTrafficByFirewallRules**

| Nome del campo | Type | Elenco di valori |
| ---------- | ---- | -------------- |
| **server** | string | Indirizzo IP |
| **client** | string | Indirizzo IP |
| **port** | Numeric | - |
| **trasporto** | string | TCP, UDP o ICMP |

**weakFirewallRules**

| Nome del campo | Type | Elenco di valori |
| ---------- | ---- | -------------- |
| **sources** | Matrice JSON di origini. Ogni origine può essere in uno dei quattro formati. | "Any", "indirizzo IP (host)", "da IP a IP (intervallo)", "indirizzo IP, subnet mask (rete)" |
| **destinazioni** | Matrice JSON di destinazioni. Ogni destinazione può essere in uno dei quattro formati. | "Any", "indirizzo IP (host)", "da IP a IP (intervallo)", "indirizzo IP, subnet mask (rete)" |
| **porte** | Matrice JSON di porte in uno dei tre formati | "Qualsiasi", "porta (protocollo, se rilevato)", "da porta a porta (protocollo, se rilevato)" |

**Accesso**

| Nome del campo | Type | Elenco di valori |
| ---------- | ---- | -------------- |
| **macAddress** | string | Indirizzo MAC |
| **fornitore** | string | Nome del fornitore |
| **ipAddress** | string | Indirizzo IP o N/A |
| **nome** | string | Nome dispositivo o N/A |
| **wireless** | string | No, sospetto o Sì |

**connectionsBetweenSubnets**

| Nome del campo | Type | Elenco di valori |
| ---------- | ---- | -------------- |
| **server** | string | Indirizzo IP |
| **client** | string | Indirizzo IP |

**industrialMalwareIndicators**

| Nome del campo | Type | Elenco di valori |
| ---------- | ---- | -------------- |
| **detectionTime** | Numeric | Epoch (UTC) |
| **alertMessage** | string | - |
| **description** | string | - |
| **dispositivi** | Matrice JSON | Nomi dei dispositivi | 

**internetConnections**

| Nome del campo | Type | Elenco di valori |
| ---------- | ---- | -------------- |
| **internalAddress** | string | Indirizzo IP |
| **autorizzato** | Boolean | Sì o No | 
| **externalAddresses** | Matrice JSON | Indirizzo IP |

#### <a name="response-example"></a>Esempio di risposta

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}

```

### <a name="retrieve-operational-vulnerabilities"></a>Recuperare le vulnerabilità operative

Usare questa API per richiedere i risultati di una valutazione della vulnerabilità generale. Questa valutazione fornisce informazioni dettagliate sullo stato operativo della rete. Si basa su informazioni generali sulla rete e sul sistema e non sulla valutazione di un dispositivo specifico.

#### <a name="apiv1reportsvulnerabilitiesoperational"></a>/api/v1/reports/vulnerabilities/operational

#### <a name="method"></a>Metodo

**GET**

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Oggetto JSON che rappresenta i risultati valutati. Ogni chiave contiene una matrice JSON di risultati.

#### <a name="result-fields"></a>Campi risultato

**Chiavi**

**backupServer**

| Nome del campo | Type | Elenco di valori |
|--|--|--|
| **source** | string | Indirizzo IP |
| **destinazione** | string | Indirizzo IP |
| **port** | Numeric | - |
| **trasporto** | string | TCP o UDP |
| **backupMaximalInterval** | string | - |
| **lastSeenBackup** | Numeric | Epoch (UTC) |

**ipNetworks**

| Nome del campo | Type | Elenco di valori |
|--|--|--|
| **Indirizzo** s | Numeric | - |
| **rete** | string | Indirizzo IP |
| **maschera** | string | Subnet mask |

**protocolProblems**

| Nome del campo | Type | Elenco di valori |
|--|--|--|
| **protocol** | string | - |
| **indirizzi** | Matrice JSON | Indirizzi IP |
| **avviso** | string | - |
| **reportTime** | Numeric | Epoch (UTC) |

**protocolDataVolumes**

| Nome del campo | Type | Elenco di valori |
|--|--|--|
| protocol | string | - |
| volume | string | "volume numero MB" |

**disconnessioni**

| Nome del campo | Type | Elenco di valori |
|--|--|--|
| **assetAddress** | string | Indirizzo IP |
| **assetName** | string | - |
| **lastDetectionTime** | Numeric | Epoch (UTC) |
| **backToNormalTime** | Numeric | Epoch (UTC) |     

#### <a name="response-example"></a>Esempio di risposta

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}

```

### <a name="validate-user-credentials"></a>Convalida credenziali utente

Usare questa API per convalidare un Defender per nome utente e password. Per tutti i ruoli utente Defender è possibile usare l'API.

Per usare questa API, non è necessario un Defender per il token di accesso.

#### <a name="apiexternalauthenticationvalidation"></a>/api/external/authentication/validation

#### <a name="method"></a>Metodo

**POST**

#### <a name="request-type"></a>Tipo di richiesta

**JSON**

#### <a name="query-parameters"></a>Parametri di query

| **Nome** | **Tipo** | **Ammette i valori Null** |
|--|--|--|
| **username** | string | No |
| **password** | string | No |

#### <a name="request-example"></a>Esempio di richiesta

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Stringa di messaggio con i dettagli sullo stato dell'operazione:

- **Operazione riuscita-messaggio**: autenticazione riuscita

- **Errore**: convalida delle credenziali non riuscita

#### <a name="response-example"></a>Esempio di risposta

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

### <a name="change-password"></a>Cambia password

Usare questa API per consentire agli utenti di modificare le proprie password. Per tutti i ruoli utente Defender è possibile usare l'API. Per usare questa API, non è necessario un Defender per il token di accesso.

#### <a name="externalauthenticationset_password"></a>set_password/External/Authentication/

#### <a name="method"></a>Metodo

**POST**

#### <a name="request-type"></a>Tipo di richiesta

**JSON**

#### <a name="request-example"></a>Esempio di richiesta

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Stringa di messaggio con i dettagli sullo stato dell'operazione:

- **Operazione riuscita-messaggio**: la password è stata sostituita

- Errore **-errore**: errore di autenticazione utente

- Errore **-errore**: la password non corrisponde ai criteri di sicurezza

#### <a name="response-example"></a>Esempio di risposta

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Campi dispositivo

| **Nome** | **Tipo** | **Ammette i valori Null** |
|--|--|--|
| **username** | string | No |
| **password** | string | No |
| **new_password** | string | No |

### <a name="user-password-update-by-system-admin"></a>Aggiornamento della password utente dall'amministratore di sistema

Usare questa API per consentire agli amministratori di sistema di modificare le password per gli utenti specificati. Defender per i ruoli utente amministratore di Internet delle cose può funzionare con l'API. Per usare questa API, non è necessario un Defender per il token di accesso.

#### <a name="externalauthenticationset_password_by_admin"></a>set_password_by_admin/External/Authentication/

#### <a name="method"></a>Metodo

**POST**

#### <a name="request-type"></a>Tipo di richiesta

**JSON**

#### <a name="request-example"></a>Esempio di richiesta

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Stringa di messaggio con i dettagli sullo stato dell'operazione:

- **Operazione riuscita-messaggio**: la password è stata sostituita

- Errore **-errore**: errore di autenticazione utente

- Errore **-errore**: l'utente non esiste

- Errore **-errore**: la password non corrisponde ai criteri di sicurezza

- Errore **-errore**: l'utente non dispone delle autorizzazioni per modificare la password

#### <a name="response-example"></a>Esempio di risposta

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Campi dispositivo

| **Nome** | **Tipo** | **Ammette i valori Null** |
|--|--|--|
| **admin_username** | string | No |
| **admin_password** | string | No |
| **username** | string | No |
| **new_password** | string | No |

## <a name="on-premises-management-console-api-specifications"></a>Specifiche API della console di gestione locale

In questa sezione vengono descritte le API della console di gestione locale seguenti:

- **/external/v1/alerts/<UUID>**

- **Esclusioni avvisi (finestra di manutenzione)**

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Finestra di esclusione avviso che mostra le regole attive.":::

Definire le condizioni in base alle quali non verranno inviati gli avvisi. Ad esempio, definire e aggiornare l'ora di arresto e di inizio, i dispositivi o le subnet che devono essere esclusi quando si attivano gli avvisi o Defender per i motori Internet che devono essere esclusi. Ad esempio, durante una finestra di manutenzione, potrebbe essere necessario interrompere il recapito di tutti gli avvisi, tranne che per gli avvisi malware nei dispositivi critici.

Le API definite qui vengono visualizzate nella finestra **esclusioni avvisi** della console di gestione locale come regola di esclusione di sola lettura.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **Esempio di risposta**

- **risposta**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password"></a>Cambia password

Usare questa API per consentire agli utenti di modificare le proprie password. Per tutti i ruoli utente Defender è possibile usare l'API. Per usare questa API, non è necessario un Defender per il token di accesso.

- **set_password/External/Authentication/**

#### <a name="user-password-update-by-system-admin"></a>Aggiornamento della password utente dall'amministratore di sistema

Usare questa API per consentire agli amministratori di sistema di modificare le password per utenti specifici. Defender per i ruoli utente amministratore di Internet delle cose può funzionare con l'API. Per usare questa API, non è necessario un Defender per il token di accesso.

- **set_password_by_admin/External/Authentication/**

### <a name="retrieve-device-information"></a>Recuperare le informazioni sul dispositivo

Questa API richiede un elenco di tutti i dispositivi rilevati da Defender per i sensori Internet che sono connessi a una console di gestione locale.

- **/external/v1/devices**

#### <a name="method"></a>Metodo

**GET**

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano i dispositivi.

#### <a name="query-parameters"></a>Parametri di query

- **autorizzato**: per filtrare solo i dispositivi autorizzati e non autorizzati.

- **siteId**: per filtrare solo i dispositivi correlati a siti specifici.

- **IDArea**: per filtrare solo i dispositivi correlati a zone specifiche. [1](#1)

- **sensorId**: per filtrare solo i dispositivi rilevati da sensori specifici. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *è possibile che non si disponga dell'ID del sito e della zona. In tal caso, eseguire una query su tutti i dispositivi per recuperare il sito e l'ID della zona.*

#### <a name="query-parameters-example"></a>Esempio di parametri di query

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Campi dispositivo

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **sensorId** | Numeric | No | - |
| **IDArea** | Numeric | Sì | - |
| **siteId** | Numeric | Sì | - |
| **ipAddresses** | Matrice JSON | Sì | Indirizzi IP (può essere più di un indirizzo in caso di indirizzi Internet o un dispositivo con schede di rete doppie) |
| **nome** | string | No | - |
| **type** | string | No | Unknown, stazione ingegneristica, PLC, HMI, storico, controller di dominio, server di database, punto di accesso wireless, router, Switch, server, workstation, videocamera IP, stampante, firewall, stazione terminale, gateway VPN, Internet o multicast e broadcast |
| **macAddresses** | Matrice JSON | Sì | Indirizzi MAC (può essere più di un indirizzo nel caso di un dispositivo con schede di rete doppie) |
| **operatingSystem** | string | Sì | - |
| **engineeringStation** | Boolean | No | True o false |
| **scanner** | Boolean | No | True o false |
| **autorizzato** | Boolean | No | True o false |
| **fornitore** | string | Sì | - |
| **Protocolli** | Matrice JSON | Sì | Oggetto protocollo |
| **firmware** | Matrice JSON | Sì | Oggetto firmware |

#### <a name="protocol-fields"></a>Campi di protocollo

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| Nome | string | No | - |
| Indirizzi | Matrice JSON | Sì | Valori master o numerici |

#### <a name="firmware-fields"></a>Campi del firmware

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **serie** | string | No | N/A oppure il valore effettivo |
| **model** | string | No | N/A oppure il valore effettivo |
| **firmwareVersion** | Double | No | N/A oppure il valore effettivo |
| **additionalData** | string | No | N/A oppure il valore effettivo |
| **moduleAddress** | string | No | N/A oppure il valore effettivo |
| **rack** | string | No | N/A oppure il valore effettivo |
| **slot** | string | No | N/A oppure il valore effettivo |
| **address** | string | No | N/A oppure il valore effettivo |

#### <a name="response-example"></a>Esempio di risposta

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

### <a name="retrieve-alert-information"></a>Recuperare le informazioni sugli avvisi

Usare questa API per recuperare tutti gli avvisi o filtrati da una console di gestione locale.

#### <a name="externalv1alerts"></a>/external/v1/alerts

#### <a name="method"></a>Metodo

**GET**

#### <a name="query-parameters"></a>Parametri di query

- **stato**: per filtrare solo gli avvisi gestiti e non gestiti.

  **Esempio**:

  `/api/v1/alerts?state=handled`

- **fromTime**: consente di filtrare gli avvisi creati da un orario specifico (in millisecondi, UTC).

  **Esempio**:

  `/api/v1/alerts?fromTime=<epoch>`

- **ToTime**: per filtrare gli avvisi creati solo prima di un periodo di tempo specifico (in millisecondi, UTC).

  **Esempio**:

  `/api/v1/alerts?toTime=<epoch>`

- **siteId**: il sito in cui è stato individuato l'avviso. [2](#2)

- **IDArea**: area in cui è stato individuato l'avviso. [2](#2)

- **sensore**: il sensore sul quale è stato individuato l'avviso.

##### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="2">2</a> *è possibile che non si disponga dell'ID del sito e della zona. In tal caso, eseguire una query su tutti i dispositivi per recuperare il sito e l'ID della zona.*

#### <a name="alert-fields"></a>Campi avviso

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **ID** | Numeric | No | - |
| **time** | Numeric | No | Epoch (UTC) |
| **title** | string | No | - |
| **message** | string | No | - |
| **severity** | string | No | Avviso, secondario, principale o critico |
| **motore** | string | No | Violazione del protocollo, violazione dei criteri, malware, anomalie o funzionamento |
| **sourceDevice** | Numeric | Sì | ID dispositivo |
| **destinationDevice** | Numeric | Sì | ID dispositivo |
| **additionalInformation** | Oggetto informazioni aggiuntive | Sì | - |

#### <a name="additional-information-fields"></a>Campi informazioni aggiuntive

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **description** | string | No | - |
| **informazioni** | Matrice JSON | No | string |

#### <a name="response-example"></a>Esempio di risposta

```rest
[

    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Traffic Detected on sensor Interface",
        
        "additionalInformation": null,
        
        "sourceDevice": 0,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808245000,
        
        "sensorId": 1,
        
        "message": "The sensor resumed detecting network traffic on ens224.",
        
        "destinationDevice": 0,
        
        "id": 1,
        
        "severity": "Warning"
    
    },
    
    {
    
        "engine": "Anomaly",
        
        "handled": false,
        
        "title": "Address Scan Detected",
        
        "additionalInformation": null,
        
        "sourceDevice": 4,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808260000,
        
        "sensorId": 1,
        
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        
        "destinationDevice": 0,
        
        "id": 2,
        
        "severity": "Critical"
    
    },
    
    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Suspicion of Unresponsive MODBUS Device",
        
        "additionalInformation": null,
        
        "sourceDevice": 194,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808285000,
        
        "sensorId": 1,
        
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        
        "destinationDevice": 0,
        
        "id": 3,
        
        "severity": "Minor"
    
    }
  
]
```

### <a name="qradar-alerts"></a>Avvisi di QRadar

L'integrazione di QRadar con Defender for Internet è utile per identificare gli avvisi generati da Defender per l'IT e per eseguire azioni con questi avvisi. QRadar riceve i dati da Defender for Internet e quindi contatta il componente della console di gestione locale dell'API pubblica.

Per inviare i dati rilevati da Defender for QRadar, definire una regola di inoltramento nel sistema Defender for Internet e selezionare l'opzione di **gestione degli avvisi di supporto remoto** .

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="Modificare le regole di inoltri in base alle esigenze.":::

Quando si seleziona questa opzione durante il processo di configurazione delle regole di invio, in QRadar vengono visualizzati i campi aggiuntivi seguenti:

- **UUID**: identificatore di avviso univoco, ad esempio 1-1555245116250.

- **Sito**: sito in cui è stato individuato l'avviso.

- **Zona**: area in cui è stato individuato l'avviso.

Esempio di payload inviato a QRadar:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>&lt;UUID/External/V1/Alerts/&gt;

#### <a name="method"></a>Metodo

**PUT**

#### <a name="request-type"></a>Tipo di richiesta

**JSON**

#### <a name="request-content"></a>Contenuto della richiesta

Oggetto JSON che rappresenta l'azione da eseguire sull'avviso che contiene l'UUID.

#### <a name="action-fields"></a>Campi azione

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **action** | string | No | handle o handleAndLearn |

#### <a name="request-example"></a>Esempio di richiesta

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano i dispositivi.

#### <a name="response-fields"></a>Campi di risposta


| Nome | Type | Nullable | Descrizione |
|--|--|--|--|
| **contenuto/errore** | string | No | Se la richiesta ha esito positivo, viene visualizzata la proprietà Content. In caso contrario, viene visualizzata la proprietà Error. |

#### <a name="possible-content-values"></a>Possibili valori di contenuto

| Codice di stato | Valore di content | Descrizione |
|--|--|--|
| 200 | La richiesta di aggiornamento avvisi è stata completata. | La richiesta di aggiornamento è stata completata. Nessun commento. |
| 200 | Avviso già gestito (**handle**). | L'avviso è stato già gestito quando è stata ricevuta una richiesta di handle per l'avviso.<br />L'avviso rimane **gestito**. |
| 200 | Avviso già gestito e appreso (**handleAndLearn**). | L'avviso è stato già gestito e appreso quando è stata ricevuta una richiesta a **handleAndLearn** .<br />L'avviso rimane nello stato **handledAndLearn** . |
| 200 | Avviso già gestito (**gestito**).<br />Handle e Learn (**handleAndLearn**) sono stati eseguiti nell'avviso. | L'avviso è stato già gestito quando è stata ricevuta una richiesta a **handleAndLearn** .<br />L'avviso diventa **handleAndLearn**. |
| 200 | Avviso già gestito e appreso (**handleAndLearn**). Richiesta di handle ignorata. | L'avviso è già stato **handleAndLearn** quando è stata ricevuta una richiesta di gestione dell'avviso. L'avviso rimane **handleAndLearn**. |
| 500 | Azione non valida. | L'azione inviata non è un'azione valida da eseguire sull'avviso. |
| 500 | Si è verificato un errore imprevisto. | Si è verificato un errore imprevisto. Per risolvere il problema, contattare il supporto tecnico. |
| 500 | Non è stato possibile eseguire la richiesta perché non è stato trovato alcun avviso per questo UUID. | L'UUID di avviso specificato non è stato trovato nel sistema. |

#### <a name="response-example"></a>Esempio di risposta

**Connessione riuscita**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**Connessione non riuscita**

```rest
{
    "error": "Invalid action"
}
```

### <a name="alert-exclusions-maintenance-window"></a>Esclusioni avvisi (finestra di manutenzione)

Definire le condizioni in base alle quali non verranno inviati gli avvisi. Ad esempio, definire e aggiornare l'ora di arresto e di inizio, i dispositivi o le subnet che devono essere esclusi quando si attivano gli avvisi o Defender per i motori Internet che devono essere esclusi. Ad esempio, durante una finestra di manutenzione, potrebbe essere necessario arrestare il recapito di tutti gli avvisi, tranne che per gli avvisi malware nei dispositivi critici.

Le API definite qui vengono visualizzate nella finestra **esclusioni avvisi** della console di gestione locale come regola di esclusione di sola lettura.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="La finestra Esclusioni avvisi, che mostra un elenco di tutte le regole di esclusione. ":::

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

#### <a name="method---post"></a>Metodo-POST

#### <a name="query-parameters"></a>Parametri di query

- **ID ticket**: definisce l'ID ticket di manutenzione nei sistemi dell'utente.

- **TTL**: definisce la durata (TTL, time to Live), ovvero la durata della finestra di manutenzione in minuti. Dopo il periodo di tempo definito da questo parametro, il sistema avvia automaticamente l'invio di avvisi.

- **motori**: definisce da quale motore di sicurezza escludere gli avvisi durante il processo di manutenzione:

   - ANOMALIA

   - MALWARE

   - OPERATIVO

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds**: definisce da quale Defender per il sensore Internet per l'eliminazione degli avvisi durante il processo di manutenzione. Si tratta dello stesso ID recuperato da/API/V1/Appliances (GET).

- **subnet**: definisce da quale subnet escludere gli avvisi durante il processo di manutenzione. La subnet viene inviata nel formato seguente: 192.168.0.0/16.

#### <a name="error-codes"></a>Codici di errore

- **201 (creato)**: l'azione è stata completata correttamente.

- **400 (richiesta non valida)**: viene visualizzato nei casi seguenti:

   - Il parametro **TTL** non è numerico o non è positivo.

   - Il parametro **subnet** è stato definito con un formato errato.

   - Parametro **ID ticket** mancante.

   - Il parametro del **motore** non corrisponde ai motori di sicurezza esistenti.

- **404 (non trovato)**: uno dei sensori non esiste.

- **409 (conflitto)**: l'ID ticket è collegato a un'altra finestra di manutenzione aperta.

- **500 (errore interno del server)**: qualsiasi altro errore imprevisto.

> [!NOTE]
> Verificare che l'ID ticket non sia collegato a una finestra aperta esistente. Viene generata la seguente regola di esclusione: manutenzione-{token Name}-{Ticket ID}.

#### <a name="method---put"></a>Metodo-PUT

Consente di aggiornare la durata della finestra di manutenzione dopo aver avviato il processo di manutenzione modificando il parametro **TTL** . La nuova definizione Duration sostituisce quella precedente.

Questo metodo è utile quando si desidera impostare una durata maggiore di quella attualmente configurata.

#### <a name="query-parameters"></a>Parametri di query

- **ID ticket**: definisce l'ID ticket di manutenzione nei sistemi dell'utente.

- **TTL**: definisce la durata della finestra in minuti.

#### <a name="error-code"></a>Codice di errore

- **200 (OK)**: l'azione è stata completata correttamente.

- **400 (richiesta non valida)**: viene visualizzato nei casi seguenti:

   - Il parametro **TTL** non è numerico o non è positivo.

   - Parametro **ID ticket** mancante.

   - Il parametro **TTL** è mancante.

- **404 (non trovato)**: l'ID ticket non è collegato a una finestra di manutenzione aperta.

- **500 (errore interno del server)**: qualsiasi altro errore imprevisto.

> [!NOTE]
> Verificare che l'ID ticket sia collegato a una finestra aperta esistente.

#### <a name="method---delete"></a>Metodo-DELETE

Chiude una finestra di manutenzione esistente.

#### <a name="query-parameters"></a>Parametri di query

- **ID ticket**: registra l'ID del ticket di manutenzione nei sistemi dell'utente.

#### <a name="error-code"></a>Codice di errore

- **200 (OK)**: l'azione è stata completata correttamente.

- **400 (richiesta non valida)**: parametro **ID ticket** mancante.

- **404 (non trovato)**: l'ID ticket non è collegato a una finestra di manutenzione aperta.

- **500 (errore interno del server)**: qualsiasi altro errore imprevisto.

> [!NOTE]
> Verificare che l'ID ticket sia collegato a una finestra aperta esistente.

#### <a name="method---get"></a>Metodo-GET

Recuperare un log di tutte le azioni di apertura, chiusura e aggiornamento eseguite nel sistema durante la manutenzione. È possibile recuperare un log solo per le finestre di manutenzione che erano attive in passato e chiuse.

#### <a name="query-parameters"></a>Parametri di query

- **FromDate**: filtra i log dalla data predefinita e versioni successive. Il formato è 2019-12-30.

- **ToDate**: filtra i registri fino alla data predefinita. Il formato è 2019-12-30.

- **ID ticket**: filtra i log correlati a un ID ticket specifico.

- **tokenname**: filtra i log correlati a un nome di token specifico.

#### <a name="error-code"></a>Codice di errore

- **200 (OK)**: l'azione è stata completata correttamente.

- **400 (richiesta non valida)**: il formato della data non è corretto.

- **204 (nessun contenuto)**: non sono presenti dati da visualizzare.

- **500 (errore interno del server)**: qualsiasi altro errore imprevisto.

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano le operazioni della finestra di manutenzione.

#### <a name="response-structure"></a>Struttura della risposta

| Nome | Type | Commento | Nullable |
|--|--|--|--|
| **dateTime** | string | Esempio: "2012-04-23T18:25:43.511 Z" | no |
| **ID ticket** | string | Esempio: "9a5fe99c-D914-4BDA-9332-307384fe40bf" | no |
| **Nometoken** | string | - | no |
| **motori** | Matrice di stringhe | - | sì |
| **sensorIds** | Matrice di stringhe | - | sì |
| **subnet** | Matrice di stringhe | - | sì |
| **ttl** | Numeric | - | sì |
| **operationType** | string | I valori sono "OPEN", "UPDATE" e "CLOSE" | no |

### <a name="authenticate-user-credentials"></a>Autenticare le credenziali utente

Usare questa API per convalidare le credenziali utente. Per tutti i ruoli utente Defender è possibile usare l'API. Per usare questa API, non è necessario un Defender per il token di accesso.

#### <a name="externalauthenticationvalidation"></a>/external/authentication/validation

#### <a name="method"></a>Metodo

**POST**

#### <a name="request-type"></a>Tipo di richiesta

**JSON**

#### <a name="request-example"></a>Esempio di richiesta

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Stringa di messaggio con i dettagli sullo stato dell'operazione:

- **Operazione riuscita-messaggio**: autenticazione riuscita

- **Errore-errore**: convalida delle credenziali non riuscita

#### <a name="device-fields"></a>Campi dispositivo

| **Nome** | **Tipo** | **Ammette i valori Null** |
|--|--|--|
| **username** | string | No |
| **password** | string | No |

#### <a name="response-example"></a>Esempio di risposta

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

### <a name="change-password"></a>Cambia password

Usare questa API per consentire agli utenti di modificare le proprie password. Per tutti i ruoli utente Defender è possibile usare l'API. Per usare questa API, non è necessario un Defender per il token di accesso.

#### <a name="externalauthenticationset_password"></a>set_password/External/Authentication/

#### <a name="method"></a>Metodo

**POST**

#### <a name="request-type"></a>Tipo di richiesta

**JSON**

#### <a name="request-example"></a>Esempio di richiesta

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Stringa di messaggio con i dettagli sullo stato dell'operazione:

- **Operazione riuscita-messaggio**: la password è stata sostituita

- Errore **-errore**: errore di autenticazione utente

- Errore **-errore**: la password non corrisponde ai criteri di sicurezza

#### <a name="response-example"></a>Esempio di risposta

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Campi dispositivo

| **Nome** | **Tipo** | **Ammette i valori Null** |
|--|--|--|
| **username** | string | No |
| **password** | string | No |
| **new_password** | string | No |

### <a name="user-password-update-by-system-admin"></a>Aggiornamento della password utente dall'amministratore di sistema

Usare questa API per consentire agli amministratori di sistema di modificare le password per gli utenti specificati. Defender per i ruoli utente amministratore di Internet delle cose può funzionare con l'API. Per usare questa API, non è necessario un Defender per il token di accesso.

#### <a name="externalauthenticationset_password_by_admin"></a>set_password_by_admin/External/Authentication/

#### <a name="method"></a>Metodo

**POST**

#### <a name="request-type"></a>Tipo di richiesta

**JSON**

#### <a name="request-example"></a>Esempio di richiesta

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Stringa di messaggio con i dettagli sullo stato dell'operazione:

- **Operazione riuscita-messaggio**: la password è stata sostituita

- Errore **-errore**: errore di autenticazione utente

- Errore **-errore**: l'utente non esiste

- Errore **-errore**: la password non corrisponde ai criteri di sicurezza

- Errore **-errore**: l'utente non dispone delle autorizzazioni per modificare la password

#### <a name="response-example"></a>Esempio di risposta

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Campi dispositivo

| **Nome** | **Tipo** | **Ammette i valori Null** |
|--|--|--|
| **admin_username** | string | No |
| **admin_password** | string | No |
| **username** | string | No |
| **new_password** | string | No |

## <a name="see-also"></a>Vedere anche
[Esaminare i rilevamenti dei sensori nell'inventario](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 dei dispositivi [Esaminare tutti i rilevamenti dei sensori aziendali nell'inventario di un dispositivo](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
