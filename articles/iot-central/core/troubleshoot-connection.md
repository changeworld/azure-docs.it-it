---
title: Risolvere i problemi relativi alle connessioni dei dispositivi Azure IoT Central | Microsoft Docs
description: Risolvere i problemi relativi al motivo per cui non vengono visualizzati i dati dai dispositivi IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: 494608f9dd8fbf986dcda6eeb782a64f6a2ca008
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378568"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Risolvere i problemi relativi al perché i dati dei dispositivi non vengono visualizzati in Azure IoT Central

Questo documento consente agli sviluppatori di dispositivi di scoprire perché i dati inviati dai dispositivi IoT Central potrebbero non essere visualizzati nell'applicazione.

Esistono due aree principali da analizzare:

- Problemi di connettività dei dispositivi
  - Problemi di autenticazione, ad esempio il dispositivo ha credenziali non valide
  - Problemi relativi alla connettività di rete
  - Il dispositivo non è approvato o bloccato
- Problemi di forma del payload del dispositivo

Questa guida alla risoluzione dei problemi è incentrata sui problemi di connettività dei dispositivi e sui problemi di forma del payload del dispositivo.

## <a name="device-connectivity-issues"></a>Problemi di connettività dei dispositivi

Questa sezione consente di determinare se i dati stanno per raggiungere IoT Central.

Se non è già stato fatto, installare lo strumento e `az cli` `azure-iot` l'estensione.

Per informazioni su come installare , vedere Installare l'interfaccia della `az cli` riga di comando di [Azure.](/cli/azure/install-azure-cli)

