---
title: Creare report vettori di attacco
description: I report vettoriali di attacco forniscono una rappresentazione grafica di una catena di vulnerabilità di dispositivi sfruttabili.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 6c178f379561ca4a572a7ae8f556ae6753d6e2be
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784374"
---
# <a name="attack-vector-reporting"></a>Segnalazione di vettori di attacco

## <a name="about-attack-vector-reports"></a>Informazioni sui report vettoriali degli attacchi

I report vettoriali di attacco forniscono una rappresentazione grafica di una catena di vulnerabilità di dispositivi sfruttabili. Queste vulnerabilità possono consentire a un utente malintenzionato di accedere ai dispositivi di rete principali. Il simulatore di vettori di attacco calcola i vettori di attacco in tempo reale e analizza tutti i vettori di attacco per una destinazione specifica.

L'uso del vettore di attacco consente di valutare l'effetto delle attività di mitigazione nella sequenza di attacco. È quindi possibile determinare, ad esempio, se un aggiornamento del sistema interrompe il percorso del pirata informatico suddividendo la catena di attacchi o se un percorso di attacco alternativo rimane. Queste informazioni consentono di assegnare priorità alle attività di correzione e mitigazione.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Visualizzare gli avvisi nel centro di controllo.":::

> [!NOTE]
> Gli amministratori e gli analisti della sicurezza possono eseguire le procedure descritte in questa sezione.

## <a name="create-an-attack-vector-report"></a>Creare un report vettoriale di attacco

Per creare una simulazione vettoriale di attacco:

1. Selezionare :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="segno più":::nel menu laterale per aggiungere una simulazione.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="Simulazione del vettore di attacco.":::

2. Immettere le proprietà di simulazione:

   - **Nome**: nome della simulazione.

   - Numero **massimo** di vettori: il numero massimo di vettori in una singola simulazione.

   - **Mostra in mappa dei dispositivi**: Mostra il vettore di attacco come filtro sulla mappa del dispositivo.

   - **Tutti i dispositivi di origine**: il vettore di attacco prenderà in considerazione tutti i dispositivi come origine di attacco.

   - **Origine attacco**: il vettore di attacco prende in considerazione solo i dispositivi specificati come origine di attacco.

   - **Tutti i dispositivi di destinazione**: il vettore di attacco prenderà in considerazione tutti i dispositivi come destinazione di attacco.

   - **Destinazione attacco**: il vettore di attacco considererà solo i dispositivi specificati come destinazione di attacco.

   - **Escludi i dispositivi**: i dispositivi specificati verranno esclusi dalla simulazione del vettore di attacco.

   - **Escludi subnet**: le subnet specificate verranno escluse dalla simulazione del vettore di attacco.

3. Selezionare **Aggiungi simulazione**. La simulazione verrà aggiunta all'elenco simulazioni.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Aggiungere una nuova simulazione.":::

4. Selezionare :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: se si vuole modificare la simulazione.

   Selezionare :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: se si desidera eliminare la simulazione.

   Selezionare :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: se si vuole contrassegnare la simulazione come preferita.

5. Viene visualizzato un elenco di vettori di attacco che include il Punteggio vettoriale (su 100), il dispositivo di origine di attacco e il dispositivo di destinazione attacco. Selezionare un attacco specifico per la rappresentazione grafica dei vettori di attacco.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Vettori di attacco.":::

## <a name="see-also"></a>Vedi anche

[Segnalazione di vettori di attacco](how-to-create-attack-vector-reports.md)


