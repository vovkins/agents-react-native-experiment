/**
 * TaskItem Component
 * 
 * Displays a single task item in the task list.
 * Shows task icon, title, description, due date, manager, and status badge.
 * 
 * @module features/tasks/components/TaskItem
 */

import React from 'react';
import { TouchableOpacity, Text, View, StyleSheet } from 'react-native';
import { ChevronRight } from 'lucide-react-native';
import Animated, { FadeInRight } from 'react-native-reanimated';
import { TaskIcon } from './TaskIcon';
import { TaskStatusBadge } from './TaskStatusBadge';
import { 
  formatDueDate, 
  getPriorityIndicator, 
  getTaskAccessibilityLabel,
  getTaskAccessibilityHint,
  truncateText 
} from '../utils/taskFormatters';
import type { Task } from '../types/taskTypes';

/**
 * Props for the TaskItem component
 */
interface TaskItemProps {
  /** Task data */
  task: Task;
  /** Callback when task is pressed */
  onPress: (task: Task) => void;
  /** Test ID for testing */
  testID?: string;
}

/**
 * TaskItem displays a single task with all relevant information.
 * 
 * @example
 * ```tsx
 * <TaskItem
 *   task={task}
 *   onPress={(task) => navigation.navigate('TaskDetail', { taskId: task.id })}
 * />
 * ```
 */
export const TaskItem: React.FC<TaskItemProps> = ({
  task,
  onPress,
  testID,
}) => {
  const priorityStyle = getPriorityIndicator(task.priority);
  
  const accessibilityLabel = getTaskAccessibilityLabel(task);
  const accessibilityHint = getTaskAccessibilityHint(task);
  
  return (
    <Animated.View entering={FadeInRight.duration(300).springify()}>
      <TouchableOpacity
        onPress={() => onPress(task)}
        className={`
          flex-row items-start bg-white dark:bg-gray-900 px-4 py-3 
          border-b border-gray-100 dark:border-gray-800 ${priorityStyle}
        `}
        accessible={true}
        accessibilityLabel={accessibilityLabel}
        accessibilityHint={accessibilityHint}
        accessibilityRole="button"
        testID={testID}
        activeOpacity={0.7}
        style={styles.container}
      >
        {/* Task Type Icon */}
        <TaskIcon type={task.type} testID={`task-icon-${task.id}`} />
        
        {/* Content */}
        <View className="flex-1 ml-3" style={styles.content}>
          {/* Title */}
          <Text
            className="text-base font-medium text-gray-900 dark:text-gray-100"
            numberOfLines={1}
            style={styles.title}
          >
            {task.title}
          </Text>
          
          {/* Description */}
          {task.description && (
            <Text
              className="text-sm text-gray-600 dark:text-gray-400 mt-0.5"
              numberOfLines={2}
              style={styles.description}
            >
              {truncateText(task.description, 100)}
            </Text>
          )}
          
          {/* Meta Info */}
          <View className="flex-row items-center mt-1.5" style={styles.meta}>
            {/* Due Date */}
            {task.dueDate && (
              <Text 
                className="text-xs text-gray-500 dark:text-gray-500"
                style={styles.metaText}
              >
                {formatDueDate(task.dueDate, task.status)}
              </Text>
            )}
            
            {/* Separator */}
            {task.dueDate && task.manager && (
              <Text className="text-xs text-gray-300 mx-1">•</Text>
            )}
            
            {/* Manager */}
            {task.manager && (
              <Text 
                className="text-xs text-blue-600 dark:text-blue-400"
                style={styles.metaText}
              >
                From {task.manager.name}
              </Text>
            )}
          </View>
        </View>
        
        {/* Status & Chevron */}
        <View className="flex-row items-center ml-2" style={styles.rightContent}>
          <TaskStatusBadge 
            status={task.status} 
            testID={`status-badge-${task.id}`}
          />
          <ChevronRight 
            size={20} 
            color="#9CA3AF"
            style={styles.chevron}
          />
        </View>
      </TouchableOpacity>
    </Animated.View>
  );
};

/**
 * Styles for the TaskItem component
 */
const styles = StyleSheet.create({
  container: {
    minHeight: 44,
  },
  content: {
    flex: 1,
  },
  title: {
    fontWeight: '600',
  },
  description: {
    lineHeight: 20,
  },
  meta: {
    alignItems: 'center',
    marginTop: 6,
  },
  metaText: {
    fontSize: 12,
  },
  rightContent: {
    alignItems: 'center',
  },
  chevron: {
    marginLeft: 8,
  },
});

export default TaskItem;
