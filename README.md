# polcAPIengine

<details><summary>Introduksjon</summary><span id="fase0"></span>

  ![integrasjon_07](https://user-images.githubusercontent.com/16031302/198845916-c8b893d6-43c5-4454-9e49-5d5c8627ca21.png)
  </details>

<details><summary>Installasjon iPhone</summary><span id="fase1"></span>   <!-- https://github.com/qrv/polcAPIengine?tab=readme-ov-file#user-content-fase'1 -->

  Når bruker er aktivert i skadebil og iTunes kan installasjon av app og testing av bruker gjennomføres som beskrevet nedenfor.

  ![install_02](https://github.com/user-attachments/assets/33f8bbaf-0bdd-41ac-b4bf-f3476e2d71d4)
  </details>
  
<details><summary>DNS / PAT></summary><span id="fase2"></span>

  Kunde er selv ansvarlig for å åpne porter inn mot polcAPIengine som normalt skal installeres på Delenett server.  Portene er konfigurerbare men default er satt til 8443.

  I lokalinstallasjon vil nginx være et godt alternativ som revers proxy særlig dersom ssl sertifikater er tilgjenglige og man ønsker å benytte https.

  Dersom man ønsker å holde kommunikasjonen intern er dette også mulig, men det blir utført en 'Handshake' under start av appen for å validere brukeren, dette krever internett forbindelse.  
  </details>

<details><summary>Test av app</summary><span id="fase3"></span>

  Både QR-, eller strekkoder kan brukes av app.  For å skille mellom biler og deler er B brukt som prefiks for deler, mens X brukes som prefiks på biler.
  <br>
  Dersom ikke høvelige delelapper er tilgjengelig under test kan testnummer lages her:
  - https://qr.io/
  - https://barcode.tec-it.com/en

  ![fase3_a_01](https://github.com/user-attachments/assets/9e19d62d-3d8c-475c-a132-2d058a12ed97)
      
  Nedenfor er vist man går frem for å skanne inn bilder på del B428135
    
  ![fase3_b_01](https://github.com/user-attachments/assets/31c4c45b-4191-4a97-b263-2bfe5e727e15)
  <br><br><br><br><br><br>
  ![fase3_c_01](https://github.com/user-attachments/assets/d0872b82-0a78-4ba4-b8a7-0a7e80c9a3a4)
  <br><br><br><br>
  Dersom man går til polcAPIengine fra en browser vil man se status på opplastede bilder og tilhørende csv filer som brukes av delenett til import. Dersom ikke runtime versjonen av polcAPIengine er installert kan genererte filer kopieres fra testserver til lokalinstallasjon på:
  - server f.eks 192.168.1.2
  - csv katalog f.eks ...LexitInn
  - bildekatalog f.eks ...LexitInn+LexitBilder
  <br><br>
  ![fase3dc_01](https://github.com/user-attachments/assets/e7c47723-7ee4-481c-b9b0-2cdda7eba500)
  </details>

<details><summary>Runtime</summary><span id="fase4"></span>
  Siste versjon av polcAPI runtime kan hentes fra https://github.com/qrv/polcAPIengine/tree/main/runtime
  ini-fil vil inneholde kundespesifikke opplysninger vil bli oversendt pr mail.  

<details><summary>API</summary>

## post SAT, med Postman og manuell import i DeleNett

Før iPhone-app kobles mot polcAPIengine må 'Site Acceptance Test' utføres på alle post-apiene.
Postman brukes altså til å simulere iPhonens kommunikasjon mot polcAPIengine, deretter kjøres import i DeleNett. Hele poenget med SAT er å verifiserer at hver enkelt post-api gir et menigsfullt resultat i DeleNett.

### api-prefix

api-prefix må fremskaffes til bruk i Postman.  Den finner du ved å åpne polcAPIengine på lokal server (<http://192.168.xxx>) og gå til get_iphone_init apien.  Apien er listet rett under versjonen til polcAPIengine.

![get-iPhone init](https://user-images.githubusercontent.com/16031302/198873801-f9ae04c6-8857-4139-846d-b60ee1766bc7.png)

Host elementet i JSON responsen peker mot din lokale polcAPIengine, merk at urlen slutter med en 'forward slash' /  Du skal ikke bruke lokal serveradresse av type <http://192.168.xxx> under disse testene, men altså den offisielle adressen gitt i host-elementet.

```
    {
        items: [
            {
                host: "https://github.com/qrv/polcAPIengine/",
                apple_id: "viggo@icloud.com",
                site_id: "Vazelina",
                user_id: "Viggo",
                user_email: "viggo@vazelina.no",
                img_height: 4032
            }
        ]
    }
```

### csv

post-apiene generer automagisk csv filer som brukes ved innlasting i DeleNett.  Bildene og csv-filene kan du se ved navigere til api-prefiksen med nettleseren din.

## post-img-deler

Denne apien brukes til å laste inn bilder av deler.  Bildene vil lastes opp separat av iPhone appen derfor trenges det bare å testes med ett delenr og ett bilde.

- url
  - api-prefix post-img-deler
- header (delnummer fra qr/bar code)
  - setvalues=B123456
- body (binary)
  - bilde av delen

## post-img-biler

Denne apien er lik post-img-deler men brukes til å laste opp bilder av biler.

- url
  - api-prefix post-img-biler
- header (delnummer fra qr/bar code)
  - setvalues=X123456
- body (binary)
  - bilde av bilen

### Postman eksempel ved opplasting av bilder

![ShareX_Mk0AYUDtYO](https://user-images.githubusercontent.com/16031302/198852505-9ebe6d12-43d9-4798-8fdc-1beb07b8fd86.png)

![ShareX_VQkVmR4l9d](https://user-images.githubusercontent.com/16031302/198852516-323d1507-23a4-4539-a11a-ca695eb748b7.png)

</details>
