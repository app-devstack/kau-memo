# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Core Commands
- `bun build` - Build all packages (workspaces)
- `bun test` - Run tests using Vitest
- `bun test:watch` - Run tests in watch mode

### Native App (Expo)
- `bun native` - Start Expo dev server
- `bun ios` - Run on iOS simulator
- `bun android` - Run on Android emulator
- `bun native:prebuild` - Prebuild native code

### Web App (Next.js)
- `bun web` - Build and run Next.js production server
- `bun web:prod` - Build Next.js for production
- `bun web:prod:serve` - Serve production build

### Code Quality
- Code formatting and linting is handled by Biome (see biome.json)
- Run Biome checks using the configuration in biome.json
- Tests use Vitest framework

## Project Architecture

This is a **monorepo** built with Tamagui that supports both React Native (Expo) and Next.js web applications.

### Workspace Structure
- **Root**: Yarn workspaces configuration with Turbo for build orchestration
- **apps/expo/**: React Native mobile app using Expo
- **apps/next/**: Next.js web app with both App Router and Pages Router examples
- **packages/app/**: Shared React components and screens
- **packages/ui/**: Shared UI component library built with Tamagui
- **packages/config/**: Tamagui configuration and theme setup

### Key Technologies
- **UI Framework**: Tamagui (cross-platform UI system)
- **Mobile**: React Native with Expo
- **Web**: Next.js 14 (App Router and Pages Router)
- **Build System**: Turbo monorepo
- **Testing**: Vitest
- **Code Quality**: Biome (formatting, linting)
- **Package Manager**: Bun 1.0.0 (Node 22 required)

### Application Domain
This is a Japanese shopping list management app called "かうめも" (kau-memo) with Animal Crossing-style gamification features. The app allows families/groups to:
- Share shopping lists with categorized items
- Grow characters through XP gained from completing shopping tasks
- Track group achievements and daily statistics
- Use a SQLite database with group-based data isolation

### Cross-Platform Code Sharing
- **packages/app/**: Contains shared screens that work on both platforms
- **Solito**: Used for navigation abstractions between React Native and Next.js
- **Tamagui**: Provides consistent styling across platforms
- Components automatically adapt to web/native environments

### Database Schema
- Uses SQLite with Expo SQLite
- Key entities: users, groups, group_members, shopping_items, categories, group_characters
- Features group-based data isolation and logical deletion patterns
- XP system rewards: item addition (+5), completion (+10), group invite (+30)

When working with this codebase:
1. Changes to shared UI components go in packages/ui/
2. Cross-platform screens go in packages/app/features/
3. Platform-specific code goes in respective apps/ directories
4. Always test both web and native platforms when making shared changes
5. Follow the gamification principles - avoid excessive purchase promotion