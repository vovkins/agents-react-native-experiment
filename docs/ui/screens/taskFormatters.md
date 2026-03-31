/**
 * Task Formatters
 * 
 * Utility functions for formatting task-related data.
 * Includes date formatting, priority indicators, and accessibility helpers.
 * 
 * @module features/tasks/utils/taskFormatters
 */

import { differenceInDays, format, formatRelative, isValid, parseISO } from 'date-fns';
import type { TaskStatus, TaskPriority, Task } from '../types/taskTypes';

/**
 * Format a due date based on the task status
 * 
 * @param dueDate - The due date
 * @param status - Current status of the task
 * @returns Formatted date string
 * 
 * @example
 * ```typescript
 * formatDueDate(new Date(), 'pending'); // 'Due today'
 * formatDueDate(tomorrow, 'pending'); // 'Due tomorrow'
 * formatDueDate(pastDate, 'overdue'); // 'Overdue by 3 days'
 * formatDueDate(pastDate, 'completed'); // 'Completed yesterday'
 * ```
 */
export function formatDueDate(dueDate: Date | string, status: TaskStatus): string {
  try {
    const date = typeof dueDate === 'string' ? parseISO(dueDate) : dueDate;
    
    if (!isValid(date)) {
      return 'Invalid date';
    }
    
    const now = new Date();
    const diff = differenceInDays(date, now);
    
    // If completed, show when it was completed
    if (status === 'completed') {
      return `Completed ${formatRelative(date, now)}`;
    }
    
    // If overdue, show how many days overdue
    if (status === 'overdue' || diff < 0) {
      const overdueDays = Math.abs(diff);
      return overdueDays === 1 
        ? 'Overdue by 1 day' 
        : `Overdue by ${overdueDays} days`;
    }
    
    // Due today
    if (diff === 0) {
      return 'Due today';
    }
    
    // Due tomorrow
    if (diff === 1) {
      return 'Due tomorrow';
    }
    
    // Due within a week
    if (diff > 1 && diff <= 7) {
      return `Due in ${diff} days`;
    }
    
    // Due later - show the date
    return `Due ${format(date, 'MMM d')}`;
  } catch (error) {
    console.error('Error formatting due date:', error);
    return 'Date unavailable';
  }
}

/**
 * Get Tailwind CSS classes for priority indicator
 * 
 * @param priority - Task priority level
 * @returns Tailwind CSS classes for the priority indicator
 * 
 * @example
 * ```typescript
 * const classes = getPriorityIndicator('urgent');
 * // Returns: 'border-l-4 border-l-error-500'
 * ```
 */
export function getPriorityIndicator(priority: TaskPriority): string {
  switch (priority) {
    case 'urgent':
      return 'border-l-4 border-l-error-500';
    case 'high':
      return 'border-l-4 border-l-warning-500';
    case 'normal':
      return 'border-l-4 border-l-transparent';
    case 'low':
      return 'border-l-4 border-l-transparent';
    default:
      return 'border-l-4 border-l-transparent';
  }
}

/**
 * Get text description for priority level
 * 
 * @param priority - Task priority level
 * @returns Human-readable priority text
 */
export function getPriorityText(priority: TaskPriority): string {
  const priorityMap: Record<TaskPriority, string> = {
    urgent: 'Urgent priority',
    high: 'High priority',
    normal: 'Normal priority',
    low: 'Low priority',
  };
  return priorityMap[priority] || 'Normal priority';
}

/**
 * Get text description for task status
 * 
 * @param status - Task status
 * @returns Human-readable status text
 */
export function getStatusText(status: TaskStatus): string {
  const statusMap: Record<TaskStatus, string> = {
    pending: 'Pending',
    in_progress: 'In progress',
    overdue: 'Overdue',
    completed: 'Completed',
  };
  return statusMap[status] || status;
}

/**
 * Generate accessibility label for a task
 * 
 * @param task - Task object
 * @returns Comprehensive accessibility label for screen readers
 * 
 * @example
 * ```typescript
 * const label = getTaskAccessibilityLabel(task);
 * // "Sign Investment Agreement. Please review and sign. Urgent priority. Pending. Due tomorrow. From Alexander Petrov"
 * ```
 */
export function getTaskAccessibilityLabel(task: Task): string {
  const parts: string[] = [];
  
  // Add title
  if (task.title) {
    parts.push(task.title);
  }
  
  // Add description (truncated for accessibility)
  if (task.description) {
    const truncatedDesc = task.description.length > 100 
      ? `${task.description.substring(0, 100)}...`
      : task.description;
    parts.push(truncatedDesc);
  }
  
  // Add priority for urgent and high priority tasks
  if (task.priority === 'urgent' || task.priority === 'high') {
    parts.push(getPriorityText(task.priority));
  }
  
  // Add status
  parts.push(getStatusText(task.status));
  
  // Add due date
  if (task.dueDate) {
    parts.push(`Due ${formatDueDate(task.dueDate, task.status)}`);
  }
  
  // Add manager info
  if (task.manager?.name) {
    parts.push(`From ${task.manager.name}`);
  }
  
  return parts.filter(Boolean).join('. ');
}

/**
 * Generate accessibility hint for a task
 * 
 * @param task - Task object
 * @returns Hint text describing what action will be taken
 */
export function getTaskAccessibilityHint(task: Task): string {
  if (task.status === 'completed') {
    return 'Completed. Tap to view details.';
  }
  
  if (task.actionLabel) {
    return `Tap to ${task.actionLabel.toLowerCase()}`;
  }
  
  return 'Tap to view task details';
}

/**
 * Format task count for display
 * 
 * @param count - Number of tasks
 * @returns Formatted count string
 */
export function formatTaskCount(count: number): string {
  if (count === 0) return '0';
  if (count > 99) return '99+';
  return String(count);
}

/**
 * Check if a task is overdue
 * 
 * @param dueDate - Task due date
 * @param status - Task status
 * @returns True if task is overdue
 */
export function isTaskOverdue(dueDate: Date | string | undefined, status: TaskStatus): boolean {
  if (!dueDate || status === 'completed') {
    return false;
  }
  
  try {
    const date = typeof dueDate === 'string' ? parseISO(dueDate) : dueDate;
    if (!isValid(date)) {
      return false;
    }
    
    const now = new Date();
    return differenceInDays(date, now) < 0;
  } catch {
    return false;
  }
}

/**
 * Get relative time from now
 * 
 * @param date - Date to compare
 * @returns Relative time string (e.g., "2 hours ago", "in 3 days")
 */
export function getRelativeTime(date: Date | string): string {
  try {
    const parsedDate = typeof date === 'string' ? parseISO(date) : date;
    
    if (!isValid(parsedDate)) {
      return 'Unknown time';
    }
    
    return formatRelative(parsedDate, new Date());
  } catch {
    return 'Unknown time';
  }
}

/**
 * Truncate text to a maximum length
 * 
 * @param text - Text to truncate
 * @param maxLength - Maximum length (default: 100)
 * @returns Truncated text with ellipsis if needed
 */
export function truncateText(text: string, maxLength: number = 100): string {
  if (!text || text.length <= maxLength) {
    return text;
  }
  
  return `${text.substring(0, maxLength).trim()}...`;
}
