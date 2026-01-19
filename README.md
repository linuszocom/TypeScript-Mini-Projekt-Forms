# Måndag: Gör ditt projekt interaktivt

Ni har redan lagt grunden med HTML, CSS och datatyper. Idag är målet att gå från statisk kod till en dynamisk applikation genom att låta användaren mata in egen information/styra händelse.

## Dagens Uppgift

Din uppgift är att implementera ett **formulär** i ditt projekt (t.ex. för att lägga till en produkt, skapa en karaktär eller skicka ett meddelande).

Du ska koppla ihop formuläret med din TypeScript-kod genom att lyssna på `submit`-händelsen. I din event-hantering ska du stoppa sidladdningen med `preventDefault()`, hämta värdena från dina input-fält och konvertera dem till rätt datatyper (t.ex. text till siffror).

Utmaningen är att använda **Array Destructuring** på minst ett ställe för att hantera data från en sträng (t.ex. dela upp ett för- och efternamn, ett datum eller en tidsangivelse). Slutligen ska du paketera datan till ett nytt objekt som följer ditt Interface och antingen lägga till det i din lista eller presentera det för användaren.

## Checklista 

Svårt att komma igång? Använd denna lista som hjälp.

* **Event Handling:** En `addEventListener` på formulärets "submit" som använder `e.preventDefault()`.
* **Data-hantering:** Hämtning av `.value` från input-fält samt typkonvertering (t.ex. `Number()`).
* **Logik:** Användning av `.split()` kombinerat med **Array Destructuring** (t.ex. `const [a, b] = str.split(...)`).
* **Objekt-skapande:** Generering av ett nytt objekt som matchar ditt Interface (gärna med ett unikt ID via `Date.now()`).
* **Feedback:** Användaren ska se resultatet direkt (t.ex. genom att listan renderas om eller via en utskrift/alert).

---

### Viktigt att veta
När du laddar om sidan (F5) kommer datan du lagt till via formuläret att försvinna. Detta är helt normalt eftersom vi just nu inte sparar våran data någonstans. Vi kommer att gå igenom hur man sparar data med LocalStorage under morgondagens lektion.

# Dagens Kod

**html**

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="stylesheet" href="./style.css" />
    <link
      rel="stylesheet"
      href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0"
    />
    <script type="module" src="./dist/index.js"></script>
    <title>Codeify Music</title>
  </head>

  <body>
    <header class="top-bar">
      <input
        type="text"
        id="search-input"
        class="search-input"
        placeholder="Sök efter låt"
        aria-label="Sök efter låt"
      />
      <button id="open-modal-btn" class="btn-primary btn-icon">
        <span class="material-symbols-outlined">add</span>
      </button>
    </header>
    <main id="song-list-container" class="song-grid"></main>

    <footer id="player-footer" class="player-card fixed-bottom">
      <div class="info-wrapper">
        <h2 id="song-title" class="song-title">Välj en låt</h2>
        <span id="song-artist" class="artist-name">...</span>
      </div>

      <div class="controls-wrapper">
        <button
          id="prev-btn"
          class="control-btn"
          aria-label="Spela föregående låt"
        >
          <span class="material-symbols-outlined">skip_previous</span>
        </button>

        <button
          id="play-btn"
          class="control-btn play-btn"
          aria-label="Spela eller pausa"
        >
          <span class="material-symbols-outlined">play_arrow</span>
        </button>

        <button id="next-btn" class="control-btn" aria-label="Spela nästa låt">
          <span class="material-symbols-outlined">skip_next</span>
        </button>
      </div>
    </footer>

    <dialog id="add-song-dialog" class="modal">
      <form id="add-song-form" class="modal-form">
        <h3>Ny låt</h3>
        <div class="input-group">
          <label for="title-input">Titel</label>
          <input
          type="text"
          id="title-input"
          class="input-field"
          placeholder="T.ex. Wonderwall"
          required/>
        </div>
      
         <div class="input-group">
          <label for="artist-input">Artist</label>
          <input
          type="text"
          id="artist-input"
          class="input-field"
          placeholder="T.ex. Oasis"
          required/>
        </div>
        <div class="input-group"></div>
         <div class="input-group">
          <label for="duration-input">Längd (min:sek)</label>
          <input
          type="text"
          id="duration-input"
          class="input-field"
          placeholder="T.ex. 3:45"
          required/>
        </div>
          <button type="button" id="close-modal-btn" class="btn-secondary">
            Avbryt
          </button>
          <button type="submit" class="btn-primary">Spara</button>
        </div>
      </form>
    </dialog>
  </body>
</html>
```
**TypeScript**

```typescript
// Interfaces/Typer
interface Song {
  id: number;
  title: string;
  artist: string;
  durationInSeconds: number;
  album: Album;
}

interface Album {
  title: string;
  year: number;
  coverUrl?: string;
}

type PlayerStatus = "playing" | "paused" | "stopped";

