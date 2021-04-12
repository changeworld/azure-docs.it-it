---
title: Considerazioni sulla topologia di rete per Azure Active Directory Application Proxy
description: Illustra le considerazioni relative alla topologia di rete quando si usa Azure Active Directory Application Proxy.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 2873bd9668bfba887ad9add061e68f36a747d5b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492320"
---
# <a name="optimize-traffic-flow-with-azure-active-directory-application-proxy"></a>Ottimizzare il flusso del traffico con Azure Active Directory Application Proxy

Questo articolo illustra come ottimizzare il flusso del traffico e le considerazioni relative alla topologia di rete quando si usa il proxy di applicazione Azure Active Directory (Azure AD) per la pubblicazione e l'accesso alle applicazioni in remoto.

## <a name="traffic-flow"></a>Flusso del traffico

Quando un'applicazione viene pubblicata tramite il proxy applicazione di Azure AD, il traffico dagli utenti alle applicazioni passa attraverso tre connessioni:

1. L'utente si connette all'endpoint pubblico del servizio proxy di applicazione di Azure AD in Azure
1. Il servizio proxy di applicazione si connette al connettore del proxy di applicazione
1. Il connettore del proxy di applicazione si connette all'applicazione di destinazione

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-three-hops.png" alt-text="Diagramma che mostra il flusso di traffico dall'utente all'applicazione di destinazione." lightbox="./media/application-proxy-network-topology/application-proxy-three-hops.png":::

## <a name="optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview"></a>Ottimizzare i gruppi di connettori per usare il servizio cloud proxy applicazione più vicino (anteprima)

Quando ci si iscrive a un tenant di Azure AD, l'area del tenant è determinata dal paese specificato. Quando si Abilita il proxy di applicazione, le istanze del servizio cloud proxy applicazione **predefinite** per il tenant vengono scelte nella stessa area del tenant Azure ad o nell'area più vicina.

Se, ad esempio, il paese o l'area del tenant di Azure AD è il Regno Unito, a tutti i connettori del proxy di applicazione verrà assegnato un **valore predefinito** per l'uso di istanze del servizio nei data center europei. Quando gli utenti accedono alle applicazioni pubblicate, il traffico passa attraverso le istanze del servizio cloud del proxy di applicazione in questa posizione.

Se i connettori sono installati in aree diverse dall'area predefinita, può essere utile modificare l'area in cui il gruppo di connettori è ottimizzato per migliorare le prestazioni di accesso a queste applicazioni. Una volta specificata un'area per un gruppo di connettori, si connetterà ai servizi cloud del proxy di applicazione nell'area designata.

Per ottimizzare il flusso del traffico e ridurre la latenza a un gruppo di connettori, assegnare il gruppo di connettori all'area più vicina. Per assegnare un'area:

> [!IMPORTANT]
> Per usare questa funzionalità, i connettori devono usare almeno la versione 1.5.1975.0.

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore dell'applicazione della directory che usa il proxy di applicazione. Se il dominio del tenant è contoso.com, ad esempio, l'amministratore deve essere admin@contoso.com o qualsiasi altro alias amministratore di tale dominio.
1. Selezionare il nome utente nell'angolo superiore destro. Verificare di aver effettuato l'accesso a una directory che usa il proxy di applicazione. Se è necessario cambiare directory, selezionare **Cambia directory** e scegliere una directory che usa il proxy di applicazione.
1. Nel riquadro di spostamento a sinistra selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Proxy di applicazione**.
1. Selezionare **nuovo gruppo di connettori** e specificare un **nome** per il gruppo di connettori.
1. Successivamente, in **Impostazioni avanzate** , selezionare l'elenco a discesa in Ottimizza per un'area specifica e selezionare l'area geografica più vicina ai connettori.
1. Selezionare **Crea**.
    
    :::image type="content" source="./media/application-proxy-network-topology/geo-routing.png" alt-text="Configurare un nuovo gruppo di connettori." lightbox="./media/application-proxy-network-topology/geo-routing.png":::

1. Una volta creato il nuovo gruppo di connettori, è possibile selezionare i connettori da assegnare a questo gruppo di connettori. 
   - È possibile spostare i connettori al gruppo di connettori solo se si trova in un gruppo di connettori usando l'area predefinita. L'approccio migliore consiste nell'iniziare sempre con i connettori posizionati nel "gruppo predefinito" e quindi spostarli nel gruppo di connettori appropriato.
   - È possibile modificare l'area di un gruppo di connettori solo **se non vi sono connettori** assegnati o app assegnati.
