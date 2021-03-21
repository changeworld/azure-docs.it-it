---
title: Elemento TextBox dell'interfaccia utente
description: Illustra l'elemento Microsoft.Common.TextBox dell'interfaccia utente per il portale di Azure. Usare per aggiungere testo non formattato.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
ms.openlocfilehash: 8e4cfcc7fe46c19bf1e58ee5eadf1e0bb8c7bd8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124330"
---
# <a name="microsoftcommontextbox-ui-element"></a>Elemento Microsoft.Common.TextBox dell'interfaccia utente

Elemento dell'interfaccia utente che può essere utilizzato per aggiungere testo non formattato. L' `Microsoft.Common.TextBox` elemento è un campo di input a riga singola, ma supporta l'input su più righe con la `multiLine` Proprietà.

## <a name="ui-sample"></a>Esempio di interfaccia utente

L' `TextBox` elemento usa una casella di testo a riga singola o a più righe.

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox.png" alt-text="Casella di testo a riga singola dell'elemento Microsoft. Common. TextBox.":::

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox-multi-line.png" alt-text="Casella di testo a più righe dell'elemento Microsoft. Common. TextBox.":::

## <a name="schema"></a>SCHEMA

```json
{
  "name": "nameInstance",
  "type": "Microsoft.Common.TextBox",
  "label": "Name",
  "defaultValue": "contoso123",
  "toolTip": "Use only allowed characters",
  "placeholder": "",
  "multiLine": false,
  "constraints": {
    "required": true,
    "validations": [
      {
        "regex": "^[a-z0-9A-Z]{1,30}$",
        "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
      },
      {
        "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
        "message": "Must start with 'contoso'."
      }
    ]
  },
  "visible": true
}
```

## <a name="sample-output"></a>Output di esempio

```json
"contoso123"
```

## <a name="remarks"></a>Commenti

- Utilizzare la `toolTip` proprietà per visualizzare il testo sull'elemento quando il cursore del mouse viene posizionato sul simbolo delle informazioni.
- La `placeholder` proprietà è il testo della guida che scompare quando l'utente inizia la modifica. Se `placeholder` e `defaultValue` sono entrambi definiti, avrà la `defaultValue` precedenza e verrà visualizzato.
- La `multiLine` proprietà è booleana `true` o `false` . Per utilizzare una casella di testo a più righe, impostare la proprietà su `true` . Se non è necessaria una casella di testo a più righe, impostare la proprietà su `false` o escludere la proprietà. Per le nuove righe, viene visualizzato `\n` l'output JSON per il feed di riga. La casella di testo a più righe accetta `\r` un ritorno a capo (CR) e `\n` un avanzamento riga (LF). Ad esempio, un valore predefinito può includere `\r\n` per specificare CRLF.
- Se `constraints.required` è impostato su `true` , la convalida del valore della casella di testo deve essere corretta. Il valore predefinito è `false`.
- La `validations` proprietà è una matrice in cui si aggiungono le condizioni per verificare il valore fornito nella casella di testo.
- La `regex` proprietà è un modello di espressione regolare JavaScript. Se specificato, il valore della casella di testo deve corrispondere al criterio per convalidare correttamente. Il valore predefinito è `null`. Per ulteriori informazioni sulla sintassi Regex, vedere [riferimento rapido a espressioni regolari](/dotnet/standard/base-types/regular-expression-language-quick-reference).
- La `isValid` proprietà contiene un'espressione che restituisce `true` o `false` . All'interno dell'espressione si definisce la condizione che determina se la casella di testo è valida.
- La `message` proprietà è una stringa da visualizzare quando il valore della casella di testo non riesce a eseguire la convalida.
- È possibile specificare un valore per `regex` quando `required` è impostato su `false` . In questo scenario non è richiesto un valore perché la convalida della casella di testo abbia esito positivo. Se viene specificato, deve corrispondere al modello di espressione regolare.

## <a name="examples"></a>Esempio

Gli esempi illustrano come usare una casella di testo a riga singola e una casella di testo a più righe.

### <a name="single-line"></a>Riga singola

Nell'esempio seguente viene usata una casella di testo con il controllo [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md) per verificare la disponibilità di un nome di risorsa.

```json
"basics": [
  {
    "name": "nameApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
      "method": "POST",
      "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
      "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
    }
  },
  {
    "name": "txtStorageName",
    "type": "Microsoft.Common.TextBox",
    "label": "Storage account name",
    "constraints": {
      "validations": [
        {
          "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
          "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
        }
      ]
    }
  }
]
```

### <a name="multi-line"></a>Più righe

In questo esempio viene utilizzata la `multiLine` proprietà per creare una casella di testo a più righe con testo segnaposto.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "demoTextBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Multi-line text box",
        "defaultValue": "",
        "toolTip": "Use 1-60 alphanumeric characters, hyphens, spaces, periods, and colons.",
        "placeholder": "This is a multi-line text box:\nLine 1.\nLine 2.\nLine 3.",
        "multiLine": true,
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z -.:\n]{1,60}$",
              "message": "Only 1-60 alphanumeric characters, hyphens, spaces, periods, and colons are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "textBox": "[basics('demoTextBox')]"
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione alla creazione di definizioni dell'interfaccia utente, vedere [CreateUiDefinition.json per l'esperienza di creazione di un'applicazione gestita di Azure](create-uidefinition-overview.md).
- Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
