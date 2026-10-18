# Revolut
### Baze de date - proiect

---

# TODO:

### stage -1: feedback! (doar daca e constructiv)
### stage 1: pui ce task-uri ai in tabel, completezi tabelele tale, daca e nevoie adaugi atribute + note / motivatii neaparat daca nu e ceva simplu. (incercati sa fiti consistenti)
### stage 2: tbd
### vezi ca llm-urile baga vrajeala proasta uneori, nu strica munca daca nu esti sigur :/ (intreaba)

## Componența echipei

| **Nume**                       | **Grupă** | **Email**                                    | Task-uri |
|:-------------------------------|:---------:|:---------------------------------------------|:--------:|
| **Adăscăliței Cristian-Mihai** |   1307A   | cristian-mihai.adascalitei@student.tuiasi.ro |          |
| **Brumă Cristian**             |   1306B   | cristian.bruma3@student.tuiasi.ro            |          |
| **Enache Teodora-Iasmina**     |   1309B   | teodora-iasmina.enache@student.tuiasi.ro     |          |
| **Ferariu Ioan-Andrei**        |   1306A   | ioan-andrei.ferariu@student.tuiasi.ro        |          |
| **Marinkaș Ioana-Alexandra**   |   1307A   | ioana-alexandra.marinkas@student.tuiasi.ro   |          |
| **Mateiuc Florentina-Bianca**  |   1306A   | florentina-bianca.mateiuc@student.tuiasi.ro  |          |
| **Mihalache Costin-Daniel**    |   1309B   | costin-daniel.mihalache@student.tuiasi.ro    |          |
| **Pâslaru Luiza-Maria**        |   1309B   | luiza-maria.paslaru@student.tuiasi.ro        |          |
| **Pâslaru Robert-Mihai**       |   1309B   | robert-mihai.paslaru@student.tuiasi.ro       |          |
| **Sava Teofan**                |   1307A   | teofan.sava@student.tuiasi.ro                |          |

---

# Tema proiectului

Scopul proiectului este să simulăm sistemul bancar Revolut, împreună cu multiple alte facilități. Clienții noștri se pot
înregistra după ce datele personale le sunt validate și își pot alege oricând tipul de abonament (Standard, Plus, Metal). 
Aceste abonamente dictează multiple beneficii sau limitări, fiind modalitatea de clasificare a utilizatorilor.

Avem câteva valute admise (RON, GBP, EUR, USD), dintre care cea principală este RON, contul în această monedă fiind 
deschis automat. Ratele de conversie se modifică zilnic, deci vom monitoriza constant aceste modificări. Pe lângă contul
în RON, pot fi deschise conturi curente și în alte valute, care însă sunt unice pentru fiecare client (maxim un cont 
curent per valută) și pot fi închise din motive justificate. Cardurile sunt atașate conturilor, având propriile date de 
identificare (număr, data expirării, CVC și PIN) și pot fi multiple, dar abonamentul asociat clientului limitează 
numărul și tipul lor (ex: un utilizator Standard nu poate folosi un card Metal). În cazul în care abonamentul este 
retrogradat, sunt dezactivate cardurile de tipuri devenite inaccesibile și ultimele carduri create, până se ajunge la 
limita permisă.

Plata abonamentelor este în RON, efectuată zilnic, fiind salvată într-un istoric. În cazul eșuării plății, clientul 
redevine automat unul Standard. Depozitele sunt fictive, din surse externe, în contul curent activ asociat clientului, 
identificat prin valuta aleasă pentru transfer. Plățile folosesc carduri active asociate conturilor curente. În cazul în
care plata se face într-o altă valută decât RON și fondurile din acel cont sunt insuficiente, vom folosi compensarea 
split-payment: se utilizează întregul sold disponibil din contul în valuta respectivă, iar diferența este convertită și 
dedusă din contul principal în RON. Utilizatorii își pot transfera fonduri între propriile conturi curente, folosind 
ratele de conversie zilnică. Transferurile către alți clienți (peer-to-peer) folosesc aceeași logică de split-payment ca
în cazul plăților. Deoarece orice formă de tranzacție implică posibilitatea unei eșuări, reținem statusul acesteia și, 
eventual, motivul eșuării.

