# Ionic-2048 🎮

English | [简体中文](README.md)

A modern 2048 game application developed with Ionic + Vue 3 + TypeScript, supporting cross-platform deployment (Web, iOS, Android).

## 🚀 Project Features

- **Modern Tech Stack**: Built with Ionic 8, Vue 3 Composition API, and TypeScript
- **Cross-Platform Support**: One codebase for Web, iOS, and Android platforms
- **Responsive Design**: Perfect adaptation for phones, tablets, and desktop devices
- **Touch & Keyboard Support**: Supports swipe gestures and arrow key operations
- **Local Storage**: Automatically saves high score records
- **Modern UI**: Features gradient backgrounds, glass morphism effects, and smooth animations
- **Complete Game Features**: Includes scoring system, game over detection, victory judgment, etc.

## 🎯 Game Rules

- Use arrow keys or swipe gestures to move number tiles
- Tiles with the same number merge into a larger number
- A new 2 or 4 tile appears randomly after each move
- The goal is to create a 2048 tile
- Game ends when no moves are possible and no empty spaces remain

## 🛠️ Tech Stack

### Frontend Framework
- **Vue 3**: Uses Composition API and `<script setup>` syntax
- **Ionic 8**: Cross-platform mobile application development framework
- **TypeScript**: Provides type safety and better development experience

### Build Tools
- **Vite**: Fast build tool and development server
- **Capacitor**: Native app packaging and feature integration

### Development Tools
- **ESLint**: Code quality checking
- **Cypress**: E2E testing framework
- **Vitest**: Unit testing framework

## 📁 Project Structure

```
src/
├── App.vue                 # Root application component
├── main.ts                 # Application entry point
├── vite-env.d.ts          # Vite type declarations
├── components/            # Components directory
│   └── Game2048.vue       # Core 2048 game component
├── router/                # Router configuration
│   └── index.ts           # Route definitions
├── theme/                 # Theme styles
│   └── variables.css      # CSS variable definitions
└── views/                 # Page views
    └── HomePage.vue       # Main page component
```

## 🚀 Quick Start

### Requirements

- Node.js 18+ 
- npm or yarn
- Git

### Install Dependencies

```bash
# Clone the project
git clone https://github.com/zym9863/Ionic-2048.git
cd Ionic-2048

# Install dependencies
npm install
```

### Development Mode

```bash
# Start development server
npm run dev

# Visit http://localhost:5173
```

### Build Project

```bash
# Build for production
npm run build

# Preview build result
npm run preview
```

### Mobile Development

```bash
# Add iOS/Android platforms
npx cap add ios
npx cap add android

# Build and sync to native platforms
npm run build
npx cap sync

# Open in native IDE
npx cap open ios
npx cap open android
```

## 🧪 Testing

```bash
# Run unit tests
npm run test:unit

# Run E2E tests
npm run test:e2e

# Code linting
npm run lint
```

## 🎨 Game Component Details

### Game2048.vue Core Features

- **Game State Management**: Uses Vue 3 reactive API to manage game board, scores, and other states
- **Movement Logic**: Implements tile movement and merging algorithms for all four directions
- **Touch Control**: Supports swipe gesture recognition with 50px distance threshold
- **Keyboard Control**: Listens for arrow key events, supporting desktop operations
- **Animation Effects**: CSS3 animations and CSS variables for smooth visual effects
- **Local Storage**: localStorage for saving high score data

### Style Features

- **Modern Color Scheme**: Uses gradients and semi-transparent effects
- **Responsive Layout**: Adapts to different screen sizes
- **Glass Morphism**: backdrop-filter for modern visual experience
- **Animation System**: Tile appearance, merging, and movement animations

## 📱 Platform Features

### Web
- Responsive design supporting desktop and mobile browsers
- Dual keyboard and touch operation support
- PWA ready, installable to home screen

### Mobile (iOS/Android)
- Native performance experience
- Device vibration feedback support
- Status bar adaptation
- Full-screen gaming experience

## 🔧 Configuration Files

- `package.json`: Project dependencies and script configuration
- `vite.config.ts`: Vite build configuration
- `capacitor.config.ts`: Capacitor native platform configuration
- `ionic.config.json`: Ionic CLI configuration
- `tsconfig.json`: TypeScript compilation configuration

## 🌟 Development Highlights

1. **Modern Vue 3 Syntax**: Full use of Composition API and `<script setup>`
2. **Type Safety**: TypeScript provides complete type checking
3. **Performance Optimization**: Vite build tool ensures fast development and build experience
4. **Code Standards**: ESLint ensures code quality and consistency
5. **Test Coverage**: Unit tests and E2E tests guarantee code quality
6. **Cross-Platform Deployment**: One codebase supports multi-platform publishing

## 🎯 Future Plans

- [ ] Add game sound effects and background music
- [ ] Implement game replay functionality
- [ ] Add multiple game modes (3x3, 5x5, 6x6)
- [ ] Integrate online leaderboards
- [ ] Add game theme switching
- [ ] Support game save and restore
- [ ] Add game statistics

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🤝 Contributing

Welcome to submit Issues and Pull Requests to help improve the project!

1. Fork this repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📞 Contact

For questions or suggestions, please contact through:

- Submit GitHub Issues
- Send email to project maintainers

---

⭐ If this project helps you, please give it a Star!
