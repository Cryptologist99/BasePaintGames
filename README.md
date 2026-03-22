# BasePaint Games

An interconnected world built from 938+ daily CC0 pixel art canvases from [BasePaint](https://basepaint.xyz).

## Play Online

**Live Game:** [https://yourusername.github.io/BasePaintGames/](https://yourusername.github.io/BasePaintGames/)

## About

BasePaint Games transforms daily pixel art canvases into an explorable, connected world. Each canvas (256×256 pixels) becomes a room, building, or area you can navigate. Walk through doors, discover hidden minigames, and explore unique artwork.

## Features

- 🎮 **Navigable Canvases** - Explore multiple interconnected pixel art worlds
- 🚶 **Animated Character** - Smooth walk animations in all four directions
- 🚪 **Interactive Doors** - Dynamic animations when approaching entrances
- 🎨 **Spot the Difference** - Hidden minigame on canvas 456
- 🌳 **Layered Rendering** - Characters walk behind trees, buildings, and objects
- 🎯 **Collision Detection** - Realistic world interaction

## How to Play

**Controls:**
- **WASD** or **Arrow Keys** - Move character
- **Walk into doors** - Enter buildings and transition between canvases
- **Walk off edges** - Some canvases connect at borders

**Starting Point:** Canvas 939

## File Structure

```
BasePaintGames/
├── index.html                    # Landing page
├── canvas-navigator.html         # Canvas 939 (starting point)
├── canvas-navigator-927.html     # Canvas 927 (with animated door)
├── canvas-201-viewer.html        # Canvas 201 static viewer
├── spot-the-difference-game.html # Minigame
├── difference-mapper.html        # Tool for creating minigames
├── assets/
│   ├── day-939.png              # Canvas images
│   ├── day-939-map.png          # Collision/door maps
│   ├── day-927.png
│   ├── day-927-map.png
│   ├── day-456.png
│   ├── day-456-differences.json
│   ├── day-201.png
│   ├── player-animated.png      # Character sprite
│   └── door-spritesheet.png     # Door animation
└── README.md
```

## Map Color System

When creating map files in Aseprite:

- **Magenta (#FF00FF)** - Collision areas (walls, objects)
- **Cyan (#00FFFF)** - Door entrances
- **Yellow (#FFFF00)** - Overlay layer (draws over player - trees, roofs)
- **Orange (#FF8800)** - Collision + overlay (solid walls that render over player)

## Development

### Adding New Canvases

1. Export canvas as `day-XXX.png` (256×256)
2. Create map file `day-XXX-map.png` with marker colors
3. Create new HTML file or update door routing
4. Add canvas images to `assets/` folder

### Creating Minigames

Use `difference-mapper.html` to:
1. Load a canvas image
2. Mark interactive pixels
3. Export JSON data
4. Integrate with game logic

## Credits

- **Artwork:** All canvases from [BasePaint](https://basepaint.xyz) (CC0)
- **Game Engine:** Vanilla JavaScript
- **Sprite Animation:** Custom pixel art

## License

Game code: MIT License
Artwork: CC0 (Public Domain)

---

Built with ❤️ using BasePaint's daily pixel art canvases
