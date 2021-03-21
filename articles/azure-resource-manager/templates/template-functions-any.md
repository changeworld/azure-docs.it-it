---
title: Funzioni modello-any
description: Viene descritta la funzione any disponibile in bicipite per convertire i tipi.
ms.topic: conceptual
author: tfitzmac
ms.author: tomfitz
ms.service: azure-resource-manager
ms.subservice: templates
ms.date: 03/02/2021
ms.openlocfilehash: b0cb51c9a79d1100de7f1ef32fe326eddcdd6dcc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745854"
---
# <a name="any-function-for-bicep"></a>Qualsiasi funzione per bicipite

Bicipite supporta una funzione chiamata `any()` per risolvere gli errori di tipo nel sistema di tipi bicipite. Usare questa funzione quando il formato del valore fornito non corrisponde a quello previsto dal sistema di tipi. Se, ad esempio, la proprietà richiede un numero ma è necessario specificarla come stringa, ad esempio `'0.5'` . Utilizzare la `any()` funzione per disattivare l'errore segnalato dal sistema di tipi.

Questa funzione non esiste nel runtime del modello di Azure Resource Manager. Viene usato solo da bicipite e non viene emesso nel codice JSON per il modello compilato.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="any"></a>any

`any(value)`

Restituisce un valore compatibile con qualsiasi tipo di dati.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| Valore | Sì | tutti i tipi | Valore da convertire in un tipo compatibile. |

### <a name="return-value"></a>Valore restituito

Il valore in un form compatibile con qualsiasi tipo di dati.

### <a name="examples"></a>Esempio

Il modello di esempio seguente mostra come usare la `any()` funzione per fornire valori numerici come stringhe.

```bicep
resource wpAci 'microsoft.containerInstance/containerGroups@2019-12-01' = {
  name: 'wordpress-containerinstance'
  location: location
  properties: {
    containers: [
      {
        name: 'wordpress'
        properties: {
          ...
          resources: {
            requests: {
              cpu: any('0.5')
              memoryInGB: any('0.7')
            }
          }
        }
      }
    ]
  }
}
```

La funzione funziona su qualsiasi valore assegnato in bicipite. Nell'esempio seguente viene usato `any()` con un'espressione ternaria come argomento.  

```bicep
publicIPAddress: any((pipId == '') ? null : {
  id: pipId
})
```

## <a name="next-steps"></a>Passaggi successivi

Per gli utilizzi più complessi della `any()` funzione, vedere gli esempi seguenti:

* [Risorse figlio che richiedono nomi specifici](https://github.com/Azure/bicep/blob/main/docs/examples/201/api-management-create-all-resources/main.bicep#L246)
* [Proprietà di risorsa non definita nel tipo della risorsa, anche se esiste](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)

