# Introduzione

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

# Primitive

## DEF-CLASS

__SINTASSI:
’(’ def-class <class-name> <parent> <slot-value>* ’)’__

Definisce la struttura di una classe e la memorizza in una locazione 
centralizzata (variabile globale -> *classes-specs*)
Se le condizioni di invocazione sono rispettate, il metodo ritorna 
il nome della classe 

## CREATE

__SINTASSI:
’(’ new <class-name> [<slot-name> <value>]* ’)’__


Crea un'istanza di una classe definita tramite def-class
Se il nome dell'istanza esiste gia', l'istanza precedente viene 
sostituita da quella attuale
Se non esiste, ritorna un errore

## IS-CLASS

__SINTASSI:
’(’ is-class <class-name> ’)’__


Restituisce T se il simbolo passato e' il nome di una classe

## IS-INSTANCE

__SINTASSI:
’(’ is-instance <value> [<class-name>]’)’__

Restituisce T se l’oggetto passato e' l’istanza di una classe

## <<

__SINTASSI:
’(’ << <instance> <slot-name> ’)’__

Se <instance> non ha <slot-name>, fa un controllo della sua 
classe.
Se anche la classe non ha <slot-name>, fa un controllo delle 
classi parents.
Ritorna lo <slot-value> relativo all'istanza.
Se <slot-name> non esiste nella classe relativa all'istanza 
passata, viene segnalato un errore

## <<*

__SINTASSI:
’(’ <<* <instance> <slot-name>+ ’)’__

Ritorna il valore associato all'ultimo elemento 
di <slot-name> nell'ultima istanza
Se <slot-name> non è presente nella classe dell'istanza, 
la funzione ritorna un errore

# Funzioni

## check-method

Se slots non è nullo, estrae i metodi dagli slots, 
ritornando T

## get-method-names

Dato in input una lista che contiene metodi, estrae 
e restituisce come cons solo i nomi del metodo senza il corpo.

## slot-structure

Crea utilizzando <slots>, una cons contenente 
i valori ritornati dalle funzioni 
process-method e get-method-names

## check-slot-exists

Se la classe inserita non ha <slots>, ritorna nil.
Se <slots> esiste all'interno della classe, il metodo 
ritorna T.

## get-class-data

Se la classe non ha <slot-name>, lo ricerca all'interno 
delle classi genitori.
Se non viene trovato <method> o <slot-name>, ritorna un 
errore.

## process-method

Viene chiamata la rewrite-method-code per inserire il 
parametro "this".
Permette di richiamare un metodo con la notazione 
funzionale

## rewrite-method-code

Prende come argomenti <method-name> e <method-spec>
Inserisce con una funzione lambda il parametro 
this in cima alla lista degli argomenti

# Alcuni Test Effettuati

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
ENG: This is an italian project, Doc and comments in italian.
 
Made by Elio Gargiulo, Stefano Rigato for UNIMIB Linguaggi di Programmazione Course.

