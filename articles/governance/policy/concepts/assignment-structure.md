---
title: Dettagli della struttura di assegnazione dei criteri
description: Descrive la definizione di assegnazione dei criteri usata Criteri di Azure per correlare le definizioni dei criteri e i parametri alle risorse per la valutazione.
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 9de210b17264330e79ab5978a449e7a494054be2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535867"
---
# <a name="azure-policy-assignment-structure"></a>Struttura di assegnazione di Criteri di Azure

Le assegnazioni di criteri vengono usate Criteri di Azure per definire le risorse a cui vengono assegnati i criteri o le iniziative. L'assegnazione dei criteri può determinare i valori dei parametri per il gruppo di risorse in fase di assegnazione, rendendo possibile il riutilizzo delle definizioni dei criteri che rilascino le stesse proprietà delle risorse con esigenze di conformità diverse.

Usare JSON per creare un'assegnazione di criteri. L'assegnazione dei criteri contiene elementi per:

- nome visualizzato
- description
- metadata
- modalità di imposizione
- ambiti esclusi
- definizione dei criteri
- messaggi di non conformità
- parametri

Ad esempio, il codice JSON seguente mostra un'assegnazione di criteri in _modalità DoNotEnforce_ con parametri dinamici:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "nonComplianceMessages": [
            {
                "message": "Resource names must start with 'DeptA' and end with '-LC'."
            }
        ],
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Tutti gli esempi di Criteri di Azure sono disponibili in [Esempi di Criteri di Azure](../samples/index.md).

## <a name="display-name-and-description"></a>Nome visualizzato e descrizione

Usare **displayName e** **description per** identificare l'assegnazione dei criteri e fornire il contesto per l'uso con il set specifico di risorse. **displayName** ha una lunghezza massima di _128_ caratteri e **description** una lunghezza massima di _512_ caratteri.

## <a name="metadata"></a>Metadati

La proprietà `metadata` facoltativa archivia informazioni sull'assegnazione dei criteri. I clienti possono definire qualsiasi proprietà e valore utile per l'organizzazione in `metadata` . Tuttavia, esistono alcune _proprietà comuni_ usate da Criteri di Azure. Ogni `metadata` proprietà ha un limite di 1024 caratteri.

### <a name="common-metadata-properties"></a>Proprietà comuni dei metadati

