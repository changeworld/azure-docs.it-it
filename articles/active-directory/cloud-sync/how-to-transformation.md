---
title: Azure AD Connect le trasformazioni di sincronizzazione cloud
description: Questo articolo descrive come usare le trasformazioni per modificare i mapping degli attributi predefiniti.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 1c81ee0ebace65e50cdab5b5b223d5e5eae4ff9a
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613572"
---
# <a name="transformations"></a>Trasformazioni

Con una trasformazione è possibile modificare il comportamento predefinito del modo in cui un attributo viene sincronizzato con Azure Active Directory (Azure AD) utilizzando la sincronizzazione cloud.

Per eseguire questa operazione, è necessario modificare lo schema e quindi inviarlo nuovamente tramite una richiesta Web.

Per ulteriori informazioni sugli attributi di sincronizzazione cloud, vedere [informazioni sullo schema di Azure ad](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Recuperare lo schema
Per recuperare lo schema, attenersi alla procedura descritta in [visualizzare lo schema](concept-attributes.md#view-the-schema). 

## <a name="custom-attribute-mapping"></a>Mapping di attributi personalizzati
Per aggiungere un mapping di attributi personalizzato, attenersi alla seguente procedura.

1. Copiare lo schema in un editor di testo o di codice, ad esempio [Visual Studio Code](https://code.visualstudio.com/).
1. Individuare l'oggetto che si desidera aggiornare nello schema.

   ![Oggetto nello schema](media/how-to-transformation/transform-1.png)</br>
1. Individuare il codice per `ExtensionAttribute3` sotto l'oggetto utente.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. Modificare il codice in modo che venga eseguito il mapping dell'attributo Company a `ExtensionAttribute3` .

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. Copiare di nuovo lo schema in Graph Explorer, modificare il **tipo di richiesta** in **put** e selezionare **Esegui query**.

    ![Eseguire la query](media/how-to-transformation/transform-2.png)

 1. A questo punto, nella portale di Azure passare alla configurazione di sincronizzazione cloud e selezionare **Riavvia provisioning**.

    ![Riavviare il provisioning](media/how-to-transformation/transform-3.png)

 1. Dopo un po' di tempo, verificare che gli attributi vengano popolati eseguendo la query seguente in Graph Explorer: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}` .
 1. A questo punto verrà visualizzato il valore.

    ![Viene visualizzato il valore](media/how-to-transformation/transform-4.png)

## <a name="custom-attribute-mapping-with-function"></a>Mapping di attributi personalizzati con function
Per un mapping più avanzato, è possibile usare funzioni che consentono di modificare i dati e creare valori per gli attributi in base alle esigenze dell'organizzazione.

Per eseguire questa operazione, seguire i passaggi precedenti e quindi modificare la funzione usata per costruire il valore finale.

Per informazioni sulla sintassi e sugli esempi di espressioni, vedere [scrittura di espressioni per i mapping degli attributi in Azure Active Directory](reference-expressions.md).


## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è Azure AD Connect sincronizzazione cloud?](what-is-cloud-sync.md)