Analog contului curent, orice client poate avea un cont de economii (maxim unul per valută). Acesta este flexibil, 
utilizatorul putând adăuga sau retrage fonduri oricând, iar dobânzile sunt calculate zilnic și reținute într-un istoric.
Ratele de dobândă sunt stabilite în funcție de abonament și valută, iar tranzacțiile se fac doar între contul curent și 
cel de economii de același tip, asociate aceluiași client.


Sistemul atribuie puncte de loialitate fiecărui client, salvate într-un portofel unic. Punctele sunt acumulate la 
fiecare plată, rata de conversie fiind dictată de valută și abonament. Tranzacțiile cu puncte sunt legate de o anumită 
plată reușită și pot fi folosite pentru diferite opțiuni de răscumpărare.

Introducem și conceptul de campanii referral, care oferă bonusuri (depozite) dacă un client nou, invitat de un 
utilizator existent, reușește să efectueze un număr de plăți de minim X RON până la o anumită dată. Valoarea bonusului 
este dată de tipul de abonament al utilizatorului care lansează invitația. Logica este separată între campanii, 
invitații și recompense (acestea din urmă fiind legate direct de un anumit depozit efectuat).

---

## Descrierea tehnică

---

### 1. Abonamente

> Stochează nivelurile de cont disponibile în aplicație (Standard, Plus, Metal) și costul zilnic al acestora.

| **Câmp**     | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:-------------|-------------:|:-----------------|:---------------------|
| denumire     |              |                  |                      | 
| taxa_zilnica |              |                  |                      |

### 2. Clienti

> Gestionează identitatea clienților și reține care este abonamentul lor actual.

| **Câmp**           | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:-------------------|-------------:|:-----------------|:---------------------|
| id                 |              |                  |                      |
| nume               |              |                  |                      |
| prenume            |              |                  |                      |
| cnp                |              |                  |                      |
| telefon            |              |                  |                      |
| email              |              |                  |                      |
| denumire_abonament |              |                  |                      |
| data_inregistrare  |              |                  |                      |

### 3. Valute

> Nomenclatorul monedelor cu care sistemul poate lucra. Baza este RON.

| **Câmp** | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:---------|-------------:|:-----------------|:---------------------|
| cod      |              |                  |                      |
| denumire |              |                  |                      | 

### 4. Rate_Conversie

> Monitorizează modificările zilnice ale cursurilor de schimb.

| **Câmp**              | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:----------------------|-------------:|:-----------------|:---------------------|
| id                    |              |                  |                      |
| valuta_sursa_cod      |              |                  |                      |
| valuta_destinatie_cod |              |                  |                      |
| rata_schimb           |              |                  |                      |
| data_actualizare      |              |                  |                      |

---

### 5. Conturi_Curente

> Fiecare client primește un cont curent în RON, cu posibilitatea de a deschide în alte valute.

| **Câmp**        | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:----------------|-------------:|:-----------------|:---------------------|
| id              |              |                  |                      |
| client_id       |              |                  |                      |
| valuta_cod      |              |                  |                      |
| iban            |              |                  |                      |
| sold            |              |                  |                      |
| status          |              |                  |                      |
| data_deschidere |              |                  |                      |

### 6. Tipuri_Carduri

> Definește formele pe care le pot lua cardurile.

| **Câmp**          | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:------------------|-------------:|:-----------------|:---------------------|
| denumire          |              |                  |                      |
| limita_tranzactie |              |                  |                      |

### 7. Posibilitati_Carduri_NM

> Aceasta este tabela de legătură care rezolvă logica de retrogradare și limitare.

| **Câmp**            | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:--------------------|-------------:|:-----------------|:---------------------|
| abonament_denumire  |              |                  |                      |
| tip_card_denumire   |              |                  |                      |
| permite_plus        |              |                  |                      |
| permite_metal       |              |                  |                      |
| max_carduri_permise |              |                  |                      |

### 8. Carduri

> Tabelul care stochează datele sensibile și starea cardului. Ele sunt direct atașate unui `Cont_Curent`.

