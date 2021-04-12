---
title: Gestire gli aggiornamenti del cluster Service Fabric
description: Gestire il momento e la modalità di aggiornamento del runtime del cluster Service Fabric
ms.topic: how-to
ms.date: 03/26/2021
ms.openlocfilehash: 98c3300e5cc51c32d894397839879e25190d979b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731168"
---
# <a name="manage-service-fabric-cluster-upgrades"></a>Gestire gli aggiornamenti del cluster Service Fabric

Un cluster di Azure Service Fabric è una risorsa di cui si è proprietari, ma è parzialmente gestito da Microsoft. Ecco come gestire quando e in che modo Microsoft aggiorna il cluster di Azure Service Fabric.

Per ulteriori informazioni sui concetti e i processi di aggiornamento del cluster, vedere [aggiornamento e aggiornamento dei cluster di Azure Service Fabric](service-fabric-cluster-upgrade.md)

## <a name="set-upgrade-mode"></a>Impostare la modalità di aggiornamento

È possibile impostare il cluster per ricevere gli aggiornamenti automatici del Service Fabric non appena vengono rilasciati da Microsoft oppure è possibile scegliere manualmente da un elenco di versioni attualmente supportate impostando la modalità di aggiornamento per il cluster. Questa operazione può essere eseguita tramite il controllo *modalità di aggiornamento dell'infrastruttura* in portale di Azure o l' `upgradeMode` impostazione nel modello di distribuzione del cluster.

### <a name="azure-portal"></a>Portale di Azure

Con portale di Azure è possibile scegliere tra gli aggiornamenti automatici o manuali durante la creazione di un nuovo cluster di Service Fabric.

:::image type="content" source="media/service-fabric-cluster-upgrade/portal-new-cluster-upgrade-mode.png" alt-text="Scegliere tra gli aggiornamenti automatici o manuali quando si crea un nuovo cluster in portale di Azure dalle opzioni ' avanzate '":::

È anche possibile passare da aggiornamenti automatici o manuali dalla sezione **aggiornamenti dell'infrastruttura** di una risorsa cluster esistente.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Selezionare aggiornamenti automatici o manuali nella sezione ' aggiornamenti dell'infrastruttura ' della risorsa cluster in portale di Azure":::

### <a name="manual-upgrades-with-azure-portal"></a>Aggiornamenti manuali con portale di Azure

Quando si seleziona l'opzione di aggiornamento manuale, è sufficiente selezionare dall'elenco a discesa versioni disponibili per avviare un aggiornamento, quindi *salvare*. A questo punto, l'aggiornamento del cluster viene immediatamente interrotto.

