# Esercizi: Crittografia Simmetrica

Introduzione

Per iniziare è necessario installare Python, la libreria PyCryptodome, e ImageMagick.

Su Ubuntu 21.04 / Debian 11
```
sudo apt install python3 python3-pip imagemagick
pip3 install --user pycryptodome
```
Su Arch Linux
```
sudo pacman -S python python-pycryptodome imagemagick
```

Il formato PPM: un formato immagine semplice

Poiché esaminare file in formato esadecimale può risultare complicato, negli esercizi andremo a cifrare immagini. Per questo utilizziamo il formato Portable Pixel Map (PPM), noto per la sua semplicità.

Ogni file PPM inizia con un’intestazione leggibile che contiene le dimensioni dell’immagine e il valore massimo per ogni canale colore (nel nostro caso 255)

Segue quindi un blocco di byte pari a tre volte la larghezza per l’altezza dell’immagine: ogni pixel è rappresentato da tre byte consecutivi (rosso, verde e blu). I pixel sono ordinati per righe.

Il formato PPM non è compresso e può essere piuttosto inefficiente, per cui probabilmente non usi file in questo formato. Alcuni esempi sono forniti, ma puoi anche convertire immagini nel formato PPM con:

```
convert immagine.png immagine.ppm
```
Per interagire con questi file, offriamo una classe Python PPMImage che permette di caricarli, modificarli e salvarli, accedendo facilmente ai dati raw dei pixel. Troverai questa classe nel file cryptoexercise.py.

Documentazione del formato PPM: http://netpbm.sourceforge.net/doc/ppm.html

## Esercizio 1: Modalità Electronic Code Book (ECB)

Realizza crittografia e decrittografia in modalità ECB nella classe PPMImage, usando PyCryptodome. Puoi consultare gli esempi PyCryptodome e la documentazione API.

Prendi un file immagine qualunque, caricalo, criptalo, e salva il risultato cifrato:

image = PPMImage.load_from_file(open('image.ppm', 'rb'))
image.encrypt(key, 'ecb')
image.write_to_file(open('image_encrypted.ppm', 'wb'))

Apri la versione cifrata e osserva cosa succede.

Prova a modificare l’immagine cifrata (ad esempio, con un editor esadecimale o modificando la proprietà .data):

image.data[42] = 0x42  # imposta il byte alla posizione 42 a 0x42

Prova a ragionare se puoi effettuare modifiche parzialmente controllate all’immagine.

## Esercizio 2: Modalità CBC e CTR

Implementa crittografia e decrittografia con modalità CBC e CTR, similmente a quanto fatto con ECB.

Entrambe queste modalità richiedono un parametro aggiuntivo:

- CBC necessita di un vettore di inizializzazione (IV) di 16 byte (passalo tramite parametro iv)
- CTR usa un nonce di 8 byte (passalo tramite parametro nonce)

Questi valori vengono inseriti nei commenti dell’intestazione PPM per poter decriptare successivamente.

Quali differenze osservi rispetto alla modalità ECB?

- Analizza le immagini cifrate che contengono differenti pattern.
- Modifica dei byte nel testo cifrato e osserva l’effetto sul testo in chiaro dopo la decrittazione.

## Esercizio 3: Manomissione in modalità CTR

Nonostante CBC e CTR generino testi cifrati apparentemente casuali, un attaccante può comunque manipolare il testo cifrato.

Per dimostrare questo problema in CTR, realizza il seguente esperimento:

1. Cripta l’immagine del tricolore italiano ita.ppm in modalità CTR.
2. Modifica l’immagine cifrata senza conoscere la chiave, in modo che dopo la decrittazione risulti l’immagine della bandiera svedese.

Nota: ti è fornita un’immagine del Burundi bur.ppm con le stesse dimensioni di ita.ppm.

## Esercizio 5: Crittografia Autenticata

Ora che usiamo la crittografia autenticata, la manomissione delle immagini cifrate è impossibile, giusto?

Il file security.ppm contiene un messaggio semplice.

Con questa ipotesi:

- Cripta il file in modalità GCM.
- Prova a modificare il file cifrato (senza la chiave) in modo che la decrittazione abbia successo, ma mostri un messaggio modificato.

Cosa hai imparato da questo esperimento?