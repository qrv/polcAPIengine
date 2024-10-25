# polcAPIengine

<details><summary>Introduksjon</summary><span id="fase0"></span>

  ![integrasjon_07](https://user-images.githubusercontent.com/16031302/198845916-c8b893d6-43c5-4454-9e49-5d5c8627ca21.png)
  </details>



<details><summary>Installasjon iPhone</summary><span id="fase1"></span>   <!-- https://github.com/qrv/polcAPIengine?tab=readme-ov-file#user-content-fase'1 -->

  Når bruker er aktivert i skadebil og iTunes kan installasjon av app og testing av bruker gjennomføres som beskrevet nedenfor.

  ![install_02](https://github.com/user-attachments/assets/33f8bbaf-0bdd-41ac-b4bf-f3476e2d71d4)
  </details>
  


<details><summary>DNS / PAT></summary><span id="fase2"></span>

  Kunde er selv ansvarlig for å åpne porter inn mot polcAPIengine som normalt skal installeres på Delenett server.  Portene er konfigurerbare men default er satt til 8443 over http.

  I lokalinstallasjon vil nginx være et godt alternativ som revers proxy særlig dersom ssl sertifikater er tilgjenglige og man ønsker å benytte https.  Det er forøvrig lagt til rette for direkte https mot polcAPIengine dersom det er ønskelig.

  Dersom man ønsker å holde kommunikasjonen intern er dette også mulig, men det blir utført en 'Handshake' under start av appen for å validere brukeren, dette krever internett forbindelse.  

  Dersom DNS i produksjonsdomene er problematisk kan https://account.dyn.com/ brukes.
  </details>




<details><summary>Test av app</summary><span id="fase3"></span>

  Både QR-, eller strekkoder kan brukes av app.  For å skille mellom biler og deler er B brukt som prefiks for deler, mens X brukes som prefiks på biler.
  <br>
  Følgende scannere er testet og virker uten spesielle tilpasninger:
   - Symbol CS4070
   - Netcum C750

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
  
  ini-fil vil inneholde kundespesifikke opplysninger vil bli oversendt pr mail. Filen skal være selvforklarende og kan brukes 'As is'. Den skal plasseres på samme katalog som exe-filen.  
  
  For å overvåke applikasjonen kan AlwaysUp brukes https://www.coretechnologies.com/products/AlwaysUp/ eller en variant av batch fil vist under:
  ```bat
        @echo off
        REM Wait for 30 seconds to ensure all services are up
        timeout /t 30 /nobreak

        REM Change directory to where the executable is located
        cd /d C:\Data\DeleNett\POL\runtime

        :check_running
        REM Check if polcAPIengine.exe is already running
        tasklist /FI "IMAGENAME eq polcAPIengine.exe" 2>NUL | find /I "polcAPIengine.exe" > NUL
        if "%ERRORLEVEL%"=="0" (
            echo polcAPIengine.exe is already running. Exiting.
            pause
            exit /B
        )

        :start
        echo Starting polcAPIengine...
        start "polcAPIengine" cmd /k "polcAPIengine.exe"
        if "%ERRORLEVEL%" NEQ "0" (
            echo Failed to start polcAPIengine.exe.
            pause
            exit /B
        )
        echo polcAPIengine started successfully.

        :wait_and_check
        REM Wait and check if the program is still running
        timeout /t 5 /nobreak
        tasklist /FI "IMAGENAME eq polcAPIengine.exe" 2>NUL | find /I "polcAPIengine.exe" > NUL
        if "%ERRORLEVEL%"=="0" (
            goto wait_and_check
        )
        echo polcAPIengine crashed. Restarting in 5 seconds...
        timeout /t 5 /nobreak
        goto start
  ``` 

  </details>

<details><summary>Database Gateway</summary><span id="fase5"></span>
  For å koble opp mot MSSQL som hoster Delenett-databasen trenges modifisering av ini-fil samt noen nye view og en referanse-tabell.
  

  ### ini-filen
  ini-filen er også er beskrevet i https://github.com/qrv/polcAPIengine?tab=readme-ov-file#user-content-fase4
  <br>Seksjonene YMSE og MSSQL modifiseres som angitt under,  husk at polcAPIengine må restartes for at endringene i ini-filen skal effektueres.


  ```ini
    # YMSE
      # MSSQL tilgang krever at POLC_ACCESS_TOKEN er i samsvar med med innlogget brukers skadebil.site_id_gu
      # token og nødvendig sql-script blir utlevert dersom MSSQL integrasjon med delenett ønskes
      POLC_ACCESS_TOKEN='hex'           # site xxx

    # MS-SQL                    inregrasjon Delenett sql database
      # Alle verdier som trengs under kan hentes i lokalt oppsett av Delenett på C:\Data\DeleNett\System\Innstillinger.txt
      # C:\Data\DeleNett er i dette tilfelle lokal katalog hvor MSSQL databasen er installert.
      MS_YN       = 'Y'                                                   # Y MSSQL gateway brukes,  N deaktiverer gatewayen 
      MS_PORT     = '1433'                                                # Port standard port som må åpnes i Firewall er tcp 1433 og udp 1434 for SQL Browser
      MS_SERVER   = 'XXX\SQLEXPRESS'                                      # SQLServer / servername
      MS_USER     = 'bruker'                                              # Brukernavn
      MS_PWD      = 'passord'                                             # Passord
      MS_DB       = 'DeleNett'                                            # Database, antar TabellEier er dbo på alle installasjoner, men dette overstyres av scipt som brukes
      MS_SQL1     = 'select top (10) bilmerkenr, navn from dbo.bilmerke'  # Test-script som logges ved oppstart av polcAPIengine som quick and dirty test av gateway

  ``` 

  Som angitt over er ini-filen en god plass å teste sql-script. Ved oppstart vil MS_SQL1 statmentet eksekveres og resultatet listes ut i konsollet.
  <br>Da må man altså starte polcAPIengine direkte i kommandoprompten eller så kan loggen fra en service pipes til en fil.
  <br>Typisk listing  ```MS_SQL1 = select top (10) bilmerkenr, navn from dbo.bilmerke'```  er vist under:

  ```js
      MS-SQL status ...
      SSL=N, protokoll=http, port=8443 --> url=http://192.168.1.123:8443
          ver4 startes fra url=http://192.168.1.123:8443
          Running version polcAPIengine_2024_03_12_01 Server 192.168.1.123
          ver3/4 server running...
      {
        recordsets: [
          [
            [Object], [Object],
            [Object], [Object],
            [Object], [Object],
            [Object], [Object],
            [Object], [Object]
          ]
        ],
        recordset: [
          { bilmerkenr: 1, navn: 'XPENG' },
          { bilmerkenr: 2, navn: 'DAIHATSU' },
          { bilmerkenr: 3, navn: 'BUICK' },
          { bilmerkenr: 4, navn: 'CADILLAC' },
          { bilmerkenr: 5, navn: 'JAGUAR' },
          { bilmerkenr: 6, navn: 'FORD LASTEBIL UTGÅR' },
          { bilmerkenr: 7, navn: 'IVECO' },
          { bilmerkenr: 8, navn: 'LASTEBIL' },
          { bilmerkenr: 9, navn: 'SCANIA LASTEBIL UTGÅR' },
          { bilmerkenr: 10, navn: 'VOLVO LASTEBIL UTGÅR' }
        ],
        output: {},
        rowsAffected: [ 10 ]
      }

  ``` 
  ### view og tabell
  Microsoft SQL Server Management Studio e.l. brukes til å legge inn view og tabell.
  <br>Scriptene vises ikke i denne delen av dokumentasjonen med oversendes separat.

</details>


<details><summary>Logg på polcAPIengine</summary>

  ## Daglig oppfølging
  Status på import av filer må følges opp kontinuerlig for å evnt å plukke opp feil som kan ha oppstått i:
  <br>


   - selve skanne prosessen (ugyldige, strekkoder, etc.)
   - polcAPIengine
   - Delnett import

  Typisk så kan status se som ut vist under:

  ![chrome_r1eKkKAXq8](https://github.com/user-attachments/assets/7a883e4c-4c17-4ae3-81f2-f29219a07a40)
  
  ## Hvordan logge på
  Det enkleste er å sjekke status fra en nettleser, men verktøy som Postman kan alternativt brukes.

  For å få tilgang må en http-header sendes sammen med forespørselen,  denne utleveres ved installasjon.

  Dersom nettleseren brukes vil det være enklest å sende http-header med ett av de følgende alternativene:
  - https://requestly.com/    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(best, kan virke gratis hvis du har litt flaks)
  - https://modheader.com/    &nbsp;&nbsp;(gratis og god nok...)

  Nedefor er vist hvordan man går fram for å legge til modheader utvidelsen i chrome

  ![modheader_1](https://github.com/user-attachments/assets/048cd96f-6e88-4f1c-b81c-60488ebbceb5)

  ![modheader_2](https://github.com/user-attachments/assets/a3f8d773-7b6e-44f2-abef-cef1797961d8)

  ![modheader_3](https://github.com/user-attachments/assets/e5d6ffcc-1e85-44c1-b61a-e63b9eda13bd)

  ![modheader_4](https://github.com/user-attachments/assets/a06ff4be-4a60-488e-820e-76cbed00eb17)

  ![modheader_5](https://github.com/user-attachments/assets/8ac94675-3bc2-4a8f-bfca-aba6dfc8e4e3)

  ![modheader_6](https://github.com/user-attachments/assets/2abccadd-4f07-4e0b-ba65-172cf2518ae9)







</details>


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