| **Câmp**      | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:--------------|-------------:|:-----------------|:---------------------|
| id            |              |                  |                      |
| cont_id       |              |                  |                      |
| tip_card      |              |                  |                      |
| numar_card    |              |                  |                      |
| data_expirare |              |                  |                      |
| cvv           |              |                  |                      |
| pin           |              |                  |                      |
| status        |              |                  |                      |
| data_emitere  |              |                  |                      |

---

### 9. Depozite

> Sunt intrările de fonduri din exterior. Aici ne interesează doar contul în care intră banii (fără să implice un card).

| **Câmp**        | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:----------------|-------------:|:-----------------|:---------------------|
| id              |              |                  |                      |
| cont_id         |              |                  |                      |
| suma            |              |                  |                      |
| nume_sursa      |              |                  |                      |
| data_tranzactie |              |                  |                      |
| status          |              |                  |                      |
| motiv_esuare    |              |                  |                      |

### 10. Plăți

> Sunt pur fictive și implică un card, aplică logica de *split-payment*.

| **Câmp**        | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:----------------|-------------:|:-----------------|:---------------------|
| id              |              |                  |                      |
| card_id         |              |                  |                      |
| valuta_cod      |              |                  |                      |
| suma_totala     |              |                  |                      |
| nume_destinatie |              |                  |                      |
| suma_valuta     |              |                  |                      |
| rata_conversie  |              |                  |                      |
| suma_split_RON  |              |                  |                      |
| data_tranzactie |              |                  |                      |
| status          |              |                  |                      |
| motiv_esuare    |              |                  |                      |

### 11. Transferuri_Interne

> Sunt operațiunile de schimb valutar pe care clientul le face între propriile conturi.

| **Câmp**           | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:-------------------|-------------:|:-----------------|:---------------------|
| id                 |              |                  |                      |
| valuta_cod_trimisa |              |                  |                      |
| valuta_cod_primita |              |                  |                      |
| suma_trimisa       |              |                  |                      |
| cont_sursa_id      |              |                  |                      |
| cont_destinatie_id |              |                  |                      |
| rata_conversie     |              |                  |                      |
| suma_primita       |              |                  |                      |
| data_tranzactie    |              |                  |                      |
| status             |              |                  |                      |
| motiv_esuare       |              |                  |                      |

### 12. Transferuri_Externe

> Plăți între clienți diferiți ai aplicației, aplică logica de *split-payment*.

| **Câmp**                  | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:--------------------------|-------------:|:-----------------|:---------------------|
| id                        |              |                  |                      |
| valuta_cod                |              |                  |                      |
| suma                      |              |                  |                      |
| client_sursa_id           |              |                  |                      |
| client_destinatie_id      |              |                  |                      |
| cont_sursa_id             |              |                  |                      |
| cont_destinatie_valuta_id |              |                  |                      |
| cont_destinatie_RON_id    |              |                  |                      |
| rata_conversie            |              |                  |                      |
| suma_debitata_valuta      |              |                  |                      |
| suma_debitata_RON         |              |                  |                      |
| data_tranzactie           |              |                  |                      |
| status                    |              |                  |                      |
| motiv_esuare              |              |                  |                      |

### 13. Istoric_Plati_Abonamente

> Plată care se declanșează recurent și debitează doar contul de RON.

| **Câmp**                  | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:--------------------------|-------------:|:-----------------|:---------------------|
| id                        |              |                  |                      |
| client_id                 |              |                  |                      |
| abonament_id              |              |                  |                      |
| suma                      |              |                  |                      |
| data_tranzactie           |              |                  |                      |
| status                    |              |                  |                      |
| motiv_esuare              |              |                  |                      |

---

### 14. Beneficii_Abonamente_NM

> Pentru fiecare combinație de `Abonament` + `Valută`, care este dobânda la economii și care este rata de generare a punctelor.

| **Câmp**             | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:---------------------|-------------:|:-----------------|:---------------------|
| abonament_id         |              |                  |                      |
| valuta_id            |              |                  |                      |
| rata_dobanda_anuala  |              |                  |                      |
| multiplicator_puncte |              |                  |                      |

---

### 15. Conturi_Economii

> Similar cu un cont curent, fiecare client are dreptul la maxim un cont de economii per valută.

