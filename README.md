# Guida XP Programming per Laravel

> **Extreme Programming (XP)** è una metodologia agile che enfatizza la qualità del codice, il feedback rapido e l'adattamento continuo al cambiamento. Questa guida applica i principi XP nel contesto moderno dello sviluppo Laravel, dove il dialogo continuo e il feedback immediato sono elementi chiave del processo.

## Principi Fondamentali

**Ricorda:**
- XP non è un dogma, ma un insieme di pratiche da adattare al contesto
- La qualità del codice è un investimento, non un costo
- Il feedback rapido è fondamentale per il successo
- La semplicità è la massima sofisticazione
- Il dialogo continuo migliora sia il codice che la conoscenza

**Principio guida:**
> "Fai funzionare, fallo bene, fallo veloce" - Kent Beck

Ma sempre nell'ordine corretto: prima funziona, poi lo migliori, poi lo ottimizzi. Ogni passo deve essere validato prima di procedere al successivo.

---

## Indice

- [Valori Fondamentali](#valori-fondamentali)
- [Pratiche Essenziali](#pratiche-essenziali)
- [Workflow XP nel Contesto Laravel](#workflow-xp-nel-contesto-laravel)
- [Strumenti e Setup](#strumenti-e-setup)
- [Problem Reframing](#problem-reframing)
- [Checklist Operativa](#checklist-operativa)

---

## Valori Fondamentali

### 1. **Comunicazione**

La comunicazione è il fondamento di XP. Nel contesto moderno, questo significa:

- Condividere conoscenza continuamente attraverso il codice
- Documentare attraverso nomi semantici e architettura chiara
- Mantenere un dialogo costante durante lo sviluppo
- Code review come momento di apprendimento e confronto
- Chiedere feedback immediato quando si hanno dubbi

Il codice stesso diventa il veicolo principale di comunicazione: deve essere leggibile, esplicativo e raccontare la storia del dominio.

### 2. **Semplicità**

La semplicità è la massima sofisticazione. Questo valore guida ogni decisione:

- **YAGNI** (You Aren't Gonna Need It): implementare solo ciò che serve ora
- Evitare over-engineering e astrazioni premature
- Refactoring continuo per mantenere il codice semplice
- Architettura chiara che comunica l'intento senza complessità inutile
- Valutare sempre se esiste un modo più semplice

Quando si valuta una soluzione, chiedersi: "È la soluzione più semplice che funziona?" Se qualcuno suggerisce un approccio più complesso, valuta criticamente se la complessità aggiuntiva è giustificata.

### 3. **Feedback**

Il feedback rapido è essenziale per adattarsi e migliorare:

- Test automatici per feedback immediato sul comportamento del codice
- Feedback continuo durante lo sviluppo, non solo alla fine
- Deploy frequenti per validare con gli utenti reali
- Retrospettive regolari per migliorare il processo
- Metriche di qualità del codice per identificare trend

Il ciclo di feedback deve essere il più corto possibile. Ogni modifica al codice dovrebbe essere validata rapidamente attraverso test o esecuzione.

### 4. **Coraggio**

Il coraggio permette di prendere decisioni difficili ma necessarie:

- Refactoring senza paura quando il codice può essere migliorato
- Eliminare codice morto o non più necessario
- Cambiare architettura quando serve, anche se richiede lavoro
- Ammettere errori e imparare da essi
- Sfidare soluzioni complesse quando esistono alternative più semplici

Il coraggio non significa temerarietà: significa avere la sicurezza di fare le scelte giuste supportati da test e feedback continuo.

---

## Pratiche Essenziali

### Test-Driven Development (TDD)

Il TDD è il cuore di XP. Il ciclo Red-Green-Refactor guida ogni implementazione:

1. **RED**: Scrivi un test che fallisce, descrivendo il comportamento desiderato
2. **GREEN**: Scrivi il minimo codice necessario per far passare il test
3. **REFACTOR**: Migliora il codice mantenendo i test verdi

**Nel contesto Laravel/Pest:**

```php
// 1. RED: Test che descrive il comportamento desiderato
test('un utente può creare un nuovo progetto', function () {
    $user = User::factory()->create();
    
    $response = $this->actingAs($user)
        ->post('/progetti', [
            'nome' => 'Nuovo Progetto',
            'descrizione' => 'Descrizione test'
        ]);
    
    $response->assertStatus(201);
    $this->assertDatabaseHas('progetti', [
        'nome' => 'Nuovo Progetto',
        'user_id' => $user->id
    ]);
});

// 2. GREEN: Implementazione minima
// 3. REFACTOR: Migliorare mantenendo i test verdi
```

**Regole d'oro:**
- Un test = un comportamento specifico
- Test descrittivi che documentano l'intento e il dominio
- Test veloci e isolati per feedback immediato
- Mantenere alta copertura delle parti critiche del sistema

**Workflow con feedback continuo:**
- Scrivi il test pensando al comportamento desiderato
- Valida che il test fallisca per il motivo giusto
- Implementa la soluzione più semplice
- Migliora il codice mantenendo i test verdi
- Chiedi feedback se il test o l'implementazione non sono chiari

### Refactoring Continuo

Il refactoring è parte integrante del ciclo di sviluppo, non un'attività separata:

**Quando refactorizzare:**
- Dopo aver fatto passare un test (fase REFACTOR del ciclo TDD)
- Quando si nota duplicazione (DRY)
- Quando il codice diventa difficile da leggere o capire
- Quando si aggiunge una feature e si nota un pattern emergente
- Quando si identifica un code smell o un'opportunità di miglioramento

**Principi:**
- Piccoli passi incrementali, mai grandi riscritture
- Mantenere i test sempre verdi durante il refactoring
- Non aggiungere funzionalità durante il refactoring
- Migliorare la semantica e la leggibilità
- Chiedere feedback se non si è sicuri della direzione

**Esempio:**

```php
// Prima: codice poco chiaro
public function process($data) {
    if ($data['type'] == 'A') {
        return $this->doA($data);
    } else if ($data['type'] == 'B') {
        return $this->doB($data);
    }
}

// Dopo refactoring: più semantico e estendibile
public function process(PaymentData $data): PaymentResult {
    return $this->paymentProcessorFactory
        ->createFor($data->getType())
        ->process($data);
}
```

**Identificare opportunità di refactoring:**
- Analizzare il codice per pattern ripetuti
- Cercare nomi poco chiari o generici
- Identificare responsabilità multiple in una singola classe
- Valutare la complessità ciclomatica
- Chiedere un'opinione esterna quando si è incerti

### Pair Programming

Il pair programming è una pratica fondamentale di XP che migliora qualità e conoscenza attraverso il confronto continuo.

**Quando usarlo:**
- Per feature complesse o critiche per il business
- Per condividere conoscenza del dominio o tecniche
- Per code review in tempo reale
- Per onboarding di nuovi sviluppatori
- Quando si vuole un confronto continuo durante lo sviluppo

**Modalità tradizionali:**
- **Driver/Navigator**: uno scrive il codice, l'altro guida la direzione
- **Ping-Pong**: alternanza tra scrivere test e implementazione
- **Mob Programming**: tutto il team insieme su un problema complesso

**Pair Programming con Agenti IA**

Nel contesto moderno, il pair programming si estende naturalmente al lavoro con agenti IA, creando un dialogo continuo che migliora sia la conoscenza che la qualità del codice.

**Come funziona:**
- **Alternanza dei ruoli**: a seconda del contesto, tu o l'agente guidate la soluzione
- **Confronto continuo**: l'agente propone soluzioni, tu valuti e migliori
- **Code review in tempo reale**: analisi continua del codice con suggerimenti di miglioramento
- **Apprendimento reciproco**: l'agente impara dal tuo contesto, tu apprendi nuove tecniche

**Quando l'agente guida (Navigator):**
- Proponi un problema o una feature da implementare
- L'agente suggerisce approcci, pattern o soluzioni alternative
- Tu valuti le proposte e scegli la direzione migliore
- L'agente aiuta a identificare edge case o potenziali problemi

**Quando tu guidi (Driver):**
- Scrivi il codice seguendo la tua visione e conoscenza del dominio
- L'agente fa code review e suggerisce miglioramenti
- L'agente identifica duplicazioni, code smell o opportunità di refactoring
- L'agente propone test da aggiungere o migliorare

**Vantaggi:**
- **Conoscenza aumentata**: condivisione di pattern e best practice
- **Qualità migliorata**: feedback continuo sul codice
- **Velocità**: risposte immediate a domande tecniche
- **Documentazione**: aiuto nella documentazione di decisioni e approcci
- **Copertura**: suggerimenti di test che potresti aver dimenticato

**Best practice:**
- Sii esplicito nel contesto: condividi architettura, vincoli e obiettivi
- Valida sempre le proposte: non accettare ciecamente, valuta criticamente
- Usa il confronto per esplorare alternative: chiedi "come possiamo farlo diversamente?"
- Mantieni il controllo: tu sei il decisore finale, l'agente è un consulente
- Impara dai suggerimenti: usa l'interazione per migliorare le tue competenze

**Esempio di interazione:**

```
Tu: "Devo creare un servizio per calcolare il totale di un ordine con sconti e tasse"

Agente: "Propongo un approccio con una classe OrderCalculator che separa 
le responsabilità. Vuoi che implementi prima i test?"

Tu: "Sì, ma considera anche gli sconti cumulativi"

Agente: "Ecco i test per gli sconti cumulativi. Nota che ho usato 
una strategia pattern per gestire diversi tipi di sconto..."

Tu: "Ottimo, ma preferisco una soluzione più semplice per ora"

Agente: "Capito. Ecco una versione semplificata che mantiene la 
flessibilità per estensioni future..."
```

Questa forma di pair programming è particolarmente efficace perché mantiene i benefici del confronto continuo, accelera l'apprendimento e migliora la qualità attraverso feedback costante.

### Design Semplice

Il design semplice è un obiettivo continuo, non un punto di arrivo:

**Principi:**
- **KISS** (Keep It Simple, Stupid): la semplicità è sempre preferibile
- **SOLID** applicato con pragmatismo, non dogmaticamente
- Pattern solo quando necessario, non per dimostrare conoscenza
- Evitare astrazioni premature: astrarre quando emerge un pattern reale

**Nel contesto Laravel:**
- Usare le convenzioni del framework invece di creare nuove astrazioni
- Service classes per logica di business complessa
- Form Request per validazione, non logica custom nei controller
- Resource classes per trasformazione dati API
- Eloquent per accesso ai dati (evitare repository pattern se non necessario)

**Valutare la semplicità:**
- Chiedersi: "Questa soluzione è la più semplice che funziona?"
- Confrontare approcci alternativi prima di implementare
- Valutare se l'astrazione aggiunge valore o solo complessità
- Preferire codice esplicito a pattern nascosti

### Test Automatici

I test automatici sono la rete di sicurezza che permette di refactorizzare e migliorare con fiducia:

**Piramide dei test:**

```
        /\
       /  \  E2E (pochi, critici)
      /____\
     /      \  Integration (alcuni, flussi importanti)
    /________\
   /          \  Unit (molti, logica di business)
  /____________\
```

**Con Pest:**
- **Unit tests**: logica pura, veloci, molti
- **Feature tests**: interazione con Laravel (DB, HTTP), alcuni
- **Browser tests**: Livewire components, interazioni complesse, pochi

**Esempio struttura:**

```php
// tests/Unit/Services/PaymentProcessorTest.php
test('calcola correttamente il totale con sconto', function () {
    $processor = new PaymentProcessor();
    $result = $processor->calculateTotal(100, 10);
    
    expect($result)->toBe(90.0);
});

// tests/Feature/Projects/CreateProjectTest.php
test('un utente autenticato può creare un progetto', function () {
    $user = User::factory()->create();
    
    $response = $this->actingAs($user)
        ->post(route('progetti.store'), [
            'nome' => 'Test Project'
        ]);
    
    $response->assertRedirect(route('progetti.index'));
    $this->assertDatabaseHas('progetti', ['nome' => 'Test Project']);
});
```

**Copertura e qualità:**
- Focus sulla copertura delle parti critiche, non sul 100%
- Test che documentano il comportamento, non l'implementazione
- Test veloci per feedback immediato
- Identificare gap di copertura attraverso analisi del codice

### Integrazione Continua

L'integrazione continua mantiene il codice sempre in uno stato deployabile:

**Pratiche:**
- Commit frequenti (almeno una volta al giorno, idealmente più spesso)
- Push frequenti (non lasciare codice solo in locale)
- CI/CD per eseguire test automaticamente ad ogni push
- Deploy frequenti (ogni feature completata e testata)

**Git workflow suggerito:**
- Branch per feature (`feature/nome-feature`)
- Pull request con review per condividere conoscenza
- Merge solo dopo test verdi e review
- Tag per release significative

**Feedback dal CI/CD:**
- I test devono passare prima del merge
- Il codice deve rispettare gli standard (linter, formatter)
- Le metriche di qualità devono essere monitorate
- I deploy devono essere automatici e reversibili

---

## Workflow XP nel Contesto Laravel

### 1. **Planning Game**

Il planning game definisce cosa fare e in che ordine:

**User Stories:**
- Formato: "Come [ruolo], voglio [azione] per [beneficio]"
- Stime con story points o tempo basate su esperienza
- Priorità con il cliente/product owner
- Breaking down in task piccole e implementabili

**Esempio:**
```
Come sviluppatore, voglio vedere i test fallire prima di implementare
per essere sicuro che il test verifichi il comportamento corretto.
```

**Durante la pianificazione:**
- Chiarire i requisiti attraverso domande
- Identificare i test necessari per validare la feature
- Stimare la complessità considerando test, implementazione e refactoring
- Valutare alternative più semplici se possibile

### 2. **Small Releases**

Release piccole e frequenti riducono il rischio e aumentano il feedback:

- Release frequenti (settimanali o più spesso se possibile)
- Feature complete e testate prima del deploy
- Deploy incrementale che aggiunge valore continuamente
- Rollback semplice se qualcosa va storto

Ogni release dovrebbe aggiungere valore misurabile. Se una feature è troppo grande, spezzala in release più piccole.

### 3. **Metaphor del Sistema**

Il sistema deve comunicare attraverso il codice:

- Naming semantico che riflette il dominio del business
- Architettura che comunica l'intento senza bisogno di documentazione esterna
- Documentazione viva nel codice, non in file separati
- Modelli che rappresentano chiaramente il dominio

**Esempio:**
```php
// Buono: il nome comunica l'intento
class ProjectCompletionService {
    public function markAsCompleted(Project $project): void {
        // ...
    }
}

// Evitare: nomi generici o tecnici
class ProjectHandler {
    public function doStuff($p): void {
        // ...
    }
}
```

**Mantenere la metafora:**
- Rivedere i nomi quando il dominio evolve
- Refactorizzare quando la metafora non è più chiara
- Chiedere feedback se i nomi non sono chiari
- Usare il linguaggio del dominio, non quello tecnico

### 4. **Collective Code Ownership**

Tutti sono responsabili di tutto il codice:

- Tutti possono modificare qualsiasi parte del codice
- Responsabilità condivisa per la qualità
- Code review per apprendimento e condivisione, non controllo
- Nessuna parte del codice è "di proprietà" di una persona

Questo principio permette di refactorizzare liberamente e migliorare continuamente il codice, senza timore di "toccare" codice di altri.

### 5. **Coding Standards**

Standard condivisi rendono il codice più leggibile e manutenibile:

**Per Laravel:**
- PSR-12 per stile codice
- Laravel conventions per struttura e organizzazione
- Nomi semantici e descrittivi che comunicano l'intento
- Commenti solo quando necessario (codice auto-documentante preferito)

**Esempio:**
```php
// Buono: codice auto-esplicativo
public function calculateProjectCompletionPercentage(Project $project): int {
    $completedTasks = $project->tasks()->completed()->count();
    $totalTasks = $project->tasks()->count();
    
    if ($totalTasks === 0) {
        return 0;
    }
    
    return (int) round(($completedTasks / $totalTasks) * 100);
}

// Evitare: codice criptico
public function calc($p) {
    $c = $p->t()->where('done', 1)->count();
    $t = $p->t()->count();
    return $t ? round($c/$t*100) : 0;
}
```

**Mantenere gli standard:**
- Usare linter e formatter automatici
- Code review per condividere e rafforzare gli standard
- Refactorizzare quando il codice non rispetta gli standard
- Documentare eccezioni quando necessario

### 6. **Sustainable Pace**

Il ritmo sostenibile è l'unico ritmo che funziona nel lungo termine:

- Lavorare a ritmo sostenibile, non sprint continui
- Evitare crunch time che degradano la qualità
- Qualità > velocità nel lungo termine
- Prendere pause per mantenere la concentrazione

La qualità del codice e la sostenibilità del processo sono più importanti della velocità a breve termine.

---

## Strumenti e Setup

### Stack Tecnologico

- **Laravel 9+**: Framework PHP con convenzioni chiare
- **Livewire V3**: Componenti reattivi per interfacce dinamiche
- **Pest**: Framework di testing moderno e leggibile
- **Docker (Laravel Sail)**: Ambiente di sviluppo consistente

### Configurazione Pest

**phpunit.xml** (o **pest.php**):
```php
<?php

use Pest\Laravel\TestCase;

uses(TestCase::class)->in('Feature');
uses()->in('Unit');
```

**Struttura test:**
```
tests/
├── Unit/
│   ├── Services/
│   ├── Models/
│   └── Helpers/
├── Feature/
│   ├── Projects/
│   ├── Users/
│   └── Api/
└── Pest.php
```

### Git Hooks (opzionale)

**pre-commit**: eseguire test veloci prima del commit per feedback immediato
```bash
#!/bin/sh
./vendor/bin/pest --filter="Unit"
```

### CI/CD

**GitHub Actions esempio:**
```yaml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: '8.1'
      - name: Install Dependencies
        run: composer install
      - name: Run Tests
        run: ./vendor/bin/pest
```

---

## Problem Reframing

Il **Problem Reframing** è una tecnica di pensiero che aiuta a vedere i problemi da prospettive diverse, trovando soluzioni più creative ed efficaci.

### Processo

1. **Identificare il problema iniziale**
   - Cosa sembra essere il problema?
   - Quali sono i sintomi osservabili?
   - Cosa stiamo assumendo?

2. **Chiedere "Perché?" più volte**
   - Perché questo è un problema?
   - Perché accade?
   - Qual è il bisogno sottostante?
   - Cosa stiamo davvero cercando di risolvere?

3. **Riformulare il problema**
   - Cambiare prospettiva e punto di vista
   - Guardare il problema come opportunità
   - Chiedere "Come possiamo...?" invece di "Perché non possiamo...?"
   - Esplorare il problema da angolazioni diverse

4. **Esplorare soluzioni alternative**
   - Brainstorming senza giudizio prematuro
   - Soluzioni creative e non convenzionali
   - Valutare pro e contro di ogni alternativa
   - Confrontare approcci diversi prima di scegliere

### Esempio Pratico

**Problema iniziale:**
> "Gli utenti non compilano il form di registrazione"

**Reframing:**
1. **Perché?** → Il form è troppo lungo/complesso
2. **Perché?** → Richiediamo troppe informazioni iniziali
3. **Riformulazione:** 
   - "Come possiamo ridurre l'attrito nella registrazione?"
   - "Quali informazioni sono davvero necessarie subito?"
   - "Cosa succederebbe se chiedessimo meno informazioni?"
4. **Soluzioni:**
   - Registrazione con email + password minimale
   - Completamento profilo dopo il primo login
   - Social login per velocizzare il processo
   - Form progressivo che mostra progresso

### Applicazione nello Sviluppo

**Quando reframare:**
- Prima di implementare una soluzione complessa
- Quando i test sono difficili da scrivere (forse il problema è mal definito)
- Quando una feature non viene usata come previsto
- Quando ci sono conflitti su come implementare qualcosa
- Quando si sente che "c'è qualcosa che non va" ma non si sa cosa

**Domande utili:**
- "Cosa stiamo davvero cercando di risolvere?"
- "C'è un modo più semplice di vedere questo problema?"
- "Cosa succederebbe se non facessimo nulla?"
- "Come lo risolverebbe un principiante senza conoscenze pregresse?"
- "Qual è il minimo che risolverebbe il problema?"

**Usare il confronto per reframing:**
- Proporre il problema e chiedere prospettive alternative
- Esplorare soluzioni diverse prima di implementare
- Valutare criticamente le assunzioni iniziali
- Chiedere feedback su approcci alternativi

---

## Checklist Operativa

### Prima di iniziare una feature

- [ ] User story chiara e ben definita
- [ ] Compreso il problema/necessità (problem reframing se necessario)
- [ ] Identificati i test da scrivere per validare il comportamento
- [ ] Ambiente di sviluppo pronto e funzionante
- [ ] Chiariti eventuali dubbi sul dominio o sui requisiti

### Durante lo sviluppo

- [ ] Seguire il ciclo TDD (Red-Green-Refactor)
- [ ] Commit frequenti con messaggi chiari e descrittivi
- [ ] Codice semantico e leggibile che comunica l'intento
- [ ] Refactoring continuo quando emergono opportunità
- [ ] Test sempre verdi prima di procedere
- [ ] Chiedere feedback quando si è incerti sulla direzione

### Prima del commit

- [ ] Tutti i test passano
- [ ] Codice senza duplicazioni evidenti
- [ ] Nomi semantici e chiari
- [ ] Nessun codice commentato o morto
- [ ] Struttura coerente con il resto del progetto
- [ ] Codice rispetta gli standard del progetto

### Prima del merge

- [ ] Code review completata (con feedback incorporato)
- [ ] Test di integrazione passano
- [ ] Documentazione aggiornata se necessario
- [ ] Nessun warning del linter
- [ ] Feature completa e funzionante
- [ ] Test aggiuntivi suggeriti durante la review implementati

### Retrospettiva (settimanale)

- [ ] Cosa è andato bene questa settimana?
- [ ] Cosa possiamo migliorare nel processo?
- [ ] Quali ostacoli abbiamo incontrato?
- [ ] Quali tecniche o pattern abbiamo imparato?
- [ ] Azioni concrete per la prossima settimana

---

## Risorse e Approfondimenti

### Letture Consigliate

- **"Extreme Programming Explained"** - Kent Beck
- **"Test Driven Development: By Example"** - Kent Beck
- **"Refactoring"** - Martin Fowler
- **"Clean Code"** - Robert C. Martin

### Link Utili

- [Laravel Testing Documentation](https://laravel.com/docs/testing)
- [Pest Documentation](https://pestphp.com/docs)
- [XP Values and Practices](http://www.extremeprogramming.org/)

