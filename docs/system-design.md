# Technical Standards and Conventions

## Document Information
- **Project:** Asset Management Client App
- **Version:** 1.0
- **Date:** 2026-03-31
- **Author:** Software Architect Agent
- **Status:** Final

---

## Table of Contents
1. [Coding Standards](#1-coding-standards)
2. [Naming Conventions](#2-naming-conventions)
3. [Folder Structure](#3-folder-structure)
4. [Git Workflow](#4-git-workflow)
5. [Testing Standards](#5-testing-standards)
6. [Documentation Standards](#6-documentation-standards)
7. [Code Review Guidelines](#7-code-review-guidelines)

---

## 1. Coding Standards

### 1.1 TypeScript/JavaScript Conventions

#### 1.1.1 TypeScript Configuration

**tsconfig.json:**
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["ES2020"],
    "module": "ESNext",
    "moduleResolution": "bundler",
    "jsx": "react-native",
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    "alwaysStrict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "noUncheckedIndexedAccess": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"],
      "@app/*": ["src/app/*"],
      "@features/*": ["src/features/*"],
      "@shared/*": ["src/shared/*"],
      "@entities/*": ["src/entities/*"],
      "@services/*": ["src/services/*"],
      "@assets/*": ["src/assets/*"],
      "@config/*": ["src/config/*"]
    }
  },
  "include": ["src/**/*", "index.js", "App.tsx"],
  "exclude": ["node_modules", "babel.config.js", "metro.config.js"]
}
```

#### 1.1.2 Type Definitions

**Prefer Interfaces for Objects:**
```typescript
// ✅ Good - Use interface for object shapes
interface User {
  id: string;
  email: string;
  firstName: string;
  lastName: string;
}

// ✅ Good - Use type for unions, primitives, or complex types
type Status = 'pending' | 'active' | 'inactive';
type UserRole = 'user' | 'manager' | 'admin';
type UserWithRole = User & { role: UserRole };

// ❌ Bad - Don't use type for simple object shapes
type User = {
  id: string;
  email: string;
};
```

**Use Strict Null Checks:**
```typescript
// ✅ Good - Explicit null/undefined handling
function getUser(id: string): User | null {
  const user = users.find(u => u.id === id);
  return user ?? null;
}

// ✅ Good - Use optional chaining
const userName = user?.profile?.displayName;

// ✅ Good - Use nullish coalescing
const displayName = user.name ?? 'Unknown';

// ❌ Bad - Assuming non-null
function getUser(id: string): User {
  return users.find(u => u.id === id); // Error: can be undefined
}
```

**Avoid `any` Type:**
```typescript
// ✅ Good - Use unknown for truly unknown types
function parseJSON(json: string): unknown {
  return JSON.parse(json);
}

// ✅ Good - Use type guards
function isUser(data: unknown): data is User {
  return typeof data === 'object' 
    && data !== null 
    && 'id' in data 
    && 'email' in data;
}

// ❌ Bad - Using any
function parseJSON(json: string): any {  // Loses type safety
  return JSON.parse(json);
}
```

#### 1.1.3 Variable Declarations

```typescript
// ✅ Good - Use const by default
const MAX_RETRIES = 3;
const user = { name: 'John', age: 30 };

// ✅ Good - Use let only when reassignment is needed
let count = 0;
count += 1;

// ❌ Bad - Using var
var name = 'John';  // Function-scoped, can cause bugs

// ✅ Good - Destructuring with defaults
const { timeout = 5000, retries = 3 } = options;

// ✅ Good - Object shorthand
const name = 'John';
const user = { name };  // Instead of { name: name }

// ✅ Good - Spread operator for immutability
const updatedUser = { ...user, age: 31 };
```

#### 1.1.4 Function Standards

```typescript
// ✅ Good - Explicit return types for public functions
export function formatCurrency(amount: number, currency: string): string {
  return new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency,
  }).format(amount);
}

// ✅ Good - Arrow functions for callbacks and inline functions
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(n => n * 2);

// ✅ Good - Named functions for top-level functions
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}

// ✅ Good - Async/await for asynchronous operations
async function fetchUser(id: string): Promise<User> {
  const response = await api.get(`/users/${id}`);
  return response.data;
}

// ✅ Good - Error handling with try-catch
async function safeFetchUser(id: string): Promise<Result<User, ApiError>> {
  try {
    const user = await fetchUser(id);
    return { success: true, data: user };
  } catch (error) {
    return { success: false, error: toApiError(error) };
  }
}

// ✅ Good - Default parameters
function createTimeout(promise: Promise<T>, ms: number = 5000): Promise<T> {
  return Promise.race([promise, timeout(ms)]);
}

// ✅ Good - Rest parameters
function combine<T>(...arrays: T[][]): T[] {
  return arrays.flat();
}
```

#### 1.1.5 Async/Await Best Practices

```typescript
// ✅ Good - Proper async error handling
async function loadData(): Promise<void> {
  try {
    setIsLoading(true);
    const data = await fetchData();
    setData(data);
  } catch (error) {
    handleError(error);
  } finally {
    setIsLoading(false);
  }
}

// ✅ Good - Parallel operations with Promise.all
async function loadDashboard(): Promise<DashboardData> {
  const [user, portfolio, products] = await Promise.all([
    fetchUser(userId),
    fetchPortfolio(userId),
    fetchProducts(),
  ]);
  
  return { user, portfolio, products };
}

// ✅ Good - Sequential operations when needed
async function sequentialUpdate(): Promise<void> {
  await updateUser(user);
  await refreshCache();
  await notifyUser(user.id);
}

// ❌ Bad - Sequential when parallel is better
async function loadDataBad(): Promise<DashboardData> {
  const user = await fetchUser(userId);
  const portfolio = await fetchPortfolio(userId);  // Unnecessary wait
  const products = await fetchProducts();  // Unnecessary wait
  return { user, portfolio, products };
}
```

### 1.2 React Native Best Practices

#### 1.2.1 Component Structure

**Functional Components Only:**
```typescript
// ✅ Good - Functional component with TypeScript
interface ProductCardProps {
  product: Product;
  onPress: (product: Product) => void;
  isFavorite?: boolean;
}

export const ProductCard: React.FC<ProductCardProps> = ({
  product,
  onPress,
  isFavorite = false,
}) => {
  const [isLoading, setIsLoading] = useState(false);
  
  const handlePress = useCallback(() => {
    onPress(product);
  }, [product, onPress]);
  
  return (
    <TouchableOpacity onPress={handlePress} testID="product-card">
      <View style={styles.container}>
        <Text style={styles.name}>{product.name}</Text>
        <Text style={styles.price}>
          {formatCurrency(product.price, 'USD')}
        </Text>
      </View>
    </TouchableOpacity>
  );
};

// ❌ Bad - Class components (unless absolutely necessary)
class ProductCard extends React.Component<ProductCardProps> {
  render() {
    return <View>...</View>;
  }
}
```

**Component Organization:**
```typescript
// ✅ Good - Logical ordering
export const UserProfile: React.FC<UserProfileProps> = (props) => {
  // 1. Hooks at the top
  const navigation = useNavigation();
  const theme = useTheme();
  const [isEditing, setIsEditing] = useState(false);
  
  // 2. Derived state
  const fullName = `${props.user.firstName} ${props.user.lastName}`;
  
  // 3. Callbacks
  const handleEdit = useCallback(() => {
    setIsEditing(true);
  }, []);
  
  const handleSave = useCallback(async (data: UserUpdate) => {
    await updateUser(data);
    setIsEditing(false);
  }, []);
  
  // 4. Effects
  useEffect(() => {
    Analytics.trackScreen('UserProfile');
  }, []);
  
  // 5. Early returns
  if (!props.user) {
    return <EmptyState />;
  }
  
  // 6. Main render
  return (
    <View style={styles.container}>
      {/* Component JSX */}
    </View>
  );
};
```

#### 1.2.2 Hooks Best Practices

```typescript
// ✅ Good - Custom hooks for reusable logic
function useUser(userId: string) {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);
  
  useEffect(() => {
    let isMounted = true;
    
    async function loadUser() {
      try {
        setIsLoading(true);
        const data = await fetchUser(userId);
        if (isMounted) {
          setUser(data);
        }
      } catch (e) {
        if (isMounted) {
          setError(e instanceof Error ? e : new Error('Unknown error'));
        }
      } finally {
        if (isMounted) {
          setIsLoading(false);
        }
      }
    }
    
    loadUser();
    
    return () => {
      isMounted = false;
    };
  }, [userId]);
  
  return { user, isLoading, error };
}

