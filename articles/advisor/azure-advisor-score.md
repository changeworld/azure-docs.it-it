---
title: Ottimizzare i carichi di lavoro di Azure usando il Punteggio di Advisor
description: USA Azure Advisor score per sfruttare al meglio Azure.
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 11b20bc3b4d604d3a7ff4608cd1c21f129c1cb6d
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630123"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>Ottimizzare i carichi di lavoro di Azure usando il Punteggio di Advisor

## <a name="introduction-to-advisor-score"></a>Introduzione al Punteggio di Advisor

Azure Advisor fornisce consigli sulle procedure consigliate per i carichi di lavoro. Questi consigli sono personalizzati e possono essere utili per eseguire le operazioni seguenti:

* Migliorare la postura dei carichi di lavoro e ottimizzare le distribuzioni di Azure.
* Prevenire in modo proattivo i problemi principali seguendo le procedure consigliate.
* Valutazione dei carichi di lavoro di Azure rispetto ai cinque pilastri della [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/).

Come caratteristica principale di Advisor, il Punteggio di Advisor può aiutarti a raggiungere questi obiettivi in modo efficace ed efficiente.

Per ottenere il massimo da Azure, è fondamentale comprendere il percorso di ottimizzazione del carico di lavoro. È necessario stabilire quali servizi o risorse vengono utilizzati correttamente e quali non lo sono. Inoltre, è necessario essere in grado di definire le priorità delle azioni, in base alle raccomandazioni, per massimizzare il risultato.

È anche importante tenere traccia e segnalare lo stato di avanzamento in questo percorso di ottimizzazione. Con il Punteggio di Advisor, è possibile eseguire facilmente tutte queste operazioni con la nuova esperienza di gamification.

Come consulente cloud personalizzato, Azure Advisor valuta continuamente la telemetria di utilizzo e la configurazione delle risorse per verificare la presenza di procedure consigliate per il settore. Advisor quindi aggrega i risultati in un unico punteggio. Con questo punteggio è possibile stabilire a colpo d'occhio se si stanno eseguendo i passaggi necessari per creare soluzioni affidabili, sicure e convenienti.

Il Punteggio di Advisor è costituito da un punteggio complessivo, che può essere suddiviso ulteriormente in cinque punteggi di categoria. Un punteggio per ogni categoria di Advisor rappresenta i cinque pilastri del Framework Well-Architected.

