# EXBanka-2-Infrastructure

## Trgovina na berzi (bank-service) — API ključevi i worker

Za osvežavanje cena hartija `bank-service` očekuje u `.env` sledeće promenljive (vidi `.env.example`):

- **EODHD_API_KEY** — primarni izvor (akcije, forex, futures, istorija).
- **FINNHUB_API_KEY** — sekundarni izvor za akcije / forex.
- **ALPHAVANTAGE_API_KEY** — Company Overview za akcije i forex kurs (sekundarno).

Ponašanje kada API ne odgovori: uz **`LISTING_REQUIRE_LIVE_QUOTES=true`** (podrazumevano u `docker-compose`) worker **ne upisuje** sintetičke ili nasumične cene; u bazi ostaje poslednji poznati quote dok se sledeći uspešan odgovor ne dobije. Za lokalno testiranje bez ključeva možete postaviti `LISTING_REQUIRE_LIVE_QUOTES=false` (samo development).

**Napomena:** opcije koriste Yahoo Finance lanac u worker-u; ako Yahoo ne vrati podatke, uz `LISTING_REQUIRE_LIVE_QUOTES=true` takođe nema ažuriranja mock vrednostima.

**Provizija (bank-service):** Market/Stop ≈ min(14% notionala, fiksni iznos); Limit/Stop-Limit ≈ min(24%, fiksni iznos) — implementacija u `EXBanka-2-Backend/services/bank-service/internal/trading/calculations.go`.

**Porez / država kao firma:** opciono postaviti **`STATE_REVENUE_ACCOUNT_ID`** na `core_banking.racun.id` tekućeg **RSD** računa „države”; pri `POST /bank/tax/calculate` isti iznos u RSD knjiži se kao prijem na taj račun (pored skidanja sa klijenta).
