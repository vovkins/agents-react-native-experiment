/**
 * Task Types
 * 
 * Type definitions for the Task Management feature.
 * These types define the structure of tasks, their statuses, priorities,
 * and related entities.
 * 
 * @module features/tasks/types
 */

/**
 * Types of tasks that can be assigned to a user
 */
export type TaskType = 
  | 'document_request'
  | 'signature_request'
  | 'meeting_request'
  | 'information_request'
  | 'investment_decision'
  | 'kyc_update'
  | 'review_request';

/**
 * Status of a task
 */
export type TaskStatus = 'pending' | 'in_progress' | 'overdue' | 'completed';

/**
 * Priority level of a task
 */
export type TaskPriority = 'urgent' | 'high' | 'normal' | 'low';

/**
 * Manager assigned to a task
 */
export interface Manager {
  /** Unique identifier for the manager */
  id: string;
  /** Full name of the manager */
  name: string;
  /** Optional avatar URL */
  avatar?: string;
}

/**
 * Product related to a task
 */
export interface RelatedProduct {
  /** Unique identifier for the product */
  id: string;
  /** Name of the product */
  name: string;
}

/**
 * Task entity representing an action item for the user
 */
export interface Task {
  /** Unique identifier for the task */
  id: string;
  /** Title of the task */
  title: string;
  /** Optional description of the task */
  description?: string;
  /** Type of the task */
  type: TaskType;
  /** Current status of the task */
  status: TaskStatus;
  /** Priority level of the task */
  priority: TaskPriority;
  /** Due date for the task */
  dueDate?: Date;
  /** Date when the task was created */
  createdAt: Date;
  /** Date when the task was completed */
  completedAt?: Date;
  /** Manager who assigned the task */
  manager?: Manager;
  /** Product related to the task */
  relatedProduct?: RelatedProduct;
  /** Label for the action button */
  actionLabel?: string;
  /** Type of action to perform */
  actionType?: 'navigate' | 'modal' | 'external';
}

/**
 * Summary counts for tasks by status
 */
export interface TasksSummary {
  /** Number of pending tasks */
  pending: number;
  /** Number of overdue tasks */
  overdue: number;
  /** Number of completed tasks */
  completed: number;
}

/**
 * Filters for querying tasks
 */
export interface TasksFilters {
  /** Filter by status */
  status?: 'pending' | 'overdue' | 'completed' | 'all';
  /** Filter by task type */
  type?: TaskType;
  /** Filter by priority */
  priority?: TaskPriority;
  /** Search query */
  search?: string;
  /** Page number for pagination */
  page?: number;
  /** Number of items per page */
  pageSize?: number;
}

/**
 * Pagination information
 */
export interface Pagination {
  /** Current page number */
  page: number;
  /** Number of items per page */
  pageSize: number;
  /** Total number of items */
  total: number;
  /** Whether there are more items to load */
  hasMore: boolean;
}

/**
 * Response from the tasks API
 */
export interface TasksResponse {
  /** List of tasks */
  tasks: Task[];
  /** Pagination information */
  pagination: Pagination;
  /** Summary counts */
  summary: TasksSummary;
}
