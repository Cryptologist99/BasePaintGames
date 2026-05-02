# BasePaint Games - Master Project Documentation

## Project Overview
An interconnected pixel art world built from 938+ daily CC0 canvases from BasePaint.xyz. Players navigate between canvases, discover minigames, and explore unique artwork.

**Live Site:** https://cryptologist99.github.io/BasePaintGames/
**GitHub Repo:** https://github.com/Cryptologist99/BasePaintGames

---

## Current Game State (as of session end)

### Implemented Features
- ✅ Navigable canvas system (939, 927, 201)
- ✅ Animated player character (6x11px, 4 directions, 3-frame walk cycle)
- ✅ Animated door (canvas 927 at position 107,195)
- ✅ Spot-the-difference minigame (canvas 456)
- ✅ Collision detection system
- ✅ Overlay rendering (yellow/orange markers)
- ✅ Door routing system
- ✅ Edge-based canvas transitions
- ✅ LocalStorage state persistence
- ✅ GitHub Pages hosting

### Current Canvas Network
```
Canvas 939 (Starting Point)
  ├─ Door at (127, 186) → Canvas 927
  └─ Door at (128, 99) → Canvas 201 (static viewer)

Canvas 927
  ├─ Animated door at (107, 195)
  ├─ Starting position: (119, 226)
  └─ Bottom edge → Returns to Canvas 939 at (127, 195)

Canvas 201
  └─ Static viewer with back button

Canvas 456
  └─ Spot-the-difference minigame (17 pairs, 4,643 pixels)
```

---

## Technical Architecture

### File Structure
```
BasePaintGames/
├── index.html                    # Landing page
├── canvas-navigator.html         # Canvas 939 navigator
├── canvas-navigator-927.html     # Canvas 927 navigator
├── canvas-201-viewer.html        # Canvas 201 static viewer
├── spot-the-difference-game.html # Minigame
├── difference-mapper.html        # Development tool
├── README.md
└── assets/
    ├── day-939.png              # Canvas images
    ├── day-939-map.png          # Collision/door maps
    ├── day-927.png
    ├── day-927-map.png
    ├── day-456.png
    ├── day-456-differences.json
    ├── day-201.png
    ├── player-animated.png      # 24x33 sprite sheet
    └── door-spritesheet.png     # 128x20 sprite sheet (4 frames)
```

### Core Systems

#### 1. Player Character System
- **Sprite:** `player-animated.png` (24×33 pixels)
- **Layout:** 4 columns (down, up, left, right) × 3 rows (idle, step1, step2)
- **Each frame:** 6×11 pixels
- **Speed:** 0.3 pixels per frame
- **Collision box:** Bottom-center 2×1 pixels
- **Animation:** 150ms per frame when moving

#### 2. Map Marker Color System
| Color | Hex | Collision? | Overlay? | Use |
|-------|-----|------------|----------|-----|
| Magenta | #FF00FF | ✅ Yes | ❌ No | Walls/objects player walks behind |
| Cyan | #00FFFF | ❌ No | ❌ No | Door entrances |
| Yellow | #FFFF00 | ❌ No | ✅ Yes | Trees/roofs (walkable, draws over) |
| Orange | #FF8800 | ✅ Yes | ✅ Yes | Building walls (solid, draws over) |

**Color Matching:** Uses `isMarkerColor()` function with ±2 RGB tolerance for PNG compression artifacts

#### 3. Rendering Order
1. Base canvas (background)
2. Animated sprites (doors, NPCs)
3. Player character
4. Overlay layer (yellow/orange marked pixels)

#### 4. Animation System

**Player Animation:**
```javascript
playerAnimation = {
    direction: 'down',      // down, up, left, right
    frame: 0,               // 0=idle, 1=step1, 2=step2
    frameDelay: 150,        // ms between frames
    lastFrameUpdate: 0,
    isMoving: false
}
```

**Door Animation:**
```javascript
animations.door_107_195 = {
    x: 107, y: 195,
    width: 32, height: 20,
    frames: 4,
    currentFrame: 0,
    targetFrame: 0,
    frameDelay: 50,
    isNearPlayer: false     // triggers within 10px
}
```

#### 5. State Persistence (localStorage)

