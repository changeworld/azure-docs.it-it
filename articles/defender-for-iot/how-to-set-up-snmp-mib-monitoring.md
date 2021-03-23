---
title: Configurare il monitoraggio MIB SNMP
description: È possibile eseguire il monitoraggio dell'integrità dei sensori tramite SNMP. Il sensore risponde alle query SNMP inviate da un server di monitoraggio autorizzato.
ms.date: 12/14/2020
ms.topic: how-to
ms.openlocfilehash: 1ba52236f65c6c5daba68c67677cdc6adfb699b4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781671"
---
# <a name="set-up-snmp-mib-monitoring"></a>Configurare il monitoraggio MIB SNMP

È possibile eseguire il monitoraggio dell'integrità dei sensori utilizzando Simple Network Management Protocol (SNMP). Il sensore risponde alle query SNMP inviate da un server di monitoraggio autorizzato. Il monitoraggio SNMP esegue il polling del sensore OID periodicamente (fino a 50 volte al secondo).

Le versioni supportate da SNMP sono SNMP v2 o SNMP v3. SNMP utilizza UDP come protocollo di trasporto con la porta 161 (SNMP).

Prima di iniziare la configurazione del monitoraggio SNMP, è necessario aprire la porta UDP 161 nel firewall.

## <a name="oids"></a>OID

| Console di gestione e sensore | OID | Formato | Descrizione |
|--|--|--|--|
| Nome Appliance | 1.3.6.1.2.1.1.5.0 | DISPLAYSTRING | Nome del dispositivo per la console di gestione locale |
| Vendor | 1.3.6.1.2.1.1.4.0 | DISPLAYSTRING | Supporto tecnico Microsoft (support.microsoft.com) |
| Piattaforma | 1.3.6.1.2.1.1.1.0 | DISPLAYSTRING | Sensore o console di gestione locale |
| Numero di serie | 1.3.6.1.4.1.9.9.53313.1 | DISPLAYSTRING | Stringa utilizzata dalla licenza |
| Versione del software | 1.3.6.1.4.1.9.9.53313.2 | DISPLAYSTRING | Stringa di versione completa di Xsense e stringa di versione completa di gestione |
| Utilizzo della CPU | 1.3.6.1.4.1.9.9.53313.3.1 | GAUGE32 | Indicazione da zero a 100 |
| Temperatura CPU | 1.3.6.1.4.1.9.9.53313.3.2 | DISPLAYSTRING | Indicazione Celsius da zero a 100 in base all'input Linux |
| Utilizzo memoria | 1.3.6.1.4.1.9.9.53313.3.3 | GAUGE32 | Indicazione da zero a 100 |
| Utilizzo disco | 1.3.6.1.4.1.9.9.53313.3.4 | GAUGE32 | Indicazione da zero a 100 |
| Stato del servizio | 1.3.6.1.4.1.9.9.53313.5 | DISPLAYSTRING | Online o offline se uno dei quattro componenti cruciali è inattivo |
| Larghezza di banda | Esterno all'ambito per 2,4 |  | Larghezza di banda ricevuta su ogni interfaccia di monitoraggio in Xsense |

   - Le chiavi non esistenti rispondono con il valore null, HTTP 200, in base [stack overflow](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200).
    
   - I MIB correlati all'hardware (utilizzo CPU, temperatura CPU, utilizzo memoria, utilizzo disco) devono essere testati su tutte le architetture e i sensori fisici. Non è previsto che la temperatura della CPU nelle macchine virtuali sia applicabile.

È possibile scaricare il log che contiene tutte le query SNMP ricevute dal sensore, inclusi i dati di connessione e i dati non elaborati del pacchetto.

Per definire il monitoraggio dello stato di SNMP v2:

1. Nel menu laterale selezionare impostazioni di **sistema**.

2. Nel riquadro **Active Discovery** selezionare **SNMP MIB Monitoring** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Modificare la finestra SNMP.":::

3. Nella sezione **host consentiti** selezionare **Aggiungi host** e immettere l'indirizzo IP del server che esegue il monitoraggio dell'integrità del sistema.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Immettere l'indirizzo IP per gli host consentiti.":::

4. Nella sezione **autenticazione** , nella casella **stringa della community SNMP v2** , immettere la stringa. La stringa comunità SNMP può contenere un massimo di 32 caratteri e includere qualsiasi combinazione di caratteri alfanumerici (lettere maiuscole, lettere minuscole e numeri). Gli spazi non sono consentiti.

5. Selezionare **Salva**.

Per definire il monitoraggio dello stato di SNMP v3:

1. Nel menu laterale selezionare impostazioni di **sistema**.

2. Nel riquadro **Active Discovery** selezionare **SNMP MIB Monitoring** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Modificare la finestra SNMP.":::

3. Nella sezione **host consentiti** selezionare **Aggiungi host** e immettere l'indirizzo IP del server che esegue il monitoraggio dell'integrità del sistema.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Immettere l'indirizzo IP per gli host consentiti.":::

4. Nella sezione **Authentication** impostare i parametri seguenti:

    | Parametro | Descrizione |
    |--|--|
    | **Nome utente** | Il nome utente SNMP può contenere un massimo di 32 caratteri e includere qualsiasi combinazione di caratteri alfanumerici (lettere maiuscole, lettere minuscole e numeri). Gli spazi non sono consentiti. <br /> <br />Il nome utente per l'autenticazione SNMP v3 deve essere configurato nel sistema e nel server SNMP. |
    | **Password** | Immettere una password di autenticazione con distinzione tra maiuscole e minuscole. La password di autenticazione può contenere da 8 a 12 caratteri e includere qualsiasi combinazione di caratteri alfanumerici (lettere maiuscole, lettere minuscole e numeri). <br /> <br/>Il nome utente per l'autenticazione SNMP v3 deve essere configurato nel sistema e nel server SNMP. |
    | **Tipo di autenticazione** | Selezionare MD5 o SHA. |
    | **Crittografia** | Selezionare DES o AES. |
    | **Chiave privata** | La chiave deve contenere esattamente otto caratteri e includere qualsiasi combinazione di caratteri alfanumerici (lettere maiuscole, lettere minuscole e numeri). |

5. Selezionare **Salva**.

## <a name="see-also"></a>Vedi anche

[Esportare i log di risoluzione dei problemi](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