1. Successivamente, assegnare il gruppo di connettori alle applicazioni. Quando si accede alle app, il traffico dovrebbe ora andare al servizio cloud del proxy dell'applicazione nell'area in cui è ottimizzato il gruppo di connettori.

## <a name="considerations-for-reducing-latency"></a>Considerazioni per ridurre la latenza

Tutte le soluzioni proxy introducono latenza nella connessione di rete. Indipendentemente dalla soluzione proxy o VPN scelta per l'accesso remoto, include sempre un set di server che consente la connessione all'interno della rete aziendale.

Le organizzazioni includono in genere endpoint server nella rete perimetrale. Con il proxy applicazione di Azure AD, tuttavia, il traffico passa attraverso il servizio proxy nel cloud, mentre i connettori si trovano nella rete aziendale. Non è richiesta alcuna rete perimetrale.

Le sezioni successive contengono altri suggerimenti per ridurre ulteriormente la latenza. 

### <a name="connector-placement"></a>Posizionamento dei connettori

Il servizio proxy applicazione sceglie automaticamente la posizione delle istanze in base alla località del tenant. L'utente deve comunque decidere dove installare il connettore e, in questo modo, può definire le caratteristiche di latenza del traffico di rete.

Quando si configura il servizio proxy applicazione, è consigliabile porsi le domande seguenti:

- Dove si trova l'app?
- Dove si trova la maggior parte degli utenti che accedono all'app?
- Dove si trova l'istanza del proxy applicazione?
- È già disponibile una connessione di rete dedicata ai data center di Azure, ad esempio Azure ExpressRoute o VPN simile?

Il connettore deve comunicare sia con Azure sia con le applicazioni (passaggi 2 e 3 nel diagramma di flusso del traffico) e la sua posizione influisce quindi sulla latenza di queste due connessioni. Quando si valuta il posizionamento del connettore, tenere presente quanto segue:

- Se si intende usare la delega vincolata Kerberos (KCD) per l'accesso Single Sign-On, il connettore deve avere visibilità su un data center. È necessario inoltre che il server del connettore sia aggiunto a un dominio.  
- In caso di dubbi, installare il connettore più vicino all'applicazione.

### <a name="general-approach-to-minimize-latency"></a>Approccio generale per ridurre al minimo la latenza

È possibile ridurre al minimo la latenza del traffico end-to-end ottimizzando ognuna delle connessioni di rete. Ogni connessione può essere ottimizzata eseguendo le operazioni seguenti:

- Ridurre la distanza tra le due estremità dell'hop.
- Scegliere la rete appropriata da attraversare. Ad esempio può risultare più veloce attraversare una rete privata anziché la rete Internet pubblica grazie ai collegamenti dedicati.

Se è presente un collegamento VPN o ExpressRoute dedicato tra Azure e la rete aziendale, è consigliabile usare tale collegamento.

## <a name="focus-your-optimization-strategy"></a>Individuare la migliore strategia di ottimizzazione

Non si può fare molto per controllare la connessione tra gli utenti e il servizio proxy di applicazione, Gli utenti possono accedere alle app da una rete domestica, un bar o un paese diverso. È possibile invece ottimizzare le connessioni dal servizio proxy di applicazione ai connettori del proxy di applicazione e alle app. È consigliabile incorporare i modelli seguenti nell'ambiente in uso.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Modello 1: Inserire il connettore vicino all'applicazione

Posizionare il connettore vicino all'applicazione di destinazione nella rete del cliente. Questa configurazione riduce al minimo il passaggio 3 nel diagramma della topografia, perché il connettore e l'applicazione sono vicini.

Se il connettore deve comunicare con il controller di dominio, questo modello è vantaggioso. Molti clienti usano questo modello poiché è adatto alla maggior parte degli scenari. Questo modello può essere combinato anche con il modello 2, in modo da ottimizzare il traffico tra il servizio e il connettore.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Modello 2: sfruttare i vantaggi di ExpressRoute con il peering Microsoft

Se ExpressRoute è configurato con peering Microsoft, è possibile usare la connessione ExpressRoute più veloce per il traffico tra il proxy di applicazione e il connettore. Il connettore risiede ancora nella rete, vicino all'app.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Modello 3: Sfruttare ExpressRoute con peering privato