// ✅ Good - Using the custom hook
const UserProfile: React.FC<{ userId: string }> = ({ userId }) => {
  const { user, isLoading, error } = useUser(userId);
  
  if (isLoading) return <LoadingSpinner />;
  if (error) return <ErrorMessage error={error} />;
  if (!user) return null;
  
  return <UserDetails user={user} />;
};
```

**React Query Integration:**
```typescript
// ✅ Good - React Query for server state
export function useProducts(filters?: ProductFilters) {
  return useQuery({
    queryKey: queryKeys.products.list(filters),
    queryFn: () => productsApi.getAll(filters),
    staleTime: 5 * 60 * 1000, // 5 minutes
  });
}

export function useUpdateUser() {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: (data: UserUpdate) => usersApi.update(data),
    onSuccess: (updatedUser) => {
      // Update cache
      queryClient.setQueryData(
        queryKeys.user.profile(),
        updatedUser
      );
      // Invalidate related queries
      queryClient.invalidateQueries({ 
        queryKey: queryKeys.user.all 
      });
    },
    onError: (error) => {
      // Handle error
      showErrorToast(error.message);
    },
  });
}
```

#### 1.2.3 Performance Optimization

**Memoization:**
```typescript
// ✅ Good - useMemo for expensive calculations
const sortedProducts = useMemo(() => {
  return products.sort((a, b) => b.price - a.price);
}, [products]);

// ✅ Good - useCallback for callbacks passed to children
const handleProductPress = useCallback((product: Product) => {
  navigation.navigate('ProductDetail', { productId: product.id });
}, [navigation]);

// ✅ Good - React.memo for expensive components
export const ProductItem = React.memo<ProductItemProps>(
  ({ product, onPress }) => {
    return (
      <TouchableOpacity onPress={() => onPress(product)}>
        <Text>{product.name}</Text>
      </TouchableOpacity>
    );
  },
  (prevProps, nextProps) => {
    // Custom comparison
    return prevProps.product.id === nextProps.product.id
      && prevProps.product.updatedAt === nextProps.product.updatedAt;
  }
);

// ❌ Bad - Over-memoization
const name = useMemo(() => user.name, [user.name]); // Not needed for simple access
```

**List Optimization:**
```typescript
// ✅ Good - FlatList for long lists
<FlatList
  data={products}
  keyExtractor={(item) => item.id}
  renderItem={({ item }) => (
    <ProductCard product={item} onPress={handlePress} />
  )}
  initialNumToRender={10}
  maxToRenderPerBatch={10}
  windowSize={5}
  removeClippedSubviews={true}
  ListEmptyComponent={<EmptyState />}
  ListFooterComponent={isLoading ? <LoadingSpinner /> : null}
  onEndReached={loadMore}
  onEndReachedThreshold={0.5}
/>

// ❌ Bad - ScrollView with map for long lists
<ScrollView>
  {products.map(product => (
    <ProductCard key={product.id} product={product} />
  ))}
</ScrollView>
```

#### 1.2.4 Styling Standards

```typescript
// ✅ Good - Using NativeWind (Tailwind)
import { View, Text } from 'react-native';
import Animated, { FadeIn } from 'react-native-reanimated';

export const ProductCard: React.FC<ProductCardProps> = ({ product }) => {
  return (
    <Animated.View 
      entering={FadeIn}
      className="bg-white rounded-lg p-4 shadow-md"
    >
      <Text className="text-lg font-semibold text-gray-900">
        {product.name}
      </Text>
      <Text className="text-2xl font-bold text-blue-600">
        {formatCurrency(product.price)}
      </Text>
    </Animated.View>
  );
};

// ✅ Good - Using StyleSheet when needed
import { StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#FFFFFF',
    padding: 16,
  },
  title: {
    fontSize: 18,
    fontWeight: '600',
    color: '#1F2937',
  },
});

// ✅ Good - Theme-aware styling
import { useTheme } from '@shared/hooks/useTheme';

export const ThemedComponent: React.FC = () => {
  const { colors, spacing } = useTheme();
  
  return (
    <View style={{ 
      backgroundColor: colors.background,
      padding: spacing.md,
    }}>
      <Text style={{ color: colors.text }}>Themed text</Text>
    </View>
  );
};
```

#### 1.2.5 Navigation Standards

```typescript
// ✅ Good - Type-safe navigation
import { NavigationProp, RouteProp } from '@react-navigation/native';
import { NativeStackScreenProps } from '@react-navigation/native-stack';

type RootStackParamList = {
  Login: undefined;
  ProductDetail: { productId: string };
  Portfolio: undefined;
  Chat: { chatId: string };
};

type Props = NativeStackScreenProps<RootStackParamList, 'ProductDetail'>;

export const ProductDetailScreen: React.FC<Props> = ({ route, navigation }) => {
  const { productId } = route.params;
  
  const handleRelatedPress = (relatedId: string) => {
    navigation.push('ProductDetail', { productId: relatedId });
  };
  
  return (
    <View>
      <ProductDetails id={productId} onRelatedPress={handleRelatedPress} />
    </View>
  );
};

// ✅ Good - Deep linking
const linking = {
  prefixes: ['assetmanagement://', 'https://app.assetmanagement.com'],
  config: {
    screens: {
      ProductDetail: 'products/:productId',
      Chat: 'chat/:chatId',
      Portfolio: 'portfolio',
    },
  },
};
```

### 1.3 Node.js Patterns (Backend Reference)

#### 1.3.1 Project Structure

```
backend/
├── src/
│   ├── controllers/     # Request handlers
│   ├── services/        # Business logic
│   ├── repositories/    # Data access
│   ├── models/          # Database models
│   ├── middlewares/     # Express middlewares
│   ├── routes/          # Route definitions
│   ├── validators/      # Request validation
│   ├── utils/           # Utility functions
│   ├── types/           # TypeScript types
│   └── config/          # Configuration
├── tests/
│   ├── unit/
│   └── integration/
├── prisma/
│   └── schema.prisma
├── package.json
└── tsconfig.json
```

#### 1.3.2 Controller Pattern

```typescript
// ✅ Good - Controller with dependency injection
import { Request, Response, NextFunction } from 'express';
import { ProductService } from '../services/product.service';
import { asyncHandler } from '../middlewares/async-handler';

export class ProductController {
  constructor(private readonly productService: ProductService) {}
  
  getAll = asyncHandler(async (req: Request, res: Response) => {
    const filters = this.parseFilters(req.query);
    const products = await this.productService.findAll(filters);
    
    res.json({
      success: true,
      data: { products },
    });
  });
  
  getById = asyncHandler(async (req: Request, res: Response) => {
    const { id } = req.params;
    const product = await this.productService.findById(id);
    
    if (!product) {
      res.status(404).json({
        success: false,
        error: { code: 'NOT_FOUND', message: 'Product not found' },
      });
      return;
    }
    
    res.json({
      success: true,
      data: { product },
    });
  });
  