**Canvas Transitions:**
```javascript
{
    canvas: 939,           // Target canvas ID
    returnX: 127,          // Spawn X position
    returnY: 195,          // Spawn Y position
    timestamp: Date.now()
}
```

Saved to: `basepaint_return_location`

---

## Key Code Patterns

### Adding a New Canvas

**Step 1: Create Navigator File**
```javascript
// Copy canvas-navigator-927.html as template
// Update canvas number throughout
// Set starting position in player object
```

**Step 2: Add Assets**
```
assets/day-XXX.png       // Display canvas
assets/day-XXX-map.png   // Collision/door map (marked in Aseprite)
```

**Step 3: Update Door Routing**
In source canvas's `handleDoor()`:
```javascript
const isDoor = isInSameRegion(centerX, centerY, X, Y, DOOR_COLOR);
if (isDoor) {
    const gameState = {
        canvas: XXX,
        returnX: spawnX,
        returnY: spawnY,
        timestamp: Date.now()
    };
    localStorage.setItem('basepaint_return_location', JSON.stringify(gameState));
    window.location.href = 'canvas-navigator-XXX.html';
}
```

### Adding Animated Sprites

**Sprite Sheet Format:**
- Horizontal frames: `[frame0][frame1][frame2]...`
- Frame extraction: `sourceX = frameNumber * frameWidth`

**Animation Update Loop:**
```javascript
if (now - anim.lastUpdate > anim.frameDelay) {
    if (anim.currentFrame < anim.targetFrame) anim.currentFrame++;
    else if (anim.currentFrame > anim.targetFrame) anim.currentFrame--;
    anim.lastUpdate = now;
}
```

### Creating Map Files (Aseprite)

1. Open canvas PNG in Aseprite
2. Create new layer for markers
3. Paint collision areas with exact hex colors:
   - Magenta #FF00FF for walls
   - Cyan #00FFFF for doors
   - Yellow #FFFF00 for overlays
   - Orange #FF8800 for solid overlays
4. Export as `day-XXX-map.png`
5. **Important:** Use exact hex values (no transparency, no anti-aliasing)

---

## Development Tools

### Difference Mapper (difference-mapper.html)
**Purpose:** Create spot-the-difference minigames
**Usage:**
1. Load canvas image
2. Load existing differences JSON (optional)
3. Click/drag to mark difference pixels
4. Export updated JSON
**Performance:** Optimized for 4,000+ pixels

### Local Testing
```bash
cd C:\Vibe\BasePaintGames\game
python -m http.server 8000
# Visit http://localhost:8000
```

---

## Known Issues & Solutions

### Issue: Files Not Auto-Loading
**Cause:** Browser cache or wrong file paths
**Solution:** Hard refresh (Ctrl+Shift+R) or check console for 404 errors

### Issue: Stuck Keys After Canvas Transition
**Cause:** Keys registered as "down" when leaving page
**Solution:** Window focus listener resets key states (already implemented)

### Issue: Spawning Inside Collision
**Cause:** Return position overlaps with walls
**Solution:** Offset spawn position (e.g., +5 pixels below door)

### Issue: Edge Detection Triggering Multiple Times
**Cause:** Edge collision fires every frame
**Solution:** 1-second cooldown timer (already implemented in canvas-927)

---

## Future Expansion Plans

### Phase 1: Content Expansion ✅ IN PROGRESS
- Add more navigable canvases
- Create additional minigames
- Build interconnected canvas network

### Phase 2: User Accounts & Progression
- Firebase authentication
- Save game progress
- Achievement system
- Cross-device sync

**Implementation Notes:**
- Current localStorage structure is Firebase-compatible
- Just swap `localStorage.setItem()` → `firebase.database().ref().set()`
- No game logic changes needed

### Phase 3: Social Features
- Leaderboards
- Shared world state
- Multiplayer elements

### Phase 4: Advanced Features
- NPC characters with AI
- Inventory system
- Quest system
- Day/night cycle

---

## Performance Optimization Tips

### Already Implemented:
- Batch rendering for marked pixels
- Minimal DOM updates
- Frame-based animation (not CSS)
- Image pre-loading

### Future Optimizations:
- Canvas pooling for multiple canvases
- Lazy loading for distant canvases
- Web Workers for heavy computation
- Asset sprite sheets to reduce HTTP requests