È possibile tenere traccia dello stato di avanzamento nel tempo visualizzando il punteggio complessivo e il Punteggio di categoria con tendenze quotidiane, settimanali e mensili. È anche possibile impostare i benchmark per consentire di raggiungere gli obiettivi.

 ![Screenshot che mostra la pagina dei punteggi di Advisor.](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>Interpretare un punteggio di Advisor

Advisor Visualizza il punteggio complessivo di Advisor e una suddivisione per le categorie di Advisor, in percentuali. Un punteggio pari al 100% in qualsiasi categoria indica che tutte le risorse valutate da Advisor seguono le procedure consigliate consigliate da Advisor. Nell'altra estremità dello spettro, un punteggio pari allo 0% indica che nessuna delle risorse valutate da Advisor segue le raccomandazioni di Advisor. Usando questi grani di punteggio, è possibile ottenere facilmente il flusso seguente:

* Il **Punteggio di Advisor** consente di basare il funzionamento del carico di lavoro o delle sottoscrizioni in base a un punteggio di Advisor. È anche possibile vedere le tendenze cronologiche per comprendere la tendenza.
* Il **punteggio per categoria** per ogni raccomandazione indica quali raccomandazioni in attesa miglioreranno il punteggio. Questi valori riflettono sia il peso della raccomandazione che la semplicità di implementazione prevista. Questi fattori contribuiscono ad assicurare che sia possibile ottenere il massimo valore con il tempo. Consentono inoltre di definire le priorità.
* L' **effetto del Punteggio di categoria** per ogni raccomandazione consente di assegnare una priorità alle azioni di correzione per ogni categoria.

Il contributo di ogni raccomandazione per il punteggio della categoria viene visualizzato chiaramente nella pagina **Score Advisor** della portale di Azure. È possibile aumentare il Punteggio di ogni categoria in base al punto percentuale elencato nella colonna **potenziale aumento Punteggio** . Questo valore riflette sia il peso della raccomandazione all'interno della categoria che la semplicità di implementazione prevista per risolvere le attività potenzialmente più semplici. Concentrandosi sulle raccomandazioni con un maggiore effetto sui punteggi, sarà possibile ottenere il massimo avanzamento con il tempo.

![Screenshot che mostra l'effetto del Punteggio di Advisor.](./media/advisor-score-2.png)

Se una raccomandazione di Advisor non è pertinente per una singola risorsa, è possibile posticipare o ignorare tali raccomandazioni. Verranno esclusi dal calcolo del punteggio con l'aggiornamento successivo. Advisor utilizzerà anche questo input come feedback aggiuntivo per migliorare il modello.

## <a name="how-is-an-advisor-score-calculated"></a>Come viene calcolato un punteggio di Advisor?

Advisor Visualizza i punteggi di categoria e il Punteggio di Advisor complessivo come percentuali. Un punteggio pari al 100% in qualsiasi categoria indica che tutte le risorse, *valutate in base a Advisor*, seguono le procedure consigliate consigliate da Advisor. Nell'altra estremità dello spettro, un punteggio pari allo 0% indica che nessuna delle risorse, valutata da Advisor, segue le raccomandazioni di Advisor.

**Ognuna delle cinque categorie ha un punteggio potenziale massimo di 100.** Il Punteggio complessivo di Advisor viene calcolato come somma di ogni punteggio di categoria applicabile, diviso per la somma del Punteggio potenziale più alto da tutte le categorie applicabili. Per la maggior parte delle sottoscrizioni, questo significa che Advisor somma il Punteggio di ogni categoria e lo divide per 500. *Il Punteggio di ogni categoria viene tuttavia calcolato solo se si usano risorse valutate da Advisor*.

### <a name="advisor-score-calculation-example"></a>Esempio di calcolo del Punteggio di Advisor

* **Punteggio singola sottoscrizione:** Questo esempio è la media semplice di tutti i punteggi di categoria di Advisor per la sottoscrizione. Se i punteggi della categoria Advisor sono- **cost** = 73, **affidabilità** = 85, **operational excellence** = 77 e **performance** = 100, il Punteggio di Advisor è (73 + 85 + 77 + 100)/(4x100) = 0,84% o 84%.
* **Punteggio per più sottoscrizioni:** Quando vengono selezionate più sottoscrizioni, i punteggi di Advisor complessivi generati sono i punteggi di categoria aggregati ponderati. In questo caso, ogni punteggio di categoria di Advisor viene aggregato in base alle risorse utilizzate dalle sottoscrizioni. Quando Advisor dispone dei punteggi di categoria aggregati ponderati, Advisor esegue un calcolo medio semplice per fornire un punteggio complessivo per le sottoscrizioni.

### <a name="scoring-methodology"></a>Metodologia di assegnazione dei punteggi

Il calcolo del Punteggio di Advisor può essere riepilogato in quattro passaggi:

1. Advisor calcola il *costo al dettaglio delle risorse interessate*. Queste risorse sono quelle delle sottoscrizioni con almeno una raccomandazione in Advisor.
1. Advisor calcola il *costo finale delle risorse valutate*. Queste risorse sono quelle monitorate da Advisor, indipendentemente dal fatto che dispongano o meno di raccomandazioni.
1. Per ogni tipo di raccomandazione, Advisor calcola il *rapporto tra le risorse integre*. Questo rapporto è il costo al dettaglio delle risorse interessate divise per il costo finale delle risorse valutate.
1. Advisor applica tre pesi aggiuntivi al rapporto di risorse integre in ogni categoria:

   * Le raccomandazioni con un impatto maggiore sono ponderate più pesanti rispetto alle raccomandazioni con un impatto minore.
   * Le risorse con raccomandazioni di lunga durata vengono conteggiate più in base al punteggio.
   * Le risorse rimandate o ignorate in Advisor vengono rimosse dal calcolo del punteggio.

Advisor applica questo modello a livello di categoria Advisor per fornire un punteggio di Advisor per ogni categoria. **Sicurezza** utilizza un modello di [Punteggio sicuro](../security-center/secure-score-security-controls.md#introduction-to-secure-score) . Una media semplice produce il punteggio finale dell'Advisor.

## <a name="advisor-score-faqs"></a>Domande frequenti sul punteggio di Advisor

### <a name="how-often-is-my-score-refreshed"></a>Con quale frequenza viene aggiornato il Punteggio?

Il Punteggio viene aggiornato almeno una volta al giorno.

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>Perché alcune raccomandazioni hanno il valore "-" vuoto nella colonna Categoria Impact Score?

Advisor non include immediatamente nuove raccomandazioni o raccomandazioni con le modifiche recenti apportate al modello di assegnazione dei punteggi. Dopo un breve periodo di valutazione, in genere alcune settimane sono incluse nel punteggio.

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>Perché il Punteggio di costo ha un impatto maggiore per alcune raccomandazioni anche se hanno un minor risparmio potenziale?

Il Punteggio di **costo** riflette il potenziale risparmio di risorse sottoutilizzate e la semplicità prevista per l'implementazione di tali raccomandazioni. Ad esempio, viene applicato un peso aggiuntivo alle risorse interessate che sono inattive per un periodo di tempo più lungo, anche se il risparmio potenziale è inferiore.

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>Perché non si dispone di un punteggio per una o più categorie o sottoscrizioni?

Advisor genera un punteggio solo per le categorie e le sottoscrizioni con risorse valutate da Advisor.

### <a name="what-if-a-recommendation-isnt-relevant"></a>Che cosa accade se una raccomandazione non è pertinente?

Se si ignora un Consiglio di Advisor, questo verrà omesso dal calcolo del punteggio. Le raccomandazioni di chiusura consentono inoltre a Advisor di migliorare la qualità delle raccomandazioni.

### <a name="why-did-my-score-change"></a>Perché il punteggio è stato modificato?

Il Punteggio può variare se si aggiornano le risorse interessate adottando le procedure consigliate consigliate da Advisor. Se l'utente o chiunque disponga delle autorizzazioni per la sottoscrizione ha modificato o creato nuove risorse, è possibile che vengano visualizzate anche le fluttuazioni del punteggio. Il Punteggio si basa su un rapporto tra le risorse con impatto sul costo rispetto al costo totale di tutte le risorse.

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>In che modo Advisor calcola il costo al dettaglio delle risorse in una sottoscrizione?

Advisor usa le tariffe con pagamento in base al consumo pubblicate in [prezzi di Azure](https://azure.microsoft.com/pricing/). Queste tariffe non riflettono gli sconti applicabili. Le tariffe vengono quindi moltiplicate per la quantità di utilizzo nell'ultimo giorno in cui la risorsa è stata allocata. Se si ometteno gli sconti dal calcolo del costo delle risorse, i punteggi di Advisor vengono comparati tra sottoscrizioni, tenant e registrazioni, in cui gli sconti potrebbero variare.

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>È necessario visualizzare le raccomandazioni in Advisor per ottenere i punti per il Punteggio?

No. Il Punteggio indica se adottare le procedure consigliate consigliate da Advisor, anche se si adottano tali procedure consigliate in modo proattivo e non si visualizzano mai le raccomandazioni in Advisor.

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>La metodologia di assegnazione dei punteggi distingue tra i carichi di lavoro di produzione e di sviluppo e test?

No, non per adesso. Tuttavia, è possibile ignorare le raccomandazioni sulle singole risorse se tali risorse vengono utilizzate per lo sviluppo e il test e non si applicano le indicazioni.

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>È possibile confrontare I punteggi tra una sottoscrizione con risorse 100 e una sottoscrizione con risorse 100.000?

La metodologia di assegnazione dei punteggi è progettata per controllare il numero di risorse in una sottoscrizione e una combinazione di servizi. Le sottoscrizioni con un minor numero di risorse possono avere punteggi superiori o inferiori rispetto alle sottoscrizioni con più risorse.

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>Cosa significa quando viene visualizzato "Coming Soon" nella colonna Score Impact?

Questo messaggio indica che la raccomandazione è nuova e si sta lavorando per portarla nel modello di Punteggio di Advisor. Dopo che questa nuova raccomandazione viene considerata in un calcolo del punteggio, il valore dell'effetto del punteggio per la raccomandazione verrà visualizzato.

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>Il mio punteggio dipende da quanto spendo in Azure?

No. Il Punteggio non è necessariamente un riflesso della spesa. Una spesa superflua determinerà un punteggio di **costo** inferiore.

## <a name="access-advisor-score"></a>Punteggio di Access Advisor

Il Punteggio di Advisor è in anteprima pubblica nel portale di Azure. Nel riquadro sinistro, nella sezione **Advisor** , vedere **Advisor Score**.

![Screenshot che mostra il punto di ingresso del Punteggio di Advisor.](./media/advisor-score-3.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:

* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Introduzione ad Advisor](advisor-get-started.md)
* [Raccomandazioni sui costi di Advisor](advisor-cost-recommendations.md)
* [Raccomandazioni sulle prestazioni di Advisor](advisor-performance-recommendations.md)
* [Raccomandazioni sulla sicurezza di Advisor](advisor-security-recommendations.md)
* [Raccomandazioni sull'eccellenza operativa di Advisor](advisor-operational-excellence-recommendations.md)