  private parseFilters(query: qs.ParsedQs): ProductFilters {
    return {
      page: parseInt(query.page as string) || 1,
      limit: parseInt(query.limit as string) || 20,
      type: query.type as string,
      riskLevel: query.riskLevel as RiskLevel,
    };
  }
}
```

#### 1.3.3 Service Pattern

```typescript
// ✅ Good - Service with clear separation
export class ProductService {
  constructor(
    private readonly productRepository: ProductRepository,
    private readonly cacheService: CacheService,
  ) {}
  
  async findAll(filters: ProductFilters): Promise<PaginatedResult<Product>> {
    const cacheKey = `products:${JSON.stringify(filters)}`;
    
    // Check cache
    const cached = await this.cacheService.get(cacheKey);
    if (cached) {
      return cached;
    }
    
    // Fetch from repository
    const result = await this.productRepository.findAll(filters);
    
    // Cache for 5 minutes
    await this.cacheService.set(cacheKey, result, 300);
    
    return result;
  }
  
  async findById(id: string): Promise<Product | null> {
    return this.productRepository.findById(id);
  }
  
  async create(data: CreateProductDTO): Promise<Product> {
    // Validate business rules
    if (data.minInvestment < 1000) {
      throw new ValidationError('Minimum investment must be at least 1000');
    }
    
    // Create product
    const product = await this.productRepository.create(data);
    
    // Invalidate cache
    await this.cacheService.invalidate('products:*');
    
    return product;
  }
}
```

---

## 2. Naming Conventions

### 2.1 Files and Folders

#### 2.1.1 General Rules

| Type | Convention | Example |
|------|------------|---------|
| **Components** | PascalCase.tsx | `ProductCard.tsx`, `UserProfile.tsx` |
| **Screens** | PascalCase + Screen suffix | `LoginScreen.tsx`, `ProductDetailScreen.tsx` |
| **Hooks** | camelCase + use prefix | `useAuth.ts`, `useProducts.ts` |
| **Utilities** | camelCase.ts | `formatters.ts`, `validators.ts` |
| **Constants** | camelCase.ts or UPPER_SNAKE | `constants.ts`, `API_CONFIG.ts` |
| **Types** | camelCase + Types suffix | `userTypes.ts`, `productTypes.ts` |
| **API** | camelCase + Api suffix | `productsApi.ts`, `authApi.ts` |
| **Store** | camelCase + Store suffix | `authStore.ts`, `uiStore.ts` |
| **Tests** | Same as source + .test | `ProductCard.test.tsx` |
| **Config** | camelCase + .config | `api.config.ts`, `app.config.ts` |

#### 2.1.2 Directory Naming

```
src/
├── features/
│   ├── auth/                    # Feature name in kebab-case
│   │   ├── screens/
│   │   │   ├── LoginScreen.tsx
│   │   │   └── ForgotPasswordScreen.tsx
│   │   ├── components/
│   │   │   ├── LoginForm.tsx
│   │   │   └── BiometricButton.tsx
│   │   ├── hooks/
│   │   │   ├── useAuth.ts
│   │   │   └── useBiometric.ts
│   │   ├── api/
│   │   │   ├── authApi.ts
│   │   │   └── authKeys.ts
│   │   └── types/
│   │       └── authTypes.ts
│   │
│   └── product-comparison/       # Multi-word feature: kebab-case
│       └── ...
│
└── shared/
    └── ui/
        ├── Button/              # Component: PascalCase directory
        │   ├── Button.tsx       # Index file
        │   ├── Button.styles.ts # Styles (if separate)
        │   ├── Button.types.ts  # Types
        │   ├── Button.test.tsx  # Tests
        │   └── index.ts         # Public API
        └── Input/
            ├── Input.tsx
            └── ...
```

#### 2.1.3 Index Files

```typescript
// ✅ Good - Feature module index
// features/auth/index.ts
export { LoginScreen } from './screens/LoginScreen';
export { ForgotPasswordScreen } from './screens/ForgotPasswordScreen';
export { useAuth } from './hooks/useAuth';
export { useBiometric } from './hooks/useBiometric';
export type { AuthState, AuthActions } from './types/authTypes';

// ✅ Good - UI component index
// shared/ui/Button/index.ts
export { Button } from './Button';
export type { ButtonProps, ButtonVariant } from './Button.types';
```

### 2.2 Variables and Functions

#### 2.2.1 Variable Naming

```typescript
// ✅ Good - camelCase for variables
const userName = 'John';
const isLoading = true;
const hasError = false;

// ✅ Good - UPPER_SNAKE_CASE for constants
const MAX_RETRIES = 3;
const API_BASE_URL = 'https://api.example.com';
const DEFAULT_TIMEOUT = 5000;

// ✅ Good - Boolean variables with is/has/can/should prefix
const isVisible = true;
const hasPermission = false;
const canEdit = true;
const shouldRender = false;

// ✅ Good - Collections with plural names
const users: User[] = [];
const productList: Product[] = [];

// ✅ Good - Maps and dictionaries with Map/Dict suffix
const userMap: Map<string, User> = new Map();
const errorMessages: Record<string, string> = {};

// ❌ Bad - Inconsistent naming
const UserName = 'John';  // Should be camelCase
const is_loading = true;  // Should be camelCase
const visible = true;     // Should have is prefix
```

#### 2.2.2 Function Naming

```typescript
// ✅ Good - Verb prefix for functions
function fetchUser(id: string): Promise<User> { }
function calculateTotal(items: Item[]): number { }
function validateEmail(email: string): boolean { }
function formatCurrency(amount: number): string { }
function handleButtonClick(): void { }
function renderProduct(product: Product): JSX.Element { }

// ✅ Good - Event handlers with handle prefix
const handleSubmit = () => { };
const handleInputChange = (value: string) => { };
const handleProductPress = (product: Product) => { };

// ✅ Good - Async functions don't need async prefix
async function loadUser(): Promise<User> { }  // Good
async function fetchUserData(): Promise<User> { }  // Good

// ❌ Bad - Unclear naming
function user() { }  // What does it do?
function process() { }  // Too vague
function data() { }  // Unclear
```

#### 2.2.3 React Hook Naming

```typescript
// ✅ Good - Custom hooks with use prefix
function useAuth() { }
function useProducts(filters?: ProductFilters) { }
function useDebounce<T>(value: T, delay: number) { }
function useUserProfile(userId: string) { }

// ✅ Good - Hook returning array (like useState)
function useToggle(initial: boolean): [boolean, () => void] {
  const [value, setValue] = useState(initial);
  const toggle = useCallback(() => setValue(v => !v), []);
  return [value, toggle];
}

// ✅ Good - Hook returning object
function useUser(userId: string) {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<Error | null>(null);
  
  return { user, isLoading, error };
}
```

### 2.3 Components

#### 2.3.1 Component Naming

```typescript
// ✅ Good - PascalCase for component names
export const ProductCard: React.FC<ProductCardProps> = () => { };
export const UserProfile: React.FC<UserProfileProps> = () => { };
export const NavigationHeader: React.FC<NavigationHeaderProps> = () => { };

// ✅ Good - Screen components with Screen suffix
export const LoginScreen: React.FC = () => { };
export const ProductDetailScreen: React.FC = () => { };

// ✅ Good - Higher-order components with with prefix
export const withAuth = <P extends object>(
  Component: React.ComponentType<P>
) => { };

// ✅ Good - Context providers with Provider suffix
export const AuthProvider: React.FC<{ children: React.ReactNode }> = () => { };
export const ThemeProvider: React.FC<{ children: React.ReactNode }> = () => { };