---

## Asset Creation Workflow

### Player Sprites (6×11 per frame)
**Current:** `player-animated.png` (24×33, 4×3 grid)
**To Add New:**
1. Create frames in Aseprite
2. Export as horizontal strip
3. Update sprite sheet loading code

### Door Animations (32×20 per frame)
**Current:** `door-spritesheet.png` (128×20, 4 frames)
**Format:** closed → opening → open → closing

### Canvas Art (256×256)
**Source:** BasePaint daily drops
**Format:** PNG, pixel art
**Download:** `https://basepaint.xyz/api/art/image?day={N}`

---

## Critical Constants & Magic Numbers

```javascript
// Canvas
CANVAS_WIDTH = 256
CANVAS_HEIGHT = 256
scale = 3  // Display multiplier

// Player
player.width = 6
player.height = 11
player.speed = 0.3
player.collisionWidth = 2
player.collisionHeight = 1

// Animation
playerAnimation.frameDelay = 150  // ms
doorAnimation.frameDelay = 50     // ms
doorAnimation.triggerDistance = 10 // pixels

// Map Markers
COLLISION_COLOR = '#FF00FF'
DOOR_COLOR = '#00FFFF'
OVERLAY_COLOR = '#FFFF00'
SOLID_OVERLAY_COLOR = '#FF8800'
colorTolerance = 2  // RGB values
```

---

## Testing Checklist for New Features

- [ ] Test all four movement directions
- [ ] Test collision detection (can't walk through walls)
- [ ] Test door transitions (spawn at correct location)
- [ ] Test return transitions (spawn at origin)
- [ ] Test overlay rendering (player behind trees/roofs)
- [ ] Test on different browsers (Chrome, Firefox, Safari)
- [ ] Test on mobile (touch controls not yet implemented)
- [ ] Check browser console for errors
- [ ] Verify all assets load (no 404s)
- [ ] Test with hard refresh (clear cache)

---

## Quick Reference: Common Tasks

### Update Player Starting Position
In navigator HTML:
```javascript
let player = {
    x: newX,
    y: newY,
    // ... rest
};
```

### Change Walk Speed
```javascript
player.speed = 0.3  // Lower = slower
```

### Add New Door
1. Mark cyan (#00FFFF) in map file
2. Add routing in handleDoor():
```javascript
const isDoorN = isInSameRegion(centerX, centerY, X, Y, DOOR_COLOR);
if (isDoorN) {
    // Set destination
}
```

### Disable Debug Logging
Remove or comment out:
```javascript
console.log('At', centerX, centerY, 'Color:', tileColor);
```

---

## Contact & Resources

**Project Repository:** https://github.com/Cryptologist99/BasePaintGames
**BasePaint Source:** https://basepaint.xyz
**Canvas API:** `https://basepaint.xyz/api/art/image?day={N}`

**Key Dependencies:**
- None! Pure vanilla JavaScript
- No frameworks, no build process
- Just HTML5 Canvas API

**Browser Compatibility:**
- Chrome/Edge: ✅ Full support
- Firefox: ✅ Full support
- Safari: ✅ Full support
- Mobile: ⚠️ Works but no touch controls yet

---

## Version History

**v0.1** - Initial prototype
- Single canvas navigation
- Basic collision

**v0.2** - Animation system
- Player walk cycles
- Door animations
- Spot-the-difference game

**v0.3** - Multi-canvas
- Canvas 939, 927, 201 connected
- Door routing system
- Edge transitions

**Current: v0.3.1**
- GitHub Pages deployment
- Asset organization
- Documentation complete

---

## Emergency Recovery

**If You Lose Files:**
1. All code is in GitHub: https://github.com/Cryptologist99/BasePaintGames
2. Local backup: `C:\Vibe\BasePaintGames\game\`
3. Live version: View source on GitHub Pages

**If You Need to Start Fresh Chat:**
1. Share this document with Claude
2. Reference specific sections for context
3. Mention: "Continuing BasePaint Games project - see PROJECT_DOCS.md"

---

**Last Updated:** Session ending 2026-04-11
**Next Steps:** Add more canvases, create minigames, consider user accounts
