Spillningsinventering björn, Jämtlands och Västernorrlands län 2020
================

Detta bibliotek innehåller programkod och data som använts för
populationsberäkningar av björn, baserat på spillningsinventeringen i
Jämtlands och Västernorrlands län 2020. Koden är skriven i R och anropar
programmet MARK genom gränssnittet RMark. Skriptet `prepare_data.R`
sammanställer data och fördelar individer mellan länen efter den
geografiska medelpunkten av deras funna spillningar, `functions.R`
innehåller funktioner för modellanpassning.

## Kort beskrivning av anpassade modeller

Eftersom ansträngningen var tydligt ojämn över inventeringsperioden
undersöker vi endast modeller där fångstsannolikheten varierar med
tidsperiod (kalendervecka). En sammanfattning av resultatet ges i
nedanstående tabeller för hela inventeringsområdet samt separat för
varje län. I tabellen namnges modellerna enligt den notation som används
av RMark. Modellernas parametrar kan indelas i tre grupper `pi`, `p` och
`f0`. Här anger `f0` storleken hos den del av populationen som fångats 0
gånger. Notationen `f0(sex)` i samtliga modeller betyder att denna
delats upp i separata parametrar för honor och hanar vilket är
nödvändigt för att få separata populationsskattningar. Vidare bestämmer
parametrarna `p` fångstsannolikheter, här anger `time` att en separat
parameter skattas för varje kalendervecka, `sex` att det finns en
(additiv) könseffekt och `mixture` att populationen delas in i två
klasser (“lättfångad” och en “svårfångad”) för att kompensera för
individuell heterogenitet i fångstsannolikhet. Gruppen `pi` förekommer
endast i modeller med `mixture` (modeller av typ *M<sub>th2</sub>*) och
anger då hur stor andel av populationen som är svår respektive
lättfångad, `p(1)` innebär att denna är samma för båda kön medan
`p(sex)` innebär att andelen kan vara olika för könsgrupperna.

## Resultat

``` r
library(tidyverse)
library(RMark)
source("functions.R")
data2020 <- read_csv("data/clean_data.csv")
```

### Båda län

``` r
fit2020 <- data2020 %>% 
  filter(Kon != "Okänt") %>% 
  fit_models()
fit2020 %>% 
  select(Modell = model, dAICc, Hanar,  Honor, Total) %>% 
  knitr::kable(caption = "Tabell 1: Populationsskattningar för undersökta modeller 
               baserat på inventeringen i Jämtlands och Västernorrlands län 2020. 
               Modellerna är rangordnade efter Akaikes informationskriterium med den
               högst rankade modellen överst.")
```

| Modell                          | dAICc | Hanar          | Honor           | Total             |
|:--------------------------------|------:|:---------------|:----------------|:------------------|
| pi(sex)p(time + mixture)f0(sex) |   0.0 | 537 (508, 577) | 935 (886, 1000) | 1472 (1394, 1555) |
| pi(1)p(time + mixture)f0(sex)   |   1.3 | 543 (513, 585) | 929 (882, 991)  | 1472 (1393, 1555) |
| p(time + sex)f0(sex)            | 294.6 | 460 (449, 477) | 813 (793, 839)  | 1273 (1246, 1300) |
| p(time)f0(sex)                  | 301.3 | 469 (457, 486) | 802 (784, 825)  | 1271 (1245, 1298) |

Tabell 1: Populationsskattningar för undersökta modeller baserat på
inventeringen i Jämtlands och Västernorrlands län 2020. Modellerna är
rangordnade efter Akaikes informationskriterium med den högst rankade
modellen överst.

### Jämtlands län

``` r
fit2020_Z <- data2020 %>% 
  filter(Kon != "Okänt", LnNamn == "Jämtlands") %>% 
  fit_models()
fit2020_Z %>% 
  select(Modell = model, dAICc, Hanar,  Honor, Total) %>% 
  knitr::kable(caption = "Tabell 2: Populationsskattningar för undersökta modeller 
               baserat på inventeringen i Jämtlands län 2020. 
               Modellerna är rangordnade efter Akaikes informationskriterium med den
               högst rankade modellen överst.")
```

| Modell                          | dAICc | Hanar          | Honor          | Total            |
|:--------------------------------|------:|:---------------|:---------------|:-----------------|
| pi(1)p(time + mixture)f0(sex)   |   0.0 | 355 (327, 396) | 693 (642, 763) | 1048 (965, 1138) |
| pi(sex)p(time + mixture)f0(sex) |   0.1 | 350 (323, 389) | 698 (645, 770) | 1048 (966, 1137) |
| p(time + sex)f0(sex)            | 152.2 | 291 (280, 307) | 589 (569, 616) | 880 (853, 907)   |
| p(time)f0(sex)                  | 154.7 | 298 (286, 314) | 581 (563, 604) | 879 (853, 906)   |

Tabell 2: Populationsskattningar för undersökta modeller baserat på
inventeringen i Jämtlands län 2020. Modellerna är rangordnade efter
Akaikes informationskriterium med den högst rankade modellen överst.

### Västernorrlands län

``` r
fit2020_Y <- data2020 %>% 
  filter(Kon != "Okänt", LnNamn == "Västernorrlands") %>% 
  fit_models()
fit2020_Y %>% 
  select(Modell = model, dAICc, Hanar,  Honor, Total) %>% 
  knitr::kable(caption = "Tabell 3: Populationsskattningar för undersökta modeller 
               baserat på inventeringen i Västernorrlands län 2020. 
               Modellerna är rangordnade efter Akaikes informationskriterium med den
               högst rankade modellen överst.")
```

| Modell                          | dAICc | Hanar          | Honor          | Total          |
|:--------------------------------|------:|:---------------|:---------------|:---------------|
| pi(1)p(time + mixture)f0(sex)   |   0.0 | 189 (179, 208) | 251 (237, 273) | 440 (414, 468) |
| pi(sex)p(time + mixture)f0(sex) |   1.7 | 189 (178, 207) | 251 (238, 274) | 440 (414, 468) |
| p(time)f0(sex)                  | 121.1 | 173 (168, 182) | 229 (223, 239) | 402 (392, 413) |
| p(time + sex)f0(sex)            | 121.8 | 171 (167, 181) | 230 (224, 241) | 401 (391, 412) |

Tabell 3: Populationsskattningar för undersökta modeller baserat på
inventeringen i Västernorrlands län 2020. Modellerna är rangordnade
efter Akaikes informationskriterium med den högst rankade modellen
överst.