// ❌ Bad - Inconsistent naming
export const productCard = () => { };  // Should be PascalCase
export const Product = () => { };      // Too generic
```

#### 2.3.2 Props Naming

```typescript
// ✅ Good - Props interface with Props suffix
interface ProductCardProps {
  product: Product;
  onPress: (product: Product) => void;
  isFavorite?: boolean;
  testID?: string;
}

// ✅ Good - Callback props with on prefix
interface ProductListProps {
  onProductPress: (product: Product) => void;
  onFavoriteToggle: (productId: string) => void;
  onLoadMore?: () => void;
}

// ✅ Good - Render props with render prefix
interface DataTableProps<T> {
  data: T[];
  renderRow: (item: T) => React.ReactNode;
  renderEmpty?: () => React.ReactNode;
}

// ✅ Good - Boolean props with is/has prefix
interface ButtonProps {
  isLoading?: boolean;
  isDisabled?: boolean;
  hasIcon?: boolean;
}
```

### 2.4 API Endpoints

#### 2.4.1 RESTful Naming

```
✅ Good - Resource-based URLs

GET    /products                    # List products
GET    /products/:id                # Get single product
POST   /products                    # Create product
PUT    /products/:id                # Update product
DELETE /products/:id                # Delete product

GET    /products/:id/reviews        # Nested resource
POST   /products/:id/reviews        # Create nested resource

GET    /users/:userId/portfolio     # Nested resource
GET    /users/:userId/documents     # Nested resource

✅ Good - Actions as verbs (when not CRUD)
POST   /auth/login                  # Login action
POST   /auth/logout                 # Logout action
POST   /auth/refresh                # Refresh token
POST   /auth/forgot-password        # Request password reset
POST   /auth/reset-password         # Reset password

POST   /products/:id/favorite       # Toggle favorite
DELETE /products/:id/favorite       # Remove favorite

POST   /chats/:id/messages          # Send message
POST   /chats/:id/read              # Mark as read

❌ Bad - Non-RESTful naming
GET    /getProducts
POST   /createProduct
GET    /product-list
POST   /addFavorite/:id
```

#### 2.4.2 Query Parameters

```
✅ Good - Consistent query parameters
GET /products?page=1&limit=20&sort=name&order=asc
GET /products?type=growth&riskLevel=medium&minInvestment=10000
GET /portfolio/transactions?startDate=2024-01-01&endDate=2024-12-31

✅ Good - Filtering and pagination
?page=1              # Page number
&limit=20            # Items per page
&sort=createdAt      # Sort field
&order=desc          # Sort order
&search=query        # Search term
&fields=id,name      # Field selection

❌ Bad - Inconsistent parameters
?pagenumber=1
&pageSize=20
&sortBy=name
&direction=asc
```

#### 2.4.3 WebSocket Events

```typescript
// ✅ Good - Namespace:event pattern
// Chat namespace
socket.emit('chat:join', { chatId });
socket.emit('chat:message', { content, attachments });
socket.emit('chat:typing', { isTyping });

socket.on('chat:message', callback);
socket.on('chat:message:status', callback);

// Portfolio namespace
socket.emit('portfolio:subscribe');
socket.emit('portfolio:unsubscribe');

socket.on('portfolio:update', callback);

// ✅ Good - Event naming
// Client to Server
'chat:join'           // Join a chat room
'chat:leave'          // Leave a chat room
'chat:message'        // Send a message
'chat:typing'         // Typing indicator

