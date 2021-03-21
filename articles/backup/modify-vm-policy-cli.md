---
title: Aggiornare i criteri di backup delle macchine virtuali esistenti usando l'interfaccia della riga
description: Informazioni su come aggiornare i criteri di backup delle VM esistenti usando l'interfaccia della riga di comando di Azure.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: 33083d6585d2b9296cd184ba258b8d2143d685b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98728579"
---
# <a name="update-the-existing-vm-backup-policy-using-cli"></a>Aggiornare i criteri di backup delle macchine virtuali esistenti usando l'interfaccia della riga

È possibile usare l'interfaccia della riga di comando di Azure per aggiornare un criterio di backup di VM esistente. Questo articolo illustra come esportare i criteri esistenti in un file JSON, modificare il file e quindi usare l'interfaccia della riga di comando di Azure per aggiornare i criteri con i criteri modificati.

## <a name="modify-an-existing-policy"></a>Modificare un criterio esistente

Per modificare un criterio di backup della macchina virtuale esistente, attenersi alla procedura seguente:

1. Eseguire il comando [AZ backup policy Show](/cli/azure/backup/policy#az_backup_policy_show) per recuperare i dettagli dei criteri che si desidera aggiornare.

    Esempio:

    ```azurecli
    az backup policy show --name testing123 --resource-group rg1234 --vault-name testvault
    ```

    L'esempio precedente Mostra i dettagli per i criteri della macchina virtuale con il nome *testing123*.

    Output:

    ```json
    {
    "eTag": null,
    "id": "/Subscriptions/efgsf-123-test-subscription/resourceGroups/rg1234/providers/Microsoft.RecoveryServices/vaults/testvault/backupPolicies/testing123",
    "location": null,
    "name": "testing123",
    "properties": {
        "backupManagementType": "AzureIaasVM",
        "instantRpDetails": {
        "azureBackupRgNamePrefix": null,
        "azureBackupRgNameSuffix": null
        },
        "instantRpRetentionRangeInDays": 2,
        "protectedItemsCount": 0,
        "retentionPolicy": {
        "dailySchedule": {
            "retentionDuration": {
            "count": 180,
            "durationType": "Days"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "monthlySchedule": null,
        "retentionPolicyType": "LongTermRetentionPolicy",
        "weeklySchedule": {
            "daysOfTheWeek": [
            "Sunday"
            ],
            "retentionDuration": {
            "count": 30,
            "durationType": "Weeks"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "yearlySchedule": null
        },
        "schedulePolicy": {
        "schedulePolicyType": "SimpleSchedulePolicy",
        "scheduleRunDays": null,
        "scheduleRunFrequency": "Daily",
        "scheduleRunTimes": [
            "2020-08-03T04:30:00+00:00"
        ],
        "scheduleWeeklyFrequency": 0
        },
        "timeZone": "UTC"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
    }
    ```

1. Salvare l'output precedente in un file con estensione JSON. Ad esempio, salvarlo come *Policy.js*.
1. Aggiornare il file JSON in base ai requisiti e salvare le modifiche.

    Esempio: per aggiornare la conservazione settimanale a 60 giorni, aggiornare la sezione seguente del file JSON cambiando il conteggio in 60.

    ```json
            "retentionDuration": {
          "count": 60,
          "durationType": "Weeks"
        }

    ```

1. Salvare le modifiche.
1. Eseguire il comando [AZ backup policy set](/cli/azure/backup/policy#az_backup_policy_set) e passare il percorso completo del file JSON aggiornato come valore per il parametro **--policy** .

    ```azurecli
    az backup policy set --resource-group rg1234 --vault-name testvault --policy C:\temp2\Policy.json --name testing123
    ```

>[!NOTE]
>È anche possibile recuperare il criterio JSON di esempio eseguendo il comando [AZ backup Policy Get-default-for-VM](/cli/azure/backup/policy#az_backup_policy_get_default_for_vm) .

## <a name="next-steps"></a>Passaggi successivi

- [Gestire i backup delle VM di Azure con il servizio backup di Azure](backup-azure-manage-vms.md)