---
title: "DataLayer - Cos'è e come sfruttarlo"
last_modified_at: 2022-12-27T20:00:23+02:00
author: 'Paolo Bietolini'

tags: 
    - dataLayer 
    - google tag 
    - tag manager
    - gtag
categories:
    - tag manager
    - data layer

# _pages

---

## Introduzione
Il DataLayer è un "contenitore" utilizzato per tracciare e raccogliere dati sulla navigazione dei visitatori di un sito web. Viene utilizzato principalmente dagli strumenti di Google per il marketing e serve alla raccolta di dati per Google Analytics e/o Google Ads.

Per gli sviluppatori e analisti, il DataLayer può essere utilizzato per raccogliere dati sulla navigazione dei visitatori del sito in modo da poterne tracciare il comportamento e ottenere informazioni utili per il miglioramento dell'esperienza dell'utente.

Per i marketer, il DataLayer può, invece, essere utilizzato per creare segmenti di pubblico personalizzati e per ottenere informazioni dettagliate sui visitatori del sito, come ad esempio il loro paese di provenienza, la loro età o il loro interesse per determinati prodotti o servizi. Queste informazioni possono essere utilizzate per creare campagne di marketing mirate e per ottimizzare il rendimento delle campagne esistenti.

Per utilizzare il DataLayer, è necessario inserire un pezzo di codice JavaScript nel codice HTML del sito web. Questo codice raccoglie i dati sulla navigazione dei visitatori e li invia a Google Analytics o Google Ads.

## Che cos'è il DataLayer?
Il DataLayer è un array di oggetti JavaScript che viene utilizzato per archiviare i dati in un formato strutturato, che può quindi essere facilmente utilizzato dalle tags e dai trigger di Google Tag Manager, o dalla funzione `gtag()` per raccogliere e inviare i dati a diverse piattaforme di analisi e pubblicità.
Per capire il concetto di array di oggetti immaginiamo una cassettiera dove ogni cassetto è un oggetto mentre l'intero mobile è un array. Quindi il DataLayer è un contenitore di contenitori.

{% include figure image_path="/assets/images/posts/2022/data-layer.jpg" alt="this is a placeholder image" caption="Rappresentazione accurata del Data Layer" %}

La sintassi JavaScript che indica un array sono due parentesi quadre `[]` mentre un oggetto è indicato dalle parentesi graffe `{}`. Consideriamo il seguente esempio:

```javascript
dataLayer = [
    {event: 'pageView', pagePath: '/home'},
    {variable1 : 'value'}
]
```

Come potrai vedere `dataLayer` contiene due oggetti (due cassetti) oguno di essi contenenti, rispettivamente, due e una proprietà. 
Nel prossimo paragrafo vedremo come definire l'array `dataLayer` e come inviare i dati al suo interno.

## Come utilizzare il DataLayer?
Ci sono due modi per definire il la variabile `dataLayer`. Li presenterò entrambi per completezza, ma uno dei due, il primo, non è consigliato in quanto può compromettere la lettura dei dati da parte di Google Analytics, Tag Manager o Ads.

#### Primo metodo: definire una variabile nel modo standard

Quando si assegna un valore a una variabile usando il segno di uguale ( = ), si sta in realtà riallocando la variabile a un nuovo valore e il sistema di garbage collection dell'ambiente di runtime cancella via il valore precedente.

In parole povere, se si ridefinisce `dataLayer` dopo lo snippet del contenitore GTM, si cancellano le funzioni proprietarie di GTM.
Si crea il caos perché GTM modifica il metodo JavaScript `push()` che tutti gli array hanno (aggiungendo ad esempio dei "listener" che reagiscono agli eventi nella pagina).

Ridefinendo dataLayer, i tag di Tag Manger (o Ads/Analytics) non verranno più attivati.

Consideriamo il seguente esempio:


```javascript
(function(w,d,s,l,i){
    w[l]=w[l]||[];
    w[l].push({
        'gtm.start':new Date().getTime(),
        event:'gtm.js'
    });
    var f=d.getElementsByTagName(s)[0],
        j=d.createElement(s),
        dl=l!='dataLayer'?'&l='+l:'';
    j.async=true;
    j.src='https://www.googletagmanager.com/gtm.js?id='+i+dl;
    f.parentNode.insertBefore(j,f);
    })
(window,document,'script','dataLayer','GTM-XXXXXX');
```