// Server to Client
'chat:message'        // New message received
'chat:message:status' // Message status update
'user:online'         // User online status
'error'               // Error event
```

---

## 3. Folder Structure

### 3.1 Frontend (React Native)

```
asset-management-app/
├── src/
│   ├── app/                          # App configuration and root
│   │   ├── App.tsx                   # Root component
│   │   ├── providers/                # Context providers
│   │   │   ├── index.ts              # Combine all providers
│   │   │   ├── QueryProvider.tsx     # React Query provider
│   │   │   ├── ThemeProvider.tsx     # Theme provider
│   │   │   ├── AuthProvider.tsx      # Auth context provider
│   │   │   └── NotificationProvider.tsx
│   │   └── navigation/               # Navigation configuration
│   │       ├── index.ts              # Export navigation
│   │       ├── RootNavigator.tsx     # Root navigation
│   │       ├── AuthNavigator.tsx     # Auth stack
│   │       ├── MainNavigator.tsx     # Main app stack
│   │       ├── linking.ts            # Deep linking config
│   │       └── types.ts              # Navigation types
│   │
│   ├── entities/                     # Domain entities
│   │   ├── user/
│   │   │   ├── User.ts               # User entity class
│   │   │   ├── User.types.ts         # User types
│   │   │   ├── User.mapper.ts        # DTO to Entity mapper
│   │   │   └── index.ts
│   │   ├── product/
│   │   │   ├── Product.ts
│   │   │   ├── Product.types.ts
│   │   │   ├── Product.mapper.ts
│   │   │   └── index.ts
│   │   ├── portfolio/
│   │   │   ├── Portfolio.ts
│   │   │   ├── Asset.ts
│   │   │   ├── Transaction.ts
│   │   │   ├── Portfolio.types.ts
│   │   │   └── index.ts
│   │   └── chat/
│   │       ├── Chat.ts
│   │       ├── Message.ts
│   │       ├── Chat.types.ts
│   │       └── index.ts
│   │
│   ├── features/                     # Feature modules
│   │   ├── auth/                     # Authentication feature
│   │   │   ├── screens/
│   │   │   │   ├── LoginScreen.tsx
│   │   │   │   ├── ForgotPasswordScreen.tsx
│   │   │   │   └── index.ts
│   │   │   ├── components/
│   │   │   │   ├── LoginForm.tsx
│   │   │   │   ├── BiometricButton.tsx
│   │   │   │   └── index.ts
│   │   │   ├── hooks/
│   │   │   │   ├── useAuth.ts
│   │   │   │   ├── useBiometric.ts
│   │   │   │   └── index.ts
│   │   │   ├── api/
│   │   │   │   ├── authApi.ts        # API functions
│   │   │   │   ├── authKeys.ts       # React Query keys
│   │   │   │   └── index.ts
│   │   │   ├── types/
│   │   │   │   ├── authTypes.ts
│   │   │   │   └── index.ts
│   │   │   └── index.ts              # Feature public API
│   │   │
│   │   ├── products/                 # Products feature
│   │   │   ├── screens/
│   │   │   │   ├── ProductsListScreen.tsx
│   │   │   │   ├── ProductDetailScreen.tsx
│   │   │   │   └── index.ts
│   │   │   ├── components/
│   │   │   │   ├── ProductCard.tsx
│   │   │   │   ├── ProductList.tsx
│   │   │   │   ├── ProductFilter.tsx
│   │   │   │   ├── ProductChart.tsx
│   │   │   │   └── index.ts
│   │   │   ├── hooks/
│   │   │   │   ├── useProducts.ts
│   │   │   │   ├── useProduct.ts
│   │   │   │   ├── useFavorites.ts
│   │   │   │   └── index.ts
│   │   │   ├── api/
│   │   │   │   ├── productsApi.ts
│   │   │   │   ├── productsKeys.ts
│   │   │   │   └── index.ts
│   │   │   ├── store/
│   │   │   │   ├── favoritesStore.ts
│   │   │   │   └── index.ts
│   │   │   ├── types/
│   │   │   │   ├── productsTypes.ts
│   │   │   │   └── index.ts
│   │   │   └── index.ts
│   │   │
│   │   ├── portfolio/                # Portfolio feature
│   │   │   ├── screens/
│   │   │   │   ├── PortfolioScreen.tsx
│   │   │   │   ├── AssetDetailScreen.tsx
│   │   │   │   ├── TransactionHistoryScreen.tsx
│   │   │   │   └── index.ts
│   │   │   ├── components/
│   │   │   │   ├── PortfolioChart.tsx
│   │   │   │   ├── AssetList.tsx
│   │   │   │   ├── AssetItem.tsx
│   │   │   │   ├── AllocationChart.tsx
│   │   │   │   ├── TransactionItem.tsx
│   │   │   │   └── index.ts
│   │   │   ├── hooks/
│   │   │   │   ├── usePortfolio.ts
│   │   │   │   ├── useAssets.ts
│   │   │   │   ├── useTransactions.ts
│   │   │   │   └── index.ts
│   │   │   ├── api/
│   │   │   │   ├── portfolioApi.ts
│   │   │   │   ├── portfolioKeys.ts
│   │   │   │   └── index.ts
│   │   │   ├── utils/
│   │   │   │   ├── calculations.ts
│   │   │   │   └── index.ts
│   │   │   ├── types/
│   │   │   │   └── index.ts
│   │   │   └── index.ts
│   │   │
│   │   ├── chat/                     # Chat feature
│   │   │   ├── screens/
│   │   │   │   ├── ChatListScreen.tsx
│   │   │   │   ├── ChatDetailScreen.tsx
│   │   │   │   └── index.ts
│   │   │   ├── components/
│   │   │   │   ├── MessageBubble.tsx
│   │   │   │   ├── MessageInput.tsx
│   │   │   │   ├── MessageList.tsx
│   │   │   │   ├── AttachmentPicker.tsx
│   │   │   │   ├── TypingIndicator.tsx
│   │   │   │   └── index.ts
│   │   │   ├── hooks/
│   │   │   │   ├── useChats.ts
│   │   │   │   ├── useMessages.ts
│   │   │   │   ├── useSendMessage.ts
│   │   │   │   └── index.ts
│   │   │   ├── api/
│   │   │   │   ├── chatApi.ts
│   │   │   │   ├── chatKeys.ts
│   │   │   │   └── index.ts
│   │   │   ├── websocket/
│   │   │   │   ├── chatSocket.ts
│   │   │   │   ├── socketEvents.ts
│   │   │   │   └── index.ts
│   │   │   ├── store/
│   │   │   │   ├── chatStore.ts
│   │   │   │   └── index.ts
│   │   │   └── index.ts
│   │   │
│   │   └── profile/                  # Profile feature
│   │       ├── screens/
│   │       │   ├── ProfileScreen.tsx
│   │       │   ├── SettingsScreen.tsx
│   │       │   ├── DocumentsScreen.tsx
│   │       │   └── index.ts
│   │       ├── components/
│   │       │   ├── ProfileHeader.tsx
│   │       │   ├── ProfileForm.tsx
│   │       │   ├── DocumentItem.tsx
│   │       │   └── index.ts
│   │       ├── hooks/
│   │       │   ├── useProfile.ts
│   │       │   ├── useUpdateProfile.ts
│   │       │   ├── useDocuments.ts
│   │       │   └── index.ts
│   │       ├── api/
│   │       │   ├── profileApi.ts
│   │       │   ├── profileKeys.ts
│   │       │   └── index.ts
│   │       └── index.ts
│   │
│   ├── shared/                       # Shared utilities
│   │   ├── ui/                       # Shared UI components
│   │   │   ├── Button/
│   │   │   │   ├── Button.tsx
│   │   │   │   ├── Button.types.ts
│   │   │   │   ├── Button.styles.ts
│   │   │   │   ├── Button.test.tsx
│   │   │   │   └── index.ts
│   │   │   ├── Input/
│   │   │   │   ├── Input.tsx
│   │   │   │   ├── Input.types.ts
│   │   │   │   ├── Input.test.tsx
│   │   │   │   └── index.ts
│   │   │   ├── Card/
│   │   │   ├── Modal/
│   │   │   ├── Avatar/
│   │   │   ├── Spinner/
│   │   │   ├── Typography/
│   │   │   ├── Loading/
│   │   │   ├── Error/
│   │   │   ├── Empty/
│   │   │   └── index.ts
│   │   │
│   │   ├── api/                      # Shared API utilities
│   │   │   ├── apiClient.ts          # Axios instance
│   │   │   ├── apiError.ts           # Error classes
│   │   │   ├── interceptors.ts       # Request/response interceptors
│   │   │   ├── retry.ts              # Retry logic
│   │   │   ├── types.ts              # Common API types
│   │   │   └── index.ts
│   │   │
│   │   ├── hooks/                    # Shared hooks
│   │   │   ├── useDebounce.ts
│   │   │   ├── useThrottle.ts
│   │   │   ├── useKeyboard.ts
│   │   │   ├── useAppState.ts
│   │   │   ├── useOrientation.ts
│   │   │   ├── useMount.ts
│   │   │   ├── useTimeout.ts
│   │   │   └── index.ts
│   │   │
│   │   ├── store/                    # Global stores
│   │   │   ├── authStore.ts
│   │   │   ├── uiStore.ts
│   │   │   ├── sessionStore.ts
│   │   │   ├── settingsStore.ts
│   │   │   └── index.ts
│   │   │
│   │   ├── storage/                  # Storage utilities
│   │   │   ├── secureStorage.ts
│   │   │   ├── asyncStorage.ts
│   │   │   ├── cacheStorage.ts
│   │   │   ├── encryption.ts
│   │   │   └── index.ts
│   │   │
│   │   ├── utils/                    # Utility functions
│   │   │   ├── formatters.ts         # Date, number formatting
│   │   │   ├── validators.ts         # Validation functions
│   │   │   ├── helpers.ts            # General helpers
│   │   │   ├── currency.ts           # Currency utilities
│   │   │   ├── date.ts               # Date utilities
│   │   │   ├── strings.ts            # String utilities
│   │   │   ├── constants.ts          # App constants
│   │   │   └── index.ts
│   │   │
│   │   └── types/                    # Shared TypeScript types
│   │       ├── common.ts
│   │       ├── api.ts
│   │       ├── navigation.ts
│   │       ├── result.ts             # Result type
│   │       └── index.ts
│   │
│   ├── services/                     # External services
│   │   ├── pushNotifications/
│   │   │   ├── index.ts
│   │   │   ├── handlers.ts
│   │   │   └── permissions.ts
│   │   ├── analytics/
│   │   │   ├── index.ts
│   │   │   ├── events.ts
│   │   │   └── providers/
│   │   ├── crashReporting/
│   │   │   ├── index.ts
│   │   │   └── sentry.ts
│   │   ├── biometrics/
│   │   │   ├── index.ts
│   │   │   └── types.ts
│   │   └── index.ts
│   │
│   ├── assets/                       # Static assets
│   │   ├── images/
│   │   │   ├── logo.png
│   │   │   └── icons/
│   │   ├── fonts/
│   │   │   └── CustomFont.ttf
│   │   ├── animations/
│   │   │   └── loading.json
│   │   └── index.ts
│   │
│   └── config/                       # Configuration
│       ├── api.config.ts
│       ├── app.config.ts
│       ├── env.ts
│       ├── theme.ts
│       └── index.ts
│
├── e2e/                              # E2E tests
│   ├── testID.ts                     # Test ID constants
│   ├── auth.test.ts
│   ├── products.test.ts
│   └── chat.test.ts
│
├── android/                          # Android native
│   ├── app/
│   ├── gradle/
│   └── build.gradle
│
├── ios/                              # iOS native
│   ├── AssetManagement/
│   └── Podfile
│
├── __tests__/                        # Unit tests
│   ├── setup.ts
│   └── jest.config.js
│
├── .env                              # Environment variables
├── .env.example
├── .eslintrc.js
├── .prettierrc.js
├── tsconfig.json
├── babel.config.js
├── metro.config.js
├── package.json
└── README.md
```

### 3.2 Backend (Reference)

```
asset-management-api/
├── src/
│   ├── controllers/                  # Request handlers
│   │   ├── auth.controller.ts
│   │   ├── products.controller.ts
│   │   ├── portfolio.controller.ts
│   │   ├── chat.controller.ts
│   │   └── users.controller.ts
│   │
│   ├── services/                     # Business logic
│   │   ├── auth.service.ts
│   │   ├── products.service.ts
│   │   ├── portfolio.service.ts
│   │   ├── chat.service.ts
│   │   └── notification.service.ts
│   │
│   ├── repositories/                 # Data access
│   │   ├── user.repository.ts
│   │   ├── product.repository.ts
│   │   ├── portfolio.repository.ts
│   │   └── message.repository.ts
│   │
│   ├── models/                       # Database models
│   │   ├── user.model.ts
│   │   ├── product.model.ts
│   │   ├── portfolio.model.ts
│   │   └── message.model.ts
│   │
│   ├── middlewares/                  # Express middlewares
│   │   ├── auth.middleware.ts
│   │   ├── error.middleware.ts
│   │   ├── validation.middleware.ts
│   │   └── rateLimit.middleware.ts
│   │
│   ├── routes/                       # Route definitions
│   │   ├── index.ts
│   │   ├── auth.routes.ts
│   │   ├── products.routes.ts
│   │   ├── portfolio.routes.ts
│   │   └── chat.routes.ts
│   │
│   ├── validators/                   # Request validation
│   │   ├── auth.validator.ts
│   │   ├── products.validator.ts
│   │   └── portfolio.validator.ts
│   │
│   ├── websocket/                    # WebSocket handlers
│   │   ├── index.ts
│   │   ├── chat.handler.ts
│   │   └── portfolio.handler.ts
│   │
│   ├── utils/                        # Utility functions
│   │   ├── jwt.ts
│   │   ├── hash.ts
│   │   └── logger.ts
│   │
│   ├── types/                        # TypeScript types
│   │   ├── express.d.ts
│   │   └── index.ts
│   │
│   ├── config/                       # Configuration
│   │   ├── database.ts
│   │   ├── redis.ts
│   │   └── app.ts
│   │
│   └── app.ts                        # Express app setup
│
├── prisma/
│   └── schema.prisma                 # Database schema
│
├── tests/
│   ├── unit/
│   │   ├── services/
│   │   └── utils/
│   └── integration/
│       ├── auth.test.ts
│       └── products.test.ts
│
├── .env
├── .env.example
├── package.json
├── tsconfig.json
└── README.md
```

### 3.3 Shared Code

```
shared/
├── types/                            # Shared types
│   ├── entities.ts                   # Entity interfaces
│   ├── api.ts                        # API types
│   ├── events.ts                     # Event types
│   └── index.ts
│
├── constants/                        # Shared constants
│   ├── errors.ts                     # Error codes
│   ├── status.ts                     # Status enums
│   └── index.ts
│
├── validators/                       # Shared validators
│   ├── user.ts
│   ├── product.ts
│   └── index.ts
│
└── utils/                            # Shared utilities
    ├── formatters.ts
    ├── calculations.ts
    └── index.ts
