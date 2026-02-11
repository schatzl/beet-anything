# 📐 Beet Anything - Technical Specification

Complete technical specification for the Beet Anything Progressive Web App.

**Version**: 1.2 Easy Layout  
**Last Updated**: February 2025  
**Target Audience**: Developers, Contributors, Technical Users

---

## 📋 Table of Contents

1. [Architecture Overview](#architecture-overview)
2. [Data Structures](#data-structures)
3. [Core Classes](#core-classes)
4. [Algorithms](#algorithms)
5. [UI Components](#ui-components)
6. [Internationalization](#internationalization)
7. [Storage & Persistence](#storage--persistence)
8. [Mobile Optimizations](#mobile-optimizations)
9. [Performance](#performance)

---

## 🏗️ Architecture Overview

### Single-File PWA Design

**File**: `beet-anything.html` (~145 KB, ~4,300 lines)

**Structure**:
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        /* ~1,500 lines of CSS */
    </style>
</head>
<body>
    <!-- ~800 lines of HTML -->
    <script>
        /* ~2,000 lines of JavaScript */
    </script>
</body>
</html>
```

**No Dependencies**:
- No npm packages
- No build tools
- No external CSS/JS files
- No CDN dependencies
- Pure vanilla JavaScript (ES6+)

**Browser APIs Used**:
- `localStorage` for persistence
- `Service Worker` for offline capability (future)
- CSS Grid & Flexbox for layout
- DOM manipulation for rendering

---

## 📊 Data Structures

### Vegetable Class

```javascript
class Vegetable {
    constructor(id, names, family, nutrition) {
        this.id = id;              // String: unique identifier
        this.names = names;        // Object: {en, de, it}
        this.family = family;      // String: botanical family
        this.nutrition = nutrition; // String: 'high'|'medium'|'low'
        this.companions = new Map(); // Map<vegetableId, compatibility>
        this.pests = [];           // Array<String>: pest IDs
        this.protects = new Map(); // Map<pestId, Array<vegetableId>>
    }
    
    getName(lang) {
        return this.names[lang] || this.names.en;
    }
    
    addCompanion(vegetableId, compatibility) {
        this.companions.set(vegetableId, compatibility);
        // compatibility: 1 (good), 0 (neutral), -1 (bad)
    }
    
    addPest(pestId) {
        this.pests.push(pestId);
    }
    
    protectsAgainst(pestId, vegetableIds) {
        this.protects.set(pestId, vegetableIds);
    }
    
    isCompatibleWith(vegetableId) {
        return this.companions.get(vegetableId) || 0;
    }
    
    isVulnerableTo(pestId) {
        return this.pests.includes(pestId);
    }
    
    protectsPlant(vegetableId, pestId) {
        const protectedPlants = this.protects.get(pestId) || [];
        return protectedPlants.includes(vegetableId);
    }
}
```

### Bed Class

```javascript
class Bed {
    constructor(id) {
        this.id = id;          // Number: bed identifier
        this.name = `Bed ${id}`; // String: customizable name
    }
}
```

### Planting Class

```javascript
class Planting {
    constructor(vegetableId, bedId, year) {
        this.id = vegetableId;  // String: vegetable identifier
        this.bedId = bedId;     // Number: bed identifier
        this.year = year;       // Number: planting year
    }
}
```

### Garden Class

```javascript
class Garden {
    constructor() {
        this.beds = new Map();      // Map<bedId, Bed>
        this.plantings = [];        // Array<Planting>
        this.vegetables = new Map(); // Map<vegetableId, Vegetable>
    }
    
    addBed() {
        const id = this.beds.size + 1;
        const bed = new Bed(id);
        this.beds.set(id, bed);
        return bed;
    }
    
    assignToBed(vegetableId, bedId, year) {
        const planting = new Planting(vegetableId, bedId, year);
        this.plantings.push(planting);
        return planting;
    }
    
    getPlantingsForBed(bedId, year) {
        return this.plantings
            .filter(p => p.bedId === bedId && p.year === year)
            .map(p => this.vegetables.get(p.id));
    }
    
    removePlantingsFromBed(bedId, year) {
        this.plantings = this.plantings.filter(
            p => !(p.bedId === bedId && p.year === year)
        );
    }
}
```

---

## 🎯 Core Classes

### Application Controller

```javascript
const app = {
    garden: new Garden(),
    currentYear: 2024,
    currentLanguage: 'en',
    wishlist: [],
    
    optimizer: {
        selectedPlants: [],
        mode: 'fresh',           // 'fresh' | 'fit'
        maxPlantsPerBed: 4,
        numBeds: 'auto',        // 'auto' | Number
        proposedLayout: null,
        score: null
    },
    
    init() {
        this.loadVegetables();
        this.load();
        this.setupUI();
        this.render();
    },
    
    save() {
        localStorage.setItem('beetAnything_garden', JSON.stringify({
            beds: Array.from(this.garden.beds.entries()),
            plantings: this.garden.plantings,
            currentYear: this.currentYear,
        }));
        localStorage.setItem('beetAnything_wishlist', JSON.stringify(this.wishlist));
        localStorage.setItem('beetAnything_language', this.currentLanguage);
    },
    
    load() {
        const saved = localStorage.getItem('beetAnything_garden');
        if (saved) {
            const data = JSON.parse(saved);
            this.currentYear = data.currentYear || 2024;
            this.garden.beds = new Map(data.beds);
            this.garden.plantings = data.plantings;
        }
        
        const savedWishlist = localStorage.getItem('beetAnything_wishlist');
        if (savedWishlist) {
            this.wishlist = JSON.parse(savedWishlist);
        }
        
        const savedLang = localStorage.getItem('beetAnything_language');
        if (savedLang) {
            this.currentLanguage = savedLang;
        }
    },
    
    render() {
        this.renderBeds();
        this.renderWishlist();
        this.updateYearDisplay();
        this.updateTranslations();
    }
};
```

---

## 🧮 Algorithms

### Easy Layout Optimizer

#### Core Algorithm

```javascript
optimizeLayout() {
    const selectedVegetables = this.optimizer.selectedPlants
        .map(id => this.garden.vegetables.get(id));
    
    const numBeds = this.optimizer.numBeds === 'auto' 
        ? Math.ceil(selectedVegetables.length / this.optimizer.maxPlantsPerBed)
        : this.optimizer.numBeds;
    
    // Build forbidden list (for 'fit' mode)
    const forbidden = this.buildForbiddenList(numBeds);
    
    let bestLayout = null;
    let bestScore = -Infinity;
    
    // Try 50 random attempts
    for (let attempt = 0; attempt < 50; attempt++) {
        // Shuffle plants randomly
        const shuffled = [...selectedVegetables].sort(() => Math.random() - 0.5);
        
        // Greedy placement
        let beds = this.greedyPlacement(shuffled, numBeds, forbidden);
        
        // Local optimization
        beds = this.localOptimization(beds, forbidden);
        
        // Evaluate
        const score = this.evaluateLayout(beds, forbidden);
        
        if (score > bestScore) {
            bestScore = score;
            bestLayout = beds;
        }
    }
    
    return { beds: bestLayout, score: bestScore };
}
```

#### Greedy Placement

```javascript
greedyPlacement(plants, numBeds, forbidden) {
    const beds = Array.from({ length: numBeds }, () => []);
    
    for (const plant of plants) {
        let bestBed = 0;
        let bestScore = -Infinity;
        
        for (let i = 0; i < numBeds; i++) {
            // Skip if bed is full
            if (beds[i].length >= this.optimizer.maxPlantsPerBed) continue;
            
            // Skip if forbidden (crop rotation)
            if (forbidden[i] && forbidden[i].includes(plant.family)) continue;
            
            // Temporarily add plant
            beds[i].push(plant);
            
            // Evaluate bed
            const score = this.evaluateBed(beds[i]);
            
            // Remove plant
            beds[i].pop();
            
            if (score > bestScore) {
                bestScore = score;
                bestBed = i;
            }
        }
        
        // Add to best bed (or bed 0 if all forbidden)
        beds[bestBed].push(plant);
    }
    
    return beds;
}
```

#### Local Optimization

```javascript
localOptimization(beds, forbidden) {
    let improved = true;
    let iterations = 0;
    const maxIterations = 20;
    
    while (improved && iterations < maxIterations) {
        improved = false;
        iterations++;
        
        // Try all pairs of plants across beds
        for (let i = 0; i < beds.length; i++) {
            for (let j = i + 1; j < beds.length; j++) {
                for (let pi = 0; pi < beds[i].length; pi++) {
                    for (let pj = 0; pj < beds[j].length; pj++) {
                        // Try swapping plants
                        const plantI = beds[i][pi];
                        const plantJ = beds[j][pj];
                        
                        // Check if swap is allowed (forbidden lists)
                        if (forbidden[i] && forbidden[i].includes(plantJ.family)) continue;
                        if (forbidden[j] && forbidden[j].includes(plantI.family)) continue;
                        
                        // Calculate current score
                        const currentScore = this.evaluateBed(beds[i]) + this.evaluateBed(beds[j]);
                        
                        // Swap
                        beds[i][pi] = plantJ;
                        beds[j][pj] = plantI;
                        
                        // Calculate new score
                        const newScore = this.evaluateBed(beds[i]) + this.evaluateBed(beds[j]);
                        
                        if (newScore > currentScore) {
                            improved = true;
                        } else {
                            // Swap back
                            beds[i][pi] = plantI;
                            beds[j][pj] = plantJ;
                        }
                    }
                }
            }
        }
    }
    
    return beds;
}
```

#### Scoring Function

```javascript
evaluateBed(plants) {
    let score = 0;
    
    // Companion relationships
    for (let i = 0; i < plants.length; i++) {
        for (let j = i + 1; j < plants.length; j++) {
            const compatibility = plants[i].isCompatibleWith(plants[j].id);
            if (compatibility === 1) score += 1;      // Good companion
            if (compatibility === -1) score -= 50;    // Bad companion
        }
    }
    
    // Pest protection
    for (let i = 0; i < plants.length; i++) {
        for (let j = 0; j < plants.length; j++) {
            if (i === j) continue;
            for (const pest of plants[j].pests) {
                if (plants[i].protectsPlant(plants[j].id, pest)) {
                    score += 20;  // Protection bonus
                }
            }
        }
    }
    
    // Shared pests (vulnerability)
    const pestCounts = new Map();
    for (const plant of plants) {
        for (const pest of plant.pests) {
            pestCounts.set(pest, (pestCounts.get(pest) || 0) + 1);
        }
    }
    for (const count of pestCounts.values()) {
        if (count > 1) score -= 15 * (count - 1);  // Penalty for shared pests
    }
    
    // Nutrition balance
    const nutritionCounts = { high: 0, medium: 0, low: 0 };
    for (const plant of plants) {
        nutritionCounts[plant.nutrition]++;
    }
    
    if (nutritionCounts.high > 0 && nutritionCounts.low > 0) {
        score += 5;  // Bonus for nutrition diversity
    }
    if (nutritionCounts.high === plants.length) {
        score -= 10;  // Penalty for all heavy feeders
    }
    
    return score;
}

evaluateLayout(beds, forbidden) {
    let score = 0;
    
    // Sum bed scores
    for (const bed of beds) {
        score += this.evaluateBed(bed);
    }
    
    // Crop rotation violations
    for (let i = 0; i < beds.length; i++) {
        if (!forbidden[i]) continue;
        for (const plant of beds[i]) {
            if (forbidden[i].includes(plant.family)) {
                score -= 30;  // Major penalty
            }
        }
    }
    
    return score;
}
```

### Year Rotation Wizard

```javascript
showRotationWizard() {
    const currentCalendarYear = new Date().getFullYear();
    
    // Validation
    if (this.currentYear === currentCalendarYear) {
        alert(this.t('wizard.sameYear'));
        return;
    }
    
    const previousYear = this.currentYear - 1;
    const prevPlants = new Set();
    
    // Collect plants from previous year
    this.garden.beds.forEach(bed => {
        const plantings = this.garden.getPlantingsForBed(bed.id, previousYear);
        plantings.forEach(p => prevPlants.add(p.id));
    });
    
    if (prevPlants.size === 0) {
        alert(this.t('wizard.noPrevious'));
        return;
    }
    
    // Use optimizer in 'fit' mode
    this.optimizer.selectedPlants = Array.from(prevPlants);
    this.optimizer.mode = 'fit';
    this.optimizer.maxPlantsPerBed = 4;
    this.optimizer.numBeds = this.garden.beds.size;
    
    const result = this.optimizeLayout();
    this.displayRotationWizardResults(result, previousYear);
}
```

---

## 🎨 UI Components

### Tab System

**HTML Structure**:
```html
<div class="tab-container">
    <div class="tab-nav">
        <button class="tab-btn active" onclick="app.switchTab('garden')">
            🏡 Manual Planning
        </button>
        <button class="tab-btn" onclick="app.switchTab('optimizer')">
            🧮 Easy Layout
        </button>
    </div>
    
    <div class="tab-content-wrapper">
        <div id="tab-garden" class="tab-content active">
            <!-- Manual planning content -->
        </div>
        <div id="tab-optimizer" class="tab-content">
            <!-- Optimizer content -->
        </div>
    </div>
</div>
```

**CSS Card-Style Design**:
```css
.tab-container {
    background: var(--cream);
    border-radius: 12px;
    border: 2px solid rgba(74, 124, 89, 0.2);
    overflow: hidden;
}

.tab-nav {
    display: flex;
    gap: 0;
    background: rgba(0, 0, 0, 0.03);
    padding: 0;
}

.tab-btn {
    flex: 1;
    padding: 0.75rem 1.5rem;
    background: rgba(0, 0, 0, 0.03);
    border: none;
    border-top: 4px solid transparent;
    border-radius: 0;
}

.tab-btn.active {
    background: var(--cream);
    border-top: 4px solid var(--leaf-green);
    border-radius: 8px 8px 0 0;
}
```

### Bed Card Component

```html
<div class="bed-card" onclick="app.openBedModal(bedId)">
    <div class="bed-header">
        <h3>Bed 1</h3>
        <button onclick="app.removeBed(bedId)">×</button>
    </div>
    
    <div class="bed-plants">
        <!-- Plant items -->
        <div class="plant-item">
            <span class="plant-name">Tomato</span>
            <span class="nutrition-badge high">High</span>
            <div class="pest-icons">🐌🦗</div>
        </div>
    </div>
    
    <button class="btn btn-primary" onclick="app.openPlantSelector(bedId)">
        + Plant
    </button>
</div>
```

### Plant Selector Modal

```html
<div class="modal" id="plantSelectorModal">
    <div class="modal-content">
        <h3>Select Vegetables for Bed 1</h3>
        
        <input type="text" placeholder="Search..." 
               oninput="app.filterPlantSelector(this.value)">
        
        <div class="plant-grid">
            <!-- Plant checkboxes -->
            <div class="plant-checkbox" onclick="app.togglePlant('tomato')">
                <input type="checkbox" id="plant-tomato">
                <label>🍅 Tomato</label>
            </div>
        </div>
        
        <button onclick="app.applyPlantSelection()">Apply</button>
    </div>
</div>
```

---

## 🌍 Internationalization

### Translation System

**Structure**:
```javascript
const translations = {
    en: {
        "controls.title": "Planning Controls",
        "wishlist.title": "Your Wishlist",
        "bed.add": "+ Add Garden Bed",
        // ... 245+ keys
    },
    de: {
        "controls.title": "Planungssteuerung",
        "wishlist.title": "Ihre Wunschliste",
        "bed.add": "+ Gartenbeet hinzufügen",
        // ... 245+ keys
    },
    it: {
        "controls.title": "Controlli di pianificazione",
        "wishlist.title": "La tua lista dei desideri",
        "bed.add": "+ Aggiungi aiuola",
        // ... 245+ keys
    }
};
```

**Translation Function**:
```javascript
t(key) {
    return translations[this.currentLanguage][key] || translations.en[key] || key;
}
```

**Usage in HTML**:
```html
<h2 data-i18n="controls.title">Planning Controls</h2>
```

**Dynamic Update**:
```javascript
updateTranslations() {
    document.querySelectorAll('[data-i18n]').forEach(el => {
        const key = el.getAttribute('data-i18n');
        el.textContent = this.t(key);
    });
}
```

### Plant Name Translation

```javascript
class Vegetable {
    constructor(id, names, ...) {
        this.names = {
            en: "Tomato",
            de: "Tomate",
            it: "Pomodoro"
        };
    }
    
    getName(lang) {
        return this.names[lang] || this.names.en;
    }
}
```

---

## 💾 Storage & Persistence

### localStorage Schema

**Key**: `beetAnything_garden`
```json
{
    "beds": [
        [1, {"id": 1, "name": "Bed 1"}],
        [2, {"id": 2, "name": "Bed 2"}]
    ],
    "plantings": [
        {"id": "tomato", "bedId": 1, "year": 2024},
        {"id": "basil", "bedId": 1, "year": 2024}
    ],
    "currentYear": 2024
}
```

**Key**: `beetAnything_wishlist`
```json
["tomato", "basil", "carrot", "lettuce"]
```

**Key**: `beetAnything_language`
```json
"en"
```

### Export Format

```json
{
    "version": "1.2",
    "exported": "2025-02-11T10:30:00Z",
    "garden": {
        "beds": [...],
        "plantings": [...]
    },
    "wishlist": [...],
    "currentYear": 2024,
    "language": "en"
}
```

### Auto-Save Strategy

- Save triggered on every state change
- Debounced to prevent excessive writes
- No explicit "Save" button needed
- Data persists across browser sessions

---

## 📱 Mobile Optimizations

### Responsive Breakpoints

```css
/* Desktop */
@media (min-width: 969px) {
    .main-grid {
        grid-template-columns: 1fr 2fr;
    }
}

/* Tablet */
@media (max-width: 968px) {
    .main-grid {
        grid-template-columns: 1fr;
    }
    
    .tab-btn {
        font-size: 0.9rem;
        padding: 0.6rem 1.2rem;
    }
}

/* Phone */
@media (max-width: 480px) {
    .container {
        padding: 0.75rem;
    }
    
    .panel {
        padding: 0.75rem;
    }
    
    .tab-btn {
        font-size: 0.75rem;
        padding: 0.5rem 0.7rem;
    }
    
    .plant-selector {
        grid-template-columns: repeat(2, 1fr);
    }
}
```

### Touch Optimizations

**Minimum touch target**: 44px × 44px (Apple guidelines)

```css
.btn {
    min-height: 44px;
    padding: 0.75rem 1.5rem;
}

.tab-btn {
    min-height: 44px;
}

input[type="checkbox"] {
    width: 20px;
    height: 20px;
}
```

### Mobile-Specific Features

**Touch tooltips**:
```javascript
document.addEventListener('click', (e) => {
    if (e.target.closest('.pest-icon')) {
        // Show tooltip on click (not hover)
        showTooltip(e.target);
    }
});
```

**Scroll handling**:
```css
.tab-nav {
    overflow-x: auto;
    -webkit-overflow-scrolling: touch;
    scrollbar-width: thin;
}
```

---

## ⚡ Performance

### Bundle Size

- **HTML**: ~4,300 lines
- **CSS**: ~1,500 lines
- **JavaScript**: ~2,000 lines
- **Total**: ~145 KB (uncompressed)
- **Gzipped**: ~35 KB (estimated)

### Rendering Strategy

**Initial load**:
1. Parse HTML
2. Load vegetables database
3. Load from localStorage
4. Render initial view
5. Attach event listeners

**Update cycle**:
1. User interaction
2. Update state
3. Save to localStorage
4. Re-render affected components

### Optimization Techniques

**Selective rendering**:
```javascript
render() {
    // Only re-render changed sections
    this.renderBeds();          // Only bed grid
    this.renderWishlist();      // Only wishlist
    this.updateYearDisplay();   // Only year selector
}
```

**Event delegation**:
```javascript
document.querySelector('.bed-grid').addEventListener('click', (e) => {
    if (e.target.closest('.bed-card')) {
        const bedId = e.target.dataset.bedId;
        this.openBedModal(bedId);
    }
});
```

**localStorage caching**:
```javascript
// Read once on init, write on change
const cachedData = JSON.parse(localStorage.getItem('beetAnything_garden'));
```

### Memory Management

- Vegetables loaded once at startup
- Plantings stored as lightweight objects
- Maps used for O(1) lookups
- No memory leaks (no dangling event listeners)

---

## 🔒 Security Considerations

### Data Privacy

- **No server communication**: All data client-side
- **No analytics**: No tracking or telemetry
- **No external requests**: Self-contained file
- **localStorage only**: Browser sandboxed storage

### XSS Prevention

```javascript
// Always use textContent, not innerHTML for user data
element.textContent = userInput;  // Safe

// Sanitize HTML if needed
const sanitized = DOMPurify.sanitize(userInput);  // Not needed, no user HTML
```

### Input Validation

```javascript
// Validate plant selection
if (!this.garden.vegetables.has(vegetableId)) {
    console.error('Invalid vegetable ID');
    return;
}

// Validate bed ID
if (!this.garden.beds.has(bedId)) {
    console.error('Invalid bed ID');
    return;
}
```

---

## 🧪 Testing Guidelines

### Manual Testing Checklist

**Core Functions**:
- [ ] Add bed
- [ ] Remove bed
- [ ] Assign plants to bed
- [ ] Remove plants from bed
- [ ] Change year
- [ ] Export data
- [ ] Import data
- [ ] Switch language

**Optimizer**:
- [ ] Fresh Garden mode
- [ ] Fit to Current mode
- [ ] Apply layout
- [ ] Recalculate

**Rotation Wizard**:
- [ ] Trigger from next year
- [ ] Apply rotation
- [ ] Respect crop families

**Mobile**:
- [ ] iPhone Safari
- [ ] Android Chrome
- [ ] Tablet landscape/portrait
- [ ] Touch interactions

### Browser Compatibility

**Test matrix**:
- Chrome 90+ (Windows, macOS, Linux, Android)
- Safari 14+ (macOS, iOS)
- Firefox 88+ (Windows, macOS, Linux)
- Edge 90+ (Windows, macOS)

### Performance Benchmarks

**Target metrics**:
- Initial load: <500ms
- Tab switch: <100ms
- Bed render: <50ms
- Optimizer calculation: <1000ms
- localStorage save: <10ms

---

## 📚 Code Style Guidelines

### JavaScript

```javascript
// Use ES6+ features
const plants = [...selectedVegetables];
const { id, name } = bed;

// Arrow functions for callbacks
plants.forEach(plant => console.log(plant));

// Template literals
const html = `<div>${plant.getName(lang)}</div>`;

// Destructuring
const { beds, plantings } = this.garden;
```

### CSS

```css
/* Use CSS variables */
:root {
    --leaf-green: #4A7C59;
    --cream: #FAF7F0;
}

/* Mobile-first approach */
.container {
    padding: 0.75rem;
}

@media (min-width: 969px) {
    .container {
        padding: 2rem;
    }
}
```

### Naming Conventions

- **Classes**: PascalCase (`Vegetable`, `Garden`)
- **Variables**: camelCase (`currentYear`, `selectedPlants`)
- **Constants**: SCREAMING_SNAKE_CASE (`MAX_BEDS`, `DEFAULT_YEAR`)
- **CSS classes**: kebab-case (`bed-card`, `plant-item`)

---

## 🔄 Version History

**v1.2 - Easy Layout (February 2025)**
- Easy Layout Optimizer algorithm
- Year Rotation Wizard
- Card-style tab navigation
- 2-tab simplified interface
- Mobile optimizations

**v1.1 - Pest Protection (February 2025)**
- Pest protection system
- 35 vegetables & herbs
- Visual pest indicators
- Mobile tooltips

**v1.0 - Foundation (February 2025)**
- Multi-year planning
- 30 vegetables
- Companion planting
- Trilingual support
- PWA capability

---

## 📞 Technical Support

**Issues**: https://github.com/schatzl/beet-anything/issues  
**Documentation**: See README.md and INSTALLATION.md  
**Live Demo**: https://target23.de/beet-anything.html

---

**Built with care for gardeners and developers alike. 🌱💻**
