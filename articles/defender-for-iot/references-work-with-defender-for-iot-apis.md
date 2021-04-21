---
title: Usare le API di Defender per IoT
description: Usare un'API REST esterna per accedere ai dati individuati da sensori e console di gestione ed eseguire azioni con questi dati.
ms.date: 12/14/2020
ms.topic: reference
ms.openlocfilehash: e7833a20d4f708ecb5b80394fae2c56fc07c9489
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752733"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>API del sensore e della console di gestione Di Defender per IoT

Usare un'API REST esterna per accedere ai dati individuati da sensori e console di gestione ed eseguire azioni con questi dati.

Le connessioni sono protette tramite SSL.

## <a name="getting-started"></a>Guida introduttiva

In generale, quando si usa un'API esterna nel sensore Azure Defender per IoT o nella console di gestione locale, è necessario generare un token di accesso. I token non sono necessari per le API di autenticazione usate nel sensore e nella console di gestione locale.

Per generare un token:

1. Nella finestra **Impostazioni di** sistema selezionare Token **di accesso**.
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="Screenshot delle finestre Impostazioni di sistema che evidenziano il pulsante Token di accesso.":::

2. Selezionare **Genera nuovo token**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Selezionare il pulsante per generare un nuovo token.":::

3. Descrivere lo scopo del nuovo token e selezionare **Avanti.**
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Generare un nuovo token e immettere il nome dell'integrazione associata.":::

4. Viene visualizzato il token di accesso. Copiarlo perché non verrà visualizzato di nuovo.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Copiare il token di accesso per l'integrazione.":::

5. Selezionare **Fine**. I token creati vengono visualizzati nella finestra **di dialogo Token di** accesso .
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="Screenshot della finestra di dialogo Token dispositivo con token compilati":::

   **Usato** indica l'ultima volta che è stata ricevuta una chiamata esterna con questo token.

   Se **N/D** viene visualizzato nel campo **Usato** per questo token, la connessione tra il sensore e il server connesso non funziona.

6. Aggiungere un'intestazione HTTP **intitolata Authorization** alla richiesta e impostarne il valore sul token generato.

## <a name="sensor-api-specifications"></a>Specifiche dell'API sensor

Questa sezione descrive le API dei sensori seguenti:

