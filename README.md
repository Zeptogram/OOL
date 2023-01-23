# INTRODUZIONE

Ai tempi di Simula e del primo Smalltalk, molto molto tempo prima di Python, 
Ruby, Perl e SLDJ, i programmatori Lisp già producevano una pletora di 
linguaggi object oriented. Il vostro progetto consiste nella costruzione di 
un’estensione “object oriented” di Common Lisp, chiamata OOΛ, e di 
un’estensione “object oriented” di Prolog, chiamata OOΠ. 
OOΛ è un linguaggio object-oriented con eredita' multipla. Il suo scopo e' 
didattico e mira soprattutto ad evidenziare aspetti dell’implementazione di 
linguaggi object-oriented:
- il problema di dove e come recuperare i valori ereditati
- come rappresentare i metodi e le loro chiamate
- come manipolare il codice nei metodi stessi

# PRIMITIVE

NOME:
DEF-CLASS

SINTASSI:
’(’ def-class <class-name> <parent> <slot-value>* ’)’

PARTICOLARI:
Definisce la struttura di una classe e la memorizza in una locazione 
centralizzata (variabile globale -> *classes-specs*)
Se le condizioni di invocazione sono rispettate, il metodo ritorna 
il nome della classe 
-----------------------------
NOME:
CREATE

SINTASSI:
’(’ new <class-name> [<slot-name> <value>]* ’)’

PARTICOLARI:
Crea un'istanza di una classe definita tramite def-class
Se il nome dell'istanza esiste gia', l'istanza precedente viene 
sostituita da quella attuale
Se non esiste, ritorna un errore
-------------------------------
NOME:
IS-CLASS

SINTASSI:
’(’ is-class <class-name> ’)’

PARTICOLARI:
Restituisce T se il simbolo passato e' il nome di una classe
-------------------------------
NOME:
IS-INSTANCE

SINTASSI:
’(’ is-instance <value> [<class-name>]’)’

PARTICOLARI:
Restituisce T se l’oggetto passato e' l’istanza di una classe
--------------------------------
NOME: 
<<

SINTASSI:
’(’ << <instance> <slot-name> ’)’

PARTICOLARI:
Se <instance> non ha <slot-name>, fa un controllo della sua 
classe.
Se anche la classe non ha <slot-name>, fa un controllo delle 
classi parents.
Ritorna lo <slot-value> relativo all'istanza.
Se <slot-name> non esiste nella classe relativa all'istanza 
passata, viene segnalato un errore
---------------------------------
NOME:
<<*

SINTASSI:
’(’ <<* <instance> <slot-name>+ ’)’

PARTICOLARI:
Ritorna il valore associato all'ultimo elemento 
di <slot-name> nell'ultima istanza
Se <slot-name> non è presente nella classe dell'istanza, 
la funzione ritorna un errore

# FUNZIONI

NOME: 
check-method

PARTICOLARI:
Se slots non è nullo, estrae i metodi dagli slots, 
ritornando T
-------------------------------
NOME: 
get-method-names

PARTICOLARI:
Dato in input una lista che contiene metodi, estrae 
e restituisce come cons solo i nomi del metodo senza il corpo.
-------------------------------
NOME:
slot-structure

PARTICOLARI:
Crea utilizzando <slots>, una cons contenente 
i valori ritornati dalle funzioni 
process-method e get-method-names

Esegue una chiamata ricorsiva con gli elementi 
della 
-------------------------------
NOME:
check-slot-exists

PARTICOLARI:
Se la classe inserita non ha <slots>, ritorna nil.
Se <slots> esiste all'interno della classe, il metodo 
ritorna T.
-------------------------------
NOME:
get-class-data

PARTICOLARI:
Se la classe non ha <slot-name>, lo ricerca all'interno 
delle classi genitori.
Se non viene trovato <method> o <slot-name>, ritorna un 
errore.
-------------------------------
NOME:
process-method

PARTICOLARI:
Viene chiamata la rewrite-method-code per inserire il 
parametro "this".
Permette di richiamare un metodo con la notazione 
funzionale
-------------------------------
NOME:
rewrite-method-code

PARTICOLARI:
Prende come argomenti <method-name> e <method-spec>
Inserisce con una funzione lambda il parametro 
this in cima alla lista degli argomenti

# ALCUNI TEST EFFETTUATI

(def-class 'person nil 'name "Eve" 'age 56)
Risultato: PERSON

(def-class 'superhero '(person) :age 4092 'prova "undefined")
Risultato: SUPERHERO

(defparameter test (create 'person 'name "Test" 'age 20))
Risultato: TEST

(defparameter who (create 'superhero 'name "WHO" 'age 10 'prova test))
Risultato: WHO

(defparameter who (create 'superhero 'name "WHO" 'age 10 'prova who))
Risultato: WHO

(write who)
Risultato:
(OOLINST SUPERHERO
 ((NAME . "WHO") (AGE . 10)
  (PROVA OOLINST SUPERHERO
   ((NAME . "WHO") (AGE . 10)
    (PROVA OOLINST PERSON ((NAME . "Test") (AGE . 20)))))))

(write (<< who2 'prova))
Risultato: (OOLINST PERSON ((NAME . "Test") (AGE . 20)))

(write (is-instance (<< who 'prova)))
Risultato: T

(write (eq (<< (<< who2 'prova) 'prova) (<<* who2 'prova 'prova)))
Risultato: T

(<<* who2 'prova 'prova)
Risultato: (OOLINST PERSON ((NAME . "Test") (AGE . 20)))

-------------------------------
ENG: This is an italian project
-------------------------------
EOF
