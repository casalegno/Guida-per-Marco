# VirtualBox

## I file di configurazione
Il file `~/.VirtualBox/VirtualBox.xml` dove su Windows significa `C:\User\{username}\~` contiene le indicazioni, che vengono lette dal programma, su come deve funzionare.
Abbiamo l'elenco delle macchine presenti e le indicazioni dove i file **.vbox,.vmdK** vengono salvati.
```conf
<MachineRegistry>
      <MachineEntry uuid="{d167c2df-83d6-408e-a2e0-707a918a5e82}" src="C:\Users\casalegno\VirtualBox VMs\LocaleMarco\LocaleMarco.vbox"/>
      ...
</MachineRegistry>
<SystemProperties defaultMachineFolder="D:\VirtualBox VMs" defaultHardDiskFormat="VDI" VRDEAuthLibrary="VBoxAuth" webServiceAuthLibrary="VBoxAuth" LogHistoryCount="3" proxyMode="0" exclusiveHwVirt="false"/>
```
Quest'ultimo *SystemProperties* contiene la path da modificare per le nuove installazioni. Mentre all'interno di *MachineRegistry*, troviamo gli indirizzi degli attuali HD Virtuali.

## Avvio in background
Per avviare una macchina in background, senza l'uso delle finestra, possiamo selezionare il pulsante Avvia con l'opzione Senza Finestra (dobbiamo conoscere l'indirizzo IP per accedere).
Da terminale dobbiamo lanciare il comando
```sh
VBoxManage startvm $VM --type headless
```
e per spegnerla doppia farlo all'interno della macchina con il comando
```sh
shutdown -h now
```
