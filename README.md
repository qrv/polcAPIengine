# polcAPIengine
Pol Consulting

## Integrasjon filosofi
![integrasjon_07](https://user-images.githubusercontent.com/16031302/198845916-c8b893d6-43c5-4454-9e49-5d5c8627ca21.png)

## post SAT med Postman og manuell import i DeleNett
Før iPhone-app kobles mot pocAPIengine må 'Site Acepance Test' utføres på alle post apiene.

Postman brukes altså til å simulere iPhonens kommunikasjon mot polcAPIenine. 
Det er viktig at man kjører import i DeleNett og verifiserer resultatet etter hver post api test beskrevet under.

api-prefix, til bruk i Postman, finner du i JSON host elementer som returneres fra get_iphone_init
host peker mot din lokale polcAPIengine. Merk at urlen slutter med en 'forward slash' / 
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

post apiene generer automagisk csv filer som brukes ved innlasting.  Bildene og csv-filene kan du se ved navigere til api-prefiksen med nettleseren din.

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


