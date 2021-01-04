---
title: Imposta ordine di distribuzione per le risorse
description: Viene descritto come impostare una risorsa come dipendente da un'altra risorsa durante la distribuzione. Le dipendenze garantiscono che le risorse vengano distribuite nell'ordine corretto.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: a96dca0ab30d0baee2688427d78867ea128e673a
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97722012"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Definire l'ordine per la distribuzione delle risorse nei modelli ARM

Quando si distribuiscono le risorse, potrebbe essere necessario assicurarsi che esistano alcune risorse prima di altre risorse. Ad esempio, è necessario un server SQL logico prima di distribuire un database. Questa relazione viene stabilita contrassegnando una risorsa come dipendente dall'altra risorsa. Usare l'elemento **dependsOn** per definire una dipendenza esplicita. Usare le funzioni **Reference** o **List** per definire una dipendenza implicita.

Resource Manager valuta le dipendenze tra le risorse e le distribuisce in base all'ordine di dipendenza. Quando le risorse non sono interdipendenti, Resource Manager le distribuisce in parallelo. La definizione delle dipendenze è necessaria solo per le risorse distribuite nello stesso modello.

## <a name="dependson"></a>dependsOn

All'interno del modello, l'elemento dependsOn consente di definire una risorsa come dipendente da una o più risorse. Il valore è una matrice JSON di stringhe, ognuna delle quali è un nome o un ID di risorsa. La matrice può includere risorse [distribuite in modo condizionale](conditional-resource-deployment.md). Quando una risorsa condizionale non viene distribuita, Azure Resource Manager la rimuove automaticamente dalle dipendenze richieste.

Nell'esempio seguente viene illustrata un'interfaccia di rete che dipende da una rete virtuale, un gruppo di sicurezza di rete e un indirizzo IP pubblico. Per il modello completo, vedere [il modello di avvio rapido per una VM Linux](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-simple-linux/azuredeploy.json).

```json
{
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-06-01",
    "name": "[variables('networkInterfaceName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
      "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
      "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
      "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
    ],
    ...
}
```

Anche se si potrebbe essere propensi a usare dependsOn per mappare le relazioni tra le risorse, è importante comprendere il motivo per cui si esegue tale operazione. Per documentare la modalità di interconnessione delle risorse, dependsOn non rappresenta l'approccio corretto. Non è possibile eseguire query per ottenere le risorse definite nell'elemento dependsOn dopo la distribuzione. L'impostazione di dipendenze non necessarie rallenta i tempi di distribuzione perché Gestione risorse non possono distribuire tali risorse in parallelo.

## <a name="child-resources"></a>Risorse figlio

Una dipendenza di distribuzione implicita non viene creata automaticamente tra una [risorsa figlio](child-resource-name-type.md) e la risorsa padre. Se è necessario distribuire la risorsa figlio dopo la risorsa padre, impostare la proprietà dependsOn.

Nell'esempio seguente vengono illustrati un server e un database SQL logici. Si noti che viene definita una dipendenza esplicita tra il database e il server, anche se il database è un figlio del server.

```json
"resources": [
  {
    "type": "Microsoft.Sql/servers",
    "apiVersion": "2020-02-02-preview",
    "name": "[parameters('serverName')]",
    "location": "[parameters('location')]",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "type": "databases",
        "apiVersion": "2020-08-01-preview",
        "name": "[parameters('sqlDBName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
          },
        "dependsOn": [
          "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
        ]
      }
    ]
  }
]
```

Per il modello completo, vedere il [modello di avvio rapido per il database SQL di Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-sql-database/azuredeploy.json).

## <a name="reference-and-list-functions"></a>funzioni reference e list