```

---

## 4. Git Workflow

### 4.1 Branch Naming

#### 4.1.1 Branch Types

| Type | Prefix | Description | Example |
|------|--------|-------------|---------|
| **Feature** | `feature/` | New functionality | `feature/user-authentication` |
| **Bugfix** | `bugfix/` | Bug fixes | `bugfix/login-validation` |
| **Hotfix** | `hotfix/` | Production hotfixes | `hotfix/security-patch` |
| **Release** | `release/` | Release branches | `release/v1.2.0` |
| **Refactor** | `refactor/` | Code refactoring | `refactor/auth-flow` |
| **Docs** | `docs/` | Documentation | `docs/api-documentation` |
| **Test** | `test/` | Testing | `test/auth-integration` |
| **Chore** | `chore/` | Maintenance | `chore/update-dependencies` |

#### 4.1.2 Naming Convention

```
<type>/<ticket-number>-<short-description>

Examples:
feature/AM-123-user-authentication
bugfix/AM-456-fix-login-crash
hotfix/AM-789-security-vulnerability
release/v1.2.0
refactor/AM-234-clean-up-auth-code
docs/AM-567-update-readme
```

#### 4.1.3 Branch Workflow

```
main (production)
  │
  ├── develop (staging)
  │     │
  │     ├── feature/AM-123-auth
  │     │     └── (merged to develop)
  │     │
  │     ├── feature/AM-124-products
  │     │     └── (merged to develop)
  │     │
  │     └── bugfix/AM-125-fix-crash
  │           └── (merged to develop)
  │
  └── release/v1.2.0
        └── (merged to main and develop)
```

### 4.2 Commit Message Format

#### 4.2.1 Commit Structure

```
<type>(<scope>): <subject>

<body>

<footer>
```

#### 4.2.2 Commit Types

| Type | Description | Example |
|------|-------------|---------|
| `feat` | New feature | `feat(auth): add biometric login` |
| `fix` | Bug fix | `fix(auth): resolve token refresh issue` |
| `docs` | Documentation | `docs(readme): update installation steps` |
| `style` | Code style (formatting) | `style(button): fix indentation` |
| `refactor` | Code refactoring | `refactor(auth): extract token logic` |
| `perf` | Performance improvement | `perf(list): optimize FlatList rendering` |
| `test` | Adding/updating tests | `test(auth): add login unit tests` |
| `chore` | Maintenance | `chore(deps): update dependencies` |
| `revert` | Revert commit | `revert: feat(auth): add biometric login` |

#### 4.2.3 Scope Examples

```
feat(auth): add biometric authentication
feat(products): implement product filtering
feat(chat): add message attachments
fix(portfolio): calculate total value correctly
fix(chat): resolve websocket reconnection
refactor(api): simplify error handling
test(auth): add integration tests for login
docs(api): update endpoint documentation
```

#### 4.2.4 Commit Examples

**Feature:**
```
feat(auth): add biometric authentication

Implement Touch ID/Face ID support for iOS and
fingerprint authentication for Android.

- Add biometric permission handling
- Implement biometric prompt UI
- Store biometric preference in secure storage

Closes #123
```

**Bug Fix:**
```
fix(chat): resolve message duplication on reconnect

Messages were being duplicated when WebSocket
reconnected after network interruption.

- Add message deduplication logic
- Track message IDs in local cache
- Clear pending messages on reconnect

Fixes #456
```

**Breaking Change:**
```
feat(api)!: change authentication response format

BREAKING CHANGE: Authentication endpoints now return
refresh token in response body instead of cookie.

Migration guide:
- Update auth interceptors to handle new format
- Remove cookie-based refresh logic
```

### 4.3 Pull Request Guidelines

#### 4.3.1 PR Title Format

```
[AM-123] Add user authentication feature
[AM-456] Fix login validation bug
[AM-789] Refactor API error handling
```

#### 4.3.2 PR Description Template

```markdown
## Description
Brief description of the changes.

## Type of Change
- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update
- [ ] Refactoring

## Related Issues
Closes #123
Related to #124

## Changes Made
- Added user authentication
- Implemented login screen
- Added biometric support

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [ ] Manual testing completed

## Screenshots (if applicable)
| Before | After |
|--------|-------|
| Image  | Image |

## Checklist
- [ ] Code follows project style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex logic
- [ ] Documentation updated
- [ ] No new warnings introduced
- [ ] Tests pass locally
- [ ] New tests added for new features

## Additional Notes
Any additional information for reviewers.
```

#### 4.3.3 Code Review Checklist

**For Reviewers:**

```markdown
## Code Review Checklist

### Functionality
- [ ] Code accomplishes the stated purpose
- [ ] Edge cases are handled
- [ ] Error handling is appropriate

