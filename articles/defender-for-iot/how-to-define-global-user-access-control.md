---
title: Definire il controllo degli accessi utente globale
description: Nelle organizzazioni di grandi dimensioni le autorizzazioni utente possono essere complesse e possono essere determinate da una struttura organizzativa globale, oltre che dalla struttura standard del sito e della zona.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/08/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: de3ff6ead1f0dd86e07c86b992a720a676a2095c
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97840925"
---
# <a name="define-global-access-control"></a>Definire il controllo di accesso globale

Nelle organizzazioni di grandi dimensioni le autorizzazioni utente possono essere complesse e possono essere determinate da una struttura organizzativa globale, oltre che dalla struttura standard del sito e della zona.

Per supportare la richiesta di autorizzazioni di accesso utente globali e più complesse, è possibile creare una topologia aziendale globale basata su Business Unit, aree e siti. È quindi possibile definire le autorizzazioni di accesso utente per queste entità.

L'utilizzo della topologia degli strumenti di accesso per le aziende consente alle organizzazioni di implementare strategie di attendibilità zero controllando in modo più efficiente la gestione e l'analisi degli asset in Azure Defender per la piattaforma Internet.

## <a name="about-access-groups"></a>Informazioni sui gruppi di accesso

Il controllo di accesso globale viene stabilito tramite la creazione dei gruppi di accesso utente. I gruppi di accesso sono costituiti da regole relative agli utenti che possono accedere a entità aziendali specifiche. L'uso dei gruppi consente di controllare l'accesso alla visualizzazione e alla configurazione per Defender per i ruoli utente specifici in business unit, aree e siti pertinenti.

Ad esempio, è possibile consentire agli analisti della sicurezza di un gruppo di Active Directory di accedere a tutte le linee di produzione dell'Europa occidentale e del vetro, insieme a una linea di plastica in un'area.

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="Diagramma del gruppo Active Directory analisti di sicurezza.":::

Prima di creare i gruppi di accesso, è consigliabile:

- Configurare la topologia aziendale con attenzione. Per altre informazioni sulla topologia aziendale, vedere [usare le visualizzazioni della mappa del sito](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views).

- Pianificare quali utenti sono associati ai gruppi di accesso creati. Sono disponibili due opzioni per l'assegnazione di utenti ai gruppi di accesso:

  - **Assegnare gruppi di gruppi di Active Directory**: verificare di aver configurato un'istanza di Active Directory per l'integrazione con la console di gestione locale.
  
  - **Assegna utenti locali**: verificare di aver creato gli utenti. Per ulteriori informazioni, vedere [define Users](how-to-create-and-manage-users.md#define-users).

Gli utenti amministratori non possono essere assegnati ai gruppi di accesso. Per impostazione predefinita, questi utenti possono accedere a tutte le entità della topologia di business.

## <a name="create-access-groups"></a>Creare gruppi di accesso

In questa sezione viene descritto come creare gruppi di accesso. Le business unit e le aree globali predefinite vengono create per il primo gruppo creato. È possibile modificare le entità predefinite quando si definisce il primo gruppo.

Per creare i gruppi:

1. Selezionare **gruppi di accesso** dal menu laterale della console di gestione locale.

2. Selezionare :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false":::. Nella finestra di dialogo **Aggiungi gruppo di accesso** immettere un nome per il gruppo di accesso. La console supporta 64 caratteri. Assegnare il nome in modo che consenta di distinguere facilmente questo gruppo dagli altri gruppi.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="La finestra di dialogo Aggiungi gruppo di accesso in cui si creano i gruppi di accesso.":::

3. Se viene visualizzata l'opzione **assegna un gruppo di Active Directory** , è possibile assegnare un gruppo di utenti Active Directory a questo gruppo di accesso.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Assegnare un gruppo di Active Directory nella finestra di dialogo Crea gruppo di accesso.":::

   Se l'opzione non viene visualizzata e si desidera includere Active Directory gruppi in gruppi di accesso, selezionare **impostazioni di sistema**. Nel riquadro **Integrations (integrazioni** ) definire i gruppi. Immettere un nome di gruppo esattamente come appare nelle configurazioni del Active Directory e in minuscolo.

5. Nel riquadro **utenti** assegnare il numero di utenti necessario al gruppo. È anche possibile assegnare utenti a gruppi diversi. Se si lavora in questo modo, è necessario creare e salvare le regole e il gruppo di accesso, quindi assegnare gli utenti al gruppo dal riquadro **utenti** .

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="Gestire i ruoli degli utenti e assegnarli in base alle esigenze.":::

6. Creare regole nella finestra di dialogo **Aggiungi regole per il *nome*** in base ai requisiti di accesso della topologia aziendale. Le opzioni visualizzate in questa sezione si basano sulla topologia progettata nelle finestre **Enterprise View** e **Gestione sito** . 

   È possibile creare più di una regola per gruppo. Potrebbe essere necessario creare più di una regola per gruppo quando si lavora con la granularità di accesso complesso in più siti. 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="Aggiungere una regola al sistema.":::

Le regole create vengono visualizzate nella finestra di dialogo **Aggiungi gruppo di accesso** . Qui è possibile eliminarle o modificarle.

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="Visualizzare e modificare tutti i gruppi di accesso da questa finestra.":::

### <a name="about-rules"></a>Informazioni sulle regole

Quando si creano regole, tenere presente le informazioni seguenti:

- Quando un gruppo di accesso contiene diverse regole, la logica della regola aggrega tutte le regole. Ad esempio, le regole usano e logica, not o logica.

- Per applicare correttamente una regola, è necessario assegnare i sensori alle zone nella finestra di **gestione del sito** .

- È possibile assegnare un solo elemento per regola. Ad esempio, è possibile assegnare una business unit, un'area e un sito per ogni regola. Creare altre regole per il gruppo se, ad esempio, si vuole che gli utenti di un gruppo di Active Directory dispongano dell'accesso a diverse unità aziendali in aree diverse.

- Se si modifica un'entità e la modifica interessa la logica della regola, la regola verrà eliminata. Se le modifiche apportate a un'entità topologia influiscono sulla logica della regola, in modo che tutte le regole vengano eliminate, il gruppo di accesso rimane, ma gli utenti non possono accedere alla console di gestione locale. Gli utenti ricevono una notifica per contattare l'amministratore per eseguire l'accesso.

- Se non è selezionata alcuna unità o area business, gli utenti avranno accesso a tutte le unità e le aree aziendali definite.

## <a name="see-also"></a>Vedere anche

[Informazioni su Defender per gli utenti della console](how-to-create-and-manage-users.md)
