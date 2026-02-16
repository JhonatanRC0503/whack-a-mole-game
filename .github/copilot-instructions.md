# GitHub Copilot Instructions for Whack-a-Mole Game

## Project Overview
This is a browser-based Whack-a-Mole game built with vanilla JavaScript, HTML5, and CSS3. The project emphasizes clean code, performance, and user experience.

## Code Style and Standards

### JavaScript
- Use **ES6+ modern JavaScript** syntax (arrow functions, const/let, template literals)
- Follow **camelCase** naming convention for variables and functions
- Add **JSDoc comments** for all functions explaining parameters and return values
- Use **async/await** for asynchronous operations when applicable
- Avoid jQuery or external libraries - keep it vanilla JS
- Maximum function length: 50 lines (split complex functions)
- Use descriptive variable names (avoid single letters except in loops)

### HTML
- Use **semantic HTML5** elements (section, article, nav, etc.)
- Include **ARIA labels** for accessibility
- Add **data-attributes** for JavaScript hooks instead of classes
- Maintain consistent indentation (2 spaces)

### CSS
- Use **CSS custom properties** (variables) for colors and common values
- Follow **BEM methodology** for class naming (block__element--modifier)
- Prefer **flexbox and grid** over floats
- Include **responsive design** considerations (mobile-first approach)
- Add **smooth transitions** for all interactive elements (0.3s ease)
- Use **CSS animations** instead of JavaScript animations when possible

## Architecture Preferences

### Game Logic
- Separate concerns: UI, game state, and business logic in different sections
- Use **class-based approach** for game managers (HighScoreManager, GameEngine, etc.)
- Implement **event-driven architecture** for user interactions
- Store game state in **localStorage** for persistence
- Use **setInterval/setTimeout** for game timing, not requestAnimationFrame

### Performance
- Minimize DOM manipulations (batch updates when possible)
- Use **event delegation** instead of multiple event listeners
- Implement **debouncing** for resize and scroll events
- Lazy load resources when applicable
- Keep CSS animations GPU-accelerated (transform, opacity)

### Browser Compatibility
- Support modern browsers: Chrome 90+, Firefox 88+, Safari 14+, Edge 90+
- No IE11 support required
- Use Web Audio API for sounds (no external audio files)
- Test on both desktop and mobile devices

## Project-Specific Assumptions

### Game Features
- Game duration: **45 seconds** (configurable via constant)
- Difficulty levels: Easy (1.5s), Medium (1s), Hard (0.5s)
- Grid size: **3x3** (9 cells)
- Score increment: **+1 per hit**
- Sound effects: Generated with Web Audio API
- High scores: Store top 10 in localStorage

### Naming Conventions
- Game constants: `UPPER_SNAKE_CASE` (e.g., `GAME_DURATION`)
- DOM element references: suffix with "Element" (e.g., `scoreElement`)
- Event handlers: prefix with "handle" (e.g., `handleCellClick`)
- Class names: PascalCase (e.g., `HighScoreManager`)
- CSS classes: kebab-case (e.g., `high-score-table`)

### Error Handling
- Always validate user inputs
- Provide user-friendly error messages (Spanish language)
- Use try-catch blocks for localStorage operations
- Gracefully handle missing browser features
- Log errors to console in development mode

## UI/UX Guidelines

### Design System
- Primary color gradient: `#667eea` to `#764ba2`
- Accent color: `#ffd700` (gold)
- Success color: `#4CAF50` (green)
- Error color: `#F44336` (red)
- Font family: 'Arial', sans-serif
- Border radius: 10px for buttons, 15px for containers

### Visual Feedback
- Show **loading states** for async operations
- Display **success animations** for achievements
- Provide **haptic feedback** effects (visual scale/shake)
- Use **emoji** for visual enhancement and fun
- Maintain **consistent spacing** (multiples of 5px)

### Accessibility
- Minimum contrast ratio: **4.5:1** for text
- Support **keyboard navigation**
- Include **focus indicators** on all interactive elements
- Provide **alt text** for all images/icons
- Use **aria-live** regions for dynamic content

## Internationalization
- All user-facing text in **Spanish** (es-ES)
- Use descriptive emoji for universal understanding
- Format dates with Spanish locale
- Use Spanish number formatting

## Testing Approach
- Prefer manual testing in browser console
- Test all difficulty levels
- Verify localStorage limits (quota)
- Test on different screen sizes
- Validate with browser DevTools performance

## Documentation
- Add inline comments for complex logic
- Document all public APIs
- Include usage examples for utility functions
- Keep README.md updated with new features
- Document browser-specific workarounds

## Security Considerations
- Sanitize all user inputs (player names)
- Validate data from localStorage before use
- Prevent XSS attacks (no innerHTML with user data)
- Use textContent instead of innerHTML when possible

## Future Enhancements Priority
1. High Scores system (Issue #1)
2. Enhanced sound effects (Issue #2)
3. Power-ups and special moles
4. Theme customization
5. Achievements system
6. Multiplayer mode

## When Suggesting Code
- Always explain WHY, not just WHAT
- Provide complete, working code snippets
- Include error handling
- Add console logs for debugging
- Suggest performance optimizations
- Consider mobile touch events vs mouse events
- Provide Spanish language comments when helpful

## Common Patterns to Follow

### Event Listener Pattern
```javascript
// Use arrow functions for proper 'this' binding
element.addEventListener('click', (event) => {
    event.preventDefault();
    // Handle event
});
```

### LocalStorage Pattern
```javascript
try {
    const data = JSON.parse(localStorage.getItem(key)) || defaultValue;
    // Use data
} catch (error) {
    console.error('Error loading from localStorage:', error);
    return defaultValue;
}
```

### Animation Pattern
```css
.animated-element {
    transition: all 0.3s ease;
    transform-origin: center;
}

.animated-element:hover {
    transform: scale(1.05);
}
```

## Anti-Patterns to Avoid
- ❌ Global variables (use modules or class properties)
- ❌ Inline styles (use CSS classes)
- ❌ Magic numbers (use named constants)
- ❌ Deep nesting (max 3 levels)
- ❌ Synchronous operations that block UI
- ❌ Using var (always use const/let)

---

**Remember:** The goal is to create a fun, performant, and accessible game that runs smoothly on all modern browsers!