### Code Quality
- [ ] Code is readable and well-organized
- [ ] Naming is clear and consistent
- [ ] No unnecessary code duplication
- [ ] Functions are focused and small

### Best Practices
- [ ] TypeScript types are properly defined
- [ ] React best practices followed
- [ ] Performance considerations addressed
- [ ] Security best practices followed

### Testing
- [ ] Tests are meaningful and comprehensive
- [ ] Test coverage is adequate
- [ ] Edge cases are tested

### Documentation
- [ ] Complex logic is commented
- [ ] Public APIs are documented
- [ ] README updated if needed
```

---

## 5. Testing Standards

### 5.1 Unit Test Requirements

#### 5.1.1 Test File Organization

```
__tests__/
├── setup.ts                          # Jest setup
├── mocks/                            # Mock implementations
│   ├── api.ts
│   ├── storage.ts
│   └── navigation.ts
│
├── unit/
│   ├── utils/
│   │   ├── formatters.test.ts
│   │   ├── validators.test.ts
│   │   └── calculations.test.ts
│   │
│   ├── hooks/
│   │   ├── useAuth.test.ts
│   │   ├── useProducts.test.ts
│   │   └── useDebounce.test.ts
│   │
│   ├── components/
│   │   ├── Button.test.tsx
│   │   ├── Input.test.tsx
│   │   └── ProductCard.test.tsx
│   │
│   └── store/
│       ├── authStore.test.ts
│       └── uiStore.test.ts
```

#### 5.1.2 Test Structure (AAA Pattern)

```typescript
import { render, fireEvent, waitFor } from '@testing-library/react-native';
import { ProductCard } from './ProductCard';

describe('ProductCard', () => {
  // Setup common test data
  const mockProduct: Product = {
    id: '1',
    name: 'Test Product',
    price: 100,
    riskLevel: 'medium',
  };
  
  const mockOnPress = jest.fn();
  
  beforeEach(() => {
    jest.clearAllMocks();
  });
  
  describe('rendering', () => {
    it('should render product name correctly', () => {
      // Arrange
      const { getByText } = render(
        <ProductCard product={mockProduct} onPress={mockOnPress} />
      );
      
      // Act & Assert
      expect(getByText('Test Product')).toBeTruthy();
    });
    
    it('should render formatted price', () => {
      // Arrange
      const { getByText } = render(
        <ProductCard product={mockProduct} onPress={mockOnPress} />
      );
      
      // Act & Assert
      expect(getByText('$100.00')).toBeTruthy();
    });
  });
  
  describe('interactions', () => {
    it('should call onPress when pressed', () => {
      // Arrange
      const { getByTestId } = render(
        <ProductCard product={mockProduct} onPress={mockOnPress} />
      );
      
      // Act
      fireEvent.press(getByTestId('product-card'));
      
      // Assert
      expect(mockOnPress).toHaveBeenCalledWith(mockProduct);
      expect(mockOnPress).toHaveBeenCalledTimes(1);
    });
  });
  
  describe('accessibility', () => {
    it('should have correct accessibility label', () => {
      // Arrange
      const { getByLabelText } = render(
        <ProductCard product={mockProduct} onPress={mockOnPress} />
      );
      
      // Act & Assert
      expect(getByLabelText('Product: Test Product')).toBeTruthy();
    });
  });
});
```

#### 5.1.3 Hook Testing

```typescript
import { renderHook, act } from '@testing-library/react-hooks';
import { useAuth } from './useAuth';
import { authApi } from '@features/auth/api';

jest.mock('@features/auth/api');

describe('useAuth', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });
  
  describe('login', () => {
    it('should authenticate user successfully', async () => {
      // Arrange
      const mockUser = { id: '1', email: 'test@example.com' };
      const mockTokens = { accessToken: 'token', refreshToken: 'refresh' };
      (authApi.login as jest.Mock).mockResolvedValue({
        user: mockUser,
        ...mockTokens,
      });
      
      // Act
      const { result } = renderHook(() => useAuth());
      
      await act(async () => {
        await result.current.login('test@example.com', 'password');
      });
      
      // Assert
      expect(result.current.user).toEqual(mockUser);
      expect(result.current.isAuthenticated).toBe(true);
    });
    
    it('should handle login error', async () => {
      // Arrange
      const mockError = new Error('Invalid credentials');
      (authApi.login as jest.Mock).mockRejectedValue(mockError);
      
      // Act
      const { result } = renderHook(() => useAuth());
      
      await act(async () => {
        try {
          await result.current.login('test@example.com', 'wrong');
        } catch (error) {
          // Expected
        }
      });
      
      // Assert
      expect(result.current.user).toBeNull();
      expect(result.current.isAuthenticated).toBe(false);
      expect(result.current.error).toEqual(mockError);
    });
  });
});
```

### 5.2 E2E Test Guidelines

#### 5.2.1 Detox Configuration

```javascript
// e2e/config.json
{
  "testEnvironment": "./environment",
  "testRunner": "jest",
  "runnerConfig": "e2e/config.json",
  "configurations": {
    "ios.sim.debug": {
      "binaryPath": "ios/build/Build/Products/Debug-iphonesimulator/AssetManagement.app",
      "build": "xcodebuild -workspace ios/AssetManagement.xcworkspace -scheme AssetManagement -configuration Debug -sdk iphonesimulator -derivedDataPath ios/build",
      "type": "ios.simulator",
      "device": {
        "type": "iPhone 15"
      }
    },
    "android.emu.debug": {
      "binaryPath": "android/app/build/outputs/apk/debug/app-debug.apk",
      "build": "cd android && ./gradlew assembleDebug assembleAndroidTest -DtestBuildType=debug",
      "type": "android.emulator",
      "device": {
        "avdName": "Pixel_5_API_33"
      }
    }
  }
}
```

#### 5.2.2 E2E Test Structure

```typescript
// e2e/auth.test.ts
import { device, element, by, expect as detoxExpect } from 'detox';
import { TEST_IDS } from './testID';

describe('Authentication', () => {
  beforeAll(async () => {
    await device.launchApp();
  });
  
  beforeEach(async () => {
    await device.reloadReactNative();
  });
  
  describe('Login', () => {
    it('should login successfully with valid credentials', async () => {
      // Arrange - Enter email
      await element(by.id(TEST_IDS.AUTH.EMAIL_INPUT)).tap();
      await element(by.id(TEST_IDS.AUTH.EMAIL_INPUT)).typeText('user@test.com');
      
      // Act - Enter password and submit
      await element(by.id(TEST_IDS.AUTH.PASSWORD_INPUT)).tap();
      await element(by.id(TEST_IDS.AUTH.PASSWORD_INPUT)).typeText('password123');
      await element(by.id(TEST_IDS.AUTH.LOGIN_BUTTON)).tap();
      
      // Assert - Should navigate to main screen
      await detoxExpect(element(by.id(TEST_IDS.MAIN.TAB_NAVIGATOR))).toBeVisible();
    });
    
    it('should show error for invalid credentials', async () => {
      // Arrange
      await element(by.id(TEST_IDS.AUTH.EMAIL_INPUT)).typeText('wrong@test.com');
      await element(by.id(TEST_IDS.AUTH.PASSWORD_INPUT)).typeText('wrongpass');
      
      // Act
      await element(by.id(TEST_IDS.AUTH.LOGIN_BUTTON)).tap();
      
      // Assert
      await detoxExpect(element(by.text('Invalid credentials'))).toBeVisible();
    });
  });
  
  describe('Biometric Login', () => {
    it('should show biometric prompt when enabled', async () => {
      // Setup biometric in settings
      // ...
      
      // Assert biometric button is visible
      await detoxExpect(element(by.id(TEST_IDS.AUTH.BIOMETRIC_BUTTON))).toBeVisible();
    });
  });
});

