# Måndag: Gör ditt projekt interaktivt

Ni har redan lagt grunden med HTML, CSS och datatyper. Idag är målet att gå från statisk kod till en dynamisk applikation genom att låta användaren mata in egen information/styra händelse.

## Dagens Uppgift

Din uppgift är att implementera ett **formulär** i ditt projekt (t.ex. för att lägga till en produkt, skapa en karaktär eller skicka ett meddelande).

Du ska koppla ihop formuläret med din TypeScript-kod genom att lyssna på `submit`-händelsen. I din event-hantering ska du stoppa sidladdningen med `preventDefault()`, hämta värdena från dina input-fält och konvertera dem till rätt datatyper (t.ex. text till siffror).

Utmaningen är att använda **Array Destructuring** på minst ett ställe för att hantera data från en sträng (t.ex. dela upp ett för- och efternamn, ett datum eller en tidsangivelse). Slutligen ska du paketera datan till ett nytt objekt som följer ditt Interface och antingen lägga till det i din lista eller presentera det för användaren.

## Checklista 

För att bli godkänd på momentet ska din kod innehålla:

* **Event Handling:** En `addEventListener` på formulärets "submit" som använder `e.preventDefault()`.
* **Data-hantering:** Hämtning av `.value` från input-fält samt typkonvertering (t.ex. `Number()`).
* **Logik:** Användning av `.split()` kombinerat med **Array Destructuring** (t.ex. `const [a, b] = str.split(...)`).
* **Objekt-skapande:** Generering av ett nytt objekt som matchar ditt Interface (gärna med ett unikt ID via `Date.now()`).
* **Feedback:** Användaren ska se resultatet direkt (t.ex. genom att listan renderas om eller via en utskrift/alert).

---

### Viktigt att veta
När du laddar om sidan (F5) kommer datan du lagt till via formuläret att försvinna. Detta är helt normalt eftersom vi just nu inte sparar våran data någonstans. Vi kommer att gå igenom hur man sparar data med LocalStorage under morgondagens lektion.
