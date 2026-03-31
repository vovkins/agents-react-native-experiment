/**
 * TaskScreen Component
 * 
 * Main screen for the Task Management feature.
 * Displays task summary cards, filter tabs, and task list with infinite scroll.
 * 
 * @module features/tasks/screens/TaskScreen
 */

import React, { useState, useCallback, useMemo } from 'react';
import { 
  View, 
  FlatList, 
  RefreshControl, 
  StyleSheet,
  ActivityIndicator,
  Text,
} from 'react-native';
import { useSafeAreaInsets } from 'react-native-safe-area-context';
import { useNavigation } from '@react-navigation/native';
import { NativeStackNavigationProp } from '@react-navigation/native-stack';
import * as Haptics from 'expo-haptics';
import { SummaryCard } from '../components/SummaryCard';
import { FilterTabs } from '../components/FilterTabs';
import { TaskItem } from '../components/TaskItem';
import { EmptyState } from '../components/EmptyState';
import { LoadingSkeleton } from '../components/LoadingSkeleton';
import { useTasksInfinite } from '../hooks/useTasks';
import type { Task, TasksFilters, TasksSummary } from '../types/taskTypes';

/**
 * Tab configuration for filter tabs
 */
const TABS = [
  { key: 'all', label: 'All' },
  { key: 'pending', label: 'Pending' },
  { key: 'overdue', label: 'Overdue' },
  { key: 'completed', label: 'Completed' },
];

/**
 * Default summary values
 */
const DEFAULT_SUMMARY: TasksSummary = {
  pending: 0,
  overdue: 0,
  completed: 0,
};

/**
 * TaskScreen provides a centralized view of all tasks.
 * 
 * @example
 * ```tsx
 * <TaskScreen />
 * ```
 */
export const TaskScreen: React.FC = () => {
  const insets = useSafeAreaInsets();
  const navigation = useNavigation();
  
  // Local state
  const [activeFilter, setActiveFilter] = useState<string>('all');
  const [isRefreshing, setIsRefreshing] = useState(false);
  
  // Derive status filter from active tab
  const status = activeFilter === 'all' 
    ? undefined 
    : activeFilter as TasksFilters['status'];
  
  // Fetch tasks with infinite scroll
  const {
    data,
    isLoading,
    isError,
    error,
    refetch,
    fetchNextPage,
    hasNextPage,
    isFetchingNextPage,
  } = useTasksInfinite({ status });
  
  // Flatten paginated data
  const tasks: Task[] = useMemo(() => {
    return data?.pages.flatMap((page) => page.tasks) ?? [];
  }, [data]);
  
  // Get summary from first page
  const summary: TasksSummary = useMemo(() => {
    return data?.pages[0]?.summary ?? DEFAULT_SUMMARY;
  }, [data]);
  
  // Calculate total task count
  const totalTasks = summary.pending + summary.overdue + summary.completed;
  
  // Handlers
  const handleRefresh = useCallback(async () => {
    setIsRefreshing(true);
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
    await refetch();
    setIsRefreshing(false);
  }, [refetch]);
  
  const handleFilterChange = useCallback((filter: string) => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    setActiveFilter(filter);
  }, []);
  
  const handleTaskPress = useCallback((task: Task) => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    navigation.navigate('TaskDetail' as never, { taskId: task.id } as never);
  }, [navigation]);
  
  const handleSummaryPress = useCallback((type: 'pending' | 'overdue' | 'completed') => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    setActiveFilter(type);
  }, []);
  
  const handleEndReached = useCallback(() => {
    if (hasNextPage && !isFetchingNextPage) {
      fetchNextPage();
    }
  }, [hasNextPage, isFetchingNextPage, fetchNextPage]);
  
  // Tabs with counts
  const tabsWithCounts = useMemo(() => {
    return TABS.map((tab) => ({
      ...tab,
      count: tab.key === 'all' 
        ? totalTasks
        : summary[tab.key as keyof typeof summary],
    }));
  }, [totalTasks, summary]);
  
  // Render loading state
  if (isLoading) {
    return <LoadingSkeleton />;
  }
  
  // Render error state
  if (isError) {
    return (
      <EmptyState
        type="error"
        onRetry={() => refetch()}
        testID="tasks-error-state"
      />
    );
  }
  
  // Render main content
  return (
    <View 
      className="flex-1 bg-white dark:bg-gray-950"
      style={[styles.container, { paddingTop: insets.top }]}
    >
      {/* Header */}
      <View style={styles.header}>
        <Text style={styles.headerTitle}>Tasks</Text>
      </View>
      
      {/* Summary Cards */}
      <View style={styles.summaryContainer}>
        <SummaryCard
          type="pending"
          count={summary.pending}
          onPress={() => handleSummaryPress('pending')}
          testID="summary-pending"
        />
        <SummaryCard
          type="overdue"
          count={summary.overdue}
          onPress={() => handleSummaryPress('overdue')}
          testID="summary-overdue"
        />
        <SummaryCard
          type="completed"
          count={summary.completed}
          onPress={() => handleSummaryPress('completed')}
          testID="summary-completed"
        />
      </View>
      
      {/* Filter Tabs */}
      <FilterTabs
        tabs={tabsWithCounts}
        activeTab={activeFilter}
        onTabChange={handleFilterChange}
        testID="filter-tabs"
      />
      
      {/* Task List */}
      {tasks.length === 0 ? (
        <EmptyState
          type={activeFilter !== 'all' ? 'no_results' : 'no_tasks'}
          onReset={() => setActiveFilter('all')}
          testID="tasks-empty-state"
        />
      ) : (
        <FlatList
          data={tasks}
          keyExtractor={(item) => item.id}
          renderItem={({ item }) => (
            <TaskItem
              task={item}
              onPress={handleTaskPress}
              testID={`task-item-${item.id}`}
            />
          )}
          contentContainerStyle={[
            styles.listContent,
            { paddingBottom: insets.bottom + 80 }
          ]}
          refreshControl={
            <RefreshControl
              refreshing={isRefreshing}
              onRefresh={handleRefresh}
              tintColor="#3B82F6"
              colors={['#3B82F6']}
            />
          }
          onEndReached={handleEndReached}
          onEndReachedThreshold={0.5}
          ListFooterComponent={
            isFetchingNextPage ? (
              <View style={styles.loadingMore}>
                <ActivityIndicator size="small" color="#3B82F6" />
              </View>
            ) : null
          }
          showsVerticalScrollIndicator={false}
        />
      )}
    </View>
  );
};

/**
 * Styles for the TaskScreen component
 */
const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  header: {
    paddingHorizontal: 16,
    paddingVertical: 12,
  },
  headerTitle: {
    fontSize: 28,
    fontWeight: '700',
    color: '#111827',
  },
  summaryContainer: {
    flexDirection: 'row',
    paddingHorizontal: 16,
    paddingVertical: 8,
    gap: 12,
  },
  listContent: {
    paddingTop: 16,
  },
  loadingMore: {
    paddingVertical: 16,
    alignItems: 'center',
  },
});

export default TaskScreen;
