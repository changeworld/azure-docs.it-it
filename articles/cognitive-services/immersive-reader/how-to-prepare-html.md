---
title: Come preparare il contenuto HTML per il lettore immersivo
titleSuffix: Azure Cognitive Services
description: Informazioni su come avviare il lettore immersivo usando HTML, JavaScript, Python, Android o iOS. Il lettore immersivo usa tecniche collaudate per migliorare la comprensione della lettura per gli approvatori di linguaggio, i lettori emergenti e gli studenti con differenze di apprendimento.
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 40aab9510eb90c405f92be49a3b4c0a3f756c872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620066"
---
# <a name="how-to-prepare-html-content-for-immersive-reader"></a>Come preparare il contenuto HTML per il lettore immersivo

Questo articolo illustra come strutturare il codice HTML e recuperare il contenuto, in modo che possa essere usato dal lettore immersivo.

## <a name="prepare-the-html-content"></a>Preparare il contenuto HTML

Posizionare il contenuto di cui si desidera eseguire il rendering nel Reader immersivo all'interno di un elemento contenitore. Assicurarsi che l'elemento contenitore disponga di un univoco `id` . Il lettore immersivo fornisce supporto per gli elementi HTML di base. per altre informazioni, vedere il [riferimento](reference.md#html-support) .

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>Ottenere il contenuto HTML in JavaScript

Usare l'oggetto `id` dell'elemento contenitore per ottenere il contenuto HTML nel codice JavaScript.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Avviare il lettore immersivo con il contenuto HTML

Quando si chiama `ImmersiveReader.launchAsync` , impostare la proprietà del blocco `mimeType` su `text/html` per abilitare il rendering del codice HTML.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Passaggi successivi

* Leggere le [informazioni di riferimento su Immersive Reader SDK](reference.md)