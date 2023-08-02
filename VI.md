# GUIDA VI

## Il file di configurazione

Per visualizzare quale file di configurazione bisogna utilizzare, è meglio lanciare il comando
```sh
vi --version
VIM - Vi IMproved 8.2 (2019 Dec 12, compiled Feb 28 2023 00:00:00)
Included patches: 1-2637

Small version without GUI.
[...]
   system vimrc file: "/etc/virc"
     user vimrc file: "$HOME/.virc"
 2nd user vimrc file: "~/.vim/virc"
      user exrc file: "$HOME/.exrc"
       defaults file: "$VIMRUNTIME/defaults.vim"
  fall-back for $VIM: "/etc"
[...]
```
si scopre cosi che bisogna salvare le impostazioni nel file `.virc`
Questo è la versione preinstallata di base sui sitemi OL9. Per ottenere tutte le feature di VIm è opportuno installare la 'versione gigante'.
```sh
yum install vim
```
ed otteniamo una versione completa con la sintassi colorata e tante altre funzioni.
Questo è un esempio di [file di configurazione di vim](./file_configurazione/.vimrc)
che contiene molte delle impostazioni utili nell'uso del programma.



## Impostazioni da utilizzare
Queste impostazioni risolvono alcuni problemi di compatibilità tra la PuTTy e Vi inoltre aggiungono la visualizzazione dei numeri riga.
```vi
:set term=builtin_ansi
:set nocompatible
:set number
```
Possono essere copiati all'interno del file o aggiunti tramite uno echo
```sh
echo ':set number' >> ~/.vimrc
```



`?` ricerca il testo indicato
`n` trova la parolasuccessiva
`cw` Cancella tutto il contenuto fino al prossimo carattere speciale o spazio
`.` ripete l'ultima azione fatta
`dd` cancella tutta la riga
`u` UNDU cancella l'utlima azione
`yy` YANK copia la linea in cui sono
`p` PASTE incolla cio che ho di copiato esattamente dove mi trovo.
`v` in modalità visuale permette di selezionare il testo che poi andrà copiato con `y`
`:set number!` toggle visualizza/nasconde i numeri delle righe

## Split e Tab Mode
Per avviare Vi in modalita Tabbed uso l'opzione `-p` 

## Hard Force closed
Con il comando CTRL+Z forzo la chiusura di vim

## 