// e2e/testID.ts
export const TEST_IDS = {
  AUTH: {
    EMAIL_INPUT: 'auth-email-input',
    PASSWORD_INPUT: 'auth-password-input',
    LOGIN_BUTTON: 'auth-login-button',
    BIOMETRIC_BUTTON: 'auth-biometric-button',
    FORGOT_PASSWORD_LINK: 'auth-forgot-password',
  },
  MAIN: {
    TAB_NAVIGATOR: 'main-tab-navigator',
    PRODUCTS_TAB: 'products-tab',
    PORTFOLIO_TAB: 'portfolio-tab',
    CHAT_TAB: 'chat-tab',
    PROFILE_TAB: 'profile-tab',
  },
  PRODUCTS: {
    LIST: 'products-list',
    PRODUCT_CARD: 'product-card',
    PRODUCT_DETAIL: 'product-detail',
  },
  // ... more test IDs
};
```

### 5.3 Coverage Minimums

#### 5.3.1 Coverage Configuration

```javascript
// jest.config.js
module.exports = {
  preset: 'react-native',
  setupFiles: ['./__tests__/setup.ts'],
  transform: {
    '^.+\\.tsx?$': 'ts-jest',
  },
  moduleFileExtensions: ['ts', 'tsx', 'js', 'jsx', 'json'],
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1',
    '^@features/(.*)$': '<rootDir>/src/features/$1',
    '^@shared/(.*)$': '<rootDir>/src/shared/$1',
    '^@entities/(.*)$': '<rootDir>/src/entities/$1',
  },
  collectCoverage: true,
  coverageDirectory: 'coverage',
  coverageReporters: ['text', 'lcov', 'html'],
  coverageThreshold: {
    global: {
      branches: 70,
      functions: 70,
      lines: 70,
      statements: 70,
    },
    './src/entities/': {
      branches: 90,
      functions: 90,
      lines: 90,
      statements: 90,
    },
    './src/shared/utils/': {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80,
    },
    './src/features/': {
      branches: 70,
      functions: 70,
      lines: 70,
      statements: 70,
    },
  },
  coveragePathIgnorePatterns: [
    '/node_modules/',
    '/__tests__/',
    '/e2e/',
    '.*\\.d\\.ts',
    '.*\\.config\\.ts',
    '.*\\.styles\\.ts',
    './src/assets/',
    './src/types/',
  ],
};
```

#### 5.3.2 Coverage Requirements by Layer

| Layer | Branches | Functions | Lines | Statements |
|-------|----------|-----------|-------|------------|
| **Global** | 70% | 70% | 70% | 70% |
| **Entities** | 90% | 90% | 90% | 90% |
| **Utilities** | 80% | 80% | 80% | 80% |
| **Features** | 70% | 70% | 70% | 70% |
| **Components** | 70% | 70% | 70% | 70% |

---

## 6. Documentation Standards

### 6.1 Code Documentation

#### 6.1.1 JSDoc for Public APIs

```typescript
/**
 * Formats a number as currency with the specified currency code.
 * 
 * @param amount - The amount to format
 * @param currency - ISO 4217 currency code (e.g., 'USD', 'EUR')
 * @param options - Formatting options
 * @returns Formatted currency string
 * 
 * @example
 * ```typescript
 * formatCurrency(1234.56, 'USD'); // '$1,234.56'
 * formatCurrency(1234.56, 'EUR', { locale: 'de-DE' }); // '1.234,56 €'
 * ```
 */
export function formatCurrency(
  amount: number,
  currency: string,
  options?: FormatCurrencyOptions
): string {
  // Implementation
}

/**
 * Custom hook for managing user authentication state.
 * 
 * Provides authentication state and actions for login, logout,
 * and token management.
 * 
 * @returns Authentication state and actions
 * 
 * @example
 * ```typescript
 * const { user, isAuthenticated, login, logout } = useAuth();
 * 
 * if (!isAuthenticated) {
 *   await login(email, password);
 * }
 * ```
 */
export function useAuth(): UseAuthReturn {
  // Implementation
}
```

#### 6.1.2 Component Documentation

```typescript
/**
 * ProductCard displays a product with its key information.
 * 
 * @example
 * ```tsx
 * <ProductCard
 *   product={product}
 *   onPress={(product) => navigation.navigate('ProductDetail', { id: product.id })}
 *   isFavorite={favorites.includes(product.id)}
 * />
 * ```
 */
export const ProductCard: React.FC<ProductCardProps> = ({
  product,
  onPress,
  isFavorite = false,
}) => {
  // Implementation
};
```

#### 6.1.3 README Files

```markdown
# Feature: Authentication

## Overview
Authentication feature provides secure login, logout, and session
management for users.

## Components
- `LoginScreen` - Main login interface
- `ForgotPasswordScreen` - Password recovery
- `BiometricButton` - Biometric authentication trigger

## Hooks
- `useAuth` - Authentication state management
- `useBiometric` - Biometric authentication

## API
- `authApi.login(email, password)` - Authenticate user
- `authApi.logout()` - End session
- `authApi.refresh()` - Refresh tokens

## Usage
```typescript
import { useAuth, LoginScreen } from '@features/auth';

function App() {
  const { isAuthenticated } = useAuth();
  
  return isAuthenticated ? <MainApp /> : <LoginScreen />;
}
```

## Dependencies
- `@react-navigation/native`
- `react-native-biometrics`
- `expo-secure-store`
```

### 6.2 API Documentation

```typescript
/**
 * @api {post} /auth/login Authenticate User
 * @apiName Login
 * @apiGroup Authentication
 * @apiVersion 1.0.0
 * 
 * @apiBody {String} email User's email address
 * @apiBody {String} password User's password
 * @apiBody {String} [deviceId] Device identifier for session tracking
 * 
 * @apiSuccess {Object} user User object
 * @apiSuccess {String} user.id User unique ID
 * @apiSuccess {String} user.email User email
 * @apiSuccess {String} accessToken JWT access token
 * @apiSuccess {String} refreshToken JWT refresh token
 * @apiSuccess {Number} expiresIn Token expiration in seconds
 * 
 * @apiError (401) Unauthorized Invalid credentials
 * @apiError (400) BadRequest Validation error
 * 
 * @apiExample {curl} Example usage:
 *     curl -X POST https://api.example.com/v1/auth/login \
 *       -H "Content-Type: application/json" \
 *       -d '{"email":"user@example.com","password":"secret"}'
 */
```

---

## 7. Code Review Guidelines

### 7.1 Review Process

1. **Author creates PR** with description and checklist
2. **CI/CD runs** automated checks (lint, tests, build)
3. **Reviewer assigned** based on code ownership
4. **Review completed** within 24 hours
5. **Author addresses** feedback
6. **Approval required** from at least one reviewer
7. **Squash and merge** to target branch

### 7.2 Review Criteria

**Must Have:**
- [ ] Code compiles without errors
- [ ] All tests pass
- [ ] Coverage meets minimum requirements
- [ ] No TypeScript errors
- [ ] No ESLint warnings
- [ ] Follows naming conventions
- [ ] Proper error handling

**Should Have:**
- [ ] Self-explanatory code
- [ ] Adequate test coverage
- [ ] Performance considerations
- [ ] Security best practices
- [ ] Accessibility support

### 7.3 Approval Requirements

| Change Type | Approvers Required |
|-------------|-------------------|
| Feature | 1 developer + 1 tech lead |
| Bugfix | 1 developer |
| Hotfix | 1 tech lead |
| Breaking change | 2 tech leads |
| Security fix | Security team + tech lead |

---

**Document Status:** Final  
**Last Updated:** 2026-03-31  
**Next Review:** 2026-04-30