Se tra Azure e la rete aziendale è configurato un collegamento VPN o ExpressRoute dedicato con peering privato, è disponibile un'altra opzione. In questa configurazione, la rete virtuale in Azure è in genere considerata come un'estensione della rete aziendale. È quindi possibile installare il connettore nel data center di Azure soddisfacendo comunque i requisiti di bassa latenza della connessione da connettore ad app.

Poiché il traffico scorre attraverso una connessione dedicata, la latenza non è compromessa. Si migliora inoltre la latenza dal servizio proxy applicazione al connettore perché il connettore è installato in un data center di Azure vicino alla posizione del tenant di Azure AD.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-expressroute-private.png" alt-text="Diagramma che illustra l'installazione del connettore in un data center di Azure" lightbox="./media/application-proxy-network-topology/application-proxy-expressroute-private.png":::

### <a name="other-approaches"></a>Altri approcci

Nonostante questo articolo sia incentrato sul posizionamento del connettore, per ottenere caratteristiche di latenza migliori è anche possibile modificare il posizionamento dell'applicazione.

Le organizzazioni spostano sempre più spesso le loro reti in ambienti in hosting. Ciò consente di posizionare le app in un ambiente ospitato che fa anche parte della rete aziendale rimanendo comunque all'interno del dominio. In questo caso, i modelli illustrati nelle sezioni precedenti possono essere applicati alla nuova posizione dell'applicazione. Se si sta considerando questa opzione, vedere [Azure AD Domain Services](../../active-directory-domain-services/overview.md).

È possibile anche valutare l'opportunità di organizzare i connettori usando [gruppi di connettori](application-proxy-connector-groups.md) per raggiungere le app che si trovano in posizioni e reti diverse.

## <a name="common-use-cases"></a>Casi d'uso comuni

In questa sezione si esaminano alcuni scenari comuni. Si supponga che il tenant di Azure AD e di conseguenza l'endpoint del servizio proxy si trovino negli Stati Uniti. Le considerazioni illustrate in questi casi d'uso si applicano anche ad altre aree nel mondo.

In questi scenari ogni connessione viene chiamata "hop" e viene numerata per semplificare la discussione:

- **Hop 1**: dall'utente al proxy del servizio di applicazione
- **Hop 2**: dal servizio del proxy di applicazione al connettore del proxy di applicazione
- **Hop 3**: dal connettore del proxy di applicazione all'applicazione di destinazione 

### <a name="use-case-1"></a>Caso d'uso 1

**Scenario:** l'app si trova in una rete dell'organizzazione negli Stati Uniti, con utenti nella stessa area. Tra il data center di Azure e la rete aziendale non esiste alcun collegamento VPN o ExpressRoute.

**Raccomandazione:** seguire il modello 1 illustrato nella sezione precedente. Per migliorare la latenza, valutare la possibilità di usare ExpressRoute, se necessario.

Si tratta di un modello semplice. Si ottimizza l'hop 3 posizionando il connettore vicino all'app. Questa è anche una scelta naturale, perché il connettore viene in genere installato in modo che comunichi con l'app e il data center per eseguire operazioni KCD.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern1.png" alt-text="Il diagramma che Mostra gli utenti, il proxy, il connettore e l'app sono tutti negli Stati Uniti." lightbox="./media/application-proxy-network-topology/application-proxy-pattern1.png":::

### <a name="use-case-2"></a>Caso d'uso 2

**Scenario:** l'app si trova in una rete dell'organizzazione negli Stati Uniti, con utenti distribuiti in tutto il mondo. Tra il data center di Azure e la rete aziendale non esiste alcun collegamento VPN o ExpressRoute.

**Raccomandazione:** seguire il modello 1 illustrato nella sezione precedente.

Anche in questo caso, il modello comune consiste nell'ottimizzare l'hop 3, posizionando il connettore vicino all'app. L'hop 3 non è in genere costoso, se si trova interamente all'interno della stessa area. L'hop 1 può invece essere più costoso a seconda di dove si trova l'utente, perché gli utenti nel mondo devono accedere all'istanza del proxy applicazione negli Stati Uniti. È opportuno notare che qualsiasi soluzione proxy presenta caratteristiche simili in relazione a utenti distribuiti in tutto il mondo.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern2.png" alt-text="Gli utenti sono distribuiti a livello globale, ma tutto il resto negli Stati Uniti" lightbox="./media/application-proxy-network-topology/application-proxy-pattern2.png":::

