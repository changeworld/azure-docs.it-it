---
title: Entità denominate per Healthcare
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 805c726d33f2050f6f2797c0689069aa5ec4ee71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599318"
---
[Analisi del testo per](../../how-tos/text-analytics-for-health.md) i processi di integrità ed estrae informazioni dettagliate da dati medici non strutturati. Il servizio rileva e Visualizza i concetti medici, assegna le asserzioni ai concetti, deduce le relazioni semantiche tra i concetti e li collega alle ontologie mediche comuni.

Analisi del testo per l'integrità rileva i concetti medici nelle categorie seguenti. In questa versione di anteprima è supportato solo il testo in lingua inglese ed è disponibile una sola versione del modello.

| Category  | Descrizione  |
|---------|---------|
| [ANATOMIA](#anatomy) | concetti che acquisiscono informazioni su sistemi di corpo, anatomia, siti, posizioni o aree. |
 | [Demografia](#demographics) | concetti che acquisiscono informazioni sul sesso e l'età. |
 | [ESAME](#examinations) | concetti che consentono di acquisire informazioni su procedure e test diagnostici. |
 | [ATTRIBUTI GENERALI](#general-attributes) | concetti che forniscono ulteriori informazioni su altri concetti delle categorie precedenti. |
 | [GENOMICA](#genomics) | concetti che acquisiscono informazioni su geni e varianti. |
 | [SETTORE sanitario](#healthcare) | concetti che acquisiscono informazioni sugli eventi amministrativi, gli ambienti di assistenza e le professioni sanitarie. |
 | [CONDIZIONE MEDICA](#medical-condition) | concetti che acquisiscono informazioni sulle diagnosi, i sintomi o i segni. |
 | [FARMACO](#medication) | concetti che acquisiscono informazioni sui medicinali, inclusi i nomi dei farmaci, le classi, il dosaggio e il percorso di amministrazione. |
 | [SOCIALE](#social) | concetti che acquisiscono informazioni sugli aspetti sociali medici pertinenti, ad esempio la relazione tra le famiglie. |
 | [TRATTAMENTO](#treatment) | concetti che consentono di acquisire informazioni sulle procedure terapeutiche. |

Per ulteriori informazioni ed esempi, vedere di seguito.

## <a name="anatomy"></a>Anatomia

### <a name="entities"></a>Entità

Sistemi del corpo **BODY_STRUCTURE** , posizioni anatomiche o aree e siti del corpo. Ad esempio, ARM, ginocchio, addome, Nose, fegato, Head, sistema respiratorio, linfociti.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Esempio di entità della struttura del corpo.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Esempio espanso dell'entità della struttura del corpo.":::

## <a name="demographics"></a>Demographics

### <a name="entities"></a>Entità

**Età** : tutti i termini e le frasi, inclusi quelli per i pazienti, i membri della famiglia e altri. Ad esempio, 40-anno-vecchio, 51 yo, 3 mesi precedenti, adulti, Infant, anziani, giovani, minori, a metà età.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Esempio di un'entità Age.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Un altro esempio di entità Age.":::


**Sesso** : termini che divulgano il sesso dell'oggetto. Ad esempio, Maschio, femminile, donna, gentiluomo, signora.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Esempio di entità Gender.":::

## <a name="examinations"></a>Esami

### <a name="entities"></a>Entità

**EXAMINATION_NAME** : procedure e test diagnostici, inclusi segni di vita e misurazioni del corpo. Ad esempio, MRI, ECG, test HIV, emoglobina, numero di piastrine, sistemi di scalabilità come la *scala di Bristol sgabello*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Esempio di entità Exam.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Un altro esempio di entità nome esame.":::

## <a name="general-attributes"></a>Attributi generali

### <a name="entities"></a>Entità

**Data** : data completa relativa a una condizione medica, un esame, un trattamento, un farmaco o un evento amministrativo.

**Direction** : termini direzionali che possono essere correlati a una struttura del corpo, a una condizione medica, a un esame o a un trattamento, ad esempio Left, laterale, Upper, posterior.

**Frequency** : descrive la frequenza con cui si verifica una condizione medica, un esame, un trattamento o un farmaco, che deve verificarsi.

**MEASUREMENT_VALUE** : valore correlato a un esame o a una misurazione della condizione medica.

**MEASUREMENT_UNIT** : unità di misura relativa a un esame o a una misurazione della condizione medica.

**RELATIONAL_OPERATOR** le frasi che esprimono la relazione quantitativa tra un'entità e altre informazioni.

Termini **temporali** relativi all'inizio e/o alla lunghezza (durata) di una condizione medica, un esame, un trattamento, un farmaco o un evento amministrativo. 

## <a name="genomics"></a>Genomica

### <a name="entities"></a>Entità

**GENE_OR_PROTEIN** : tutti i nomi e i simboli dei geni umani, nonché i cromosomi e le parti di cromosomi e proteine. Ad esempio, MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Esempio di un'entità gene.":::

**Variant** : tutte le menzioni delle variazioni dei geni e delle mutazioni. Ad esempio, `c.524C>T` , `(MTRR):r.1462_1557del96`
  
## <a name="healthcare"></a>Servizi sanitari

### <a name="entities"></a>Entità
  
**ADMINISTRATIVE_EVENT** : eventi correlati al sistema sanitario, ma di natura amministrativa/semi-amministrativa. Ad esempio, registrazione, ammissione, valutazione, immissione di studi, trasferimento, discarico, ricovero, degenza, ospedale. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Esempio di entità evento Healthcare.":::

**CARE_ENVIRONMENT** : un ambiente o una località in cui i pazienti sono interessati. Ad esempio, stanza di emergenza, ufficio del medico, unità cardio, Hospice, ospedale.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Questa schermata mostra un esempio di entità dell'ambiente sanitario.":::

**HEALTHCARE_PROFESSION** : un professionista sanitario autorizzato o non concesso in licenza. Ad esempio, dentista, patologo, neurologo, radiologo, farmacista, nutrizionista, fisioterapista, chiropratico.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Questo screenshot mostra un altro esempio di entità dell'ambiente sanitario.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Un altro esempio di entità dell'ambiente sanitario.":::

## <a name="medical-condition"></a>Condizione medica

### <a name="entities"></a>Entità

**Diagnosi** : malattia, sindrome, avvelenamento. Ad esempio, cancro del seno, Alzheimer, HTN, CHF, lesione del midollo spinale.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Esempio di entità di condizione medica.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Un altro esempio di entità condizione medica.":::

**SYMPTOM_OR_SIGN** : evidenza soggettiva o oggettiva della malattia o di altre diagnosi. Ad esempio, dolore toracico, cefalea, vertigini, rash, SINGHIOZZo, addome è stato leggero, ottimo suono intestinale, ben nutrito.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Esempio di un segno di condizione medica o di un'entità sintomo.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Un altro esempio di un segno di condizione medica o di un'entità sintomo.":::

Termini qualitativi **CONDITION_QUALIFIER** utilizzati per descrivere una condizione medica. Tutte le sottocategorie seguenti sono considerate qualificatori:

1.  Espressioni correlate al tempo: si tratta di termini che descrivono in modo qualitativo la dimensione temporale, ad esempio improvviso, acuto, cronico, di lunga durata. 
2.  Espressioni di qualità: si tratta di termini che descrivono la "natura" della condizione medica, ad esempio Burning, Sharp.
3.  Espressioni di gravità: grave, lieve, un bit, non controllato.
4.  Espressioni Extensivity: locale, focale, diffusa.
5.  Espressioni di radiazione: radianti, radiazioni.
6.  Scala della condizione: in alcuni casi, una condizione è caratterizzata da una scala, ovvero un elenco di valori ordinato finito. Ad esempio, pazienti con cancro pancreatico di fase III.
7.  Corso della condizione: un termine che è correlato al corso o alla progressione di una condizione, ad esempio miglioramento, peggioramento, risoluzione, restituzione. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Un esempio di attributo di qualificatore della condizione e di un'entità di diagnostica.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Un altro esempio di attributo di qualificatore della condizione e di un'entità di diagnostica.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Esempio di attributo di qualificatore della condizione con entità sintomo e farmaco.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Questa schermata mostra un altro esempio di attributo di qualificatore della condizione con un'entità di diagnostica.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Questa schermata mostra un esempio aggiuntivo di attributo di qualificatore della condizione con un'entità di diagnosi.":::

## <a name="medication"></a>Farmaco

### <a name="entities"></a>Entità

**MEDICATION_CLASS** : un set di farmaci che hanno un meccanismo di azione simile, una modalità di azione correlata, una struttura chimica simile e/o vengono usati per trattare la stessa malattia. Ad esempio, inibitore ACE, oppioidi, antibiotici, antidolorifici.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Esempio di entità di classe medica.":::

**MEDICATION_NAME** : i farmaci citati, inclusi i nomi delle marche con copyright e i nomi non di marchio. Ad esempio, Ibuprofen.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Esempio di entità nome di un farmaco.":::

**Dosaggio** : quantità di medicinali ordinata. Ad esempio, infondere la soluzione cloruro di sodio *1000 ml*.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Esempio di attributo di dosaggio dei farmaci.":::

**MEDICATION_FORM** : il formato del farmaco. Ad esempio soluzione, pillola, capsula, tablet, patch, gel, incolla, schiuma, spray, gocce, panna, sciroppo.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Esempio di attributo del form di un farmaco.":::

**MEDICATION_ROUTE** : metodo di amministrazione dei farmaci. Ad esempio, orale, vaginale, IV, epidurale, topico, inalato.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Esempio di attributo route di un farmaco.":::

## <a name="social"></a>Social network

### <a name="entities"></a>Entità

**FAMILY_RELATION** : menzioni dei parenti della famiglia dell'oggetto. Ad esempio, father, Daughter, di pari livello, parents.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Screenshot mostra un altro esempio di attributo tempo di trattamento.":::

## <a name="treatment"></a>Modalità di gestione

### <a name="entities"></a>Entità

**TREATMENT_NAME** : procedure terapeutiche. Ad esempio, chirurgia di sostituzione del ginocchio, trapianto del midollo osseo, TAVI, dieta.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Esempio di entità nome trattamento.":::
