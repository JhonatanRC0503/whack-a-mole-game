# Whack-a-Mole Game - Technical Documentation

> **Generated with assistance from GitHub Copilot**  
> Last Updated: February 2026

---

## Table of Contents

1. [Architecture Overview](#architecture-overview)
2. [Global Variables](#global-variables)
3. [Functions Reference](#functions-reference)
4. [Event Handlers](#event-handlers)
5. [Code Style Guide](#code-style-guide)
6. [Testing Guide](#testing-guide)
7. [Maintenance Guide](#maintenance-guide)

---

## Architecture Overview

### Technology Stack
- **HTML5** - Structure and semantic markup
- **CSS3** - Styling with custom properties, gradients, animations
- **Vanilla JavaScript (ES6+)** - Game logic, no external dependencies
- **Web Audio API** - Sound effects generation

### Design Pattern
- **Event-Driven Architecture** - User interactions trigger game state changes
- **Modular Functions** - Each function has a single responsibility
- **State Management** - Global variables track game state

### File Structure
```
whack-a-mole-game/
├── index.html          # Complete game (HTML + CSS + JS)
├── game.test.js        # Comprehensive test suite
├── package.json        # Jest configuration and dependencies
├── README.md           # User-facing documentation
├── DOCUMENTATION.md    # This file - technical documentation
└── .github/
    ├── copilot-instructions.md  # AI coding standards
    └── workflows/
        └── ci.yml      # CI/CD pipeline configuration
```

---

## Global Variables

### Game State Variables

| Variable | Type | Initial Value | Description |
|----------|------|---------------|-------------|
| `score` | `number` | `0` | Player's current score |
| `timeLeft` | `number` | `45` | Remaining seconds in current game |
| `currentMolePosition` | `number\|null` | `null` | Index (0-8) of cell containing mole |
| `gameActive` | `boolean` | `false` | Indicates if game is currently running |
| `moleInterval` | `number\|null` | `null` | Interval ID for mole movement timer |
| `timerInterval` | `number\|null` | `null` | Interval ID for countdown timer |
| `currentDifficulty` | `string` | `'medium'` | Current difficulty level |

### Configuration Constants

#### `difficultySettings` Object
```javascript
const difficultySettings = {
    easy: { speed: 1500, label: 'Fácil' },      // Mole moves every 1.5s
    medium: { speed: 1000, label: 'Medio' },    // Mole moves every 1.0s
    hard: { speed: 500, label: 'Difícil' }      // Mole moves every 0.5s
};
```

### DOM References

| Constant | Element ID | Description |
|----------|-----------|-------------|
| `gridElement` | `grid` | Container for 3x3 game board |
| `scoreElement` | `score` | Score display span |
| `timeElement` | `time` | Timer display span |
| `startButton` | `startButton` | Game start/restart button |
| `resetButton` | `resetButton` | Game reset button |
| `gameOverMessage` | `gameOverMessage` | End game message container |
| `difficultyButtons` | `.difficulty-btn` | NodeList of difficulty selector buttons |

---

## Functions Reference

### 1. `playHitSound()`

**Purpose:** Generates and plays a sound effect when mole is hit

**Parameters:** None

**Returns:** `void`

**Implementation Details:**
- Uses Web Audio API (`AudioContext` or `webkitAudioContext`)
- Creates sine wave oscillator
- Frequency sweeps from 400Hz to 800Hz over 100ms
- Volume fades from 0.3 to 0.01 over 150ms
- Total duration: 150ms

**Browser Compatibility:**
- Chrome/Edge: ✅ Full support
- Firefox: ✅ Full support
- Safari: ✅ Requires `webkitAudioContext`

**Example:**
```javascript
playHitSound(); // Plays when mole is successfully hit
```

**Error Handling:**
- Try-catch block prevents crash if AudioContext unavailable
- Graceful degradation: game continues without sound

---

### 2. `createGrid()`

**Purpose:** Creates the 3x3 game board with interactive cells

**Parameters:** None

**Returns:** `void`

**Side Effects:**
- Clears `gridElement.innerHTML`
- Creates 9 new `<div>` elements
- Attaches click event listeners to each cell
- Appends cells to DOM

**Cell Structure:**
```html
<div class="cell" data-index="0"></div>
<!-- ...8 more cells with indices 1-8 -->
```

**Performance Considerations:**
- Called multiple times (on start, reset)
- Efficiently recreates DOM structure
- Event delegation not used (individual listeners for clarity)

**Example:**
```javascript
createGrid(); // Generates fresh 3x3 board
```

---

### 3. `moveMole()`

**Purpose:** Moves mole to random position on grid

**Parameters:** None

**Returns:** `void`

**Algorithm:**
1. Remove mole from current position (`has-mole` class)
2. Generate random number 0-8
3. Ensure new position ≠ current position (when possible)
4. Add mole to new position
5. Update `currentMolePosition` global variable

**State Modifications:**
- `currentMolePosition` - Updated to new index

**DOM Modifications:**
- Removes `has-mole` class from old cell
- Adds `has-mole` class to new cell
- Updates `innerHTML` with mole emoji (`🐹`)

**Edge Cases:**
- First call with `currentMolePosition = null` → Places mole randomly
- If all cells occupied (impossible in 3x3) → May repeat position

**Example:**
```javascript
moveMole(); // Mole jumps to new random cell
```

---

### 4. `handleCellClick(event)`

**Purpose:** Handles player clicks on grid cells

**Parameters:**
- `event` (`Event`) - Click event object

**Returns:** `void`

**Logic Flow:**
```
1. Check if gameActive → Return early if false
2. Get clicked cell element
3. Check if cell has 'has-mole' class
   ├─ YES:
   │   ├─ Increment score
   │   ├─ Update score display
   │   ├─ Play hit sound
   │   ├─ Add 'hit' class (visual feedback)
   │   ├─ Remove 'hit' class after 300ms
   │   └─ Move mole immediately
   └─ NO: Do nothing
```

**State Modifications:**
- `score` - Incremented by 1 on successful hit

**DOM Modifications:**
- Updates `scoreElement.textContent`
- Temporarily adds/removes `hit` class for animation

**Performance:**
- Immediate mole relocation prevents double-hits
- 300ms animation timeout doesn't block execution

**Example:**
```javascript
cell.addEventListener('click', handleCellClick);
```

---

### 5. `updateTimer()`

**Purpose:** Decrements timer and updates display

**Parameters:** None

**Returns:** `void`

**Critical Logic:**
```javascript
if (!gameActive) {
    return; // Prevents ghost timers after reset
}
```

**Execution Flow:**
1. Check `gameActive` → Early return if false
2. Decrement `timeLeft` by 1
3. Update display
4. Check if `timeLeft <= 0` → Call `endGame()`

**State Modifications:**
- `timeLeft` - Decremented by 1

**Called By:**
- `setInterval(updateTimer, 1000)` in `startGame()`

**Bug Prevention:**
- Early return prevents timer from continuing after reset
- Critical fix for issue where timer wouldn't stop

**Example:**
```javascript
timerInterval = setInterval(updateTimer, 1000);
```

---

### 6. `setDifficulty(difficulty)`

**Purpose:** Changes game difficulty level

**Parameters:**
- `difficulty` (`'easy'|'medium'|'hard'`) - Target difficulty

**Returns:** `void`

**State Modifications:**
- `currentDifficulty` - Updated to selected value

**DOM Modifications:**
- Removes `active` class from all difficulty buttons
- Adds `active` class to selected button

**Effect on Game:**
- Difficulty takes effect on **next game start**
- Does not affect currently active game

**Validation:**
- Should validate difficulty exists in `difficultySettings`
- Current implementation trusts valid input

**Example:**
```javascript
setDifficulty('hard'); // Next game will use 0.5s mole speed
```

---

### 7. `startGame()`

**Purpose:** Initializes and starts new game session

**Parameters:** None

**Returns:** `void`

**Execution Sequence:**
```
1. Clear existing intervals (prevents overlap)
2. Reset game state (score=0, time=45, gameActive=true)
3. Get mole speed from difficultySettings
4. Update UI (scores, buttons, messages)
5. Disable start and difficulty buttons
6. Enable reset button
7. Create grid
8. Spawn first mole
9. Start mole movement interval
10. Start countdown interval
```

**State Modifications:**
- `score` → 0
- `timeLeft` → 45
- `currentMolePosition` → null
- `gameActive` → true
- `moleInterval` → New setInterval ID
- `timerInterval` → New setInterval ID

**Critical Fix:**
- Clears intervals **before** creating new ones
- Prevents multiple concurrent timers

**Example:**
```javascript
startButton.addEventListener('click', startGame);
```

---

### 8. `endGame()`

**Purpose:** Terminates current game session

**Parameters:** None

**Returns:** `void`

**Execution Sequence:**
```
1. Set gameActive to false
2. Stop both intervals (mole and timer)
3. Remove mole from board
4. Set timer display to '0'
5. Show game over message with score
6. Enable start button (text: 'Jugar de Nuevo')
7. Disable reset button
8. Enable difficulty buttons
9. Style cells as disabled (visual feedback)
```

**State Modifications:**
- `gameActive` → false

**DOM Modifications:**
- Shows `gameOverMessage`
- Updates button states
- Changes cell appearance

**Called By:**
- `updateTimer()` when `timeLeft <= 0`

**Example:**
```javascript
if (timeLeft <= 0) {
    endGame();
}
```

---

### 9. `resetGame()`

**Purpose:** Completely resets game to initial state

**Parameters:** None

**Returns:** `void`

**Critical Implementation:**
```javascript
// Set gameActive to false FIRST
gameActive = false;

// THEN clear intervals
if (moleInterval) {
    clearInterval(moleInterval);
    moleInterval = null;
}
// ...
```

**Execution Sequence:**
```
1. Set gameActive to false (stops updateTimer)
2. Clear and nullify both intervals
3. Reset all game state variables
4. Update UI displays (score, timer)
5. Clear grid (remove moles, reset styles)
6. Enable start button and difficulty buttons
7. Disable reset button
```

**State Modifications:**
- `gameActive` → false
- `score` → 0
- `timeLeft` → 45
- `currentMolePosition` → null
- `moleInterval` → null
- `timerInterval` → null

**Bug Fixes:**
- Setting `gameActive` **first** prevents timer continuation
- Nullifying interval variables prevents stale references

**Example:**
```javascript
resetButton.addEventListener('click', resetGame);
```

---

## Event Handlers

### Click Events

| Element | Handler | Description |
|---------|---------|-------------|
| `startButton` | `startGame` | Initiates new game session |
| `resetButton` | `resetGame` | Stops and resets current game |
| `.difficulty-btn` | `setDifficulty` | Changes difficulty for next game |
| `.cell` | `handleCellClick` | Processes player hits/misses |

### Timer Events

| Interval | Function | Frequency | Purpose |
|----------|----------|-----------|---------|
| `moleInterval` | `moveMole` | Variable (500ms-1500ms) | Moves mole to new position |
| `timerInterval` | `updateTimer` | 1000ms (1 second) | Counts down game timer |

---

## Code Style Guide

### Naming Conventions

| Type | Convention | Example |
|------|-----------|---------|
| Functions | camelCase | `playHitSound()` |
| Variables | camelCase | `currentMolePosition` |
| Constants | camelCase for objects, UPPER_SNAKE_CASE for primitives | `difficultySettings`, `GAME_DURATION` |
| DOM References | suffix "Element" | `scoreElement` |
| Event Handlers | prefix "handle" | `handleCellClick` |
| CSS Classes | kebab-case | `has-mole`, `difficulty-btn` |

### Function Guidelines

- **Max Length:** 50 lines
- **Documentation:** Comments explaining "why", not "what"
- **Single Responsibility:** Each function does one thing well
- **Descriptive Names:** Avoid abbreviations

### Spanish Language

- User-facing text in Spanish: "Fácil", "Medio", "Difícil"
- Code comments bilingual (Spanish/English)
- Variable names in English (international standard)

---

## Testing Guide

### Running Tests

```bash
# Install dependencies
npm install

# Run all tests
npm test

# Run tests in watch mode
npm run test:watch

# Run tests with coverage
npm test -- --coverage
```

### Test Structure

**File:** `game.test.js`

**Test Suites:**
1. **Game Initialization** (5 tests) - Initial state validation
2. **Grid Creation** (3 tests) - Board structure
3. **Game Start** (4 tests) - Start sequence
4. **Mole Behavior** (2 tests) - Movement logic
5. **Score Tracking** (3 tests) - Score calculation
6. **Timer Functionality** (2 tests) - Countdown logic
7. **Difficulty Levels** (3 tests) - Difficulty switching
8. **Game Reset** (6 tests) - Reset functionality
9. **Edge Cases** (4 tests) - Boundary conditions
10. **Sound Effects** (1 test) - Audio capability
11. **Accessibility** (2 tests) - UI elements

**Total:** 35+ comprehensive tests

### Coverage Goals

- **Statements:** > 80%
- **Branches:** > 75%
- **Functions:** > 90%
- **Lines:** > 80%

### Edge Cases Tested

- ✅ Multiple rapid clicks on same cell
- ✅ Clicks when game is not active
- ✅ Multiple resets without starting
- ✅ Difficulty change during active game
- ✅ Timer behavior on boundaries (0, 45)

---

## Maintenance Guide

### Common Issues and Solutions

#### Issue: Timer Continues After Reset

**Symptom:** Timer keeps counting after clicking "Reiniciar"

**Root Cause:** `updateTimer()` not checking `gameActive` state

**Solution:**
```javascript
function updateTimer() {
    if (!gameActive) {
        return; // Add this check
    }
    // Rest of function...
}
```

#### Issue: Multiple Timers Running

**Symptom:** Timer counts faster than 1 second per tick

**Root Cause:** Not clearing intervals before creating new ones

**Solution:**
```javascript
function startGame() {
    clearInterval(moleInterval);  // Add these
    clearInterval(timerInterval); // before setInterval
    // Rest of function...
}
```

#### Issue: Sound Not Playing

**Symptom:** No audio on mole hits

**Diagnosis:**
1. Check browser console for AudioContext errors
2. Verify browser supports Web Audio API
3. Check if user interaction occurred (autoplay policy)

**Solution:**
```javascript
try {
    const AudioContext = window.AudioContext || window.webkitAudioContext;
    const audioContext = new AudioContext();
    // Sound generation code...
} catch (error) {
    console.warn('Audio not supported:', error);
    // Graceful degradation
}
```

### Adding New Features

#### Example: Adding Power-Ups

1. **Add State Variable:**
   ```javascript
   let powerUpActive = false;
   let powerUpType = null;
   ```

2. **Add Configuration:**
   ```javascript
   const powerUpSettings = {
       doublePoints: { duration: 5000, multiplier: 2 },
       slowTime: { duration: 8000, speedFactor: 0.5 }
   };
   ```

3. **Create Function:**
   ```javascript
   /**
    * Activates a power-up for specified duration
    * @param {string} type - Power-up type
    */
   function activatePowerUp(type) {
       powerUpActive = true;
       powerUpType = type;
       
       setTimeout(() => {
           powerUpActive = false;
           powerUpType = null;
       }, powerUpSettings[type].duration);
   }
   ```

4. **Integrate with Game Logic:**
   - Modify `handleCellClick()` to check power-ups
   - Update `moveMole()` speed calculation
   - Add visual indicators in CSS

5. **Write Tests:**
   ```javascript
   describe('Power-Ups', () => {
       test('should double points during double points power-up', () => {
           // Test implementation
       });
   });
   ```

### Performance Optimization

#### Current Performance
- **Target:** 60 FPS
- **Animation:** GPU-accelerated (transform, opacity)
- **DOM Operations:** Minimized, batched when possible

#### Optimization Checklist

- ✅ Use CSS transforms instead of position changes
- ✅ Avoid layout thrashing (read/write separation)
- ✅ Use event delegation for similar elements (optional)
- ✅ Debounce resize events if added
- ✅ Cache DOM queries in constants

### Browser Compatibility

| Browser | Min Version | Notes |
|---------|------------|-------|
| Chrome | 90+ | Full support |
| Firefox | 88+ | Full support |
| Safari | 14+ | Requires `webkitAudioContext` |
| Edge | 90+ | Full support |
| Mobile Safari | 14+ | Touch events supported |
| Mobile Chrome | 90+ | Touch events supported |

### Accessibility Checklist

- ✅ Keyboard navigation support
- ✅ Focus indicators on interactive elements
- ✅ Sufficient color contrast (4.5:1)
- ✅ Alternative text for emoji
- ✅ Aria-live regions for dynamic content
- ⚠️ Screen reader descriptions (future enhancement)

---

## Version History

### v1.0.0 - Initial Release
- Basic 3x3 whack-a-mole gameplay
- Timer (45 seconds)
- Score tracking

### v1.1.0 - Difficulty Levels
- Added Easy/Medium/Hard modes
- Variable mole speeds

### v1.2.0 - Enhanced UX
- Reset button
- Sound effects
- Mole animations

### v1.3.0 - Quality & CI/CD
- Comprehensive test suite (35+ tests)
- GitHub Actions CI pipeline
- Automated deployment to GitHub Pages

### v1.4.0 - Documentation
- Technical documentation (this file)
- JSDoc comments
- Custom Copilot instructions

---

## Contributing

### Development Workflow

1. **Create Branch:** `git checkout -b feature/your-feature`
2. **Make Changes:** Follow code style guide
3. **Write Tests:** Ensure coverage > 80%
4. **Run Tests:** `npm test`
5. **Commit:** Use conventional commits (`feat:`, `fix:`, `docs:`)
6. **Push:** `git push origin feature/your-feature`
7. **Open PR:** CI pipeline will run automatically

### Conventional Commits

| Type | Use Case | Example |
|------|---------|---------|
| `feat:` | New feature | `feat: add power-ups system` |
| `fix:` | Bug fix | `fix: timer stops completely on reset` |
| `docs:` | Documentation | `docs: add JSDoc to all functions` |
| `test:` | Tests | `test: add edge case for rapid clicks` |
| `refactor:` | Code improvement | `refactor: extract audio logic to class` |
| `style:` | CSS/formatting | `style: improve button hover effects` |
| `ci:` | CI/CD changes | `ci: add test coverage reporting` |

---

## Resources

- **Repository:** https://github.com/JhonatanRC0503/whack-a-mole-game  
- **Live Demo:** https://jhonatanrc0503.github.io/whack-a-mole-game/  
- **Issues:** https://github.com/JhonatanRC0503/whack-a-mole-game/issues  
- **CI/CD:** GitHub Actions  
- **Testing:** Jest + jsdom  

---

**📝 Note:** This documentation was created with assistance from GitHub Copilot as part of Challenge 06.

For questions or suggestions, please open an issue on GitHub.