### <a name="use-case-3"></a>Caso d'uso 3

**Scenario:** l'app si trova in una rete dell'organizzazione negli Stati Uniti. Tra Azure e la rete aziendale è presente ExpressRoute con peering Microsoft.

**Raccomandazione:** seguire i modelli 1 e 2 illustrati nella sezione precedente.

Per prima cosa, posizionare il connettore il più vicino possibile all'app. In questo modo, il sistema usa automaticamente ExpressRoute per l'hop 2.

Se il collegamento ExpressRoute usa il peering Microsoft, il traffico tra il proxy e il connettore viene trasmesso su tale collegamento. L'hop 2 ha una latenza ottimizzata.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern3.png" alt-text="Diagramma che mostra ExpressRoute tra il proxy e il connettore" lightbox="./media/application-proxy-network-topology/application-proxy-pattern3.png":::

### <a name="use-case-4"></a>Caso d'uso 4

**Scenario:** l'app si trova in una rete dell'organizzazione negli Stati Uniti. Tra Azure e la rete aziendale è presente ExpressRoute con peering privato.

**Raccomandazione:** seguire il modello 3 illustrato nella sezione precedente.

Posizionare il connettore nel data center di Azure connesso alla rete aziendale tramite il peering privato di ExpressRoute.

Il connettore può essere posizionato nel data center di Azure. Dato che il connettore comunica comunque con l'applicazione e il data center tramite la rete privata, l'hop 3 rimane ottimizzato. Viene anche ulteriormente ottimizzato l'hop 2.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern4.png" alt-text="Connettore nel Data Center di Azure, ExpressRoute tra il connettore e l'app" lightbox="./media/application-proxy-network-topology/application-proxy-pattern4.png":::

### <a name="use-case-5"></a>Caso d'uso 5

**Scenario:** L'app si trova in una rete dell'organizzazione in Europa, l'area del tenant predefinita è US e la maggior parte degli utenti in Europa.

**Raccomandazione:** posizionare il connettore vicino all'app. Aggiornare il gruppo di connettori in modo che sia ottimizzato per l'uso delle istanze del servizio proxy di applicazione dell'Europa. Per i passaggi, vedere [ottimizzare i gruppi di connettori per usare il servizio cloud proxy applicazione più vicino](application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview).

Poiché gli utenti dell'Europa accedono a un'istanza del proxy di applicazione che si trova nella stessa area, l'hop 1 non è costoso. L'hop 3 è ottimizzato. È consigliabile usare ExpressRoute per ottimizzare l'hop 2.

### <a name="use-case-6"></a>Caso d'uso 6

**Scenario:** L'app si trova in una rete dell'organizzazione in Europa, l'area del tenant predefinita è US e la maggior parte degli utenti negli Stati Uniti.

**Raccomandazione:** posizionare il connettore vicino all'app. Aggiornare il gruppo di connettori in modo che sia ottimizzato per l'uso delle istanze del servizio proxy di applicazione dell'Europa. Per i passaggi, vedere [ottimizzare i gruppi di connettori per usare il servizio cloud proxy applicazione più vicino](application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview). Hop 1 può essere più costoso poiché tutti gli utenti degli Stati Uniti devono accedere all'istanza del proxy di applicazione in Europa.

In questa situazione è anche possibile prendere in considerazione l'uso di un'altra variante. Se la maggior parte degli utenti nell'organizzazione si trova negli Stati Uniti, la rete potrebbe estendersi anche negli Stati Uniti. Posizionare il connettore negli Stati Uniti, continuare a usare l'area degli Stati Uniti predefinita per i gruppi di connettori e usare la linea di rete aziendale interna dedicata per l'applicazione in Europa. In questo modo, gli hop 2 e 3 vengono ottimizzati.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern5c.png" alt-text="Il diagramma Mostra gli utenti, il proxy e il connettore nell'app degli Stati Uniti in Europa." lightbox="./media/application-proxy-network-topology/application-proxy-pattern5c.png":::

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare il proxy dell’applicazione](application-proxy-add-on-premises-application.md)
- [Abilitare l'accesso Single Sign-On](application-proxy-configure-single-sign-on-with-kcd.md)
- [Abilitare l'accesso condizionale](application-proxy-integrate-with-sharepoint-server.md)
- [Risolvere i problemi che si verificano con il proxy di applicazione](application-proxy-troubleshoot.md)