I [criteri di integrità del cluster](#custom-policies-for-manual-upgrades) (una combinazione dell'integrità del nodo e dell'integrità di tutte le applicazioni in esecuzione nel cluster) vengono rispettati durante l'aggiornamento. Se i criteri di integrità del cluster non vengono soddisfatti, verrà eseguito il rollback dell'aggiornamento.

Dopo aver risolto i problemi che hanno determinato il rollback, è necessario avviare di nuovo l'aggiornamento, seguendo la stessa procedura descritta in precedenza.

### <a name="resource-manager-template"></a>Modello di Resource Manager

Per modificare la modalità di aggiornamento del cluster usando un modello di Gestione risorse, specificare *automatico* o *manuale* per la  `upgradeMode` proprietà della definizione di risorsa *Microsoft. ServiceFabric/Clusters* . Se si scelgono gli aggiornamenti manuali, impostare anche `clusterCodeVersion` su una [versione di Fabric attualmente supportata](#query-for-supported-cluster-versions).

:::image type="content" source="./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG" alt-text="Screenshot mostra un modello, che è rientrato in testo non crittografato per riflettere la struttura. Le proprietà' clusterCodeVersion ' è upgradeMode ' sono evidenziate.":::

Al completamento della distribuzione del modello, verranno applicate le modifiche alla modalità di aggiornamento del cluster. Se il cluster è in modalità manuale, l'aggiornamento del cluster verrà eseguito automaticamente.

I [criteri di integrità del cluster](#custom-policies-for-manual-upgrades) (una combinazione dell'integrità del nodo e dell'integrità di tutte le applicazioni in esecuzione nel cluster) vengono rispettati durante l'aggiornamento. Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento.

Dopo aver risolto i problemi che hanno determinato il rollback, è necessario avviare di nuovo l'aggiornamento, seguendo la stessa procedura descritta in precedenza.

## <a name="wave-deployment-for-automatic-upgrades"></a>Distribuzione Wave per aggiornamenti automatici

Con la modalità di aggiornamento automatico, è possibile abilitare il cluster per la distribuzione Wave. Con la distribuzione Wave è possibile creare una pipeline per l'aggiornamento dei cluster di test, di gestione temporanea e di produzione in sequenza, separati da "tempo di preparazione" predefinito per convalidare le prossime versioni di Service Fabric prima che i cluster di produzione vengano aggiornati.

### <a name="enable-wave-deployment"></a>Abilita distribuzione Wave

> [!NOTE]
> Per la distribuzione Wave è richiesta la `2020-12-01-preview` versione dell'API (o versioni successive) per la risorsa *Microsoft. ServiceFabric/Clusters* .

Per abilitare la distribuzione Wave per l'aggiornamento automatico, determinare innanzitutto quale Wave assegnare il cluster:

* **Wave 0** ( `Wave0` ): i cluster vengono aggiornati non appena viene rilasciata una nuova compilazione Service Fabric. Progettato per i cluster di test/sviluppo.
* **Wave 1** ( `Wave1` ): i cluster vengono aggiornati una settimana (sette giorni) dopo il rilascio di una nuova compilazione. Progettato per i cluster pre-produzione/gestione temporanea.
* **Wave 2** ( `Wave2` ): i cluster vengono aggiornati due settimane (14 giorni) dopo il rilascio di una nuova compilazione. Progettato per i cluster di produzione.

Quindi, aggiungere semplicemente una `upgradeWave` proprietà al modello di risorsa cluster con uno dei valori Wave elencati sopra. Verificare che la versione dell'API della risorsa cluster sia `2020-12-01-preview` o successiva.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "fabricSettings": [...],
        "managementEndpoint": ...,
        "nodeTypes": [...],
        "provisioningState": ...,
        "reliabilityLevel": ...,
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
       ...
```

Una volta distribuito il modello aggiornato, il cluster verrà registrato nell'onda specificata per il periodo di aggiornamento successivo e successivamente.

È possibile eseguire la [registrazione per le notifiche di posta elettronica](#register-for-notifications) con collegamenti a ulteriori informazioni in caso di errore dell'aggiornamento del cluster.

### <a name="register-for-notifications"></a>Registrazione per le notifiche

È possibile registrarsi per le notifiche in caso di errore di aggiornamento del cluster. Verrà inviato un messaggio di posta elettronica agli indirizzi di posta elettronica designati con ulteriori dettagli sull'errore di aggiornamento e i collegamenti a ulteriori informazioni.

> [!NOTE]
> La registrazione nella distribuzione Wave non è necessaria per ricevere notifiche per gli errori di aggiornamento.

Per registrare le notifiche, aggiungere una `notifications` sezione al modello di risorsa cluster e designare uno o più indirizzi di posta elettronica (*ricevitori*) per ricevere le notifiche:

```json
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        "notifications": [
        {
            "isEnabled": true,
            "notificationCategory": "WaveProgress",
            "notificationLevel": "Critical",
            "notificationTargets": [
            {
                "notificationChannel": "EmailUser",
                "receivers": [
                    "devops@contoso.com"
                ]
            }]
        }]
```

Una volta distribuito il modello aggiornato, verranno registrate le notifiche degli errori di aggiornamento.

## <a name="custom-policies-for-manual-upgrades"></a>Criteri personalizzati per gli aggiornamenti manuali

È possibile specificare criteri di integrità personalizzati per gli aggiornamenti manuali del cluster. Questi criteri vengono applicati ogni volta che si seleziona una nuova versione di runtime, che attiva il sistema per avviare l'aggiornamento del cluster. Se non si esegue l'override dei criteri, vengono usati quelli predefiniti.

È possibile specificare i criteri di integrità personalizzati o rivedere le impostazioni correnti nella sezione **aggiornamenti dell'infrastruttura** della risorsa cluster in portale di Azure selezionando l'opzione *personalizzata* per i criteri di **aggiornamento**.

:::image type="content" source="./media/service-fabric-cluster-upgrade/custom-upgrade-policy.png" alt-text="Selezionare l'opzione ' Custom ' per i criteri di aggiornamento nella sezione ' aggiornamenti dell'infrastruttura ' della risorsa cluster in portale di Azure per impostare criteri di integrità personalizzati durante l'aggiornamento":::

## <a name="query-for-supported-cluster-versions"></a>Eseguire una query per le versioni del cluster supportate

È possibile usare l' [API REST di Azure](/rest/api/azure/) per elencare tutte le versioni di Service Fabric runtime disponibili ([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list)) disponibili per il percorso specificato e la sottoscrizione.

Per ulteriori informazioni sulle versioni e sui sistemi operativi supportati, è anche possibile fare riferimento a [Service Fabric versioni](service-fabric-versions.md) .

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2018-02-01

"value": [
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
    "name": "5.0.1427.9490",
    "type": "Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.0.1427.9490",
      "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
    "name": "5.1.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.1.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
    "name": "4.4.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "4.4.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Linux"
    }
  }
]
}
```

`supportExpiryUtc`Nel report di output quando una determinata versione sta per scadere o è scaduta. Le versioni più recenti non avranno una data valida, bensì un valore di *9999-12-31T23:59:59.9999999*, che significa semplicemente che la data di scadenza non è ancora stata impostata.


## <a name="next-steps"></a>Passaggi successivi

* [Gestire gli aggiornamenti di Service Fabric](service-fabric-cluster-upgrade-version-azure.md)
* Personalizzare le [impostazioni del cluster di Service Fabric](service-fabric-cluster-fabric-settings.md)
* [Ridimensionare il cluster all'interno e all'esterno](service-fabric-cluster-scale-in-out.md)
* Informazioni sugli [aggiornamenti delle applicazioni](service-fabric-application-upgrade.md)


<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