- `assignedBy` (stringa): nome descrittivo dell'entità di sicurezza che ha creato l'assegnazione.
- `createdBy` (stringa): GUID dell'entità di sicurezza che ha creato l'assegnazione.
- `createdOn` (stringa): formato DateTime ISO 8601 universale dell'ora di creazione dell'assegnazione.
- `parameterScopes` (oggetto): raccolta di coppie chiave-valore in cui la chiave corrisponde a un nome di parametro configurato [strongType](./definition-structure.md#strongtype) e il valore definisce l'ambito della risorsa usato nel portale per fornire l'elenco delle risorse disponibili tramite la corrispondenza _di strongType_. Il portale imposta questo valore se l'ambito è diverso dall'ambito di assegnazione. Se impostato, una modifica dell'assegnazione dei criteri nel portale imposta automaticamente l'ambito per il parametro su questo valore. Tuttavia, l'ambito non è bloccato al valore e può essere modificato in un altro ambito.

  L'esempio seguente di è per un parametro `parameterScopes` _strongType_ denominato **backupPolicyId** che imposta un ambito per la selezione delle risorse quando l'assegnazione viene modificata nel portale.

  ```json
  "metadata": {
      "parameterScopes": {
          "backupPolicyId": "/subscriptions/{SubscriptionID}/resourcegroups/{ResourceGroupName}"
      }
  }
  ```

- `updatedBy` (stringa): nome descrittivo dell'entità di sicurezza che ha aggiornato l'assegnazione, se presente.
- `updatedOn` (stringa): formato DateTime ISO 8601 universale dell'ora di aggiornamento dell'assegnazione, se presente.

## <a name="enforcement-mode"></a>Modalità di imposizione

La **proprietà enforcementMode** offre ai clienti la possibilità di testare il risultato di un criterio sulle risorse esistenti senza avviare l'effetto dei criteri o attivare le voci nel [log attività di Azure.](../../../azure-monitor/essentials/platform-logs-overview.md) Questo scenario viene comunemente definito "What If" e si allinea alle procedure di distribuzione sicure. **enforcementMode è** diverso [dall'effetto Disabilitato,](./effects.md#disabled) in quanto tale effetto impedisce che la valutazione delle risorse avvenga affatto.

Questa proprietà ha i valori seguenti:

|Modalità |Valore JSON |Tipo |Correggere manualmente |Voce del log attività |Descrizione |
|-|-|-|-|-|-|
|Abilitato |Predefinito |string |Sì |Sì |L'effetto dei criteri viene applicato durante la creazione o l'aggiornamento delle risorse. |
|Disabled |DoNotEnforce |string |Sì |No | L'effetto dei criteri non viene applicato durante la creazione o l'aggiornamento delle risorse. |

Se **enforcementMode non** viene specificato in una definizione di criteri o iniziative, viene usato _il_ valore Predefinito. [Le attività di correzione](../how-to/remediate-resources.md) possono essere avviate per i criteri [deployIfNotExists,](./effects.md#deployifnotexists) anche quando **enforcementMode** è impostato su _DoNotEnforce_.

## <a name="excluded-scopes"></a>Ambiti esclusi

**L'ambito** dell'assegnazione include tutti i contenitori di risorse figlio e le risorse figlio. Se a un contenitore di risorse figlio o a una  risorsa figlio non deve essere applicata la definizione, ognuno può essere escluso dalla valutazione impostando **notScopes**. Questa proprietà è una matrice per consentire l'esclusione di uno o più contenitori di risorse o risorse dalla valutazione. **notScopes** può essere aggiunto o aggiornato dopo la creazione dell'assegnazione iniziale.

> [!NOTE]
> Una _risorsa_ esclusa è diversa da una _risorsa esenta._ Per altre informazioni, vedere [Informazioni sull'ambito in Criteri di Azure](./scope.md).

## <a name="policy-definition-id"></a>ID definizione criteri

Questo campo deve essere il nome del percorso completo di una definizione di criteri o di un'iniziativa.
`policyDefinitionId` è una stringa e non una matrice. Se vengono spesso assegnati più criteri, è consigliabile usare [un'iniziativa.](./initiative-definition-structure.md)

## <a name="non-compliance-messages"></a>Messaggi di non conformità

Per impostare un messaggio personalizzato che descriva il motivo per cui una risorsa non è conforme alla definizione dei criteri o dell'iniziativa, impostare `nonComplianceMessages` nella definizione di assegnazione. Questo nodo è una matrice `message` di voci. Questo messaggio personalizzato è in aggiunta al messaggio di errore predefinito per la non conformità ed è facoltativo.

> [!IMPORTANT]
> I messaggi personalizzati per la non conformità sono supportati solo nelle definizioni o nelle [iniziative con Resource Manager definizioni delle modalità.](./definition-structure.md#resource-manager-modes)

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

Se l'assegnazione è per un'iniziativa, è possibile configurare messaggi diversi per ogni definizione di criteri nell'iniziativa. I messaggi usano il `policyDefinitionReferenceId` valore configurato nella definizione dell'iniziativa. Per informazioni dettagliate, vedere [Proprietà delle definizioni dei criteri](./initiative-definition-structure.md#policy-definition-properties).

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    },
    {
        "message": "Message for just this policy definition by reference ID",
        "policyDefinitionReferenceId": "10420126870854049575"
    }
]
```

## <a name="parameters"></a>Parametri

Questo segmento dell'assegnazione di criteri fornisce i valori per i parametri definiti nella definizione dei criteri o nella definizione [dell'iniziativa](./definition-structure.md#parameters). Questa progettazione consente di riutilizzare una definizione di criteri o di iniziativa con risorse diverse, ma verifica la presenza di valori o risultati aziendali diversi.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

In questo esempio i parametri definiti in precedenza nella definizione dei criteri sono `prefix` e `suffix` . Questa particolare assegnazione di criteri `prefix` imposta **su DeptA** e `suffix` su **-LC.** La stessa definizione di criteri è riutilizzabile con un set diverso di parametri per un reparto diverso, riducendo la duplicazione e la complessità delle definizioni dei criteri, offrendo al tempo stesso flessibilità.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [struttura delle definizioni dei criteri](./definition-structure.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Leggere le informazioni su come [ottenere dati sulla conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