La [funzione di riferimento](template-functions-resource.md#reference) consente un'espressione per derivare il valore da altri nomi JSON e coppie valore o risorse di runtime. Le [funzioni list*](template-functions-resource.md#list) restituiscono valori per una risorsa da un'operazione elenco.

Le espressioni di riferimento ed elenco dichiarano in modo implicito che una risorsa dipende da un'altra. Quando possibile, usare un riferimento implicito per evitare di aggiungere una dipendenza non necessaria.

Per applicare una dipendenza implicita, fare riferimento alla risorsa in base al nome, non all'ID risorsa. Se alla funzione reference o list viene passato l'ID risorsa, non viene creato alcun riferimento implicito.

Il formato generale della funzione reference è:

```json
reference('resourceName').propertyPath
```

Il formato generale della funzione listKeys è:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

Nell'esempio seguente, un endpoint della rete CDN dipende in modo esplicito dal profilo CDN e in modo implicito da un'app Web.

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

Per altre informazioni, vedere la [funzione del riferimento](template-functions-resource.md#reference).

## <a name="depend-on-resources-in-a-loop"></a>In base alle risorse in un ciclo

Per distribuire le risorse che dipendono dalle risorse in un [ciclo di copia](copy-resources.md), sono disponibili due opzioni. È possibile impostare una dipendenza dalle singole risorse nel ciclo o nell'intero ciclo.

> [!NOTE]
> Per la maggior parte degli scenari, è necessario impostare la dipendenza per le singole risorse all'interno del ciclo di copia. È sufficiente che dipenda dall'intero ciclo quando è necessario che tutte le risorse del ciclo esistano prima di creare la risorsa successiva. Impostando la dipendenza sull'intero ciclo, le dipendenze vengono espanse in modo significativo, soprattutto se le risorse in ciclo dipendono da altre risorse. Le dipendenze espanse rendono difficoltoso il completamento della distribuzione in modo efficiente.

Nell'esempio seguente viene illustrato come distribuire più macchine virtuali. Il modello crea lo stesso numero di interfacce di rete. Ogni macchina virtuale dipende da un'interfaccia di rete, anziché da un ciclo intero.

```json
{
  "type": "Microsoft.Network/networkInterfaces",
  "apiVersion": "2020-05-01",
  "name": "[concat(variables('nicPrefix'),'-',copyIndex())]",
  "location": "[parameters('location')]",
  "copy": {
    "name": "nicCopy",
    "count": "[parameters('vmCount')]"
  },
  ...
},
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "name": "[concat(variables('vmPrefix'),copyIndex())]",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]"
  ],
  "copy": {
    "name": "vmCopy",
    "count": "[parameters('vmCount')]"
  },
  "properties": {
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]",
          "properties": {
            "primary": "true"
          }
        }
      ]
    },
    ...
  }
}
```

Nell'esempio seguente viene illustrato come distribuire tre account di archiviazione prima di distribuire la macchina virtuale. Si noti che il nome dell'elemento Copy è impostato su `storagecopy` e anche l'elemento dependsOn per la macchina virtuale è impostato su `storagecopy` .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="circular-dependencies"></a>Dipendenze circolari

Resource Manager identifica le dipendenze circolari durante la convalida del modello. Se viene visualizzato un errore per una dipendenza circolare, valutare il modello per verificare se è possibile rimuovere le dipendenze. Se la rimozione delle dipendenze non funziona, è possibile evitare dipendenze circolari spostando alcune operazioni di distribuzione in risorse figlio. Distribuire le risorse figlio dopo le risorse con la dipendenza circolare. Si supponga, ad esempio, di distribuire due macchine virtuali e che sia necessario impostare in ognuna proprietà che fanno riferimento all'altra. È possibile eseguire la distribuzione nell'ordine seguente:

1. VM 1
2. VM 2
3. L'estensione in VM 1 dipende da VM 1 e VM 2. L'estensione imposta in VM 1 valori ottenuti da VM 2.
4. L'estensione in VM 2 dipende da VM 1 e VM 2. L'estensione imposta in VM 2 valori ottenuti da VM 1.

Per informazioni sulla valutazione dell'ordine di distribuzione e la risoluzione degli errori di dipendenza, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](common-deployment-errors.md).

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire un'esercitazione, vedere [Esercitazione: Creare modelli di Azure Resource Manager con risorse dipendenti](template-tutorial-create-templates-with-dependent-resources.md).
* Per un modulo Microsoft Learn che copre le dipendenze delle risorse, vedere [gestire le distribuzioni cloud complesse usando le funzionalità avanzate del modello ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Per suggerimenti sull'impostazione di dipendenze, vedere [Procedure consigliate per la creazione di modelli di Azure Resource Manager](template-best-practices.md).
* Per informazioni sulla risoluzione dei problemi relativi alle dipendenze durante la distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](common-deployment-errors.md).
* Per informazioni sulla creazione di modelli di Gestione risorse di Azure, vedere [Creazione di modelli](template-syntax.md).
* Per un elenco delle funzioni disponibili in un modello, vedere [Funzioni di modelli](template-functions.md).

