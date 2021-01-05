---
title: Risolvere i problemi di connettività SMTP in uscita in Azure | Microsoft Docs
description: Informazioni sul metodo consigliato per l'invio di messaggi di posta elettronica e su come risolvere i problemi relativi alla connettività SMTP in uscita in Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: genli
ms.openlocfilehash: e1e7e78cab1f3a240737b5e25e0dff28c420add8
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883113"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Risolvere i problemi di connettività SMTP in uscita in Azure

A partire dal 15 novembre 2017, i messaggi di posta elettronica in uscita inviati direttamente a domini esterni, ad esempio outlook.com e gmail.com, da una macchina virtuale (VM) vengono resi disponibili solo per determinati tipi di sottoscrizione in Azure. Le connessioni SMTP in uscita che usano la porta TCP 25 sono state bloccate. La porta 25 viene utilizzata principalmente per il recapito di posta elettronica non autenticato.

Questa modifica del comportamento si applica solo alle sottoscrizioni e alle distribuzioni create dopo il 15 novembre 2017.

## <a name="recommended-method-of-sending-email"></a>Metodo consigliato per l'invio di posta elettronica

Si consiglia di usare i servizi di inoltro SMTP autenticato per inviare messaggi di posta elettronica da macchine virtuali di Azure o da app Azure servizio. Questi servizi di inoltro in genere si connettono tramite la porta TCP 587 o 443, ma supportano le altre porte. Questi servizi vengono utilizzati per gestire la reputazione IP o di dominio per ridurre al minimo la possibilità che i provider di posta elettronica di terze parti rifiutino i messaggi. [SendGrid](https://sendgrid.com/partners/azure/) è un servizio di inoltro SMTP, ma ne esistono altri. Potrebbe anche essere presente un servizio di inoltro SMTP sicuro in esecuzione in locale che è possibile usare.

In Azure non sono previste restrizioni relative all'uso di questi servizi di recapito di posta elettronica, indipendentemente dal tipo di sottoscrizione.

## <a name="enterprise-agreement"></a>Enterprise Agreement

Per gli utenti di Azure con contratto Enterprise Agreement, non vi sono variazioni riguardo alla capacità tecnica di inviare posta elettronica senza usare un servizio di inoltro autenticato. Gli utenti con contratto Enterprise Agreement, sia nuovi che esistenti, possono tentare il recapito di posta elettronica in uscita direttamente dalle macchine virtuali di Azure a provider di posta elettronica esterni, senza restrizioni da parte della piattaforma Azure. Non vi è alcuna garanzia che i provider di posta elettronica accettino i messaggi di posta elettronica in arrivo da un determinato utente. Tuttavia, la piattaforma Azure non bloccherà i tentativi di recapito per le macchine virtuali all'interno Enterprise Agreement sottoscrizioni. Dovrai collaborare direttamente con i provider di posta elettronica per risolvere eventuali problemi di recapito dei messaggi o di filtro della posta indesiderata che coinvolgono provider specifici.

## <a name="pay-as-you-go"></a>Pagamento in base al consumo

Se ci si è iscritti prima del 15 novembre 2017 per una sottoscrizione con pagamento in base al consumo, non vi sarà alcuna modifica nella capacità tecnica di provare il recapito della posta elettronica in uscita. Sarà comunque possibile provare il recapito tramite posta elettronica in uscita dalle macchine virtuali di Azure all'interno di queste sottoscrizioni direttamente a provider di posta elettronica esterni senza alcuna restrizione dalla piattaforma Azure. Anche in questo caso, non c'è alcuna garanzia che i provider di posta elettronica accettino i messaggi di posta elettronica in arrivo da qualsiasi utente Gli utenti dovranno collaborare direttamente con i provider di posta elettronica per risolvere eventuali problemi di recapito dei messaggi o di filtro della posta indesiderata che coinvolgono provider specifici.

Per le sottoscrizioni con pagamento in base al consumo create dopo il 15 novembre 2017, saranno previste restrizioni tecniche che bloccano la posta elettronica inviata direttamente dalle macchine virtuali all'interno delle sottoscrizioni. Se si vuole essere in grado di inviare messaggi di posta elettronica da macchine virtuali di Azure direttamente a provider di posta elettronica esterni (senza usare un inoltro SMTP autenticato) e si dispone di un account in grado di ottenere una cronologia dei pagamenti, è possibile richiedere la rimozione della restrizione. Questa operazione può essere eseguita nella sezione **connettività** del pannello **diagnostica e risolvi** per una risorsa di rete virtuale di Azure nel portale di Azure. Se la richiesta viene accettata, la sottoscrizione verrà abilitata o si riceveranno istruzioni per i passaggi successivi. 

Dopo che una sottoscrizione con pagamento in base al consumo è stata esentata e le macchine virtuali vengono arrestate e riavviate nella portale di Azure, tutte le macchine virtuali della sottoscrizione verranno esentate in futuro. L'esenzione si applica solo alla sottoscrizione richiesta e solo al traffico delle macchine virtuali indirizzato direttamente a Internet.

> [!NOTE]
> Microsoft si riserva il diritto di revocare tali esenzioni se è stato rilevato che si è verificata una violazione delle condizioni del servizio.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, Education, Azure per studenti, Visual Studio e versione di valutazione gratuita

Se è stato creato uno dei seguenti tipi di sottoscrizione dopo il 15 novembre 2017, saranno presenti restrizioni tecniche che bloccano la posta elettronica inviata dalle macchine virtuali all'interno della sottoscrizione direttamente ai provider di posta elettronica:
- MSDN
- Azure Pass
- Azure in Open
- Education
- Azure for Students
- Versione di valutazione gratuita
- Qualsiasi sottoscrizione di Visual Studio  

Le restrizioni vengono applicate per evitare abusi. Non verranno concesse richieste di rimozione di queste restrizioni.

Se si usano questi tipi di sottoscrizione, si consiglia di usare i servizi di inoltro SMTP, come descritto in precedenza in questo articolo, o di modificare il tipo di sottoscrizione.

## <a name="cloud-solution-provider"></a>Provider di soluzioni cloud

Se si usano risorse di Azure tramite un provider di soluzioni cloud, è possibile effettuare una richiesta per rimuovere la restrizione nella sezione **connettività** del riquadro **diagnostica e risoluzione** per una risorsa di rete virtuale nel portale di Azure. Se la richiesta viene accettata, la sottoscrizione verrà abilitata o si riceveranno istruzioni per i passaggi successivi.

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network, BizSpark Plus o Azure Sponsorship

Per le sottoscrizioni dei seguenti tipi creati dopo il 15 novembre 2017, saranno previste restrizioni tecniche che bloccano la posta elettronica inviata direttamente dalle macchine virtuali all'interno delle sottoscrizioni:

- Microsoft Partner Network (MPN)
- BizSpark Plus
- Azure Sponsorship

Se si vuole essere in grado di inviare messaggi di posta elettronica da macchine virtuali di Azure direttamente a provider di posta elettronica esterni, senza usare un inoltro SMTP autenticato, è possibile effettuare una richiesta aprendo un caso di supporto usando il tipo di problema seguente: la connettività di  >  **rete virtuale** tecnica  >    >  **non può inviare messaggi di posta elettronica (SMTP/porta 25)**. Assicurarsi di aggiungere i dettagli sul motivo per cui la distribuzione deve inviare messaggi di posta elettronica direttamente ai provider di posta anziché usare un inoltro autenticato. Le richieste verranno esaminate e approvate a discrezione di Microsoft. Le richieste verranno concesse solo dopo il completamento di ulteriori controlli anti-frode. 

Dopo che una sottoscrizione è stata esentata e le macchine virtuali sono state arrestate e riavviate nella portale di Azure, tutte le macchine virtuali della sottoscrizione verranno esentate in futuro. L'esenzione si applica solo alla sottoscrizione richiesta e solo al traffico delle macchine virtuali indirizzato direttamente a Internet.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema. Usare questo tipo di problema : la connettività di  >  **rete virtuale** tecnica  >    >  **non può inviare messaggi di posta elettronica (SMTP/porta 25)**.