| **Câmp**        | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:----------------|-------------:|:-----------------|:---------------------|
| id              |              |                  |                      |
| client_id       |              |                  |                      |
| valuta_cod      |              |                  |                      |
| sold            |              |                  |                      |
| status          |              |                  |                      |
| data_deschidere |              |                  |                      |

### 16. Tranzactii_Economii

> Aici înregistrăm mișcările de bani făcute de client, depuneri sau retrageri.

| **Câmp**         | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:-----------------|-------------:|:-----------------|:---------------------|
| id               |              |                  |                      |
| cont_curent_id   |              |                  |                      |
| cont_economii_id |              |                  |                      |
| suma             |              |                  |                      |
| tip_operatiune   |              |                  |                      |
| status           |              |                  |                      |
| data_tranzactie  |              |                  |                      |

### 17. Istoric_Dobanzi

> Intrări zilnice pentru acordări de dobânzi.

| **Câmp**             | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:---------------------|-------------:|:-----------------|:---------------------|
| id                   |              |                  |                      |
| cont_economii_id     |              |                  |                      |
| sold_initial         |              |                  |                      |
| rata_dobanda_zilnica |              |                  |                      |
| suma_generata        |              |                  |                      |
| status               |              |                  |                      |
| data_acordare        |              |                  |                      |

---

### 18. Portofele_Puncte

> Fiecare client are exact un singur portofel de puncte.

| **Câmp**    | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:------------|-------------:|:-----------------|:---------------------|
| id          |              |                  |                      |
| client_id   |              |                  |                      |
| sold        |              |                  |                      |
| data_creare |              |                  |                      |

### 19. Optiuni_Rascumparare

> Este "magazinul" de unde clienții își pot cheltui punctele.

| **Câmp**        | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:----------------|-------------:|:-----------------|:---------------------|
| id              |              |                  |                      |
| denumire        |              |                  |                      |
| descriere       |              |                  |                      |
| cost            |              |                  |                      |
| stoc_disponibil |              |                  |                      |

### 20. Tranzactii_Puncte

> Intrările sunt trimiteri către `Plati` sau `Optiuni_Rascumparare`.

| **Câmp**                | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:------------------------|-------------:|:-----------------|:---------------------|
| id                      |              |                  |                      |
| portofel_id             |              |                  |                      |
| suma                    |              |                  |                      |
| tip_operatiune          |              |                  |                      |
| plata_referinta_id      |              |                  |                      |
| optiune_rascumparare_id |              |                  |                      |
| data_tranzactie         |              |                  |                      |

---

### 21. Campani_Referral

> Definește regulile temporale și financiare ale promoției.

| **Câmp**       | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:---------------|-------------:|:-----------------|:---------------------|
| id             |              |                  |                      |
| denumire       |              |                  |                      |
| data_inceput   |              |                  |                      |
| data_sfarsit   |              |                  |                      |
| min_tranzactii |              |                  |                      |
| min_suma_ron   |              |                  |                      |
| status         |              |                  |                      |

### 22. Recompense_Referral_NM

> Unifică tipul de abonament cu campania.

| **Câmp**       | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:---------------|-------------:|:-----------------|:---------------------|
| abonament_id   |              |                  |                      |
| campanie_id    |              |                  |                      |
| suma           |              |                  |                      |

### 23. Invitatii_Referral

> Urmărește exact cine pe cine a invitat și în ce stadiu de completare este.

| **Câmp**         | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:-----------------|-------------:|:-----------------|:---------------------|
| id               |              |                  |                      |
| client_invita_id |              |                  |                      |
| client_nou_id    |              |                  |                      |
| campanie_id      |              |                  |                      |
| status           |              |                  |                      |
| data_invitatie   |              |                  |                      |

### 24. Invitatii_Referral

> Dacă invitația este `Completed`, intrarea în acest tabel este o referință către `Depozite`.

| **Câmp**             | **Tip Date** | **Constrângeri** | **Notă / Motivație** |
|:---------------------|-------------:|:-----------------|:---------------------|
| id                   |              |                  |                      |
| invitatie_id         |              |                  |                      |
| suma                 |              |                  |                      |
| depozit_referinta_id |              |                  |                      |
| data_tranzactie      |              |                  |                      |