Per [installare](/cli/azure/azure-cli-reference-for-IoT#extension-reference-installation) `azure-iot` l'estensione, eseguire il comando seguente:

```azurecli
az extension add --name azure-iot
```

> [!NOTE]
> Potrebbe essere richiesto di installare la libreria la `uamqp` prima volta che si esegue un comando di estensione.

Dopo aver installato l'estensione, avviare il dispositivo per verificare se i messaggi inviati stanno per `azure-iot` essere IoT Central.

Usare i comandi seguenti per accedere alla sottoscrizione in cui si trova l IoT Central app IoT Central sottoscrizione:

```azurecli
az login
az set account --subscription <your-subscription-id>
```

Per monitorare i dati di telemetria inviati dal dispositivo, usare il comando seguente:

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

Se il dispositivo si è connesso correttamente IoT Central, viene visualizzato un output simile al seguente:

```output
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

Per monitorare gli aggiornamenti delle proprietà che il dispositivo sta scambiando con IoT Central, usare il comando di anteprima seguente:

```azurecli
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

Se il dispositivo invia correttamente gli aggiornamenti delle proprietà, viene visualizzato un output simile al seguente:

```output
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

Se i dati vengono visualizzati nel terminale, i dati vengono visualizzati fino all'IoT Central applicazione.

Se i dati non vengono visualizzati dopo alcuni minuti, provare a premere il tasto o sulla tastiera, nel caso in cui `Enter` `return` l'output sia bloccato.

Se nel terminale non vengono ancora visualizzati dati, è probabile che il dispositivo presenti problemi di connettività di rete o che non invii correttamente i dati IoT Central.

### <a name="check-the-provisioning-status-of-your-device"></a>Controllare lo stato di provisioning del dispositivo

Se i dati non vengono visualizzati nel monitoraggio, controllare lo stato di provisioning del dispositivo eseguendo il comando seguente:

```azurecli
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

L'output seguente mostra un esempio di dispositivo a cui non è possibile connettersi:

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| Stato del provisioning dei dispositivi | Descrizione | Possibile mitigazione |
| - | - | - |
| Sottoposto a provisioning | Nessun problema immediatamente riconoscibile. | N/D |
| Registrato | Il dispositivo non è ancora connesso a IoT Central. | Controllare i log del dispositivo per verificare la presenza di problemi di connettività. |
| Bloccato | Il dispositivo non è in grado di connettersi IoT Central. | Il dispositivo non è in grado di connettersi all IoT Central appalto. Sbloccare il dispositivo in IoT Central e riprovare. Per altre informazioni, vedere [Bloccare i dispositivi.](concepts-get-connected.md#device-status-values) |
| Non approvata | Il dispositivo non è approvato. | Il dispositivo non è approvato per connettersi all'applicazione IoT Central richiesta. Approvare il dispositivo in IoT Central e riprovare. Per altre informazioni, vedere [Approvare i dispositivi](concepts-get-connected.md#device-registration) |
| Nonssociated | Il dispositivo non è associato a un modello di dispositivo. | Associare il dispositivo a un modello di dispositivo in modo che IoT Central sia in grado di analizzare i dati. |

Altre informazioni sui [codici di stato del dispositivo.](concepts-get-connected.md#device-status-values)

### <a name="error-codes"></a>Codici di errore

Se non è ancora possibile diagnosticare il motivo per cui i dati non vengono visualizzati in , il passaggio successivo consiste nel cercare i codici di errore `monitor-events` segnalati dal dispositivo.

Avviare una sessione di debug nel dispositivo o raccogliere i log dal dispositivo. Verificare la presenza di eventuali codici di errore segnalati dal dispositivo.

Le tabelle seguenti illustrano i codici di errore comuni e le possibili azioni da mitigare.

Se si verificano problemi relativi al flusso di autenticazione:

| Codice di errore | Descrizione | Possibile mitigazione |
| - | - | - |
| 400 | Il corpo della richiesta non è valido. Ad esempio, non può essere analizzato o l'oggetto non può essere convalidato. | Assicurarsi di inviare il corpo della richiesta corretto come parte del flusso di attestazione o usare un SDK per dispositivi. |
| 401 | Il token di autorizzazione non può essere convalidato. Ad esempio, è scaduto o non si applica all'URI della richiesta. Questo codice di errore viene restituito anche ai dispositivi come parte del flusso di attestazione TPM. | Assicurarsi che il dispositivo abbia le credenziali corrette. |
| 404 | L'istanza del servizio Device Provisioning o una risorsa, ad esempio una registrazione, non esiste. | [Creare un ticket con il supporto tecnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 412 | nella richiesta non corrisponde a della risorsa esistente, come `ETag` da `ETag` RFC7232. | [Stendiamo un ticket con il supporto tecnico.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) |
| 429 | Le operazioni vengono limitate dal servizio. Per i limiti del servizio specifici, vedere Limiti del servizio [Device Provisioning in hub IoT.](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits) | Ridurre la frequenza dei messaggi, suddividere le responsabilità tra più dispositivi. |
| 500 | An internal error occurred. | [Stendiamo un ticket con](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) il supporto tecnico per verificare se possono essere di aiuto. |

### <a name="file-upload-error-codes"></a>Codici di errore di caricamento file

Di seguito è riportato un elenco di codici di errore comuni che possono essere visualizzati quando un dispositivo tenta di caricare un file nel cloud. Tenere presente che prima che il dispositivo possa caricare un file, è necessario configurare i [caricamenti di file del dispositivo](howto-configure-file-uploads.md) nell'applicazione.

| Codice di errore | Descrizione | Possibile mitigazione |
| - | - | - |
| 403006  | È stato superato il numero di operazioni simultanee di caricamento file. Ogni client del dispositivo è limitato a 10 caricamenti di file simultanei. | Assicurarsi che il dispositivo inseri una notifica IoT Central che l'operazione di caricamento file è stata completata. Se non funziona, provare a ridurre il timeout della richiesta. |

## <a name="payload-shape-issues"></a>Problemi di forma del payload

Dopo aver stabilito che il dispositivo sta inviando dati IoT Central, il passaggio successivo consiste nell'assicurarsi che il dispositivo invii i dati in un formato valido.

Esistono due categorie principali di problemi comuni che causano la non visualizzazione dei dati del dispositivo IoT Central:

- Mancata corrispondenza tra modello di dispositivo e dati del dispositivo:
  - Mancata corrispondenza nella denominazione, ad esempio errori di digitazione o di corrispondenza tra maiuscole e minuscole.
  - Proprietà non modellate in cui lo schema non è definito nel modello di dispositivo.
  - Mancata corrispondenza dello schema, ad esempio un tipo definito nel modello come `boolean` , ma i dati sono una stringa.
  - Lo stesso nome di telemetria è definito in più interfacce, ma il dispositivo non è IoT Plug and Play conforme.
- La forma dei dati non è valida per JSON. Per altre informazioni, vedere [Telemetria, payload di proprietà e comandi.](concepts-telemetry-properties-commands.md)

Per rilevare le categorie in cui si trova il problema, eseguire il comando più appropriato per lo scenario:

- Per convalidare i dati di telemetria, usare il comando di anteprima:

    ```azurecli
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- Per convalidare gli aggiornamenti delle proprietà, usare il comando preview

    ```azurecli
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

Potrebbe essere richiesto di installare la libreria `uamqp` la prima volta che si esegue un `validate` comando.

L'output seguente mostra messaggi di errore e di avviso di esempio dal comando validate:

```output
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

Se si preferisce usare un'interfaccia utente grafica, usare la IoT Central **dati** non elaborati per verificare se un elemento non è modellato. La **visualizzazione dati** non elaborati non rileva se il dispositivo sta inviando codice JSON in formato non valido.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="Screenshot della visualizzazione Dati non elaborati":::

Dopo aver rilevato il problema, potrebbe essere necessario aggiornare il firmware del dispositivo o creare un nuovo modello di dispositivo che modella i dati precedentemente non modellati.

Se si è scelto di creare un nuovo modello che modella correttamente i dati, eseguire la migrazione dei dispositivi dal modello precedente al nuovo modello. Per altre informazioni, vedere [Gestire i dispositivi nell'applicazione Azure IoT Central.](howto-manage-devices.md)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN su Azure Stack Overflow.](https://azure.microsoft.com/support/community/) In alternativa, è possibile file di [supporto tecnico di Azure ticket](https://portal.azure.com/#create/Microsoft.Support).

Per altre informazioni, vedere Supporto [Azure IoT e opzioni della Guida.](../../iot-fundamentals/iot-support-help.md)
