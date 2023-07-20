# Vagrant

Vagrant è un software che si appoggia a VirtualBox per gestire macchine virtuali da linea di comando con pacchetti gia pronti per essere installati. 
Nello store di Vagrant si possono trovare configurazioni di macchine virtuali gia pronte all'uso con determinati programmi installati.

## Installiamo una macchina oracle
Come viene suggerito dalla pagina di [Yum Oracle](https://yum.oracle.com/boxes/) i passaggi da seguire sono semplici.
```sh
vagrant init oraclelinux/{release} <box json url>
vagrant up
vagrant ssh
```
Un esempio della prima riga per il download del Json file è
```sh
vagrant init oraclelinux/8 https://oracle.github.io/vagrant-projects/boxes/oraclelinux/8.json
```


## Location dei file
I file di base vengono installati all'interno della cartella `~/.vagrant.d/boxes` che per windows significa in `C:\User\{username}\~`.
Sembra che questi file non vengano utilizzati perchè le dimensioni dei dischi sono quelli presenti in Virtualbox

## Errore di rete
Con l'avvio tramite Vagrant non sono risucito a configurare la rete aziendale. Ho dovuto bypassare la configurazione di vagrant ed utilizzare VirtualBox


