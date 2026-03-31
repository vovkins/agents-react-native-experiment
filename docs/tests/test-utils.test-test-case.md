/**
 * Tasks Feature Test Index
 * 
 * Exports all test utilities and mocks for the tasks feature.
 * 
 * @module features/tasks/__tests__/index
 */

export const mockTask = {
  id: 'task-1',
  title: 'Sign Investment Agreement',
  description: 'Please review and sign the updated investment agreement for your portfolio.',
  type: 'signature_request' as const,
  status: 'pending' as const,
  priority: 'urgent' as const,
  dueDate: new Date('2024-12-31'),
  createdAt: new Date('2024-01-01'),
  manager: {
    id: 'manager-1',
    name: 'Alexander Petrov',
    avatar: 'https://example.com/avatar.jpg',
  },
  relatedProduct: {
    id: 'product-1',
    name: 'Balanced Growth Strategy',
  },
  actionLabel: 'Sign Now',
  actionType: 'navigate' as const,
};

export const mockTasks = [
  mockTask,
  {
    id: 'task-2',
    title: 'Submit Tax Documents',
    description: 'Annual tax documentation required for compliance.',
    type: 'document_request' as const,
    status: 'overdue' as const,
    priority: 'high' as const,
    dueDate: new Date('2024-01-05'),
    createdAt: new Date('2024-01-01'),
    manager: {
      id: 'manager-1',
      name: 'Alexander Petrov',
    },
    actionLabel: 'Upload',
    actionType: 'modal' as const,
  },
  {
    id: 'task-3',
    title: 'Review Portfolio Performance',
    description: 'Your Q4 portfolio report is ready for review.',
    type: 'review_request' as const,
    status: 'completed' as const,
    priority: 'normal' as const,
    dueDate: new Date('2024-01-20'),
    createdAt: new Date('2024-01-15'),
    completedAt: new Date('2024-01-18'),
    manager: {
      id: 'manager-1',
      name: 'Alexander Petrov',
    },
  },
];

export const mockTasksResponse = {
  tasks: mockTasks,
  pagination: {
    page: 1,
    pageSize: 20,
    total: 3,
    hasMore: false,
  },
  summary: {
    pending: 1,
    overdue: 1,
    completed: 1,
  },
};

export const mockTasksSummary = {
  pending: 5,
  overdue: 2,
  completed: 12,
};

/**
 * Creates a wrapper with QueryClient for testing hooks
 */
export function createQueryWrapper() {
  const { QueryClient, QueryClientProvider } = require('@tanstack/react-query');
  const React = require('react');
  
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: {
        retry: false,
        gcTime: 0,
        staleTime: 0,
      },
    },
  });
  
  return ({ children }: { children: React.ReactNode }) => (
    <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>
  );
}

/**
 * Creates a wrapper with Navigation for testing screens
 */
export function createNavigationWrapper() {
  const { NavigationContainer } = require('@react-navigation/native');
  const React = require('react');
  
  return ({ children }: { children: React.ReactNode }) => (
    <NavigationContainer>{children}</NavigationContainer>
  );
}

/**
 * Creates a combined wrapper with all providers
 */
export function createAllProvidersWrapper() {
  const { QueryClient, QueryClientProvider } = require('@tanstack/react-query');
  const { NavigationContainer } = require('@react-navigation/native');
  const { SafeAreaProvider } = require('react-native-safe-area-context');
  const React = require('react');
  
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: {
        retry: false,
        gcTime: 0,
        staleTime: 0,
      },
    },
  });
  
  return ({ children }: { children: React.ReactNode }) => (
    <QueryClientProvider client={queryClient}>
      <SafeAreaProvider
        initialMetrics={{
          frame: { x: 0, y: 0, width: 375, height: 812 },
          insets: { top: 47, left: 0, right: 0, bottom: 34 },
        }}
      >
        <NavigationContainer>{children}</NavigationContainer>
      </SafeAreaProvider>
    </QueryClientProvider>
  );
}

/**
 * Waits for all pending promises to resolve
 */
export function flushPromises() {
  return new Promise(resolve => setImmediate(resolve));
}

/**
 * Creates a mock task with custom overrides
 */
export function createMockTask(overrides?: Partial<typeof mockTask>) {
  return {
    ...mockTask,
    ...overrides,
  };
}

/**
 * Creates a list of mock tasks
 */
export function createMockTasks(count: number) {
  return Array.from({ length: count }, (_, i) => ({
    ...mockTask,
    id: `task-${i + 1}`,
    title: `Task ${i + 1}`,
    status: (['pending', 'in_progress', 'overdue', 'completed'] as const)[i % 4],
    priority: (['urgent', 'high', 'normal', 'low'] as const)[i % 4],
    type: ([
      'document_request',
      'signature_request',
      'meeting_request',
      'information_request',
      'investment_decision',
      'kyc_update',
      'review_request',
    ] as const)[i % 7],
    createdAt: new Date(2024, 0, i + 1),
  }));
}

export {};
