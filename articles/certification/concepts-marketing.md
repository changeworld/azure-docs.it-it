---
title: Proprietà marketing
description: Una descrizione dei diversi campi di marketing raccolti nel portale e il modo in cui verranno visualizzati nel catalogo dei dispositivi Azure Certified
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: template-concept
ms.openlocfilehash: 45ecab18451ca0915ee3891278db47feb7469915
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969354"
---
# <a name="marketing-properties"></a>Proprietà marketing

Nel processo di [aggiunta dei dettagli del dispositivo](tutorial-02-adding-device-details.md), sarà necessario fornire le informazioni di marketing che verranno visualizzate nel catalogo dei [dispositivi Azure Certified](https://devicecatalog.azure.com). Queste informazioni vengono raccolte all'interno del portale per i dispositivi Azure Certified durante il processo di invio della certificazione e verranno usate come parametri di filtro nel catalogo. Questo articolo fornisce un mapping tra i campi raccolti nel portale e il modo in cui vengono visualizzati nel catalogo. Dopo la lettura di questo articolo, i partner devono comprendere meglio quali informazioni fornire durante il processo di certificazione per rappresentare al meglio il proprio prodotto nel catalogo.

![Panoramica di PDP](./media/concepts-marketing/pdp-overview.png)

## <a name="azure-certified-device-catalog-product-tile"></a>Riquadro del prodotto catalogo dispositivi Azure Certified

I visitatori del catalogo interagiranno prima con il dispositivo come riquadro del prodotto del catalogo nella pagina di ricerca. Verrà fornita una panoramica di base del dispositivo e delle certificazioni che ha ottenuto.

![Modello di riquadro prodotto](./media/concepts-marketing/product-tile.png)

| Campo | Descrizione                  | Dove aggiungere il portale                |
|---------------|-------------------------|----------------------------------|
| Device Name | Nome pubblico del dispositivo certificato         | Scheda nozioni di base dei dettagli del dispositivo|
| Nome azienda| Nome pubblico dell'azienda  | Non modificabile nel portale. Estratto dal nome dell'account MPN |
| Foto del prodotto  | Immagine del dispositivo con la risoluzione minima 200P x 200P  | Dettagli marketing |
| Classificazione certificazione  | Etichetta di certificazione dispositivo certificato Azure obbligatoria e badge di certificazione facoltativi  | Scheda nozioni di base dei dettagli del dispositivo. Deve superare il test appropriato nella sezione Connect & test. |

## <a name="product-description-page-information"></a>Informazioni pagina Descrizione prodotto

Una volta fatto clic sul riquadro del dispositivo dalla pagina di ricerca del catalogo, un cliente verrà spostato nella pagina di descrizione del prodotto del dispositivo. Questo è il punto in cui viene rilevata la maggior parte delle informazioni fornite durante il processo di certificazione.

Nella parte superiore della pagina di descrizione del prodotto sono evidenziate le caratteristiche principali, alcune delle quali sono già state usate per il riquadro del prodotto.

![Barra superiore PDP](./media/concepts-marketing/pdp-top.png)

| Campo | Descrizione                  | Dove aggiungere il portale                |
|---------------|-------------------------|----------------------------------|
| Classe Device | Classificazione del fattore di forma e dello scopo principale del dispositivo ([altre informazioni](./resources-glossary.md))       | Scheda nozioni di base dei dettagli del dispositivo|
| Tipo di dispositivo | Classificazione del dispositivo in base alla conformità dell'implementazione ([altre informazioni](./resources-glossary.md)) | Scheda nozioni di base dei dettagli del dispositivo |
| Disponibilità geografica | Aree in cui il dispositivo è disponibile per l'acquisto  | Dettagli marketing |
| Sistemi operativi  | Sistemi operativi supportati dal dispositivo  | Scheda Dettagli prodotto dei dettagli del dispositivo |
| Settori di destinazione  | Primi 3 settori per i quali il dispositivo è ottimizzato  | Dettagli marketing |
| Descrizione prodotto  | Campo di testo gratuito in cui scrivere la descrizione di marketing del prodotto. Questo può acquisire i dettagli non elencati nel portale o aggiungere un contesto aggiuntivo per i vantaggi derivanti dall'uso del dispositivo. | Dettagli marketing|

Il resto della pagina è incentrato sulla visualizzazione delle specifiche tecniche del dispositivo in formato tabella che consentirà ai clienti di comprendere meglio il prodotto. Per praticità, sono elencate anche le informazioni visualizzate nella parte superiore della pagina. Il resto della tabella è sezionato dai componenti specificati nel portale.

![Pagina inferiore di PDP](./media/concepts-marketing/pdp-bottom.png)

| Campo | Descrizione                  | Dove aggiungere il portale                |
|---------------|-------------------------|----------------------------------|
| Tipo di componente | Classificazione del fattore di forma e dello scopo principale del dispositivo ([altre informazioni](./resources-glossary.md))       | Dettagli sul prodotto dei dettagli del dispositivo|
| Nome componente| Nome del componente che si sta descrivendo | Dettagli sul prodotto dei dettagli del dispositivo |
| Informazioni aggiuntive sul componente | Specifiche hardware aggiuntive, ad esempio sensori, connettività, acceleratori e così via.  | Informazioni aggiuntive sui componenti dei dettagli del dispositivo ([altre](./how-to-using-the-components-feature.md)informazioni)  |
| Testo delle dipendenze del dispositivo | Testo fornito dal partner che descrive le diverse dipendenze richieste dal prodotto per connettersi ad Azure ([altre informazioni](./how-to-indirectly-connected-devices.md))   | Sezione commenti per i clienti della scheda dipendenze dei dettagli del dispositivo |
| Collegamento dipendenza dispositivo  | Collegamento a un dispositivo certificato richiesto dal prodotto corrente  | Scheda dipendenze dei dettagli del dispositivo |

## <a name="shop-links"></a>Collegamenti ai negozi
Disponibile sia nel riquadro prodotto che nella pagina Descrizione prodotto è un pulsante del negozio. Quando si fa clic sul cliente, viene visualizzata una finestra che consente di selezionare un server di distribuzione. è possibile elencare fino a 5 distributori. Una volta selezionato, il cliente viene reindirizzato all'URL fornito dal partner.

![Immagine dell'esperienza popup del negozio](./media/concepts-marketing/shop.png)

| Campo | Descrizione                  | Dove aggiungere il portale                |
|---------------|-------------------------|----------------------------------|
| Nome del distributore | Nome del server di distribuzione che vende il prodotto | Dettagli marketing|
| Ottieni dispositivo| Collegamento a un sito Web esterno per il cliente per l'acquisto del dispositivo (o per richiedere un preventivo al server di distribuzione). Questo potrebbe essere lo stesso della pagina del produttore se il server di distribuzione è lo stesso del produttore del dispositivo. Se una pagina di acquisto non è disponibile, verrà reindirizzata alla pagina del server di distribuzione affinché il cliente possa contattarli direttamente.  | URL della pagina prodotto del server di distribuzione nei dettagli di marketing. Se non è disponibile alcuna pagina di acquisto, il collegamento utilizzerà per impostazione predefinita l'URL del database di distribuzione in dettaglio marketing. |

## <a name="external-links"></a>Collegamenti esterni
Inoltre, nella pagina di descrizione del prodotto sono disponibili collegamenti che passano ai siti o ai file forniti dal partner che consentono al cliente di comprendere meglio il prodotto. Vengono visualizzati nella parte superiore della pagina, sotto il testo della descrizione del prodotto. I collegamenti visualizzati variano a seconda del tipo di dispositivo e dei programmi di certificazione.

| Collegamento | Descrizione                  | Dove aggiungere il portale                |
|---------------|-------------------------|----------------------------------|
| Guida introduttiva * | File PDF con istruzioni utente per la connessione e l'uso del dispositivo con i servizi di Azure | Aggiungere la sezione della Guida introduttiva del portale|
| Pagina del produttore *|Collegamento alla pagina del produttore. Questa pagina può essere la pagina specifica del prodotto del dispositivo o dell'azienda home page se non è disponibile una pagina di marketing. | Pagina di marketing del produttore in dettagli marketing |
| Modello del dispositivo | Modelli DTDL pubblici per le soluzioni Plug and Play  | Non modificabile nel portale. Il modello di dispositivo deve essere caricato nel ([repository del modello pubblico](https://aka.ms/modelrepo) )  |
| Codice sorgente del dispositivo | URL del codice sorgente del dispositivo per i tipi di dispositivo dev Kit| Scheda nozioni di base dei dettagli del dispositivo  |

 **Obbligatorio per tutti i dispositivi pubblicati*

## <a name="next-steps"></a>Passaggi successivi
Ora che si è a conoscenza del modo in cui vengono usate le informazioni fornite durante la certificazione, è ora possibile certificare il dispositivo. Avviare il progetto di certificazione oppure tornare alla fase dettagli dispositivo per aggiungere le proprie informazioni di marketing.

- [Inizia il tuo percorso di certificazione](./tutorial-00-selecting-your-certification.md)
- [Aggiunta dei dettagli del dispositivo](./tutorial-02-adding-device-details.md)