Come da [documentazione](https://developers.google.com/tag-platform/tag-manager/web) questo snippet di codice va inserito il più in alto possibile nella pagina HTML per fare in modo che Tag Manager venga caricato quanto prima.
In questo codice, nella seconda riga, viene definita la variabile `dataLayer`.
Ma cosa accadrebbe se volessi inviare anche i dati prsenti nello snippet di prima? Ad esempio il valore di `pagePath`?
Subito dopo il codice di Tag Manager potrei fare:


```javascript
dataLayer = [
    {pagePath: '/home'},
]
```


In questo modo però andrei a cancellare tutti i dati caricati inizialmente da Tag Manager rendendo di fatto l'implementazione inutile.

Per risolvere questo problema utilizziamo l'unica soluzione che ci permette di aggiungere elementi all'array senza sovrascrivere i dati precedenti.

#### Secondo metodo: definire il Data Layer solo se non esiste
In programmazione è prevista la [valutazione a corto circuito](https://it.wikipedia.org/wiki/Valutazione_a_corto_circuito) ovvero si considera il secondo operando solo se il primo non risulta vero.

Ritorniamo alla seconda riga dello snippet di Tag Manager `w[l]=w[l]||[]`, se la estendessimo, [considerando la guida di Google](https://developers.google.com/tag-platform/tag-manager/web/datalayer) allora avremmo `window.dataLayer = window.dataLayer || []`
Ovvero:
    - definisci la variabile `dataLayer` (`window.dataLayer =`)
    - fa' che sia uguale a `window.dataLayer` se esiste
    - oppure (`||`) se `window.dataLayer` non dovesse esistere
    - `window.dataLayer` sarà un array vuoto, `[]`

in questo modo il Data Layer verrà definito solo una volta e tutte le successive interazioni non andranno a sovrascrivere i dati precedentemente inseriti.

### Invio dei dati al Data Layer
Per fare in modo che l'array `dataLayer` raccolga i dati che vogliamo inviare alle nostre piattaforme possiamo servirci del [metodo JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push) `push()`.
Il metodo `push()` aggiunge uno o più elementi alla fine di un array.
Quindi volendo inviare nuovi dati al `dataLayer` nel mio sito userò il seguente codice:
```javascript
dataLayer.push(
    {event: 'pageView', pagePath: '/home'},
    {variable1 : 'value'}}
)
```
In questo modo questi due oggetti verranno agganciati alla fine dell'array definito dal codice di Tag Manager.
Lo stesso ragionamento è valido se stiamo utilizzando la funzione `gtag()`.

### Data Layer e gtag
Il tag Google (gtag.js) è un singolo tag che è possibile aggiungere al sito web per utilizzare vari prodotti e servizi Google (ad es. Google Ads, Google Analytics, Campaign Manager, Display & amp; Video 360 e Search Ads 360). 
L'utilizzo di gtag è alternativo a quello di Tag Manager, è sconsigliato utilizzare le due soluzioni in parallelo.

Analizziamo il codice gtag utilizzato per definire, ad esempio, un'installazione di GA4

```javascript
window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments)};
  gtag('js', new Date());

  gtag('config', 'G-xxxxxx');
```
La prima riga, come vediamo, è la valutazione a corto circuito utilizzata precedentemente.
Questo snippet quindi crea il `dataLayer` solo se non già presente e definisce una funzione chiamata `gtag` che invia al `dataLayer` gli argomenti della funzione stessa utilizzando il metodo `push()`.
In altre parole quando utilizzo il comando `gtag('config', 'G-xxxxxx');` i parametri tra parentesi (`'config'` e `'G-xxxxxx'`) vengono inviati al `dataLayer` utilizzando `push()`.


### Vantaggi dell'utilizzo del  DataLayer
Il Data Layer è uno dei concetti principali di Google Tag Manager, e del tagging di Google in generale, che garantisce la massima flessibilità, portabilità e facilità di implementazione. 
Senza di esso, Tag Manager non funzionerebbe. 
È ciò che fa funzionare correttamente la gestione dei tag. È la chiave per sbloccare il potenziale di Google Tag Manager.

Quindi, un Data Layer è il luogo di un sito web in cui, in base alle necessità, è possibile memorizzare temporaneamente i dati (relativi agli utenti, al contenuto della pagina, ecc.). 
Da lì, Google Tag Manager legge queste informazioni, le utilizza in tag/trigger/variabili o le invia ad altri strumenti, come Google Analytics, Google Adwords, Facebook/Meta e così via.

La comodità più grande, a mio avviso, è la gestione temporale dei dati, ovvero, è possibile inviare parametri e/o eventi al Data Layer in maniera differita rispetto al caricamento della pagina. 
Ad esempio, possiamo decidere di inviare l'evento `form_submit` solo quando effettivament un form è stato inviato o di inviare i dati relativi ai prodotti visualizati solo quando effettivamente l'utente sta visionando quell'articolo.
Mi rendo conto dell'ovvietà dell'affermazione, proviamo a pensare cosa accadrebbe se, invece, dovessimo inviare tutti i dati al caricamento di Tag Manager senza la possibilità di raccoglierne altri man mano che l'utente naviga il sito.

### Le best practices per l'utilizzo del DataLayer di Google
Un aspetto importante che vale la pena sottolineare la pulizia e l'ordine nell'invio dei dati (ecco perché ho paragonato il Data Layer ad una cassettiera 😏).
L'organizzazione del Data Layer dovrebbe rispondere ai seguenti requisiti:
- Utilizzo di categorie logicamente pianificate
- Impiego di nomi chiari e descrittivi per le variabili e gli eventi
- Manutenibilità per rendere semplici le operazioni future

Immaginiamo di voler raccogliere i dati nella pagina del checkout e vogliamo inviare al `dataLayer` le seguenti variabili:
`totaleCarrello`, `numeroDiArticoli`, `infoArticoli`.
La soluzione migliore, a mio avviso, potrebbe essere quella di creare un oggetto contenente queste tre variabili , in altre parole creeremo un oggetto dentro un altro oggetto, quindi:
```javascript
dataLayer.push({
    checkout: {
        totaleCarrello: 52,
        numeroDiArticoli: 3,
        infoArticoli: [{
            idArticolo: 1234,
            prezzoArticolo: 0.99
        },{
           idArticolo: 9874,
            prezzoArticolo: 2.99 
        }]
    }
})
```

Nota come le tre variabili siano state categorizzate dentro l'oggetto `checkout` e come `infoArticoli` a sua volta contenga un array con altri oggetti.
In questo modo se volessi aggiungere (o rimuovere) nuove proprietà in futuro basterà fare riferimento all'oggetto `checkout`.
Vale la pena spendere due qualche parola anche sulla proprietà `infoArticoli`, come si può intuire è un array di oggetti (la stessa logica del Data Layer) perché in questo modo permette di contenere in maniera ordinata le variabili con lo stesso nome che però si riferiscono a prodotti diversi..
Quindi, come abbiamo visto, possiamo organizzare il Data Layer utilizzando oggetti che contengono altre proprietà così che possa rendere semplice e logicamente ordinato il lavoro.

### Controllare i dati nel Data Layer
Prima di raccogliere i dati con Tag Manager (o Analytics) potrebbe essere necessario eseguire un controllo sui dati nel direttamente dal browser.

1) 
   Poiché il Data Layer è un oggetto JavaScript collegato all'oggetto `window` del browser, è possibile digitare semplicemente il nome del livello dati nella console per verificarlo. 
   Ecco i passaggi:
   - Aprire dev tools nel browser.
   - Andare alla console.
   - Digitare il nome del livello dati.
   - Premere invio.
   
   Questo è un modo semplice e veloce per vedere quali dati sono attualmente presenti nel livello dati; tuttavia, il test in questo modo è manuale e non è ideale per i test ripetitivi.

 {% include figure image_path="/assets/images/posts/2022/dataLayer-chrome-console.jpg" alt="this is a placeholder image" caption="Il livello dati visualizzato nella console di Chrome" %}

1) 
   Usa un'estensione tipo [Datalayer checker](https://chrome.google.com/webstore/detail/datalayer-checker/ffljdddodmkedhkcjhpmdajhjdbkogke)
   Esistono molteplici estensioni per Chrome che ti permetteranno di controllare i dati presenti nel Data Layer, ti basterà cercare quella che più si avvicina alle tue necessità.