- [Recuperare le informazioni sul dispositivo - /api/v1/devices](#retrieve-device-information---apiv1devices)

- [Recuperare le informazioni di connessione del dispositivo - /api/v1/devices/connections](#retrieve-device-connection-information---apiv1devicesconnections)

- [Recuperare informazioni sui CVE - /api/v1/devices/cves](#retrieve-information-on-cves---apiv1devicescves)

- [Recuperare le informazioni sugli avvisi - /api/v1/alerts](#retrieve-alert-information---apiv1alerts)

- [Recuperare gli eventi della sequenza temporale - /api/v1/events](#retrieve-timeline-events---apiv1events)

- [Recuperare informazioni sulla vulnerabilità - /api/v1/reports/vulnerabilities/devices](#retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices)

- [Recuperare le vulnerabilità della sicurezza - /api/v1/reports/vulnerabilities/security](#retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity)

- [Recuperare le vulnerabilità operative - /api/v1/reports/vulnerabilities/operational](#retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational)

- [Convalidare le credenziali utente - /api/external/authentication/validation](#validate-user-credentials---apiexternalauthenticationvalidation)

- [Modificare la password - /external/authentication/set_password](#change-password---externalauthenticationset_password)

- [Aggiornamento della password utente da parte dell'amministratore di sistema - /external/authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

### <a name="retrieve-device-information---apiv1devices"></a>Recuperare le informazioni sul dispositivo - /api/v1/devices

Usare questa API per richiedere un elenco di tutti i dispositivi rilevati da un sensore Defender per IoT.

#### <a name="method"></a>Metodo

**GET**

Richiede un elenco di tutti i dispositivi rilevati dal sensore Defender per IoT.

#### <a name="query-parameters"></a>Parametri di query

- **authorized:** per filtrare solo i dispositivi autorizzati e non autorizzati.

  **Esempi**:

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano i dispositivi.

#### <a name="device-fields"></a>Campi del dispositivo

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **id** | Numerico | No | - |
| **Ipaddresses** | Matrice JSON | Sì | Indirizzi IP (può essere più di un indirizzo in caso di indirizzi Internet o di un dispositivo con schede di interfaccia di rete doppie) |
| **nome** | Stringa | No | - |
| **type** | Stringa | No | Sconosciuto, Engineering Station, PLC, HMI, Storico, Controller di dominio, server di database, punto di accesso wireless, router, commutatore, server, workstation, fotocamera IP, stampante, firewall, stazione terminale, gateway VPN, Internet o multicast e broadcast |
| **macAddresses** | Matrice JSON | Sì | Indirizzi MAC (può essere più di un indirizzo nel caso di un dispositivo con schede di interfaccia di rete doppie) |
| **operatingSystem** | string | Sì | - |
| **engineeringStation** | Boolean | No | True o false |
| **Scanner** | Boolean | No | True o false |
| **autorizzato** | Boolean | No | True o false |
| **Fornitore** | string | Sì | - |
| **Protocolli** | Matrice JSON | Sì | Oggetto Protocol |
| **Firmware** | Matrice JSON | Sì | Oggetto firmware |

#### <a name="protocol-fields"></a>Campi del protocollo

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **Nome** | Stringa | No |  |
| **Indirizzi** | Matrice JSON | Sì | Valori master o numerici |

#### <a name="firmware-fields"></a>Campi firmware

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **Seriale** | Stringa | No | N/D o il valore effettivo |
| **model** | Stringa | No | N/D o il valore effettivo |
| **firmwareVersion** | Double | No | N/D o il valore effettivo |
| **additionalData** | Stringa | No | N/D o il valore effettivo |
| **moduleAddress** | Stringa | No | N/D o il valore effettivo |
| **armadio dati** | Stringa | No | N/D o il valore effettivo |
| **Slot** | Stringa | No | N/D o il valore effettivo |
| **address** | Stringa | No | N/D o il valore effettivo |

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

#### <a name="curl-command"></a>Comando Curl

| Type | API | Esempio |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https: <span> //127 <span> .0.0.1/api/v1/devices?authorized=true |

### <a name="retrieve-device-connection-information---apiv1devicesconnections"></a>Recuperare le informazioni di connessione del dispositivo - /api/v1/devices/connections

Usare questa API per richiedere un elenco di tutte le connessioni per ogni dispositivo.

#### <a name="method"></a>Metodo

**GET**

#### <a name="query-parameters"></a>Parametri di query

Se non si impostano i parametri di query, vengono restituite tutte le connessioni del dispositivo.

**Esempio**:

`/api/v1/devices/connections`

- **deviceId:** filtrare in base a un ID dispositivo specifico per visualizzarne le connessioni.

  **Esempio**:

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes:** intervallo di tempo da ora all'indietro, in minuti, durante il quale le connessioni erano attive.

  **Esempio**:

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore:** filtra solo le connessioni rilevate prima di un'ora specifica (in millisecondi, UTC).

  **Esempio**:

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter:** filtra solo le connessioni rilevate dopo un'ora specifica (in millisecondi, UTC).

  **Esempio**:

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano le connessioni del dispositivo.

#### <a name="fields"></a>Campi

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **firstDeviceId** | Numerico | No | - |
| **secondDeviceId** | Numerico | No | - |
| **lastSeen** | Numerico | No | Epoch (UTC) |
| **Scoperto** | Numerico | No | Epoch (UTC) |
| **Porte** | Matrice di numeri | No | - |
| **Protocolli** | Matrice JSON | No | Campo Protocollo |

#### <a name="protocol-field"></a>Campo Protocollo

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **nome** | Stringa | No | - |
| **Comandi** | Matrice di stringhe | No | - |

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

#### <a name="curl-command"></a>Comando Curl

> [!div class="mx-tdBreakAll"]
> | Type | API | Esempio |
> |--|--|--|
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/connections | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/devices/connections |
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/devices/ <deviceId> /connections?lastActiveInMinutes=&discoveredBefore=&discoveredAfter=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" 'https:/ <span> /127.0.0.1/api/v1/devices /2/connections?lastActiveInMinutes=20&discoveredBefore=1594550986000&discoveredAfter=1594550986000' |

### <a name="retrieve-information-on-cves---apiv1devicescves"></a>Recuperare informazioni sui CVE - /api/v1/devices/cves

Usare questa API per richiedere un elenco di tutti i CVA noti individuati nei dispositivi nella rete.

#### <a name="method"></a>Metodo

**GET**

#### <a name="query-parameters"></a>Parametri di query

Per impostazione predefinita, questa API fornisce l'elenco di tutti gli indirizzi IP dei dispositivi con CVA, fino a 100 CVA con punteggio più alto per ogni indirizzo IP.

**Esempio**:

`/api/v1/devices/cves`

- **deviceId:** filtrare in base a un indirizzo IP del dispositivo specifico per ottenere fino a 100 CVA con il punteggio più alto identificati nel dispositivo specifico.

  **Esempio**:

  `/api/v1/devices/<ipAddress>/cves`

- **top:** numero di CVA con il punteggio più alto da recuperare per ogni indirizzo IP del dispositivo.

  **Esempio**:

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano le CVA identificate sugli indirizzi IP.

#### <a name="fields"></a>Campi

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **cveId** | Stringa | No | - |
| **Ipaddress** | Stringa | No | indirizzo IP |
| **Punteggio** | Stringa | No | 0.0 - 10.0 |
| **attackVector** | Stringa | No | Rete, Rete adiacente, Locale o Fisico |
| **description** | Stringa | No | - |

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

#### <a name="curl-command"></a>Comando Curl

| Type | API | Esempio |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/cves | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/api/v1/devices/cves |
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/ <deviceIpAddress> /cves?top= | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/api/v1/devices/10.10.10.15/cves?top=50 |

### <a name="retrieve-alert-information---apiv1alerts"></a>Recuperare le informazioni sugli avvisi - /api/v1/alerts

Usare questa API per richiedere un elenco di tutti gli avvisi rilevati dal sensore Defender per IoT.

#### <a name="method"></a>Metodo

**GET**

#### <a name="query-parameters"></a>Parametri di query

- **state**: per filtrare solo gli avvisi gestiti o non gestiti.

  **Esempio**:

  `/api/v1/alerts?state=handled`

- **fromTime:** per filtrare gli avvisi creati da un'ora specifica (in millisecondi, UTC).

  **Esempio**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime:** per filtrare gli avvisi creati solo prima di un'ora specifica (in millisecondi, UTC).

  **Esempio**:

  `/api/v1/alerts?toTime=<epoch>`

- **type**: per filtrare gli avvisi in base a un tipo specifico. Tipi esistenti in base ai quali filtrare: nuovi dispositivi imprevisti, disconnessioni.

  **Esempio**:

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano gli avvisi.

#### <a name="alert-fields"></a>Campi di avviso

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **ID** | Numerico | No | - |
| **time** | Numerico | No | Epoch (UTC) |
| **title** | Stringa | No | - |
| **message** | Stringa | No | - |
| **severity** | Stringa | No | Avviso, Secondario, Principale o Critico |
| **Motore** | Stringa | No | Violazione del protocollo, violazione dei criteri, malware, anomalie o operativa |
| **sourceDevice** | Numerico | Sì | ID dispositivo |
| **destinationDevice** | Numerico | Sì | ID dispositivo |
| **sourceDeviceAddress** | Numerico | Sì | IP, MAC, Null |
| **destinationDeviceAddress** | Numerico | Sì | IP, MAC, Null |
| **remediationSteps** | string | Sì | Passaggi di correzione descritti nell'avviso |
| **additionalInformation** | Oggetto informazioni aggiuntive | Sì | - |

Si noti che /api/v2/ è necessario per le informazioni seguenti:

- sourceDeviceAddress 
- destinationDeviceAddress
- remediationSteps

#### <a name="additional-information-fields"></a>Campi di informazioni aggiuntive

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **description** | Stringa | No | - |
| **Informazioni** | Matrice JSON | No | string |

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

#### <a name="curl-command"></a>Comando Curl

> [!div class="mx-tdBreakAll"]
> | Type | API | Esempio |
> |--|--|--|
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/alerts?state=&fromTime=&toTime=&type=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" 'https:/ <span> /127.0.0.1/api/v1/alerts?state=unhandled&fromTime=1594550986000&toTime=1594550986001&type=disconnections' |

### <a name="retrieve-timeline-events---apiv1events"></a>Recuperare gli eventi della sequenza temporale - /api/v1/events

Usare questa API per richiedere un elenco di eventi segnalati alla sequenza temporale degli eventi.

#### <a name="method"></a>Metodo

**GET**

#### <a name="query-parameters"></a>Parametri di query

- **minutesTimeFrame:** intervallo di tempo da ora a ritroso, in minuti, in cui sono stati segnalati gli eventi.

  **Esempio**:

  `/api/v1/events?minutesTimeFrame=20`

- **type**: per filtrare l'elenco di eventi in base a un tipo specifico.

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
| **timestamp** | Numerico | No | Epoch (UTC) |
| **title** | Stringa | No | - |
| **severity** | Stringa | No | INFO, NOTICE o ALERT |
| **Proprietario** | string | Sì | Se l'evento è stato creato manualmente, questo campo includerà il nome utente che ha creato l'evento |
| **content** | Stringa | No | - |

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

#### <a name="curl-command"></a>Comando Curl

| Type | API | Esempio |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/events?minutesTimeFrame=&type=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/ <span> /127.0.0.1/api/v1/events?minutesTimeFrame=20&type=DEVICE_CONNECTION_CREATED' |

### <a name="retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices"></a>Recuperare informazioni sulla vulnerabilità - /api/v1/reports/vulnerabilities/devices

Usare questa API per richiedere i risultati della valutazione della vulnerabilità per ogni dispositivo.

#### <a name="method"></a>Metodo

**GET**

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano i dispositivi valutati.

L'oggetto dispositivo contiene:

- Dati generali

- Punteggio di valutazione

- Vulnerabilità

#### <a name="device-fields"></a>Campi del dispositivo

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **nome** | Stringa | No | - |
| **Ipaddresses** | Matrice JSON | No | - |
| **securityScore** | Numerico | No | - |
| **Fornitore** | string | Sì |  |
| **firmwareVersion** | string | Sì | - |
| **model** | string | Sì | - |
| **isWirelessAccessPoint** | Boolean | No | True o false |
| **operatingSystem** | Oggetto del sistema operativo | Sì | - |
| **Vulnerabilità** | Oggetto Vulnerabilità | Sì | - |

#### <a name="operating-system-fields"></a>Campi del sistema operativo

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **Nome** | string | Sì | - |
| **Tipo** | string | Sì | - |
| **Versione** | string | Sì | - |
| **latestVersion** | string | Sì | - |

#### <a name="vulnerabilities-fields"></a>Campi delle vulnerabilità
 
| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **Antivirus** | Matrice JSON | Sì | Nomi antivirus |
| **plainTextPasswords** | Matrice JSON | Sì | Oggetti password |
| **Remoteaccess** | Matrice JSON | Sì | Oggetti di accesso remoto |
| **isBackupServer** | Boolean | No | True o false |
| **openedPorts** | Matrice JSON | Sì | Oggetti porta aperti |
| **isEngineeringStation** | Boolean | No | True o false |
| **isKnownScanner** | Boolean | No | True o false |
| **cves** | Matrice JSON | Sì | Oggetti CVE |
| **isUnauthorized** | Boolean | No | True o false |
| **malwareIndicationsDetected** | Boolean | No | True o false |
| **weakAuthentication** | Matrice JSON | Sì | Applicazioni rilevate che usano l'autenticazione debole |

#### <a name="password-fields"></a>Campi password

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **password** | Stringa | No | - |
| **protocol** | Stringa | No | - |
| **Forza** | Stringa | No | Molto debole, debole, medio o forte |

#### <a name="remote-access-fields"></a>Campi di accesso remoto

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **port** | Numerico | No | - |
| **Trasporto** | Stringa | No | TCP o UDP |
| **Client** | Stringa | No | indirizzo IP |
| **clientSoftware** | Stringa | No | Visualizzatore SSH, VNC, Desktop remoto o Team |

#### <a name="open-port-fields"></a>Aprire i campi delle porte

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **port** | Numerico | No | - |
| **Trasporto** | Stringa | No | TCP o UDP |
| **protocol** | string | Sì | - |
| **isConflictingWithFirewall** | Boolean | No | True o false |

#### <a name="cve-fields"></a>Campi CVE

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **ID** | Stringa | No | - |
| **Punteggio** | Numerico | No | Double |
| **description** | Stringa | No | - |

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

#### <a name="curl-command"></a>Comando Curl

| Type | API | Esempio |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/devices | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/api/v1/reports/vulnerabilities/devices |

### <a name="retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity"></a>Recuperare le vulnerabilità della sicurezza - /api/v1/reports/vulnerabilities/security

Usare questa API per richiedere i risultati di una valutazione della vulnerabilità generale. Questa valutazione fornisce informazioni dettagliate sul livello di sicurezza del sistema.

Questa valutazione si basa su informazioni generali sulla rete e sul sistema e non su una valutazione specifica del dispositivo.

#### <a name="method"></a>Metodo

**GET**

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Oggetto JSON che rappresenta i risultati valutati. Ogni chiave può essere nullable. In caso contrario, conterrà un oggetto JSON con chiavi non nullable.

### <a name="result-fields"></a>Campi dei risultati

**Chiavi**

**unauthorizedDevices**

| Nome del campo | Tipo | Elenco di valori |
| ---------- | ---- | -------------- |
| **address** | Stringa | indirizzo IP |
| **nome** | Stringa | - |
| **firstDetectionTime** | Numerico | Epoch (UTC) |
| lastSeen | Numerico | Epoch (UTC) |

**illegalTrafficByFirewallRules**

| Nome del campo | Tipo | Elenco di valori |
| ---------- | ---- | -------------- |
| **server** | Stringa | indirizzo IP |
| **Client** | Stringa | indirizzo IP |
| **port** | Numerico | - |
| **Trasporto** | Stringa | TCP, UDP o ICMP |

**weakFirewallRules**

| Nome del campo | Tipo | Elenco di valori |
| ---------- | ---- | -------------- |
| **sources** | Matrice JSON di origini. Ogni origine può essere in uno dei quattro formati seguenti. | "Any", "ip address (Host)", "from ip-to ip (RANGE)", "ip address, subnet mask (NETWORK)" |
| **Destinazioni** | Matrice JSON di destinazioni. Ogni destinazione può essere in uno dei quattro formati seguenti. | "Any", "ip address (Host)", "from ip-to ip (RANGE)", "ip address, subnet mask (NETWORK)" |
| **Porte** | Matrice JSON di porte in uno qualsiasi dei tre formati | "Any", "port (protocol, if detected)", "from port-to port (protocol, if detected)" |

**accessPoints**

| Nome del campo | Tipo | Elenco di valori |
| ---------- | ---- | -------------- |
| **macAddress** | Stringa | Indirizzo MAC |
| **Fornitore** | Stringa | Nome del fornitore |
| **Ipaddress** | Stringa | Indirizzo IP o N/D |
| **nome** | Stringa | Nome del dispositivo o N/D |
| **Wireless** | Stringa | No, Sospetto o Sì |

**connectionsBetweenSubnets**

| Nome del campo | Tipo | Elenco di valori |
| ---------- | ---- | -------------- |
| **server** | Stringa | indirizzo IP |
| **Client** | Stringa | indirizzo IP |

**industrialMalwareIndicators**

| Nome del campo | Tipo | Elenco di valori |
| ---------- | ---- | -------------- |
| **detectionTime** | Numerico | Epoch (UTC) |
| **alertMessage** | Stringa | - |
| **description** | Stringa | - |
| **Dispositivi** | Matrice JSON | Nomi dei dispositivi | 

**InternetConnections**

| Nome del campo | Tipo | Elenco di valori |
| ---------- | ---- | -------------- |
| **internalAddress** | Stringa | indirizzo IP |
| **autorizzato** | Boolean | Sì o No | 
| **externalAddresses** | Matrice JSON | indirizzo IP |

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

#### <a name="curl-command"></a>Comando Curl

| Type | API | Esempio |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/security | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/api/v1/reports/vulnerabilities/security |

### <a name="retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational"></a>Recuperare le vulnerabilità operative - /api/v1/reports/vulnerabilities/operational

Usare questa API per richiedere i risultati di una valutazione della vulnerabilità generale. Questa valutazione fornisce informazioni dettagliate sullo stato operativo della rete. Si basa su informazioni generali sulla rete e sul sistema e non su una valutazione specifica del dispositivo.

#### <a name="method"></a>Metodo

**GET**

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Oggetto JSON che rappresenta i risultati valutati. Ogni chiave contiene una matrice JSON di risultati.

#### <a name="result-fields"></a>Campi dei risultati

**Chiavi**

**backupServer**

| Nome del campo | Tipo | Elenco di valori |
|--|--|--|
| **source** | Stringa | indirizzo IP |
| **destinazione** | Stringa | indirizzo IP |
| **port** | Numerico | - |
| **Trasporto** | Stringa | TCP o UDP |
| **backupMaximalInterval** | Stringa | - |
| **lastSeenBackup** | Numerico | Epoch (UTC) |

**ipNetworks**

| Nome del campo | Tipo | Elenco di valori |
|--|--|--|
| **addresse** s | Numerico | - |
| **Rete** | Stringa | indirizzo IP |
| **Maschera** | Stringa | Subnet mask |

**protocolProblems**

| Nome del campo | Tipo | Elenco di valori |
|--|--|--|
| **protocol** | Stringa | - |
| **Indirizzi** | Matrice JSON | Indirizzi IP |
| **Avviso** | Stringa | - |
| **reportTime** | Numerico | Epoch (UTC) |

**protocolDataVolumes**

| Nome del campo | Tipo | Elenco di valori |
|--|--|--|
| protocol | Stringa | - |
| volume | Stringa | "volume number MB" |

**Disconnessioni**

| Nome del campo | Tipo | Elenco di valori |
|--|--|--|
| **assetAddress** | Stringa | indirizzo IP |
| **assetName** | Stringa | - |
| **lastDetectionTime** | Numerico | Epoch (UTC) |
| **backToNormalTime** | Numerico | Epoch (UTC) |     

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

#### <a name="curl-command"></a>Comando Curl

| Type | API | Esempio |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/operational | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/api/v1/reports/vulnerabilities/operational |

### <a name="validate-user-credentials---apiexternalauthenticationvalidation"></a>Convalidare le credenziali utente - /api/external/authentication/validation

Usare questa API per convalidare un nome utente e una password di Defender per IoT. Tutti i ruoli utente di Defender per IoT possono funzionare con l'API.

Non è necessario un token di accesso Defender per IoT per usare questa API.

#### <a name="method"></a>Metodo

**POST**

#### <a name="request-type"></a>Tipo di richiesta

**JSON**

#### <a name="query-parameters"></a>Parametri di query

| **Nome** | **Tipo** | **Ammette i valori Null** |
|--|--|--|
| **username** | Stringa | No |
| **password** | Stringa | No |

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

Stringa di messaggio con i dettagli dello stato dell'operazione:

- **Operazione riuscita - msg:** autenticazione riuscita

- **Errore - Errore:** Convalida delle credenziali non riuscita

#### <a name="response-example"></a>Esempio di risposta

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

#### <a name="curl-command"></a>Comando Curl

| Type | API | Esempio |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/external/authentication/validation | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/external/authentication/validation |

### <a name="change-password---externalauthenticationset_password"></a>Modificare la password - /external/authentication/set_password

Usare questa API per consentire agli utenti di modificare le proprie password. Tutti i ruoli utente di Defender per IoT possono funzionare con l'API. Non è necessario un token di accesso Defender per IoT per usare questa API.

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

Stringa di messaggio con i dettagli dello stato dell'operazione:

- **Operazione riuscita - msg:** la password è stata sostituita

- **Errore - Errore:** errore di autenticazione utente

- **Errore : errore:** la password non corrisponde ai criteri di sicurezza

#### <a name="response-example"></a>Esempio di risposta

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Campi del dispositivo

| **Nome** | **Tipo** | **Ammette i valori Null** |
|--|--|--|
| **username** | Stringa | No |
| **password** | Stringa | No |
| **new_password** | Stringa | No |

#### <a name="curl-command"></a>Comando Curl

| Type | API | Esempio |
|--|--|--|
| POST | curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json' https://<IP_ADDRESS>/api/external/authentication/set_password | curl -k -d '{"username": "myUser","password": " 1234@abcd ","new_password": " abcd@1234 "}' -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/api/external/authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Aggiornamento della password utente da parte dell'amministratore di sistema - /external/authentication/set_password_by_admin

Usare questa API per consentire agli amministratori di sistema di modificare le password per gli utenti specificati. I ruoli utente di amministratore di Defender per IoT possono usare l'API. Non è necessario un token di accesso Defender per IoT per usare questa API.

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

Stringa di messaggio con i dettagli dello stato dell'operazione:

- **Operazione riuscita - msg:** la password è stata sostituita

- **Errore : errore:** errore di autenticazione utente

- **Errore : errore:** l'utente non esiste

- **Errore : errore:** La password non corrisponde ai criteri di sicurezza

- **Errore : errore:** l'utente non dispone delle autorizzazioni per modificare la password

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

#### <a name="device-fields"></a>Campi del dispositivo

| **Nome** | **Tipo** | **Ammette i valori Null** |
|--|--|--|
| **admin_username** | Stringa | No |
| **admin_password** | Stringa | No |
| **username** | Stringa | No |
| **new_password** | Stringa | No |

#### <a name="curl-command"></a>Comando Curl

> [!div class="mx-tdBreakAll"]
> | Type | API | Esempio |
> |--|--|--|
> | POST | curl -k -d '{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}" -H 'Content-Type: application/json' https://<IP_ADDRESS>/api/external/authentication/set_password_by_admin | curl -k -d '{"admin_user":"adminUser","admin_password": " 1234@abcd ","username": "myUser","new_password": " abcd@1234 "}" -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/api/external/authentication/set_password_by_admin |

## <a name="on-premises-management-console-api-specifications"></a>Specifiche dell'API della console di gestione locale ##

Questa sezione descrive le API della console di gestione locale per:
- Esclusioni di avvisi
- Informazioni sul dispositivo
- Informazioni sull'avviso

### <a name="alert-exclusions"></a>Esclusioni di avvisi ###

Definire le condizioni in base alle quali gli avvisi non verranno inviati. Ad esempio, definire e aggiornare gli orari di arresto e avvio, i dispositivi o le subnet che devono essere esclusi quando si attivano gli avvisi o i motori Defender per IoT che devono essere esclusi. Ad esempio, durante una finestra di manutenzione, potrebbe essere necessario arrestare il recapito di tutti gli avvisi, ad eccezione degli avvisi malware nei dispositivi critici. Gli elementi definiti vengono visualizzati nella finestra Esclusioni  avvisi della console di gestione locale come regole di esclusione di sola lettura.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **Esempio di risposta**

- **Risposta:**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password---externalauthenticationset_password"></a>Modificare la password - /external/authentication/set_password 

Usare questa API per consentire agli utenti di modificare le proprie password. Tutti i ruoli utente di Defender per IoT possono funzionare con l'API. Non è necessario un token di accesso Defender per IoT per usare questa API.

#### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Aggiornamento della password utente da parte dell'amministratore di sistema - /external/authentication/set_password_by_admin 

Usare questa API per consentire agli amministratori di sistema di modificare le password per utenti specifici. I ruoli utente amministratore di Defender per IoT possono funzionare con l'API. Non è necessario un token di accesso Defender per IoT per usare questa API.

### <a name="retrieve-device-information---externalv1devices"></a>Recuperare le informazioni sul dispositivo - /external/v1/devices ###

Questa API richiede un elenco di tutti i dispositivi rilevati da Defender per i sensori IoT connessi a una console di gestione locale.

#### <a name="method"></a>Metodo

**GET**

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano i dispositivi.

#### <a name="query-parameters"></a>Parametri di query

- **authorized:** per filtrare solo i dispositivi autorizzati e non autorizzati.

- **siteId:** per filtrare solo i dispositivi correlati a siti specifici.

- **zoneId:** per filtrare solo i dispositivi correlati a zone specifiche. [1](#1)

- **sensorId:** per filtrare solo i dispositivi rilevati da sensori specifici. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *È possibile che non si abbia l'ID sito e l'ID zona. In questo caso, eseguire una query su tutti i dispositivi per recuperare il sito e l'ID zona.*

#### <a name="query-parameters-example"></a>Esempio di parametri di query

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Campi del dispositivo

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **sensorId** | Numerico | No | - |
| **Idarea** | Numerico | Sì | - |
| **Siteid** | Numerico | Sì | - |
| **Ipaddresses** | Matrice JSON | Sì | Indirizzi IP (può essere più di un indirizzo in caso di indirizzi Internet o un dispositivo con schede di interfaccia di rete doppie) |
| **nome** | Stringa | No | - |
| **type** | Stringa | No | Sconosciuto, Engineering Station, PLC, HMI, Eserezione, Controller di dominio, Server di database, Punto di accesso wireless, Router, Commutatore, Server, Workstation, Fotocamera IP, Stampante, Firewall, Stazione terminal, Gateway VPN, Internet o Multicast e broadcast |
| **macAddresses** | Matrice JSON | Sì | Indirizzi MAC (può essere più di un indirizzo nel caso di un dispositivo con schede di interfaccia di rete doppie) |
| **operatingSystem** | string | Sì | - |
| **engineeringStation** | Boolean | No | True o false |
| **Scanner** | Boolean | No | True o false |
| **autorizzato** | Boolean | No | True o false |
| **Fornitore** | string | Sì | - |
| **Protocolli** | Matrice JSON | Sì | Oggetto Protocollo |
| **Firmware** | Matrice JSON | Sì | Oggetto firmware |

#### <a name="protocol-fields"></a>Campi del protocollo

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| Nome | string | No | - |
| Indirizzi | Matrice JSON | Sì | Valori master o numerici |

#### <a name="firmware-fields"></a>Campi firmware

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **Seriale** | Stringa | No | N/D o il valore effettivo |
| **model** | Stringa | No | N/D o il valore effettivo |
| **firmwareVersion** | Double | No | N/D o il valore effettivo |
| **additionalData** | Stringa | No | N/D o il valore effettivo |
| **moduleAddress** | Stringa | No | N/D o il valore effettivo |
| **armadio dati** | Stringa | No | N/D o il valore effettivo |
| **Slot** | Stringa | No | N/D o il valore effettivo |
| **address** | Stringa | No | N/D o il valore effettivo |

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

#### <a name="curl-command"></a>Comando Curl

| Type | API | Esempio |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/devices?siteId=&zoneId=&sensorId=&authorized=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" 'https:/ <span> /127.0.0.1/external/v1/devices?siteId=1&zoneId=2&sensorId=5&authorized=true' |

### <a name="retrieve-alert-information---externalv1alerts"></a>Recuperare le informazioni sugli avvisi - /external/v1/alerts

Usare questa API per recuperare tutti gli avvisi o filtrati da una console di gestione locale.

#### <a name="method"></a>Metodo

**GET**

#### <a name="query-parameters"></a>Parametri di query

- **state:** per filtrare solo gli avvisi gestiti e non gestiti.

  **Esempio**:

  `/api/v1/alerts?state=handled`

- **fromTime:** per filtrare gli avvisi creati da un'ora specifica (in millisecondi, UTC).

  **Esempio**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime:** per filtrare gli avvisi creati solo prima di un'ora specifica (in millisecondi, UTC).

  **Esempio**:

  `/api/v1/alerts?toTime=<epoch>`

- **siteId:** sito in cui è stato individuato l'avviso.
- **zoneId:** la zona in cui è stato individuato l'avviso.
- **sensor:** sensore in cui è stato individuato l'avviso.

*È possibile che non si abbia l'ID del sito e dell'area. In questo caso, eseguire una query su tutti i dispositivi per recuperare il sito e l'ID zona.*

#### <a name="alert-fields"></a>Campi di avviso 

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **ID** | Numerico | No | - |
| **time** | Numerico | No | Epoch (UTC) |
| **title** | Stringa | No | - |
| **message** | Stringa | No | - |
| **severity** | Stringa | No | Avviso, Secondario, Principale o Critico |
| **Motore** | Stringa | No | Violazione del protocollo, violazione dei criteri, malware, anomalia o operativa |
| **sourceDevice** | Numerico | Sì | ID dispositivo |
| **destinationDevice** | Numerico | Sì | ID dispositivo |
| **sourceDeviceAddress** | Numerico | Sì | IP, MAC, Null |
| **destinationDeviceAddress** | Numerico | Sì | IP, MAC, Null |
| **remediationSteps** | string | Sì | Passaggi di correzione visualizzati nell'avviso|
| **sensorName** | string | Sì | Nome del sensore definito dall'utente nella console|
|**zoneName** | string | Sì | Nome della zona associata al sensore nella console|
| **Sitename** | string | Sì | Nome del sito associato al sensore nella console |
| **additionalInformation** | Oggetto informazioni aggiuntive | Sì | - |

Si noti che /api/v2/ è necessario per le informazioni seguenti:

- sourceDeviceAddress 
- destinationDeviceAddress
- remediationSteps
- sensorName
- zoneName
- siteName

#### <a name="additional-information-fields"></a>Campi di informazioni aggiuntive

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **description** | Stringa | No | - |
| **Informazioni** | Matrice JSON | No | string |

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

#### <a name="curl-command"></a>Comando Curl

> [!div class="mx-tdBreakAll"]
> | Type | API | Esempio |
> |--|--|--|
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/alerts?state=&zoneId=&fromTime=&toTime=&siteId=&sensor=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" 'https:/ <span> /127.0.0.1/external/v1/alerts?state=unhandled&zoneId=1&fromTime=0&toTime=1594551777000&siteId=1&sensor=1' |

### <a name="qradar-alerts"></a>Avvisi QRadar

L'integrazione di QRadar con Defender per IoT consente di identificare gli avvisi generati da Defender per IoT ed eseguire azioni con questi avvisi. QRadar riceve i dati da Defender per IoT e quindi contatta il componente della console di gestione locale dell'API pubblica.

Per inviare i dati individuati da Defender per IoT a QRadar, definire una regola di inoltro nel sistema Defender per IoT e selezionare l'opzione **gestione** degli avvisi di supporto remoto.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="Modificare le regole di inoltro in base alle esigenze.":::

Quando si seleziona questa opzione durante il processo di configurazione delle regole di inoltro, in QRadar vengono visualizzati i campi aggiuntivi seguenti:

- **UUID:** identificatore di avviso univoco, ad esempio 1-1555245116250.

- **Sito:** sito in cui è stato individuato l'avviso.

- **Zona:** la zona in cui è stato individuato l'avviso.

Esempio del payload inviato a QRadar:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/external/v1/alerts/ &lt; UUID&gt;

#### <a name="method"></a>Metodo

**PUT**

#### <a name="request-type"></a>Tipo di richiesta

**JSON**

#### <a name="request-content"></a>Contenuto della richiesta

Oggetto JSON che rappresenta l'azione da eseguire sull'avviso che contiene l'UUID.

#### <a name="action-fields"></a>Campi azione

| Nome | Type | Nullable | Elenco di valori |
|--|--|--|--|
| **action** | Stringa | No | handle o handleAndLearn |

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
| **content/error** | Stringa | No | Se la richiesta ha esito positivo, viene visualizzata la proprietà content. In caso contrario, viene visualizzata la proprietà error. |

#### <a name="possible-content-values"></a>Valori di contenuto possibili

| Codice stato | Valore di content | Descrizione |
|--|--|--|
| 200 | Richiesta di aggiornamento avvisi completata. | La richiesta di aggiornamento è stata completata correttamente. Nessun commento. |
| 200 | L'avviso è già stato gestito **(handle**). | L'avviso è già stato gestito quando è stata ricevuta una richiesta di handle per l'avviso.<br />L'avviso **rimane gestito.** |
| 200 | L'avviso è già stato gestito e appreso (**handleAndLearn**). | L'avviso è già stato gestito e appreso quando è **stata ricevuta una richiesta per gestireAndLearn.**<br />L'avviso rimane nello **stato handledAndLearn.** |
| 200 | L'avviso è già stato gestito (**gestito**).<br />Handle and learn (**handleAndLearn**) è stato eseguito sull'avviso. | L'avviso è già stato gestito quando è stata **ricevuta una richiesta per gestireAndLearn.**<br />L'avviso **diventa handleAndLearn.** |
| 200 | L'avviso è già stato gestito e appreso (**handleAndLearn**). Richiesta di handle ignorata. | L'avviso è già **stato gestitoAndLearn** quando è stata ricevuta una richiesta di gestione dell'avviso. L'avviso rimane **handleAndLearn.** |
| 500 | Azione non valida. | L'azione inviata non è un'azione valida da eseguire sull'avviso. |
| 500 | Si è verificato un errore imprevisto. | Si è verificato un errore imprevisto. Per risolvere il problema, contattare il supporto tecnico. |
| 500 | Impossibile eseguire la richiesta perché non è stato trovato alcun avviso per questo UUID. | L'UUID dell'avviso specificato non è stato trovato nel sistema. |

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

#### <a name="curl-command"></a>Comando Curl

| Type | API | Esempio |
|--|--|--|
| PUT | curl -k -X PUT -d '{"action": <ACTION> " "}" -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/external/v1/alerts/<UUID> | curl -k -X PUT -d '{"action": "handle"}' -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/external/v1/alerts/1-1594550943000 |

### <a name="alert-exclusions-maintenance-window---externalv1maintenancewindow"></a>Esclusioni di avvisi (finestra di manutenzione) - /external/v1/maintenanceWindow

Definire le condizioni in cui gli avvisi non verranno inviati. Ad esempio, definire e aggiornare gli orari di arresto e avvio, i dispositivi o le subnet da escludere quando si attivano gli avvisi o i motori Defender per IoT che devono essere esclusi. Ad esempio, durante una finestra di manutenzione, potrebbe essere necessario arrestare il recapito degli avvisi di tutti gli avvisi, ad eccezione degli avvisi malware nei dispositivi critici.

Le API definite qui vengono visualizzate nella finestra Esclusioni  avvisi della console di gestione locale come regola di esclusione di sola lettura.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Finestra Esclusioni avvisi, che mostra un elenco di tutte le regole di esclusione. ":::

#### <a name="method---post"></a>Metodo - POST

#### <a name="query-parameters"></a>Parametri di query

- **ticketId:** definisce l'ID del ticket di manutenzione nei sistemi dell'utente.

- **ttl**: definisce il TTL (time to live), ovvero la durata della finestra di manutenzione in minuti. Dopo il periodo di tempo definito da questo parametro, il sistema avvia automaticamente l'invio di avvisi.

- **engines**: definisce da quale motore di sicurezza eliminare gli avvisi durante il processo di manutenzione:

   - Anomalia

   - Malware

   - Operativo

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds:** definisce da quale sensore Defender per IoT eliminare gli avvisi durante il processo di manutenzione. Si tratta dello stesso ID recuperato da /api/v1/appliances (GET).

- **subnets:** definisce da quale subnet eliminare gli avvisi durante il processo di manutenzione. La subnet viene inviata nel formato seguente: 192.168.0.0/16.

#### <a name="error-codes"></a>Codici di errore

- **201 (Creato):** l'azione è stata completata correttamente.

- **400 (Richiesta non valida):** viene visualizzato nei casi seguenti:

   - Il **parametro ttl** non è numerico o non positivo.

   - Il **parametro subnets** è stato definito usando un formato errato.

   - Parametro **ticketId** mancante.

   - Il **parametro** engine non corrisponde ai motori di sicurezza esistenti.

- **404 (Non trovato):** uno dei sensori non esiste.

- **409 (Conflitto):** l'ID ticket è collegato a un'altra finestra di manutenzione aperta.

- **500 (errore interno del server):** qualsiasi altro errore imprevisto.

> [!NOTE]
> Assicurarsi che l'ID ticket non sia collegato a una finestra aperta esistente. Viene generata la regola di esclusione seguente: Maintenance-{nome token}-{ID ticket}.

#### <a name="method---put"></a>Metodo - PUT

Consente di aggiornare la durata della finestra di manutenzione dopo l'avvio del processo di manutenzione modificando il **parametro ttl.** La nuova definizione di durata esegue l'override di quella precedente.

Questo metodo è utile quando si vuole impostare una durata più lunga rispetto alla durata attualmente configurata.

#### <a name="query-parameters"></a>Parametri di query

- **ticketId:** definisce l'ID del ticket di manutenzione nei sistemi dell'utente.

- **ttl**: definisce la durata della finestra in minuti.

#### <a name="error-code"></a>Codice di errore

- **200 (OK):** l'azione è stata completata.

- **400 (Richiesta non valida):** viene visualizzato nei casi seguenti:

   - Il **parametro ttl** non è numerico o non positivo.

   - Parametro **ticketId** mancante.

   - Parametro **ttl** mancante.

- **404 (Non trovato):** l'ID ticket non è collegato a una finestra di manutenzione aperta.

- **500 (errore interno del server):** qualsiasi altro errore imprevisto.

> [!NOTE]
> Assicurarsi che l'ID ticket sia collegato a una finestra aperta esistente.

#### <a name="method---delete"></a>Metodo - DELETE

Chiude una finestra di manutenzione esistente.

#### <a name="query-parameters"></a>Parametri di query

- **ticketId:** registra l'ID del ticket di manutenzione nei sistemi dell'utente.

#### <a name="error-code"></a>Codice di errore

- **200 (OK):** l'azione è stata completata.

- **400 (Richiesta non valida):** parametro **ticketId** mancante.

- **404 (Non trovato):** l'ID ticket non è collegato a una finestra di manutenzione aperta.

- **500 (errore interno del server):** qualsiasi altro errore imprevisto.

> [!NOTE]
> Assicurarsi che l'ID ticket sia collegato a una finestra aperta esistente.

#### <a name="method---get"></a>Metodo - GET

Recuperare un log di tutte le azioni di apertura, chiusura e aggiornamento eseguite nel sistema durante la manutenzione. È possibile recuperare un log solo per le finestre di manutenzione attive in passato e chiuse.

#### <a name="query-parameters"></a>Parametri di query

- **fromDate:** filtra i log dalla data predefinita e successive. Il formato è 2019-12-30.

- **toDate:** filtra i log fino alla data predefinita. Il formato è 2019-12-30.

- **ticketId:** filtra i log correlati a un ID ticket specifico.

- **tokenName:** filtra i log correlati a un nome di token specifico.

#### <a name="error-code"></a>Codice di errore 

- **200 (OK):** l'azione è stata completata correttamente.

- **400 (Richiesta non valida):** il formato della data non è corretto.

- **204 (Nessun contenuto):** non sono presenti dati da visualizzare.

- **500 (errore interno del server):** qualsiasi altro errore imprevisto.

#### <a name="response-type"></a>Tipo di risposta

**JSON**

#### <a name="response-content"></a>Contenuto della risposta

Matrice di oggetti JSON che rappresentano le operazioni della finestra di manutenzione.

#### <a name="response-structure"></a>Struttura della risposta

| Nome | Type | Commento | Nullable |
|--|--|--|--|
| **dateTime** | Stringa | Esempio: "2012-04-23T18:25:43.511Z" | no |
| **ticketId** | Stringa | Esempio: "9a5fe99c-d914-4bda-9332-307384fe40bf" | no |
| **tokenName** | Stringa | - | no |
| **Motori** | Matrice di stringhe | - | sì |
| **sensorIds** | Matrice di stringhe | - | sì |
| **Subnet** | Matrice di stringhe | - | sì |
| **Ttl** | Numerico | - | sì |
| **operationType** | Stringa | I valori sono "OPEN", "UPDATE" e "CLOSE" | no |

#### <a name="curl-command"></a>Comando Curl

| Type | API | Esempio |
|--|--|--|
| POST | curl -k -X POST -d '{"ticketId": "<TICKET_ID>",ttl": <TIME_TO_LIVE>,"engines": [<ENGINE1, ENGINE2... ENGINEn>],"sensorIds": [<SENSOR_ID1, SENSOR_ID2... SENSOR_IDn>],"subnets": [<SUBNET1, SUBNET2.... SUBNETn>]}' -H "Authorization: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | curl -k -X POST -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20","engines": ["ANOMALY"],"sensorIds": ["5"," 3"],"subnets": ["10.0.0.3"]}' -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| PUT | curl -k -X PUT -d '{"ticketId": "<TICKET_ID>",ttl": "<TIME_TO_LIVE>"}' -H "Authorization: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | curl -k -X PUT -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20". }' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| DELETE | curl -k -X DELETE -d '{"ticketId": "<TICKET_ID>"}' -H "Authorization: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | curl -k -X DELETE -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/external/v1/maintenanceWindow?fromDate=&toDate=&ticketId=&tokenName=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" 'https:/ <span> /127.0.0.1/external/v1/maintenanceWindow?fromDate=2020-0 1-01&toDate=2020-07-14&ticketId=a5fe99c-d914-4bda-9332-307384fe40bf&tokenName=a' |

### <a name="authenticate-user-credentials---externalauthenticationvalidation"></a>Autenticare le credenziali utente - /external/authentication/validation

Usare questa API per convalidare le credenziali utente. Tutti i ruoli utente di Defender per IoT possono funzionare con l'API. Non è necessario un token di accesso Defender per IoT per usare questa API.

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

Stringa di messaggio con i dettagli dello stato dell'operazione:

- **Success – msg**: Autenticazione riuscita

- **Errore - errore:** convalida delle credenziali non riuscita

#### <a name="device-fields"></a>Campi del dispositivo

| **Nome** | **Tipo** | **Ammette i valori Null** |
|--|--|--|
| **username** | Stringa | No |
| **password** | Stringa | No |

#### <a name="response-example"></a>Esempio di risposta

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

#### <a name="curl-command"></a>Comando Curl

| Type | API | Esempio |
|--|--|--|
| POST | curl -k -d '{"username":"<USER_NAME>","password":"PASSWORD"}' 'https://<IP_ADDRESS>/external/authentication/validation' | curl -k -d '{"username":"myUser","password":" 1234@abcd "}' 'https:/ <span> /127.0.0.1/external/authentication/validation' |

### <a name="change-password---externalauthenticationset_password"></a>Modificare la password - /external/authentication/set_password

Usare questa API per consentire agli utenti di modificare le proprie password. Tutti i ruoli utente di Defender per IoT possono usare l'API. Non è necessario un token di accesso Defender per IoT per usare questa API.

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

Stringa di messaggio con i dettagli dello stato dell'operazione:

- **Operazione riuscita - msg:** la password è stata sostituita

- **Errore : errore:** errore di autenticazione utente

- **Errore : errore:** la password non corrisponde ai criteri di sicurezza

#### <a name="response-example"></a>Esempio di risposta

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Campi del dispositivo

| **Nome** | **Tipo** | **Ammette i valori Null** |
|--|--|--|
| **username** | Stringa | No |
| **password** | Stringa | No |
| **new_password** | Stringa | No |

#### <a name="curl-command"></a>Comando Curl

| Type | API | Esempio |
|--|--|--|
| POST | curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json' https://<IP_ADDRESS>/external/authentication/set_password | curl -k -d '{"username": "myUser","password": 1234@abcd " ","new_password": " abcd@1234 "}' -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/external/authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Aggiornamento della password utente da parte dell'amministratore di sistema - /external/authentication/set_password_by_admin

Usare questa API per consentire agli amministratori di sistema di modificare le password per gli utenti specificati. I ruoli utente amministratore di Defender per IoT possono funzionare con l'API. Non è necessario un token di accesso Defender per IoT per usare questa API.

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

Stringa di messaggio con i dettagli dello stato dell'operazione:

- **Operazione riuscita - msg:** la password è stata sostituita

- **Errore - Errore:** errore di autenticazione utente

- **Errore : errore:** l'utente non esiste

- **Errore : errore:** la password non corrisponde ai criteri di sicurezza

- **Errore - Errore:** l'utente non dispone delle autorizzazioni per modificare la password

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

#### <a name="device-fields"></a>Campi del dispositivo

| **Nome** | **Tipo** | **Ammette i valori Null** |
|--|--|--|
| **admin_username** | Stringa | No |
| **admin_password** | Stringa | No |
| **username** | Stringa | No |
| **new_password** | Stringa | No |

#### <a name="curl-command"></a>Comando Curl

> [!div class="mx-tdBreakAll"]
> | Type | API | Esempio |
> |--|--|--|
> | POST | curl -k -d '{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json' https://<IP_ADDRESS>/external/authentication/set_password_by_admin | curl -k -d '{"admin_user":"adminUser","admin_password": 1234@abcd "","username": "myUser","new_password": abcd@1234 "}' -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/external/authentication/set_password_by_admin |

## <a name="next-steps"></a>Passaggi successivi

- [Esaminare i rilevamenti dei sensori in un inventario dei dispositivi](how-to-investigate-sensor-detections-in-a-device-inventory.md)

- [Esaminare tutti i rilevamenti dei sensori aziendali un inventario di dispositivi](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
