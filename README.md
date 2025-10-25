# Historical Lexicon of Commercial Law (HLCL) - Serializzazione RDF

Questo repository contiene la serializzazione RDF (Resource Description Framework) di un glossario di esempio tratto dall'**Historical Lexicon of Commercial Law (HLCL)**. Questo lavoro è parte del progetto **MICOLL** (Migrating Commercial Law and Language), finanziato dall'ERC.

L'HLCL è una risorsa digitale multilingue progettata per documentare e confrontare la terminologia del diritto commerciale in italiano, latino e tedesco tra l'XI e il XVII secolo. Questo dataset rappresenta la metodologia per trasformare il glossario originale non strutturato dell'HLCL in un formato leggibile meccanicamente (machine-readable) utilizzando il modello **OntoLex-Lemon**.

L'obiettivo è rappresentare i dati storici e filologici in modo da preservare le relazioni concettuali e la variazione diacronica, garantendo l'interoperabilità semantica e l'integrazione con altre risorse di Linguistic Linked Open Data (LLOD).

---

## Informazioni sul Dataset

Questo specifico file è una serializzazione *proof-of-concept* di un glossario di esempio in **italiano**.

* **Voci:** 392 
* **Concetti:** 59 
* **Formato:** RDF (sintassi Turtle) 
* **Modello:** OntoLex-Lemon 

I dati sono strutturati utilizzando un **framework onomasiologico**, che raggruppa i termini sotto concetti giuridico-commerciali condivisi (ad esempio, "arbitrato", "società").

---

## Modello Dati e Vocabolari

Il dataset è formalizzato utilizzando il modello **OntoLex-Lemon** , concentrandosi sulle classi principali `ontolex:LexicalEntry`, `ontolex:Form` e `ontolex:LexicalSense`.

La mappatura dei componenti del glossario originale al modello RDF è la seguente:

* **Concetto (Concept)**: Modellato come un'istanza di `ontolex:LexicalConcept`. Questo funge da perno centrale nella struttura onomasiologica. I concetti correlati sono collegati tramite `skos:related`.
* **Termine (Term)**: Rappresentato come un `ontolex:LexicalEntry`. La forma canonica scritta è memorizzata usando `ontolex:canonicalForm`.
* **Informazioni Grammaticali (Grammatical Information)**: Codificate usando il vocabolario **LexInfo**.
    * Parte del discorso (Part of Speech): `lexinfo:partOfSpeech` 
    * Genere (Gender): `lexinfo:gender` 
* **Varietà (Variety)**: Il volgare italo-romanzo (es. fiorentino, ferrarese) è collegato all'`ontolex:LexicalSense`  utilizzando la proprietà `lexinfo:geographic`. Questo si collega al **Thesaurus of Geographical Names (TGN)**.
* **Definizione (Definition)**: Allegata all'`ontolex:LexicalConcept` come stringa leggibile dall'uomo usando `skos:definition`.
* **Prima Attestazione (First Attestation)**: Modellata utilizzando il modulo **FRAC (Frequency, Attestation and Corpus Information)**. La `LexicalEntry` è collegata tramite `frac:attestation` a un nodo di attestazione, che a sua volta punta ai metadati del documento (un'istanza `dct:Text` da **Dublin Core**) utilizzando `frac:observedIn`.
* **Note Etimologiche (Etymological Notes)**: Acquisite usando `lexinfo:etymology`.
* **Altre Note (Other Notes)**: Acquisite usando `lexinfo:note`.

---

## Esempio di Serializzazione

Il seguente frammento mostra la serializzazione RDF (in formato Turtle) per la voce **'arbitramento'**, che designa il concetto **ARBITRATO**.

```turtle
@prefix rdf: [http://www.w3.org/1999/02/22-rdf-syntax-ns](http://www.w3.org/1999/02/22-rdf-syntax-ns).
@prefix ontolex: [http://www.w3.org/ns/lemon/ontolex](http://www.w3.org/ns/lemon/ontolex).
@prefix lexinfo: [http://www.lexinfo.net/ontology/3.0/lexinfo](http://www.lexinfo.net/ontology/3.0/lexinfo).
@prefix skos: [http://www.w3.org/2004/02/skos/core](http://www.w3.org/2004/02/skos/core).
@prefix dct: [http://purl.org/dc/terms/](http://purl.org/dc/terms/) .
@prefix frac: [http://www.w3.org/ns/lemon/frac#](http://www.w3.org/ns/lemon/frac#) .
@prefix ex: [http://example.com/hlel/](http://example.com/hlel/).
@prefix tgn: [http://vocab.getty.edu/tgn/](http://vocab.getty.edu/tgn/).
@prefix xsd: [http://www.w3.org/2001/XMLSchema#](http://www.w3.org/2001/XMLSchema#) .

# Metadati del documento per l'attestazione
ex:text_lanciaordinamenti a dct:Text;
    dct:bibliographicCitation "Stat. fior. [Lancia, Ordinamenti], 1356/1357";
    dct:date "1356/1357"^^xsd:string. 

# Forma canonica per la voce lessicale
ex:form_arbitramento a ontolex:Form;
    ontolex:writtenRep "arbitramento"@it;
    lexinfo:gender lexinfo:masculine;
    lexinfo:number lexinfo:singular. 

# Il concetto giuridico "ARBITRATO"
ex:concept_arbitrato a ontolex:LexicalConcept;
    skos:definition "Sentenza dell'arbitro"@it;
    skos:related ex:concept_arbitro;
    skos:prefLabel "arbitrato"@it;
    ontolex:lexicalizedSense ex:sense_arbitramento;
    ontolex:lexicalizedSense ex:sense_sentenciaArbitraria. 

# La voce lessicale per il termine "arbitramento"
ex:entry_arbitramento a ontolex:LexicalEntry;
    lexinfo:partOfSpeech lexinfo:noun;
    ontolex:sense ex:sense_arbitramento;
    ontolex:canonicalForm ex:form_arbitramento; 
    # Link all'attestazione
    frac:attestation [
        a frac:Attestation;
        rdf:value "arbitramento"@it;
        frac:observedIn ex:text_lanciaOrdinamenti
    ]. 

# Il senso di "arbitramento", che collega la voce al concetto
ex:sense_arbitramento a ontolex:LexicalSense;
    # Metadati sulla varietà geografica
    lexinfo:geographic tgn:7000457; # Florence
    lexinfo:geographic tgn:7004264. # Ferrara 

# Entità geografiche
tgn:7000457 a skos:Concept;
    skos:prefLabel "Firenze"@it. 
tgn:7004264 a skos:Concept;
    skos:prefLabel "Ferrara"@it. 
