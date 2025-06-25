# 🎮 Pixel Bot Arena – Dokumentacja Gry (v1.0)

---

## 🧩 Opis gry

**Pixel Bot Arena** to gra oparta na pikselowej mapie, w której autonomiczne boty-gracze rywalizują, malując pola swoimi kolorami. Gra działa w nieskończonych kolejkach i turach, a stan mapy aktualizowany jest na bieżąco. Dodatkowo wspierani są obserwatorzy, którzy mogą śledzić rozgrywkę w czasie rzeczywistym.

---

## 🧠 Definicje

| Termin        | Opis |
|---------------|------|
| **Gra**       | Sesja gry składająca się z kolejek bez warunku końcowego |
| **Kolejka**   | Sekwencja tur graczy w ustalonej kolejności |
| **Tura**      | Czas przeznaczony na ruch jednego gracza |
| **Mapa**      | Dwuwymiarowa siatka pikseli |
| **Pixel**     | Najmniejszy element mapy; posiada kolor |
| **Gracz**     | Bot lub klient wysyłający komendy do API gry |
| **Komenda**   | Akcja gracza (np. malowanie pikseli) |

---

## 📡 API – REST

### `POST /join`
Dołącza gracza do gry.

**Odpowiedź:**
```json
{
  "playerId": "abc123",
  "color": "red"
}
````

---

### `POST /leave`

Usuwa gracza z gry. Wszystkie jego piksele stają się białe.

---

### `GET /game-state`

Zwraca aktualny stan gry (dla obserwatorów lub UI).

**Przykład odpowiedzi:**

```json
{
  "turnNumber": 42,
  "currentPlayerId": "player2",
  "map": [
    ["white", "blue", "blue"],
    ["red", "red", "blue"]
  ],
  "players": [
    { "id": "player1", "color": "red" },
    { "id": "player2", "color": "blue" }
  ]
}
```

---

## 🌐 WebSocket API

### 🔁 Kanały

* `/ws/player` – dla graczy
* `/ws/observer` – dla obserwatorów

---

### 📤 Gracz: `playerTurn`

Wysyłane do gracza, gdy nadchodzi jego tura.

**Przykład:**

```json
{
  "type": "playerTurn",
  "yourId": "player2",
  "turnNumber": 42,
  "map": [
    ["white", "blue", "blue"],
    ["red", "white", "white"]
  ],
  "players": [
    { "id": "player1", "color": "red" },
    { "id": "player2", "color": "blue" }
  ],
  "availableCommands": [
    { 
      "type": "paint", 
      "availableTargets": [ 
        { "x": 12, "y": 5 },
        { "x": 13, "y": 5 }
      ]
    }
  ]
}
```

---

### 📥 Gracz: `playerMove`

Wysyłane przez gracza w jego turze.

**Przykład:**

```json
{
  "type": "playerMove",
  "command": "paint",
  "target": { "x": 12, "y": 5 }
}
```

---

### 📤 Obserwator: `gameUpdate`

Wysyłane do obserwatorów po każdej turze.

**Przykład:**

```json
{
  "type": "gameUpdate",
  "turnNumber": 42,
  "currentPlayerId": "player2",
  "map": [
    ["white", "blue", "blue"],
    ["red", "red", "blue"]
  ],
  "players": [
    { "id": "player1", "color": "red" },
    { "id": "player2", "color": "blue" }
  ]
}
```

---

## 🛠 Komendy

### `paint`

Zmienia kolor danego pixela na kolor gracza. Może być wykonana tylko na dostępnych pikselach.
