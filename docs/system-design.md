# Technical Standards and Conventions

**Project**: Asset Management Client Portal  
**Version**: 1.0  
**Date**: 2025-01-18  
**Status**: Approved

---

## Table of Contents

1. [Coding Standards](#1-coding-standards)
2. [Naming Conventions](#2-naming-conventions)
3. [Folder Structure](#3-folder-structure)
4. [Git Workflow](#4-git-workflow)
5. [Testing Standards](#5-testing-standards)
6. [Code Review Guidelines](#6-code-review-guidelines)
7. [Documentation Standards](#7-documentation-standards)

---

## 1. Coding Standards

### 1.1 TypeScript/JavaScript Standards

#### 1.1.1 TypeScript Configuration

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "lib": ["ES2020"],
    "allowJs": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "noImplicitReturns": true,
    "noUncheckedIndexedAccess": true,
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "react-native",
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"],
      "@domain/*": ["src/domain/*"],
      "@adapters/*": ["src/adapters/*"],
      "@view/*": ["src/view/*"],
      "@shared/*": ["src/shared/*"],
      "@state/*": ["src/state/*"],
      "@i18n/*": ["src/i18n/*"],
      "@di/*": ["src/di/*"]
    }
  }
}
```

#### 1.1.2 General TypeScript Rules

```typescript
// ✅ GOOD: Explicit types for function parameters and return values
function calculatePortfolioValue(positions: Position[]): number {
  return positions.reduce((sum, pos) => sum + pos.value, 0);
}

// ❌ BAD: Implicit any
function calculatePortfolioValue(positions) {
  return positions.reduce((sum, pos) => sum + pos.value, 0);
}

// ✅ GOOD: Use interfaces for object shapes
interface UserProfile {
  id: string;
  email: string;
  firstName: string;
  lastName: string;
  manager?: ManagerInfo;
}

// ✅ GOOD: Use type for unions and computed types
type AuthStatus = 'authenticated' | 'unauthenticated' | 'loading';
type PortfolioActions = ActionType<typeof portfolioActions>;

// ✅ GOOD: Readonly for immutable data
interface Product {
  readonly id: string;
  readonly name: string;
  readonly description: string;
  readonly minInvestment: number;
}

// ✅ GOOD: Use const assertions for literals
const ROUTES = {
  HOME: 'Home',
  PORTFOLIO: 'Portfolio',
  PRODUCTS: 'Products',
  CHAT: 'Chat',
  PROFILE: 'Profile',
} as const;

// ✅ GOOD: Nullish coalescing for defaults
const displayName = user.name ?? 'Guest';

// ❌ BAD: Using || with falsy values
const count = value || 0; // Incorrect if value is 0
const count = value ?? 0; // Correct

// ✅ GOOD: Optional chaining for nested properties
const managerName = user?.manager?.name;

// ✅ GOOD: Discriminated unions for state
type LoadingState = { status: 'loading' };
type SuccessState<T> = { status: 'success'; data: T };
type ErrorState = { status: 'error'; error: Error };
type AsyncState<T> = LoadingState | SuccessState<T> | ErrorState;
```

#### 1.1.3 ES6+ Features

```typescript
// ✅ GOOD: Destructuring with defaults
const { 
  title, 
  subtitle = '', 
  onPress, 
  disabled = false 
} = props;

// ✅ GOOD: Object shorthand
const config = {
  title,
  onPress,
  disabled,
};

// ✅ GOOD: Spread for immutable updates
const updatedPortfolio = {
  ...portfolio,
  lastUpdated: new Date(),
};

// ✅ GOOD: Array methods over loops
const activePositions = positions.filter(p => p.isActive);
const totalValue = positions.reduce((sum, p) => sum + p.value, 0);
const positionMap = new Map(positions.map(p => [p.id, p]));

// ✅ GOOD: Template literals
const fullName = `${user.firstName} ${user.lastName}`;
const apiUrl = `${API_BASE_URL}/portfolio/${portfolioId}`;

// ✅ GOOD: Async/await over raw promises
async function fetchPortfolio(id: string): Promise<Portfolio> {
  try {
    const response = await apiClient.get<Portfolio>(`/portfolio/${id}`);
    return response.data;
  } catch (error) {
    if (error instanceof ApiError) {
      logger.error('Portfolio fetch failed', { id, error: error.message });
    }
    throw error;
  }
}

// ❌ BAD: Promise chains
function fetchPortfolio(id: string): Promise<Portfolio> {
  return apiClient.get(`/portfolio/${id}`)
    .then(response => response.data)
    .catch(error => {
      logger.error('Portfolio fetch failed', { id, error });
      throw error;
    });
}
```

### 1.2 React Native Standards

#### 1.2.1 Functional Components

```typescript
// ✅ GOOD: Component with proper typing
interface ProductCardProps {
  product: Product;
  onPress: (product: Product) => void;
  onInterestPress?: (productId: string) => void;
  testID?: string;
}

export const ProductCard: React.FC<ProductCardProps> = memo(({
  product,
  onPress,
  onInterestPress,
  testID = 'product-card',
}) => {
  const { t } = useTranslation();
  const [isPressed, setIsPressed] = useState(false);

  const handlePress = useCallback(() => {
    onPress(product);
  }, [product, onPress]);

  const handleInterestPress = useCallback(() => {
    onInterestPress?.(product.id);
  }, [product.id, onInterestPress]);

  return (
    <Pressable
      onPress={handlePress}
      onPressIn={() => setIsPressed(true)}
      onPressOut={() => setIsPressed(false)}
      testID={testID}
    >
      <View style={[styles.container, isPressed && styles.pressed]}>
        <Text style={styles.name}>{product.name}</Text>
        <Text style={styles.description}>{product.description}</Text>
        {onInterestPress && (
          <Button
            title={t('products.expressInterest')}
            onPress={handleInterestPress}
            testID={`${testID}-interest-button`}
          />
        )}
      </View>
    </Pressable>
  );
});

const styles = StyleSheet.create({
  container: {
    padding: 16,
    backgroundColor: colors.white,
    borderRadius: 12,
  },
  pressed: {
    opacity: 0.7,
  },
  name: {
    fontSize: 18,
    fontWeight: '600',
    color: colors.textPrimary,
  },
  description: {
    fontSize: 14,
    color: colors.textSecondary,
    marginTop: 4,
  },
});
```

#### 1.2.2 Hooks Rules

```typescript
// ✅ GOOD: Custom hook with proper typing
interface UsePortfolioResult {
  portfolio: Portfolio | null;
  isLoading: boolean;
  error: Error | null;
  refetch: () => Promise<void>;
}

export function usePortfolio(portfolioId: string): UsePortfolioResult {
  const queryResult = useQuery({
    queryKey: queryKeys.portfolio.detail(portfolioId),
    queryFn: () => portfolioRepository.getPortfolio(portfolioId),
    staleTime: 5 * 60 * 1000, // 5 minutes
    gcTime: 10 * 60 * 1000, // 10 minutes
  });

  return {
    portfolio: queryResult.data ?? null,
    isLoading: queryResult.isLoading,
    error: queryResult.error ?? null,
    refetch: async () => { await queryResult.refetch(); },
  };
}

// ✅ GOOD: Hook with cleanup
export function useWebSocket(endpoint: string): WebSocketState {
  const [state, setState] = useState<WebSocketState>({
    isConnected: false,
    lastMessage: null,
  });

  useEffect(() => {
    const socket = io(endpoint);

    socket.on('connect', () => {
      setState(prev => ({ ...prev, isConnected: true }));
    });

    socket.on('message', (message: ChatMessage) => {
      setState(prev => ({ ...prev, lastMessage: message }));
    });

    // Cleanup function
    return () => {
      socket.disconnect();
    };
  }, [endpoint]);

  return state;
}

// ✅ GOOD: Memoized callbacks and values
function PortfolioScreen() {
  const { portfolio } = usePortfolio('user-portfolio');

  // Memoize expensive calculations
  const totalValue = useMemo(() => {
    if (!portfolio) return 0;
    return portfolio.positions.reduce((sum, p) => sum + p.value, 0);
  }, [portfolio]);

  // Memoize callbacks passed to children
  const handlePositionPress = useCallback((position: Position) => {
    navigation.navigate('PositionDetail', { positionId: position.id });
  }, [navigation]);

  return (
    <View>
      <TotalValue value={totalValue} />
      <PositionList 
        positions={portfolio?.positions ?? []}
        onPositionPress={handlePositionPress}
      />
    </View>
  );
}
```

#### 1.2.3 Performance Optimization

```typescript
// ✅ GOOD: List virtualization
import { FlashList } from '@shopify/flash-list';

function ProductList({ products }: { products: Product[] }) {
  const renderItem = useCallback(({ item }: { item: Product }) => (
    <ProductCard product={item} />
  ), []);

  return (
    <FlashList
      data={products}
      renderItem={renderItem}
      keyExtractor={(item) => item.id}
      estimatedItemSize={120}
      testID="product-list"
    />
  );
}

// ✅ GOOD: Lazy loading screens
const PortfolioScreen = lazy(() => import('./PortfolioScreen'));
const ChatScreen = lazy(() => import('./ChatScreen'));

// ✅ GOOD: Image optimization
function ProductImage({ uri }: { uri: string }) {
  return (
    <Image
      source={{ uri, cache: 'default' }}
      style={styles.image}
      resizeMode="cover"
      defaultSource={require('./placeholder.png')}
    />
  );
}

// ✅ GOOD: InteractionManager for heavy operations
useEffect(() => {
  const task = InteractionManager.runAfterInteractions(() => {
    // Run expensive operation after animations
    calculatePortfolioMetrics();
  });

  return () => task.cancel();
}, []);
```

### 1.3 React Query Standards

```typescript
// ✅ GOOD: Query key factory
export const queryKeys = {
  auth: {
    all: ['auth'] as const,
    session: () => [...queryKeys.auth.all, 'session'] as const,
  },
  products: {
    all: ['products'] as const,
    lists: () => [...queryKeys.products.all, 'list'] as const,
    list: (filters: ProductFilters) => 
      [...queryKeys.products.lists(), filters] as const,
    details: () => [...queryKeys.products.all, 'detail'] as const,
    detail: (id: string) => [...queryKeys.products.details(), id] as const,
  },
  portfolio: {
    all: ['portfolio'] as const,
    detail: (id: string) => [...queryKeys.portfolio.all, id] as const,
    positions: (portfolioId: string) => 
      [...queryKeys.portfolio.all, portfolioId, 'positions'] as const,
    transactions: (portfolioId: string) => 
      [...queryKeys.portfolio.all, portfolioId, 'transactions'] as const,
  },
} as const;

// ✅ GOOD: Custom mutation hook
interface UseExpressInterestResult {
  mutate: (productId: string) => void;
  isPending: boolean;
  error: Error | null;
}

export function useExpressInterest(): UseExpressInterestResult {
  const queryClient = useQueryClient();
  const { showSuccess, showError } = useToast();

  const mutation = useMutation({
    mutationFn: (productId: string) => 
      productRepository.expressInterest(productId),
    onSuccess: (_, productId) => {
      // Invalidate and refetch
      queryClient.invalidateQueries({ 
        queryKey: queryKeys.products.detail(productId) 
      });
      showSuccess(t('products.interestExpressed'));
    },
    onError: (error) => {
      showError(error.message);
    },
  });

  return {
    mutate: mutation.mutate,
    isPending: mutation.isPending,
    error: mutation.error,
  };
}

// ✅ GOOD: Optimistic update
const mutation = useMutation({
  mutationFn: sendMessage,
  onMutate: async (newMessage) => {
    // Cancel outgoing refetches
    await queryClient.cancelQueries({ 
      queryKey: queryKeys.chat.history(chatId) 
    });

    // Snapshot previous value
    const previousMessages = queryClient.getQueryData(
      queryKeys.chat.history(chatId)
    );

    // Optimistically update
    queryClient.setQueryData(
      queryKeys.chat.history(chatId),
      (old: Message[] = []) => [...old, { ...newMessage, pending: true }]
    );

    return { previousMessages };
  },
  onError: (err, newMessage, context) => {
    // Rollback on error
    queryClient.setQueryData(
      queryKeys.chat.history(chatId),
      context?.previousMessages
    );
  },
  onSettled: () => {
    queryClient.invalidateQueries({ 
      queryKey: queryKeys.chat.history(chatId) 
    });
  },
});
```

---

## 2. Naming Conventions

### 2.1 Files and Folders

#### 2.1.1 General Rules

| Type | Convention | Example |
|------|-----------|---------|
| Folders | kebab-case | `product-list/`, `portfolio-view/` |
| Components | PascalCase | `ProductCard.tsx`, `PortfolioList.tsx` |
| Screens | PascalCase + Screen suffix | `PortfolioScreen.tsx`, `ChatScreen.tsx` |
| Hooks | camelCase + use prefix | `usePortfolio.ts`, `useAuth.ts` |
| Utils | camelCase | `formatters.ts`, `validators.ts` |
| Constants | camelCase | `routes.ts`, `api-config.ts` |
| Types | camelCase | `product.types.ts`, `portfolio.types.ts` |
| Tests | Same as source + .test | `ProductCard.test.tsx` |
| Styles | Same as component | `ProductCard.styles.ts` (if separate) |

#### 2.1.2 Domain Layer Files

```
domain/
├── entities/
│   ├── Product.ts          # Entity class
│   ├── Portfolio.ts        # Entity class
│   └── Position.ts         # Entity class
├── value-objects/
│   ├── Money.ts            # Value object class
│   ├── Percentage.ts       # Value object class
│   └── DateRange.ts        # Value object class
├── repositories/
│   ├── IProductRepository.ts    # Interface (I prefix)
│   ├── IPortfolioRepository.ts  # Interface (I prefix)
│   └── IAuthRepository.ts       # Interface (I prefix)
├── usecases/
│   ├── GetProductDetails.ts     # Use case class
│   ├── CalculatePortfolioValue.ts
│   └── AuthenticateUser.ts
└── services/
    ├── PortfolioCalculator.ts   # Domain service
    └── InvestmentValidator.ts
```

#### 2.1.3 Adapter Layer Files

```
adapters/
├── api/
│   ├── ApiClient.ts            # Base API client
│   ├── ProductApi.ts           # Product API client
│   ├── PortfolioApi.ts         # Portfolio API client
│   └── AuthApi.ts              # Auth API client
├── repositories/
│   ├── ProductRepository.ts    # Implementation
│   ├── PortfolioRepository.ts  # Implementation
│   └── AuthRepository.ts       # Implementation
├── storage/
│   ├── SecureStorage.ts        # Secure storage adapter
│   ├── AsyncStorage.ts         # Async storage adapter
│   └── CacheStorage.ts         # Cache storage adapter
├── websocket/
│   └── ChatWebSocket.ts        # WebSocket adapter
└── platform/
    ├── BiometricsService.ts    # Platform biometrics
    └── NotificationService.ts  # Platform notifications
```

#### 2.1.4 View Layer Files

```
view/
├── screens/
│   ├── portfolio/
│   │   ├── PortfolioScreen.tsx
│   │   ├── PortfolioScreen.styles.ts
│   │   ├── PortfolioScreen.test.tsx
│   │   └── index.ts           # Barrel export
│   └── chat/
│       ├── ChatScreen.tsx
│       ├── components/         # Screen-specific components
│       │   ├── MessageBubble.tsx
│       │   └── ChatInput.tsx
│       └── hooks/              # Screen-specific hooks
│           └── useChatMessages.ts
├── components/
│   ├── common/                 # Shared components
│   │   ├── Button/
│   │   │   ├── Button.tsx
│   │   │   ├── Button.styles.ts
│   │   │   ├── Button.test.tsx
│   │   │   └── index.ts
│   │   └── Card/
│   └── domain/                 # Domain-specific components
│       ├── ProductCard/
│       └── PortfolioCard/
└── navigation/
    ├── AppNavigator.tsx
    ├── AuthNavigator.tsx
    └── MainNavigator.tsx
```

### 2.2 Variables and Functions

#### 2.2.1 Variables

```typescript
// ✅ GOOD: camelCase for variables
const portfolioValue = 100000;
const isActiveUser = true;
const productCategories = ['stocks', 'bonds', 'funds'];

// ✅ GOOD: SCREAMING_SNAKE_CASE for true constants
const API_BASE_URL = 'https://api.example.com';
const MAX_RETRY_ATTEMPTS = 3;
const DEFAULT_TIMEOUT_MS = 30000;

// ✅ GOOD: Boolean variables with is/has/can prefixes
const isLoading = true;
const hasError = false;
const canSubmit = true;
const isAuthenticated = false;

// ✅ GOOD: Descriptive names
const filteredActiveProducts = products.filter(p => p.isActive);
const formattedCurrency = formatCurrency(value, 'RUB');

// ❌ BAD: Short or cryptic names
const p = products.filter(p => p.isActive);
const fc = formatCurrency(v, 'RUB');
const data = response.data; // Too generic
```

#### 2.2.2 Functions

```typescript
// ✅ GOOD: camelCase, verb prefix for actions
function calculatePortfolioValue(positions: Position[]): number { }
function formatCurrency(amount: number, currency: string): string { }
function validateEmail(email: string): boolean { }
function handleProductPress(product: Product): void { }

// ✅ GOOD: Async functions with async prefix not required
async function fetchPortfolio(id: string): Promise<Portfolio> { }

// ✅ GOOD: Event handlers with handle prefix
const handlePress = () => { };
const handleSubmit = () => { };
const handleInputChange = (value: string) => { };

// ✅ GOOD: Boolean-returning functions with is/has/can prefix
function isValidEmail(email: string): boolean { }
function hasActivePositions(portfolio: Portfolio): boolean { }
function canUserAccessFeature(user: User): boolean { }

// ✅ GOOD: Factory functions with create prefix
function createApiClient(config: ApiConfig): ApiClient { }
function createMockProduct(overrides?: Partial<Product>): Product { }

// ✅ GOOD: Private methods with _ prefix (optional)
class PortfolioService {
  public calculateTotal(): number { }
  private _applyTax(value: number): number { }
}
```

### 2.3 Components

```typescript
// ✅ GOOD: PascalCase component names
export const ProductCard: React.FC<ProductCardProps> = () => { };
export const PortfolioList: React.FC<PortfolioListProps> = () => { };
export const ChatMessage: React.FC<ChatMessageProps> = () => { };

// ✅ GOOD: Higher-order components with with prefix
export const withAuthentication = <P extends object>(
  Component: React.ComponentType<P>
) => { };

// ✅ GOOD: Component prop interface with Props suffix
interface ProductCardProps {
  product: Product;
  onPress: (product: Product) => void;
}

// ✅ GOOD: Component ref interface with Ref suffix
interface ProductCardRef {
  animate: () => void;
}

// ✅ GOOD: Screen components with Screen suffix
export const PortfolioScreen: React.FC = () => { };
export const ChatScreen: React.FC = () => { };

// ✅ GOOD: Domain-specific prefix for domain components
export const ProductDetailCard = () => { };
export const PortfolioSummaryCard = () => { };

// ❌ BAD: Generic names
export const Card = () => { };
export const List = () => { };
export const Item = () => { };
```

### 2.4 API Endpoints

```typescript
// ✅ GOOD: RESTful naming
GET    /api/v1/products              # List products
GET    /api/v1/products/:id          # Get product
POST   /api/v1/products/:id/interest # Express interest
GET    /api/v1/portfolio             # Get portfolio
GET    /api/v1/portfolio/positions   # Get positions
GET    /api/v1/portfolio/transactions # Get transactions

// ✅ GOOD: Resource naming (plural nouns)
/api/v1/users
/api/v1/products
/api/v1/portfolios

// ✅ GOOD: Nested resources (max 2 levels)
/api/v1/portfolios/:id/positions
/api/v1/portfolios/:id/transactions

// ✅ GOOD: Actions as verbs on resources
POST /api/v1/auth/login
POST /api/v1/auth/logout
POST /api/v1/auth/refresh
POST /api/v1/auth/forgot-password

// ❌ BAD: Verbs in URLs
/api/v1/getProducts
/api/v1/createProduct
/api/v1/deleteProduct

// ❌ BAD: Nested resources too deep
/api/v1/portfolios/:id/positions/:id/transactions/:id
```

### 2.5 Database and State

```typescript
// ✅ GOOD: Entity table names (snake_case, plural)
users
products
portfolios
portfolio_positions
chat_messages

// ✅ GOOD: Column names (snake_case)
created_at
updated_at
first_name
portfolio_id

// ✅ GOOD: Zustand stores with Store suffix
export const useAuthStore = create<AuthState>()((set, get) => ({ }));
export const usePortfolioStore = create<PortfolioState>()((set, get) => ({ }));

// ✅ GOOD: React Query keys (array format)
['auth', 'session']
['products', 'list', { category: 'stocks' }]
['portfolio', 'positions', portfolioId]
```

---

## 3. Folder Structure

### 3.1 Complete Project Structure

```
asset-management-app/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml
│   │   └── deploy.yml
│   ├── PULL_REQUEST_TEMPLATE.md
│   └── ISSUE_TEMPLATE.md
├── .husky/
│   ├── pre-commit
│   └── pre-push
├── __mocks__/
│   ├── fileMock.js
│   └── styleMock.js
├── __tests__/
│   ├── setup.ts
│   └── e2e/
├── android/
│   ├── app/
│   ├── build.gradle
│   └── gradle.properties
├── ios/
│   ├── AssetManagementApp/
│   ├── Podfile
│   └── Podfile.lock
├── docs/
│   ├── prd.md
│   ├── system-design.md
│   ├── standards.md
│   └── adr/
│       ├── ADR-001-technology-stack.md
│       └── ADR-002-architecture-pattern.md
├── src/
│   ├── domain/
│   │   ├── entities/
│   │   │   ├── Product.ts
│   │   │   ├── Portfolio.ts
│   │   │   ├── Position.ts
│   │   │   ├── Transaction.ts
│   │   │   ├── User.ts
│   │   │   ├── Manager.ts
│   │   │   └── ChatMessage.ts
│   │   ├── value-objects/
│   │   │   ├── Money.ts
│   │   │   ├── Percentage.ts
│   │   │   ├── DateRange.ts
│   │   │   └── PhoneNumber.ts
│   │   ├── repositories/
│   │   │   ├── IAuthRepository.ts
│   │   │   ├── IProductRepository.ts
│   │   │   ├── IPortfolioRepository.ts
│   │   │   └── IChatRepository.ts
│   │   ├── usecases/
│   │   │   ├── AuthenticateUser.ts
│   │   │   ├── GetProductDetails.ts
│   │   │   ├── GetPortfolio.ts
│   │   │   ├── CalculatePortfolioValue.ts
│   │   │   └── SendMessage.ts
│   │   └── services/
│   │       ├── PortfolioCalculator.ts
│   │       └── InvestmentValidator.ts
│   ├── adapters/
│   │   ├── api/
│   │   │   ├── ApiClient.ts
│   │   │   ├── AuthApi.ts
│   │   │   ├── ProductApi.ts
│   │   │   ├── PortfolioApi.ts
│   │   │   └── ChatApi.ts
│   │   ├── repositories/
│   │   │   ├── AuthRepository.ts
│   │   │   ├── ProductRepository.ts
│   │   │   ├── PortfolioRepository.ts
│   │   │   └── ChatRepository.ts
│   │   ├── storage/
│   │   │   ├── SecureStorage.ts
│   │   │   ├── AsyncStorage.ts
│   │   │   └── CacheStorage.ts
│   │   ├── websocket/
│   │   │   ├── ChatWebSocket.ts
│   │   │   └── WebSocketManager.ts
│   │   └── platform/
│   │       ├── BiometricsService.ts
│   │       ├── NotificationService.ts
│   │       └── LinkingService.ts
│   ├── view/
│   │   ├── screens/
│   │   │   ├── auth/
│   │   │   │   ├── LoginScreen.tsx
│   │   │   │   ├── ForgotPasswordScreen.tsx
│   │   │   │   └── index.ts
│   │   │   ├── portfolio/
│   │   │   │   ├── PortfolioScreen.tsx
│   │   │   │   ├── components/
│   │   │   │   │   ├── PortfolioSummary.tsx
│   │   │   │   │   ├── PositionList.tsx
│   │   │   │   │   └── TransactionList.tsx
│   │   │   │   └── index.ts
│   │   │   ├── products/
│   │   │   │   ├── ProductsScreen.tsx
│   │   │   │   ├── ProductDetailScreen.tsx
│   │   │   │   ├── components/
│   │   │   │   │   ├── ProductCard.tsx
│   │   │   │   │   └── ProductFilter.tsx
│   │   │   │   └── index.ts
│   │   │   ├── chat/
│   │   │   │   ├── ChatScreen.tsx
│   │   │   │   ├── components/
│   │   │   │   │   ├── MessageBubble.tsx
│   │   │   │   │   ├── ChatInput.tsx
│   │   │   │   │   └── ManagerStatus.tsx
│   │   │   │   └── index.ts
│   │   │   └── profile/
│   │   │       ├── ProfileScreen.tsx
│   │   │       └── index.ts
│   │   ├── components/
│   │   │   ├── common/
│   │   │   │   ├── Button/
│   │   │   │   ├── Card/
│   │   │   │   ├── Input/
│   │   │   │   ├── Text/
│   │   │   │   ├── Loading/
│   │   │   │   ├── ErrorBoundary/
│   │   │   │   └── index.ts
│   │   │   ├── forms/
│   │   │   │   ├── FormField/
│   │   │   │   ├── FormSelect/
│   │   │   │   └── index.ts
│   │   │   └── layout/
│   │   │       ├── Screen/
│   │   │       ├── Header/
│   │   │       ├── TabBar/
│   │   │       └── index.ts
│   │   ├── navigation/
│   │   │   ├── AppNavigator.tsx
│   │   │   ├── AuthNavigator.tsx
│   │   │   ├── MainNavigator.tsx
│   │   │   ├── types.ts
│   │   │   └── index.ts
│   │   └── hooks/
│   │       ├── useAuth.ts
│   │       ├── usePortfolio.ts
│   │       ├── useProducts.ts
│   │       └── useChat.ts
│   ├── state/
│   │   ├── stores/
│   │   │   ├── authStore.ts
│   │   │   ├── userStore.ts
│   │   │   └── uiStore.ts
│   │   ├── queries/
│   │   │   ├── queryClient.ts
│   │   │   ├── queryKeys.ts
│   │   │   └── queryHooks/
│   │   │       ├── usePortfolioQuery.ts
│   │   │       └── useProductsQuery.ts
│   │   └── mutations/
│   │       ├── useAuthMutations.ts
│   │       └── useChatMutations.ts
│   ├── i18n/
│   │   ├── index.ts
│   │   └── locales/
│   │       └── ru/
│   │           ├── common.json
│   │           ├── auth.json
│   │           ├── portfolio.json
│   │           ├── products.json
│   │           └── chat.json
│   ├── di/
│   │   ├── container.ts
│   │   └── providers/
│   │       ├── RepositoryProvider.ts
│   │       └── UseCaseProvider.ts
│   ├── shared/
│   │   ├── types/
│   │   │   ├── api.types.ts
│   │   │   ├── navigation.types.ts
│   │   │   └── common.types.ts
│   │   ├── utils/
│   │   │   ├── formatters.ts
│   │   │   ├── validators.ts
│   │   │   ├── helpers.ts
│   │   │   └── constants.ts
│   │   ├── constants/
│   │   │   ├── routes.ts
│   │   │   ├── api-config.ts
│   │   │   └── theme.ts
│   │   └── errors/
│   │       ├── AppError.ts
│   │       ├── ApiError.ts
│   │       └── ValidationError.ts
│   ├── App.tsx
│   └── env.d.ts
├── .env.example
├── .eslintrc.js
├── .prettierrc.js
├── .gitignore
├── babel.config.js
├── jest.config.js
├── metro.config.js
├── package.json
├── tsconfig.json
└── README.md
```

### 3.2 Module Organization

#### 3.2.1 Domain Module Example

```
domain/
├── entities/
│   ├── Product.ts
│   ├── Product.test.ts
│   └── index.ts              # Export all entities
├── value-objects/
│   ├── Money.ts
│   └── index.ts
├── repositories/
│   ├── IProductRepository.ts
│   └── index.ts
├── usecases/
│   ├── GetProductDetails.ts
│   ├── GetProductDetails.test.ts
│   └── index.ts
└── index.ts                  # Export all domain exports
```

#### 3.2.2 Barrel Exports (index.ts)

```typescript
// domain/entities/index.ts
export { Product } from './Product';
export { Portfolio } from './Portfolio';
export { Position } from './Position';
export type { ProductData, PortfolioData, PositionData } from './types';

// domain/index.ts
export * from './entities';
export * from './value-objects';
export * from './repositories';
export * from './usecases';
export * from './services';
```

---

## 4. Git Workflow

### 4.1 Branch Naming

```
<type>/<ticket-number>-<short-description>

Examples:
feature/AMC-42-product-showcase-screen
bugfix/AMC-18-fix-login-validation
hotfix/AMC-33-crash-on-chat-open
refactor/AMC-55-improve-state-management
docs/AMC-20-update-readme
chore/AMC-10-setup-ci-pipeline
```

#### Branch Types

| Type | Description | Example |
|------|-------------|---------|
| `feature` | New feature development | `feature/AMC-42-product-list` |
| `bugfix` | Bug fixes during development | `bugfix/AMC-18-login-crash` |
| `hotfix` | Critical production fixes | `hotfix/AMC-33-crash-fix` |
| `refactor` | Code refactoring | `refactor/AMC-55-state-cleanup` |
| `docs` | Documentation updates | `docs/AMC-20-readme` |
| `chore` | Maintenance tasks | `chore/AMC-10-ci-setup` |
| `test` | Adding/updating tests | `test/AMC-25-portfolio-tests` |

### 4.2 Commit Messages

```
<type>(<scope>): <subject>

[optional body]

[optional footer]

Examples:
feat(auth): add biometric authentication support

- Implement Face ID/Touch ID integration
- Add fallback to password authentication
- Store biometric preference in secure storage

Closes #AMC-42

---

fix(chat): resolve WebSocket reconnection issue

The WebSocket was not reconnecting after network interruption.
Added exponential backoff retry strategy.

Fixes #AMC-18

---

refactor(portfolio): extract calculation logic to domain service

Move portfolio calculations from screen to domain service
for better testability and reusability.
```

#### Commit Types

| Type | Description | Example Subject |
|------|-------------|-----------------|
| `feat` | New feature | `feat(products): add product filtering` |
| `fix` | Bug fix | `fix(auth): handle token expiry` |
| `refactor` | Code refactoring | `refactor(chat): extract message parsing` |
| `style` | Code style (formatting) | `style: format with prettier` |
| `test` | Adding/updating tests | `test(portfolio): add unit tests` |
| `docs` | Documentation | `docs: update api documentation` |
| `chore` | Maintenance | `chore: update dependencies` |
| `perf` | Performance improvement | `perf(list): implement virtualization` |
| `ci` | CI/CD changes | `ci: add test coverage report` |

#### Commit Rules

1. **Subject line**: Max 72 characters, imperative mood
2. **Body**: Explain what and why, not how (wrap at 72 chars)
3. **Footer**: Reference issues, breaking changes

```
✅ GOOD:
feat(products): add investment product catalog

Implement product listing with category filtering and
performance metrics display.

Closes #42

---

❌ BAD:
Add products

fixed stuff
```

### 4.3 Pull Request Guidelines

#### 4.3.1 PR Template

```markdown
## Description
<!-- Brief description of changes -->

## Type of Change
- [ ] Feature (non-breaking change which adds functionality)
- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Refactoring (no functional changes)
- [ ] Documentation update

## Related Issues
Closes #

## Testing
- [ ] Unit tests added/updated
- [ ] Component tests added/updated
- [ ] E2E tests added/updated
- [ ] Manual testing completed

## Screenshots (if applicable)
<!-- Add screenshots for UI changes -->

## Checklist
- [ ] Code follows project style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex logic
- [ ] Documentation updated
- [ ] No new warnings introduced
- [ ] Tests pass locally
- [ ] Test coverage maintained/improved

## Additional Notes
<!-- Any additional information for reviewers -->
```

#### 4.3.2 PR Title Format

```
[AMC-XX] Brief description of change

Examples:
[AMC-42] Implement product showcase screen
[AMC-18] Fix login validation bug
[AMC-55] Refactor state management
```

#### 4.3.3 PR Size Guidelines

| Size | Lines Changed | Review Time |
|------|---------------|-------------|
| XS | < 50 | < 15 min |
| S | 50 - 150 | 15 - 30 min |
| M | 150 - 300 | 30 - 60 min |
| L | 300 - 500 | 1 - 2 hours |
| XL | > 500 | Split into smaller PRs |

### 4.4 Git Workflow Process

```
main (protected)
  │
  ├── develop (protected)
  │     │
  │     ├── feature/AMC-42-product-list
  │     │     └── PR → develop
  │     │
  │     ├── feature/AMC-43-product-detail
  │     │     └── PR → develop
  │     │
  │     └── bugfix/AMC-18-login-fix
  │           └── PR → develop
  │
  └── hotfix/AMC-33-critical-fix
        └── PR → main AND develop
```

#### Workflow Steps

1. **Create branch** from `develop`
2. **Make changes** with meaningful commits
3. **Push branch** to remote
4. **Create PR** to `develop`
5. **Code review** (at least 1 approval)
6. **Squash and merge** to `develop`
7. **Delete branch**

---

## 5. Testing Standards

### 5.1 Test File Organization

```
src/
├── domain/
│   ├── entities/
│   │   ├── Product.ts
│   │   └── Product.test.ts        # Co-located tests
│   └── usecases/
│       ├── GetProductDetails.ts
│       └── GetProductDetails.test.ts
│
├── view/
│   └── screens/
│       └── portfolio/
│           ├── PortfolioScreen.tsx
│           └── PortfolioScreen.test.tsx
│
__tests__/
├── setup.ts                        # Test setup
├── mocks/                          # Global mocks
│   ├── api.mock.ts
│   └── storage.mock.ts
└── e2e/                            # E2E tests
    ├── auth.e2e.test.ts
    ├── portfolio.e2e.test.ts
    └── chat.e2e.test.ts
```

### 5.2 Test Naming Convention

```typescript
// ✅ GOOD: Describe-It pattern
describe('Product', () => {
  describe('calculateReturn', () => {
    it('should return positive percentage for profitable product', () => {
      // ...
    });

    it('should return negative percentage for losing product', () => {
      // ...
    });

    it('should throw error for invalid investment amount', () => {
      // ...
    });
  });
});

// ✅ GOOD: Component test naming
describe('ProductCard', () => {
  describe('rendering', () => {
    it('should render product name', () => { });
    it('should render product description', () => { });
    it('should not render interest button when onInterestPress is undefined', () => { });
  });

  describe('interactions', () => {
    it('should call onPress when card is pressed', () => { });
    it('should call onInterestPress when interest button is pressed', () => { });
  });

  describe('accessibility', () => {
    it('should have correct accessibility label', () => { });
    it('should be accessible by screen reader', () => { });
  });
});
```

### 5.3 Test Structure (AAA Pattern)

```typescript
describe('usePortfolio', () => {
  it('should return portfolio data on successful fetch', async () => {
    // Arrange
    const mockPortfolio = createMockPortfolio();
    mockPortfolioRepository.getPortfolio.mockResolvedValue(mockPortfolio);

    // Act
    const { result } = renderHook(() => usePortfolio('portfolio-1'));
    await waitFor(() => expect(result.current.isLoading).toBe(false));

    // Assert
    expect(result.current.portfolio).toEqual(mockPortfolio);
    expect(result.current.error).toBeNull();
  });

  it('should return error on failed fetch', async () => {
    // Arrange
    const mockError = new Error('Network error');
    mockPortfolioRepository.getPortfolio.mockRejectedValue(mockError);

    // Act
    const { result } = renderHook(() => usePortfolio('portfolio-1'));
    await waitFor(() => expect(result.current.isLoading).toBe(false));

    // Assert
    expect(result.current.portfolio).toBeNull();
    expect(result.current.error).toEqual(mockError);
  });
});
```

### 5.4 Test Coverage Requirements

| Layer | Minimum Coverage | Target Coverage |
|-------|------------------|-----------------|
| Domain Entities | 90% | 100% |
| Domain Use Cases | 90% | 100% |
| Domain Services | 80% | 90% |
| Adapters/Repositories | 70% | 80% |
| View/Components | 60% | 75% |
| View/Screens | 50% | 65% |
| **Overall MVP** | **60%** | **70%** |

#### Coverage Configuration

```javascript
// jest.config.js
module.exports = {
  collectCoverage: true,
  collectCoverageFrom: [
    'src/**/*.{ts,tsx}',
    '!src/**/*.d.ts',
    '!src/**/*.test.{ts,tsx}',
    '!src/**/__tests__/**',
    '!src/**/__mocks__/**',
  ],
  coverageThreshold: {
    global: {
      branches: 60,
      functions: 60,
      lines: 60,
      statements: 60,
    },
    './src/domain/': {
      branches: 90,
      functions: 90,
      lines: 90,
      statements: 90,
    },
  },
  coverageReporters: ['text', 'lcov', 'html'],
};
```

### 5.5 Unit Test Examples

#### 5.5.1 Domain Entity Test

```typescript
// domain/entities/Product.test.ts
import { Product } from './Product';

describe('Product', () => {
  const validProductData = {
    id: 'product-1',
    name: 'Test Product',
    description: 'A test product',
    minInvestment: 10000,
    expectedReturn: 15,
    riskLevel: 'moderate' as const,
    category: 'stocks' as const,
  };

  describe('constructor', () => {
    it('should create product with valid data', () => {
      const product = new Product(validProductData);
      
      expect(product.id).toBe('product-1');
      expect(product.name).toBe('Test Product');
      expect(product.minInvestment).toBe(10000);
    });

    it('should throw error for negative min investment', () => {
      expect(() => {
        new Product({ ...validProductData, minInvestment: -100 });
      }).toThrow('Minimum investment must be positive');
    });
  });

  describe('isEligibleForAmount', () => {
    it('should return true for amount >= minInvestment', () => {
      const product = new Product(validProductData);
      
      expect(product.isEligibleForAmount(10000)).toBe(true);
      expect(product.isEligibleForAmount(15000)).toBe(true);
    });

    it('should return false for amount < minInvestment', () => {
      const product = new Product(validProductData);
      
      expect(product.isEligibleForAmount(5000)).toBe(false);
    });
  });

  describe('calculateProjectedReturn', () => {
    it('should calculate projected return correctly', () => {
      const product = new Product(validProductData);
      const projected = product.calculateProjectedReturn(100000);
      
      expect(projected).toBe(115000); // 100000 * 1.15
    });
  });
});
```

#### 5.5.2 Component Test

```typescript
// view/components/common/Button/Button.test.tsx
import { render, fireEvent } from '@testing-library/react-native';
import { Button } from './Button';

describe('Button', () => {
  const defaultProps = {
    title: 'Click me',
    onPress: jest.fn(),
  };

  beforeEach(() => {
    jest.clearAllMocks();
  });

  describe('rendering', () => {
    it('should render title correctly', () => {
      const { getByText } = render(<Button {...defaultProps} />);
      
      expect(getByText('Click me')).toBeTruthy();
    });

    it('should render loading indicator when loading', () => {
      const { getByTestId, queryByText } = render(
        <Button {...defaultProps} loading />
      );
      
      expect(getByTestId('button-loading-indicator')).toBeTruthy();
      expect(queryByText('Click me')).toBeNull();
    });

    it('should apply disabled styles when disabled', () => {
      const { getByTestId } = render(
        <Button {...defaultProps} disabled />
      );
      
      const button = getByTestId('button');
      expect(button).toHaveStyle({ opacity: 0.5 });
    });
  });

  describe('interactions', () => {
    it('should call onPress when pressed', () => {
      const { getByText } = render(<Button {...defaultProps} />);
      
      fireEvent.press(getByText('Click me'));
      
      expect(defaultProps.onPress).toHaveBeenCalledTimes(1);
    });

    it('should not call onPress when disabled', () => {
      const { getByText } = render(
        <Button {...defaultProps} disabled />
      );
      
      fireEvent.press(getByText('Click me'));
      
      expect(defaultProps.onPress).not.toHaveBeenCalled();
    });

    it('should not call onPress when loading', () => {
      const { getByTestId } = render(
        <Button {...defaultProps} loading />
      );
      
      fireEvent.press(getByTestId('button'));
      
      expect(defaultProps.onPress).not.toHaveBeenCalled();
    });
  });

  describe('accessibility', () => {
    it('should have correct accessibility role', () => {
      const { getByRole } = render(<Button {...defaultProps} />);
      
      expect(getByRole('button')).toBeTruthy();
    });

    it('should indicate disabled state to screen reader', () => {
      const { getByRole } = render(
        <Button {...defaultProps} disabled />
      );
      
      expect(getByRole('button')).toHaveProp('accessibilityState', {
        disabled: true,
      });
    });
  });
});
```

### 5.6 E2E Test Examples

```typescript
// __tests__/e2e/portfolio.e2e.test.ts
import { device, element, by, expect as detoxExpect } from 'detox';

describe('Portfolio Flow', () => {
  beforeAll(async () => {
    await device.launchApp();
    // Login first
    await element(by.id('email-input')).typeText('test@example.com');
    await element(by.id('password-input')).typeText('password123');
    await element(by.id('login-button')).tap();
    await detoxExpect(element(by.id('portfolio-screen'))).toBeVisible();
  });

  it('should display portfolio overview', async () => {
    await detoxExpect(element(by.id('portfolio-summary'))).toBeVisible();
    await detoxExpect(element(by.id('total-value'))).toBeVisible();
  });

  it('should navigate to position details', async () => {
    await element(by.id('position-item-0')).tap();
    await detoxExpect(element(by.id('position-detail-screen'))).toBeVisible();
  });

  it('should display transactions list', async () => {
    await element(by.id('transactions-tab')).tap();
    await detoxExpect(element(by.id('transactions-list'))).toBeVisible();
  });
});
```

### 5.7 Test Utilities

```typescript
// __tests__/utils/test-utils.tsx
import { ReactElement } from 'react';
import { render, RenderOptions } from '@testing-library/react-native';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

// Create a custom render that includes providers
function createTestQueryClient() {
  return new QueryClient({
    defaultOptions: {
      queries: {
        retry: false,
      },
    },
  });
}

interface CustomRenderOptions extends Omit<RenderOptions, 'wrapper'> {
  queryClient?: QueryClient;
}

export function renderWithProviders(
  ui: ReactElement,
  options: CustomRenderOptions = {}
) {
  const { queryClient = createTestQueryClient(), ...renderOptions } = options;

  const Wrapper: React.FC<{ children: React.ReactNode }> = ({ children }) => (
    <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>
  );

  return {
    ...render(ui, { wrapper: Wrapper, ...renderOptions }),
    queryClient,
  };
}

// Mock factories
export function createMockProduct(overrides?: Partial<Product>): Product {
  return {
    id: 'product-1',
    name: 'Test Product',
    description: 'Test description',
    minInvestment: 10000,
    expectedReturn: 15,
    riskLevel: 'moderate',
    category: 'stocks',
    ...overrides,
  };
}

export function createMockPortfolio(overrides?: Partial<Portfolio>): Portfolio {
  return {
    id: 'portfolio-1',
    totalValue: 100000,
    positions: [],
    lastUpdated: new Date(),
    ...overrides,
  };
}
```

---

## 6. Code Review Guidelines

### 6.1 Review Checklist

#### Code Quality
- [ ] Code compiles without errors or warnings
- [ ] TypeScript strict mode compliance
- [ ] No `any` types without justification
- [ ] Functions are small and focused (max 30 lines)
- [ ] No magic numbers/strings (use constants)
- [ ] No commented-out code
- [ ] No console.log statements (use logger)

#### Architecture
- [ ] Follows Clean Architecture layers
- [ ] Domain layer has no external dependencies
- [ ] Dependencies flow inward
- [ ] Proper separation of concerns
- [ ] No business logic in components

#### Performance
- [ ] List virtualization for long lists
- [ ] Images optimized and cached
- [ ] Expensive calculations memoized
- [ ] Callbacks memoized when passed to children
- [ ] No unnecessary re-renders

#### Security
- [ ] No sensitive data in logs
- [ ] Tokens stored in secure storage
- [ ] API keys not hardcoded
- [ ] Input validation on user data
- [ ] No SQL injection vulnerabilities

#### Testing
- [ ] Unit tests for new logic
- [ ] Component tests for UI
- [ ] Edge cases covered
- [ ] Test coverage not decreased
- [ ] Tests are meaningful (not just for coverage)

#### Accessibility
- [ ] Touch targets min 44x44 points
- [ ] Screen reader labels provided
- [ ] Color contrast meets WCAG 2.1 AA
- [ ] Focus management correct
- [ ] Not relying solely on color

#### Documentation
- [ ] Complex logic documented
- [ ] Public APIs documented
- [ ] README updated if needed
- [ ] Type annotations complete

### 6.2 Review Process

1. **Author creates PR** with template filled
2. **CI checks pass** (build, lint, test)
3. **Reviewer assigned** (at least 1, ideally 2)
4. **Review feedback** provided within 24 hours
5. **Author addresses** feedback
6. **Approval** from all reviewers
7. **Squash and merge** to target branch
8. **Branch deleted**

### 6.3 Review Feedback Guidelines

```markdown
<!-- ✅ GOOD: Constructive feedback -->
**Suggestion**: Consider extracting this logic to a custom hook 
for better testability and reusability.

```typescript
// Proposed solution
function usePortfolioValue(positions: Position[]) {
  return useMemo(() => 
    positions.reduce((sum, p) => sum + p.value, 0),
    [positions]
  );
}
```

---

<!-- ✅ GOOD: Asking questions -->
**Question**: Why did you choose to use `useEffect` here instead 
of `useMemo`? I'm curious about the reasoning.

---

<!-- ❌ BAD: Vague or unhelpful -->
This is wrong.
Fix this.
```

---

## 7. Documentation Standards

### 7.1 Code Documentation

#### 7.1.1 Inline Comments

```typescript
// ✅ GOOD: Explain why, not what
// Use exponential backoff to prevent server overload during
// connection recovery (per WebSocket best practices)
const reconnectDelay = Math.min(1000 * Math.pow(2, retryCount), 30000);

// ✅ GOOD: Document workarounds
// FIXME: Backend returns dates as strings, remove when fixed
// Issue: https://github.com/org/repo/issues/42
const date = new Date(response.data.created_at);

// ✅ GOOD: Document complex algorithms
// Calculate compound annual growth rate (CAGR)
// Formula: (Ending Value / Beginning Value) ^ (1 / n) - 1
function calculateCAGR(endValue: number, startValue: number, years: number): number {
  return Math.pow(endValue / startValue, 1 / years) - 1;
}

// ❌ BAD: Obvious comments
// Loop through positions
for (const position of positions) {
  // Add position value to total
  total += position.value;
}
```

#### 7.1.2 Function/Method Documentation

```typescript
/**
 * Calculates the total value of a portfolio including all positions.
 * 
 * @param portfolio - The portfolio to calculate value for
 * @param includePending - Whether to include pending transactions
 * @returns The total portfolio value in base currency
 * 
 * @example
 * ```typescript
 * const portfolio = { positions: [{ value: 50000 }, { value: 30000 }] };
 * const total = calculateTotalValue(portfolio, false);
 * // Returns: 80000
 * ```
 * 
 * @throws {ValidationError} If portfolio is null or undefined
 */
export function calculateTotalValue(
  portfolio: Portfolio, 
  includePending: boolean = false
): number {
  // Implementation
}
```

#### 7.1.3 Interface Documentation

```typescript
/**
 * Represents an investment product available to clients.
 * 
 * @remarks
 * Products are read-only entities created by administrators.
 * Clients can view products and express interest, but cannot
 * directly purchase through the app (handled by managers).
 * 
 * @see {@link Portfolio} for client holdings
 * @see {@link Position} for individual product holdings
 */
export interface Product {
  /** Unique identifier for the product */
  readonly id: string;
  
  /** Display name of the product */
  readonly name: string;
  
  /** Minimum investment amount in base currency (RUB) */
  readonly minInvestment: number;
  
  /** Expected annual return as percentage (e.g., 15 = 15%) */
  readonly expectedReturn: number;
  
  /** Risk classification for regulatory compliance */
  readonly riskLevel: RiskLevel;
}
```

### 7.2 README Structure

```markdown
# Asset Management Client Portal

## Overview
Brief description of the project.

## Features
- Feature 1
- Feature 2

## Tech Stack
- React Native
- TypeScript
- etc.

## Getting Started

### Prerequisites
- Node.js 18+
- React Native CLI
- Xcode 14+ (iOS)
- Android Studio (Android)

### Installation
\`\`\`bash
npm install
cd ios && pod install
\`\`\`

### Running
\`\`\`bash
# iOS
npm run ios

# Android
npm run android
\`\`\`

## Project Structure
Brief overview of folder structure.

## Architecture
Link to system design document.

## Testing
\`\`\`bash
npm test
npm run test:e2e
\`\`\`

## Contributing
Link to contributing guidelines.

## License
MIT
```

### 7.3 API Documentation

```typescript
/**
 * @openapi
 * /auth/login:
 *   post:
 *     summary: Authenticate user
 *     description: Authenticates user with email and password, returns tokens
 *     tags:
 *       - Authentication
 *     requestBody:
 *       required: true
 *       content:
 *         application/json:
 *           schema:
 *             type: object
 *             required:
 *               - email
 *               - password
 *             properties:
 *               email:
 *                 type: string
 *                 format: email
 *               password:
 *                 type: string
 *                 format: password
 *     responses:
 *       200:
 *         description: Authentication successful
 *         content:
 *           application/json:
 *             schema:
 *               $ref: '#/components/schemas/AuthResponse'
 *       401:
 *         description: Invalid credentials
 *       429:
 *         description: Too many attempts
 */
```

---

## 8. Enforcement

### 8.1 Pre-commit Hooks

```javascript
// .husky/pre-commit
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npm run lint
npm run test:unit -- --passWithNoTests
npm run type-check
```

### 8.2 CI/CD Checks

```yaml
# .github/workflows/ci.yml
name: CI

on: [push, pull_request]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm ci
      - run: npm run lint

  type-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm ci
      - run: npm run type-check

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm ci
      - run: npm run test:coverage
      - uses: codecov/codecov-action@v3
```

### 8.3 IDE Configuration

```json
// .vscode/settings.json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "typescript.tsdk": "node_modules/typescript/lib",
  "typescript.enablePromptUseWorkspaceTsdk": true
}
```

---

## Appendix A: ESLint Configuration

```javascript
// .eslintrc.js
module.exports = {
  root: true,
  extends: [
    '@react-native-community',
    'plugin:@typescript-eslint/recommended',
    'plugin:@typescript-eslint/recommended-requiring-type-checking',
    'plugin:react-hooks/recommended',
    'plugin:react-native/all',
    'prettier',
  ],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    project: './tsconfig.json',
    ecmaFeatures: {
      jsx: true,
    },
  },
  plugins: ['@typescript-eslint', 'react-hooks', 'react-native'],
  rules: {
    '@typescript-eslint/explicit-function-return-type': 'warn',
    '@typescript-eslint/explicit-module-boundary-types': 'warn',
    '@typescript-eslint/no-explicit-any': 'error',
    '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
    '@typescript-eslint/no-floating-promises': 'error',
    '@typescript-eslint/await-thenable': 'error',
    '@typescript-eslint/no-misused-promises': 'error',
    'react-hooks/rules-of-hooks': 'error',
    'react-hooks/exhaustive-deps': 'warn',
    'react-native/no-unused-styles': 'error',
    'react-native/split-platform-components': 'error',
    'react-native/no-inline-styles': 'warn',
    'react-native/no-color-literals': 'warn',
    'import/order': ['error', {
      'groups': ['builtin', 'external', 'internal', 'parent', 'sibling', 'index'],
      'newlines-between': 'always',
      'alphabetize': { 'order': 'asc' },
    }],
    'no-console': ['warn', { allow: ['warn', 'error'] }],
  },
};
```

## Appendix B: Prettier Configuration

```javascript
// .prettierrc.js
module.exports = {
  arrowParens: 'always',
  bracketSameLine: false,
  bracketSpacing: true,
  singleQuote: true,
  trailingComma: 'es5',
  tabWidth: 2,
  semi: true,
  printWidth: 100,
  useTabs: false,
};
```

---

**Document Status**: Approved  
**Last Updated**: 2025-01-18  
**Next Review**: 2025-02-18  
**Owner**: Development Team