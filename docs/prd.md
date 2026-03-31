# Asset Management Client App

A comprehensive React Native mobile application for asset management, enabling clients to manage investment portfolios, complete tasks, communicate with personal managers, and track investment performance in real-time.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Features](#features)
- [Target Users](#target-users)
- [Quick Start](#quick-start)
- [Installation](#installation)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [Documentation](#documentation)
- [Contributing](#contributing)
- [License](#license)

---

## Project Overview

The Asset Management Client App is a mobile-first solution designed for investment clients to interact with their portfolios, communicate with personal managers, and complete required actions efficiently. Built with React Native and Expo, the app provides a professional, secure, and accessible experience for financial management.

### Key Features

| Feature | Description |
|---------|-------------|
| **Portfolio Management** | Real-time view of investment portfolio performance, asset allocation, and returns |
| **Task Management** | Centralized task list for document submissions, signature requests, meeting scheduling, and investment decisions |
| **Product Catalog** | Browse and invest in available investment products and strategies |
| **Chat Communication** | Direct messaging with personal asset managers |
| **Profile Management** | Account settings, KYC documentation, and preferences |
| **Dark Mode** | Full dark mode support for reduced eye strain |
| **Accessibility** | WCAG 2.1 AA compliant for all users |
| **Offline Support** | Cached data access when network unavailable |

### Technology Stack

| Category | Technology |
|----------|------------|
| **Framework** | React Native with Expo |
| **Language** | TypeScript (strict mode) |
| **State Management** | React Query (server state) |
| **Navigation** | React Navigation 6 |
| **Styling** | NativeWind (Tailwind CSS) |
| **Icons** | Lucide React Native |
| **Animations** | React Native Reanimated |
| **Date Handling** | date-fns |
| **Testing** | Jest, React Testing Library |

---

## Features

### 📊 Portfolio Dashboard

- Investment portfolio overview with real-time performance metrics
- Asset allocation visualization
- Historical performance charts
- Dividend and distribution tracking
- Document repository for statements and reports

### ✅ Task Management

- Unified task list with status filtering (Pending, Overdue, Completed)
- Task types: Document Request, Signature Request, Meeting Request, Information Request, Investment Decision, KYC Update, Review Request
- Priority indicators (Urgent, High, Normal, Low)
- Due date tracking with overdue alerts
- Pull-to-refresh and pagination support

### 💬 Communication

- Real-time chat with personal asset managers
- File sharing and document exchange
- Read receipts and typing indicators
- Push notifications for new messages

### 🏦 Product Catalog

- Browse investment products and strategies
- Product details with performance data
- Investment documents and prospectuses
- Investment action flows

### 🔐 Security & Compliance

- Biometric authentication support
- Secure document storage
- KYC/AML documentation management
- Audit trail for all actions

---

## Target Users

| User Type | Description |
|-----------|-------------|
| **Investment Clients** | High-net-worth individuals managing investment portfolios through the asset management firm |
| **Personal Managers** | Asset managers who manage client portfolios and communicate with clients |

---

## Quick Start

### Prerequisites

| Requirement | Version |
|-------------|---------|
| **Node.js** | v18.0.0 or higher |
| **npm** | v9.0.0 or higher |
| **Expo CLI** | Latest |
| **iOS Simulator** | Xcode 15+ (for iOS development) |
| **Android Emulator** | Android Studio (for Android development) |
| **Watchman** | Latest (macOS only) |

### Quick Installation

```bash
# Clone the repository
git clone https://github.com/your-org/asset-management-app.git
cd asset-management-app

# Install dependencies
npm install

# Start the development server
npm start

# Run on iOS simulator
npm run ios

# Run on Android emulator
npm run android
```

### Basic Usage

```typescript
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { TaskScreen } from '@features/tasks';
import { PortfolioScreen } from '@features/portfolio';
import { ChatScreen } from '@features/chat';
import { ProfileScreen } from '@features/profile';

const Tab = createBottomTabNavigator();

function App() {
  return (
    <NavigationContainer>
      <Tab.Navigator>
        <Tab.Screen name="Portfolio" component={PortfolioScreen} />
        <Tab.Screen name="Tasks" component={TaskScreen} />
        <Tab.Screen name="Chat" component={ChatScreen} />
        <Tab.Screen name="Profile" component={ProfileScreen} />
      </Tab.Navigator>
    </NavigationContainer>
  );
}
```

---

## Installation

### Step 1: Clone Repository

```bash
git clone https://github.com/your-org/asset-management-app.git
cd asset-management-app
```

### Step 2: Install Dependencies

```bash
# Install all dependencies
npm install

# Or install with specific package manager
yarn install
# or
pnpm install
```

### Step 3: Environment Configuration

Create a `.env` file in the project root:

```bash
# Copy example environment file
cp .env.example .env
```

Configure the following environment variables:

```env
# API Configuration
API_BASE_URL=https://api.assetmanagement.com
API_TIMEOUT=30000

# Authentication
AUTH_CLIENT_ID=your_client_id
AUTH_REDIRECT_URI=your_redirect_uri

# Feature Flags
ENABLE_CHAT=true
ENABLE_NOTIFICATIONS=true

# Analytics (Optional)
ANALYTICS_KEY=your_analytics_key
```

### Step 4: Configure NativeWind (Tailwind CSS)

The project uses NativeWind for styling. Configuration is in `tailwind.config.js`:

```javascript
module.exports = {
  content: ['./App.{js,jsx,ts,tsx}', './src/**/*.{js,jsx,ts,tsx}'],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#EFF6FF',
          500: '#3B82F6',
          600: '#2563EB',
          // ... full color scale
        },
        // ... additional colors
      },
    },
  },
  plugins: [],
};
```

### Step 5: iOS Setup (macOS only)

```bash
# Install iOS pods
cd ios
pod install
cd ..
```

### Step 6: Run the Application

```bash
# Start Expo development server
npm start

# Run on iOS
npm run ios

# Run on Android
npm run android

# Run on web
npm run web
```

---

## Usage

### Common Use Cases

#### 1. Task Management

View and complete tasks assigned by your manager:

```typescript
import { TaskScreen } from '@features/tasks';

// Navigate to TaskScreen
navigation.navigate('Tasks');

// Filter tasks by status
<TaskScreen initialFilter="pending" />

// Handle task completion
const handleTaskComplete = async (taskId: string) => {
  await tasksApi.complete(taskId);
  queryClient.invalidateQueries(['tasks']);
};
```

#### 2. Portfolio Viewing

Access portfolio performance and details:

```typescript
import { PortfolioScreen } from '@features/portfolio';

// Navigate to Portfolio
navigation.navigate('Portfolio');

// Fetch portfolio data
const { data: portfolio } = usePortfolio();

// Display performance metrics
<PortfolioCard 
  totalValue={portfolio.totalValue}
  returnPercentage={portfolio.returnPercentage}
  riskLevel={portfolio.riskLevel}
/>
```

#### 3. Manager Communication

Chat with your personal manager:

```typescript
import { ChatScreen } from '@features/chat';

// Navigate to Chat
navigation.navigate('Chat', { managerId: 'manager-123' });

// Send message
const handleSendMessage = async (message: string) => {
  await chatApi.sendMessage({
    managerId,
    content: message,
  });
};
```

#### 4. Document Submission

Submit required documents:

```typescript
import { DocumentUploadScreen } from '@features/documents';

// Navigate to document upload
navigation.navigate('DocumentUpload', { taskId: 'task-456' });

// Upload document
const handleUpload = async (file: File) => {
  await documentsApi.upload({
    taskId,
    file,
    type: 'tax_document',
  });
};
```

### Configuration Options

#### Theme Configuration

```typescript
// src/config/theme.ts
import { tokens } from './design-tokens';

export const lightTheme = {
  colors: {
    background: '#FFFFFF',
    surface: '#F9FAFB',
    text: {
      primary: '#111827',
      secondary: '#6B7280',
    },
  },
};

export const darkTheme = {
  colors: {
    background: '#030712',
    surface: '#111827',
    text: {
      primary: '#F3F4F6',
      secondary: '#9CA3AF',
    },
  },
};
```

#### API Configuration

```typescript
// src/config/api.ts
export const apiConfig = {
  baseURL: process.env.API_BASE_URL,
  timeout: 30000,
  headers: {
    'Content-Type': 'application/json',
  },
};

// API Client setup
export const apiClient = axios.create(apiConfig);
```

---

## Project Structure

```
asset-management-app/
├── src/
│   ├── app/                    # App-level configuration
│   │   ├── navigation/         # Navigation structure
│   │   └── providers/          # Context providers
│   │
│   ├── features/               # Feature-based modules
│   │   ├── tasks/              # Task management feature
│   │   │   ├── api/            # API functions
│   │   │   ├── components/     # Feature components
│   │   │   ├── hooks/          # Custom hooks
│   │   │   ├── screens/        # Screen components
│   │   │   ├── types/          # TypeScript types
│   │   │   └── utils/          # Utility functions
│   │   ├── portfolio/          # Portfolio feature
│   │   ├── chat/               # Chat feature
│   │   ├── products/           # Products catalog
│   │   └── profile/            # Profile management
│   │
│   ├── shared/                 # Shared resources
│   │   ├── ui/                 # Shared UI components
│   │   ├── api/                # Shared API utilities
│   │   ├── hooks/              # Shared hooks
│   │   └── utils/              # Shared utilities
│   │
│   └── config/                 # Configuration files
│       ├── theme.ts            # Theme configuration
│       └── constants.ts        # App constants
│
├── __tests__/                  # Test files
│   └── features/
│       └── tasks/              # Task feature tests
│           ├── components/     # Component tests
│           ├── hooks/          # Hook tests
│           ├── utils/          # Utility tests
│           └── screens/        # Screen tests
│
├── docs/                       # Documentation
│   ├── design-system.md        # Design system
│   ├── ui/                     # UI specifications
│   ├── api/                    # API documentation
│   └── adr/                    # Architecture decisions
│
├── App.tsx                     # App entry point
├── app.json                    # Expo configuration
├── package.json                # Dependencies
├── tsconfig.json               # TypeScript config
├── tailwind.config.js          # Tailwind config
└── README.md                   # This file
```

### Feature Architecture

Each feature follows a consistent structure:

```
features/[feature]/
├── api/           → API layer (React Query integration)
├── components/    → Presentational components
├── hooks/         → Custom React hooks
├── screens/       → Screen components
├── types/         → TypeScript definitions
├── utils/         → Helper functions
└── index.ts       → Public API exports
```

---

## Documentation

### Design System

- **[Design System](docs/design-system.md)** - Complete design system including colors, typography, spacing, components, and accessibility guidelines

### UI Specifications

- **[TaskScreen](docs/ui/screens/TaskScreen.md)** - Detailed UI specification for the task management screen

### API Documentation

- **[API Reference](docs/api/)** - API endpoints and integration guide (coming soon)

### Architecture

- **[Architecture Overview](docs/architecture.md)** - System architecture and design patterns (coming soon)
- **[ADR Index](docs/adr/)** - Architecture Decision Records

---

## Contributing

We welcome contributions! Please follow these guidelines:

### Development Setup

1. Fork and clone the repository
2. Install dependencies: `npm install`
3. Create a feature branch: `git checkout -b feature/your-feature`
4. Make your changes
5. Run tests: `npm test`
6. Submit a pull request

### Code Standards

| Standard | Tool | Config |
|----------|------|--------|
| **Linting** | ESLint | `.eslintrc.js` |
| **Formatting** | Prettier | `.prettierrc` |
| **Type Checking** | TypeScript | `tsconfig.json` |
| **Testing** | Jest | `jest.config.js` |

### Code Style Guidelines

```typescript
// Use functional components with TypeScript
export const MyComponent: React.FC<MyComponentProps> = ({ prop }) => {
  // Hooks at the top
  const [state, setState] = useState(initialState);
  
  // Event handlers
  const handlePress = useCallback(() => {
    // Implementation
  }, [dependencies]);
  
  // Render
  return (
    <View className="flex-1 bg-white">
      {/* Content */}
    </View>
  );
};
```

### Testing Requirements

- **Unit Tests**: Required for all components, hooks, and utilities
- **Integration Tests**: Required for screens and complex interactions
- **Coverage**: Minimum 70% code coverage
- **Accessibility Tests**: Required for all interactive components

```bash
# Run tests
npm test

# Run tests with coverage
npm test -- --coverage

# Run specific test file
npm test -- TaskScreen.test.tsx
```

### Commit Convention

We follow [Conventional Commits](https://www.conventionalcommits.org/):

```
feat: add new task filter functionality
fix: resolve pagination reset bug
docs: update README installation steps
test: add tests for TaskItem component
refactor: simplify task formatters
style: format code with Prettier
chore: update dependencies
```

### Pull Request Process

1. Create a feature branch from `main`
2. Make your changes with clear commit messages
3. Add/update tests for your changes
4. Update documentation if needed
5. Ensure all tests pass: `npm test`
6. Ensure no lint errors: `npm run lint`
7. Submit PR with description linking to relevant issues

### Branch Naming

| Type | Pattern |
|------|---------|
| **Feature** | `feature/issue-number-description` |
| **Bug Fix** | `fix/issue-number-description` |
| **Refactor** | `refactor/issue-number-description` |
| **Docs** | `docs/issue-number-description` |

---

## Accessibility

This application is built with accessibility as a first-class concern:

### WCAG 2.1 AA Compliance

- ✅ Color contrast ratios meet WCAG AA standards (4.5:1 for text)
- ✅ All interactive elements have visible focus indicators
- ✅ Touch targets are minimum 44x44 points
- ✅ Screen reader support with proper ARIA labels
- ✅ Keyboard navigation support
- ✅ Reduced motion support

### Testing Accessibility

```bash
# Run accessibility tests
npm test -- --testPathPattern=accessibility

# Manual testing checklist available in:
# docs/design-system.md#accessibility
```

---

## Performance

### Optimization Guidelines

- Use `FlatList` with `getItemLayout` for predictable scrolling
- Implement `React.memo` for frequently re-rendering components
- Use React Query caching with appropriate stale times
- Implement pagination for large data sets
- Lazy load features and screens

### Performance Metrics

| Metric | Target |
|--------|--------|
| Screen Load Time | < 1 second |
| List Scroll | 60fps |
| Time to Interactive | < 2 seconds |
| Bundle Size | < 5MB |

---

## Troubleshooting

### Common Issues

#### iOS Build Fails

```bash
# Clean iOS build
cd ios
rm -rf Pods Podfile.lock
pod install
cd ..
npm run ios
```

#### Android Build Fails

```bash
# Clean Android build
cd android
./gradlew clean
cd ..
npm run android
```

#### Metro Bundler Issues

```bash
# Reset Metro cache
npm start -- --reset-cache
```

---

## Scripts

| Script | Description |
|--------|-------------|
| `npm start` | Start Expo development server |
| `npm run ios` | Run on iOS simulator |
| `npm run android` | Run on Android emulator |
| `npm run web` | Run on web browser |
| `npm test` | Run tests |
| `npm run lint` | Run ESLint |
| `npm run type-check` | Run TypeScript type checking |
| `npm run build` | Build for production |

---

## Dependencies

### Core Dependencies

```json
{
  "react-native": "^0.73.0",
  "expo": "~50.0.0",
  "@react-navigation/native": "^6.0.0",
  "@tanstack/react-query": "^5.0.0",
  "nativewind": "^2.0.0",
  "lucide-react-native": "^0.300.0",
  "react-native-reanimated": "^3.0.0",
  "date-fns": "^3.0.0"
}
```

### Development Dependencies

```json
{
  "typescript": "^5.0.0",
  "jest": "^29.0.0",
  "@testing-library/react-native": "^12.0.0",
  "eslint": "^8.0.0",
  "prettier": "^3.0.0"
}
```

---

## License

This project is proprietary software. All rights reserved.

**License Type:** Proprietary

**Copyright © 2024 Asset Management Company. All rights reserved.**

Unauthorized copying, modification, distribution, or use of this software is strictly prohibited without written permission from the copyright holder.

---

## Support

For technical support or questions:

- **Documentation**: [docs/](docs/)
- **Issues**: Create an issue in the repository
- **Email**: support@assetmanagement.com

---

## Changelog

### Version 1.0.0 (Current)

- Initial release
- Task management feature
- Portfolio dashboard
- Chat functionality
- Product catalog
- Profile management
- Dark mode support
- WCAG 2.1 AA accessibility compliance

---

**Built with ❤️ for Asset Management Clients**