const playlist: Song[] = [
  {
    id: 1,
    title: "Bohemian Rhapsody",
    artist: "Queen",
    durationInSeconds: 320,
    album: {
      title: "A night at the Opera",
      year: 1975,
      coverUrl: "https://example.com/queen.jpg",
    },
  },
  {
    id: 2,
    title: "Blinding Lights",
    artist: "The Weeknd",
    durationInSeconds: 200,
    album: {
      title: "After Hours",
      year: 2020,
      coverUrl: "https://example.com/weeknd.jpg",
    },
  },
  {
    id: 3,
    title: "Africa",
    artist: "Toto",
    durationInSeconds: 300,
    album: {
      title: "Toto IV",
      year: 1982,
    },
  },
  {
    id: 4,
    title: "Never Give You Up",
    artist: "Rick Astley",
    durationInSeconds: 200,
    album: {
      title: "Whenever You Need Somebody",
      year: 1987,
    },
  },
];

// VARIABLER
const songTitleElement = document.getElementById("song-title");
const songArtistElement = document.getElementById("song-artist");
// const coverImageElement = document.getElementById(
//   "cover-img",
// ) as HTMLImageElement;

const searchInput = document.querySelector("#search-input") as HTMLInputElement;

const songListContainer = document.querySelector("#song-list-container");
const playButton = document.querySelector("#play-btn") as HTMLButtonElement;
let status: PlayerStatus = "paused";

const dialog = document.querySelector("#add-song-dialog") as HTMLDialogElement;
const openBtn = document.querySelector("#open-modal-btn") as HTMLButtonElement;
const closeBtn = document.querySelector(
  "#close-modal-btn",
) as HTMLButtonElement;

const addForm = document.querySelector("#add-song-form") as HTMLFormElement;
const titleInput = document.querySelector("#title-input") as HTMLInputElement;
const artistInput = document.querySelector("#artist-input") as HTMLInputElement;
const durationInput = document.querySelector(
  "#duration-input",
) as HTMLInputElement;

function renderSongs() {
  if (songListContainer) {
    songListContainer.replaceChildren();
  }

  playlist.forEach(({ title, artist, id }) => {
    const card = document.createElement("article");
    card.classList.add("song-card");

    const titleElement = document.createElement("h3");
    titleElement.textContent = title;

    const artistElement = document.createElement("span");
    artistElement.textContent = artist;

    card.append(title, artist);

    if (songListContainer) {
      card.addEventListener("click", () => {
        const currentActive = document.querySelector(".song-card.active");
        if (currentActive) {
          currentActive.classList.remove("active");
        }
        card.classList.add("active");
        playSong(id);
      });
      songListContainer.append(card);
    }
  });
}

renderSongs();

// setCustomValidity

// LOGIK

if (playButton) {
  playButton.addEventListener("click", () => {
    const iconElement = playButton.querySelector("span");

    if (!iconElement) return;

    if (status === "paused") {
      status = "playing";
      iconElement.innerText = "pause";
    } else {
      status = "paused";
      iconElement.innerText = "play_arrow";
    }
  });
}

if (searchInput) {
  searchInput.addEventListener("input", (e) => {
    const target = e.target as HTMLInputElement;
    const searchTerm = target.value.toLowerCase();

    const allCard = document.querySelectorAll(".song-card");

    allCard.forEach((card) => {
      const title = card.querySelector("h3")?.textContent?.toLowerCase();

      if (title?.includes(searchTerm)) {
        card.classList.remove("hidden");
      } else {
        card.classList.add("hidden");
      }
    });
  });
}

// FUNKTIONER
function playSong(id: number) {
  const songToPlay = playlist.find((song) => song.id === id);

  if (!songToPlay) return;

  if (songTitleElement) {
    songTitleElement.textContent = songToPlay.title;
  }

  if (songArtistElement) {
    songArtistElement.textContent = songToPlay.artist;
  }
}

// hantera modalen

openBtn.addEventListener("click", () => {
  dialog.showModal();
});

closeBtn.addEventListener("click", () => {
  dialog.close();
});

addForm.addEventListener("submit", (e) => {
  e.preventDefault();

  const title = titleInput.value;
  const artist = artistInput.value;
  const timeStr = durationInput.value; // T.ex "3:45"

  const [minStr, secStr] = timeStr.split(":");

  const min = Number(minStr);
  const sec = Number(secStr);

  if (isNaN(min) || isNaN(sec)) {
    durationInput.classList.add("error");
    alert("Fel tidsformat! Använd min:sek");
    return;
  }

  durationInput.classList.remove("error");
  const totalSeconds = min * 60 + sec;

  const newSong: Song = {
    id: Date.now(),
    title: title,
    artist: artist,
    durationInSeconds: totalSeconds,
    album: { title: "Singel", year: 2024 },
  };

  playlist.push(newSong);
  renderSongs();

  addForm.reset();
  dialog.close();
});


```


