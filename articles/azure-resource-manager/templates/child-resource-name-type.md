---
title: Risorse figlio nei modelli
description: Viene descritto come impostare il nome e il tipo per le risorse figlio in un modello di Azure Resource Manager (modello ARM).
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: 408914fd309676da36904a364f905a8ee809d648
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934306"
---
# <a name="set-name-and-type-for-child-resources"></a>Imposta il nome e il tipo per le risorse figlio

Le risorse figlio sono risorse che esistono solo nel contesto di un'altra risorsa. Ad esempio, un' [estensione della macchina virtuale](/azure/templates/microsoft.compute/virtualmachines/extensions) non può esistere senza una [macchina virtuale](/azure/templates/microsoft.compute/virtualmachines). La risorsa di estensione è un elemento figlio della macchina virtuale.

Ogni risorsa padre accetta solo determinati tipi di risorse come risorse figlio. Il tipo di risorsa per la risorsa figlio include il tipo di risorsa per la risorsa padre. Ad esempio, `Microsoft.Web/sites/config` e `Microsoft.Web/sites/extensions` sono entrambe risorse figlio della `Microsoft.Web/sites` . I tipi di risorse accettate sono specificati nel [schema del modello](https://github.com/Azure/azure-resource-manager-schemas) della risorsa padre.

In un modello di Azure Resource Manager (modello ARM), è possibile specificare la risorsa figlio all'interno della risorsa padre o all'esterno della risorsa padre. Nell'esempio seguente viene illustrata la risorsa figlio inclusa nella proprietà Resources della risorsa padre.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

Le risorse figlio possono essere solo definite da cinque livelli.

Nell'esempio seguente viene illustrata la risorsa figlio al di fuori della risorsa padre. Questo approccio può essere usato se la risorsa padre non viene distribuita nello stesso modello o se si vuole usare [Copy](copy-resources.md) per creare più di una risorsa figlio.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

I valori specificati per il nome della risorsa e il tipo variano a seconda che la risorsa figlio sia definita all'interno o all'esterno della risorsa padre.

## <a name="within-parent-resource"></a>All'interno della risorsa padre

Quando viene definito nel tipo di risorsa padre, i valori del tipo e del nome vengono formattati come una singola parola senza barre.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

Nell'esempio seguente viene illustrata una rete virtuale e una subnet. Si noti che la subnet è inclusa nell'array di risorse per la rete virtuale. Il nome è impostato su **Subnet1** e il tipo è impostato su **Subnet**. La risorsa figlio è contrassegnata come dipendente dalla risorsa padre perché la risorsa padre deve esistere prima che la risorsa figlio possa essere distribuita.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

Il tipo di risorsa completo è ancora `Microsoft.Network/virtualNetworks/subnets` . Non si specifica `Microsoft.Network/virtualNetworks/` perché viene ottenuto dal tipo della risorsa padre.

Il nome della risorsa figlio è impostato su **Subnet1** , ma il nome completo include il nome padre. Non viene fornito **VNet1** perché si presuppone dalla risorsa padre.

## <a name="outside-parent-resource"></a>Risorsa padre esterna

Quando viene definito all'esterno della risorsa padre, si formatta il tipo e con le barre per includere il tipo e il nome padre.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

Nell'esempio seguente vengono illustrate una rete virtuale e una subnet che sono entrambe definite a livello di radice. Si noti che la subnet non è inclusa nell'array di risorse per la rete virtuale. Il nome è impostato su **VNet1/Subnet1** e il tipo è impostato su `Microsoft.Network/virtualNetworks/subnets` . La risorsa figlio è contrassegnata come dipendente dalla risorsa padre perché la risorsa padre deve esistere prima che la risorsa figlio possa essere distribuita.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla creazione di modelli ARM, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).

* Per informazioni sul formato del nome della risorsa quando si fa riferimento alla risorsa, vedere la [funzione Reference](template-functions-resource.md#reference).
