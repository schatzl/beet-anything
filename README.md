# 🌱 Beet Anything

**Intelligent Garden Planning with Crop Rotation, Companion Planting & Pest Protection**

A modern, self-contained Progressive Web App for planning your vegetable garden across multiple years, considering crop rotation rules, companion planting relationships, and natural pest protection strategies.

*A vibe coding project*

**Version**: 1.2 Easy Layout  
**Last Updated**: February 2025  
**Status**: Production Ready  
**Languages**: English, German, Italian  
**File**: beet-anything.html (single-file PWA)  
**GitHub**: https://github.com/schatzl/beet-anything  
**License**: Free for any use - Personal, commercial, educational, or otherwise.  
**Live Demo**: [Beet Anything](https://target23.de/beet-anything.html)

---

## ✨ Key Features

### 🏡 Manual Garden Planning
- **Multi-year tracking**: Plan unlimited years, view complete planting history
- **Flexible bed management**: Create unlimited beds, assign multiple vegetables per bed
- **Wishlist system**: Build your planting wishlist and get AI-powered suggestions
- **Year rotation wizard**: Automatically rotate last year's crops with optimal bed assignments
- **Smart crop rotation**: Warns about same family planting in consecutive years
- **Companion planting**: Shows beneficial and incompatible plant relationships

### 🧮 Easy Layout Optimizer
- **Automated planning**: AI-powered optimization of bed layouts
- **Two modes**: 
  - **Fresh Garden**: Start from scratch with selected plants
  - **Fit to Current**: Respect existing beds and enforce crop rotation
- **Intelligent scoring**: Evaluates layouts based on:
  - Companion relationships (good/bad)
  - Natural pest protection
  - Nutrition balance (heavy/medium/light feeders)
  - Crop rotation compliance
- **Preview before apply**: Review optimized layout before committing

### 🛡️ Pest Protection System
- **35 vegetables & herbs** with complete pest profiles
- **Natural pest control** through companion planting
- **Protection relationships**: Shows which plants protect others from specific pests
- **Vulnerability warnings**: Alerts when plants share pest susceptibilities
- **Visual indicators**: Pest icons and protection badges on each plant

### 🌍 Fully Internationalized
- **3 languages**: English, German, Italian
- **245+ translation keys**: Complete UI translation
- **Dynamic switching**: Change language instantly without reload
- **Localized plant names**: All vegetables translated

### 📱 Mobile-First Design
- **Progressive Web App**: Install on any device
- **Touch-optimized**: Designed for iPhone, Android, tablets
- **Responsive layout**: Adapts from phone to desktop
- **Offline-capable**: Works without internet after first load
- **Card-style tabs**: Modern, accessible navigation

### 💾 Data Management
- **Auto-save**: All changes saved instantly to browser localStorage
- **Export/Import**: Backup your entire garden as JSON
- **Data persistence**: Survives browser restarts
- **Privacy-focused**: All data stays on your device

---

## 🚀 Quick Start

1. **Download**: Get `beet-anything.html` from the repository
2. **Open**: Double-click the file or open in any modern browser
3. **Install**: Click "Install as App" for native-like experience
4. **Plan**: Start adding vegetables and planning your garden!

See [INSTALLATION.md](./INSTALLATION.md) for detailed setup instructions.

---

## 📊 Plant Database

**35 vegetables & herbs** with complete data:
- Family classification (for rotation)
- Nutrition needs (heavy/medium/light feeders)
- Companion relationships (good/bad/neutral)
- Pest vulnerabilities (15 common garden pests)
- Protection abilities (which pests they repel)

**Categories**:
- Leafy Greens: Lettuce, Spinach, Kale, Cabbage, Brussels Sprouts
- Fruiting: Tomato, Pepper, Chili, Eggplant, Cucumber, Zucchini, Pumpkin
- Legumes: Peas, Beans (Bush & Pole)
- Root Vegetables: Carrot, Beet, Radish, Turnip, Parsnip, Potato
- Alliums: Onion, Garlic, Leek
- Herbs: Basil, Dill, Parsley, Rosemary, Oregano, Sage
- Brassicas: Broccoli, Cauliflower, Kohlrabi
- Others: Celery, Corn, Strawberry

---

## 🎯 How to Use

### Manual Planning Workflow

1. **Select Year**: Use arrows to navigate to desired year
2. **Build Wishlist**: Add vegetables you want to grow
3. **Create Beds**: Click "+ Add Garden Bed"
4. **Assign Plants**: Click "Plant" button next to each bed
5. **View Details**: Click bed cards to see full companion info
6. **Rotate Years**: Use "🔄 Rotate Last Year's Plan" for next season

### Easy Layout Optimizer Workflow

1. **Switch to Easy Layout** tab
2. **Select Plants**: Check vegetables you want to include
3. **Choose Mode**:
   - **Fresh Garden**: Ignores existing layout, starts fresh
   - **Fit to Current**: Respects bed count, enforces rotation
4. **Set Constraints**: Plants per bed (2-6), number of beds
5. **Calculate**: Algorithm runs 50 attempts with local optimization
6. **Review Results**: See score, bed assignments, and reasoning
7. **Apply or Recalculate**: Accept layout or try again

### Year Rotation Wizard

1. **Change to next year** (e.g., 2024 → 2025)
2. **Click "🔄 Rotate Last Year's Plan"**
3. **Review suggestions**: See optimized rotation respecting crop families
4. **Apply**: Creates beds with rotated crops
5. **Adjust manually**: Fine-tune as needed

---

## 🧠 How It Works

### Companion Planting Algorithm

Plants are rated as:
- **+1**: Good companions (mutually beneficial)
- **0**: Neutral (no significant interaction)
- **-1**: Bad companions (incompatible, should avoid)

The system shows:
- ✓ Good companions in green
- ⚠ Neutral companions in gray  
- ✗ Bad companions in red

### Crop Rotation Logic

Prevents planting same family in consecutive years:
- Brassicaceae (cabbage family)
- Solanaceae (nightshade family)
- Apiaceae (carrot family)
- Fabaceae (legume family)
- Amaranthaceae (beet family)
- Cucurbitaceae (squash family)
- Alliaceae (onion family)

### Pest Protection System

Each plant has:
- **Vulnerabilities**: Which pests attack it
- **Protections**: Which pests it repels

Visual indicators:
- 🐌 Pest icons show vulnerabilities
- 🛡️ Shield badges show protection abilities

### Layout Optimizer Scoring

**Per bed evaluation**:
- +1 per good companion pair
- -50 per bad companion pair
- +20 per pest protection relationship
- -15 per shared pest vulnerability
- +5 for nutrition balance (mix of feeders)
- -10 if all heavy feeders (soil fatigue risk)

**Layout-level penalties**:
- -30 per crop rotation violation (same family, consecutive years)

**Score interpretation**:
- 80-100: Excellent
- 70-79: Very Good
- 60-69: Good
- 50-59: Fair
- <50: Poor (consider recalculating)

Algorithm:
1. Run 50 random attempts
2. For each: Greedy placement + local optimization (up to 20 swaps)
3. Return best-scoring layout

---

## 🎨 Design Philosophy

### Card-Style Tabs

Modern, accessible navigation:
- **Active tab**: Cream background with green top border (4px)
- **Inactive tab**: Flat dark background, no rounded corners
- **Visual continuity**: Active tab flows seamlessly into content panel
- **Touch-friendly**: Large tap targets (44px minimum)

### Color Palette

- **Leaf Green** (#4A7C59): Primary accent, borders, active states
- **Cream** (#FAF7F0): Background, active tab
- **Soil Dark** (#3A2F28): Primary text
- **Soil Medium** (#8B7355): Secondary text, inactive states
- **Sage** (#8AA888): Subtle accents

### Mobile-First Approach

Responsive breakpoints:
- **Desktop** (>968px): Two-column layout
- **Tablet** (481-968px): Single column, optimized spacing
- **Phone** (<480px): Compact layout, 2-column grids

---

## 💻 Technical Details

### Architecture

**Single-file PWA**: Everything in one HTML file
- HTML structure
- CSS styling (inline `<style>`)
- JavaScript logic (inline `<script>`)
- No external dependencies
- No build process required

**Size**: ~145 KB (uncompressed)  
**Lines of code**: ~4,300

### Browser Requirements

**Minimum**:
- Chrome/Edge 90+
- Safari 14+
- Firefox 88+

**Features used**:
- CSS Grid & Flexbox
- ES6+ JavaScript
- localStorage API
- Service Worker (for PWA)

### Data Storage

**localStorage** keys:
- `beetAnything_garden`: Garden state (beds, plantings)
- `beetAnything_wishlist`: Wishlist items
- `beetAnything_currentYear`: Active year
- `beetAnything_language`: UI language

**Export format**: JSON (human-readable)

---

## 🛠️ Development

### Project Structure

```
beet-anything/
├── beet-anything.html          # Main PWA file
├── README.md                   # This file
├── SPECIFICATION.md            # Technical specification
├── INSTALLATION.md             # Setup & deployment guide
└── assets/                     # (optional) Screenshots, icons
```

### Version History

**v1.2 - Easy Layout (February 2025)**
- Easy Layout Optimizer with AI-powered bed assignments
- Year Rotation Wizard for season-to-season planning
- Card-style tab navigation (Manual Planning | Easy Layout)
- Consolidated wishlist into Manual Planning tab
- Mobile optimizations for iPhone/Android
- 2-tab interface for simplified UX

**v1.1 - Pest Protection (February 2025)**
- Comprehensive pest protection system
- 35 vegetables & herbs with pest profiles
- Natural pest control through companion planting
- Visual pest indicators and protection badges
- Mobile-optimized tooltips

**v1.0 - Foundation (February 2025)**
- Multi-year garden planning
- 30 vegetables with companion data
- Crop rotation warnings
- Trilingual support (EN/DE/IT)
- PWA installation
- Export/import functionality

---

## 📄 License

**Free for any use** - Personal, commercial, educational, or otherwise.

No restrictions, no attribution required (though appreciated!).

---

## 🤝 Contributing

This is a personal vibe coding project, but suggestions and improvements are welcome:

1. Fork the repository
2. Make your changes
3. Test thoroughly (especially mobile devices)
4. Submit a pull request

**Areas for contribution**:
- Additional vegetables/herbs
- More companion relationships
- Additional language translations
- Bug fixes
- Mobile optimizations

---

## 📞 Contact

**Project**: [github.com/schatzl/beet-anything](https://github.com/schatzl/beet-anything)  
**Live Demo**: [target23.de/beet-anything.html](https://target23.de/beet-anything.html)

---

## 🙏 Acknowledgments

Built with:
- Vanilla JavaScript (no frameworks)
- CSS Grid & Flexbox
- localStorage for persistence
- Inter font family (system fallback)

Inspired by the need for a simple, privacy-focused garden planning tool that works offline and respects user data sovereignty.

---

**Happy Gardening! 🌱🥕🍅**
