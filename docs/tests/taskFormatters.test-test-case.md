/**
 * taskFormatters Utility Tests
 * 
 * Unit tests for task formatting utility functions.
 * 
 * @module features/tasks/utils/__tests__/taskFormatters.test
 */

import {
  formatDueDate,
  getPriorityIndicator,
  getPriorityText,
  getStatusText,
  getTaskAccessibilityLabel,
  getTaskAccessibilityHint,
  formatTaskCount,
  isTaskOverdue,
  truncateText,
} from '../taskFormatters';
import type { Task, TaskStatus, TaskPriority } from '../../types/taskTypes';

describe('taskFormatters', () => {
  describe('formatDueDate', () => {
    const now = new Date();
    
    beforeEach(() => {
      jest.useFakeTimers();
      jest.setSystemTime(new Date('2024-01-15T12:00:00Z'));
    });
    
    afterEach(() => {
      jest.useRealTimers();
    });
    
    describe('Completed tasks', () => {
      it('should format completed task date', () => {
        const yesterday = new Date('2024-01-14T12:00:00Z');
        const result = formatDueDate(yesterday, 'completed');
        expect(result).toContain('Completed');
      });
    });
    
    describe('Overdue tasks', () => {
      it('should show overdue message for past dates', () => {
        const pastDate = new Date('2024-01-10');
        const result = formatDueDate(pastDate, 'overdue');
        expect(result).toContain('Overdue by');
      });
      
      it('should show correct number of days overdue', () => {
        const fiveDaysAgo = new Date('2024-01-10');
        const result = formatDueDate(fiveDaysAgo, 'overdue');
        expect(result).toBe('Overdue by 5 days');
      });
      
      it('should show 1 day overdue', () => {
        const yesterday = new Date('2024-01-14');
        const result = formatDueDate(yesterday, 'overdue');
        expect(result).toBe('Overdue by 1 day');
      });
    });
    
    describe('Due today', () => {
      it('should show "Due today" for today', () => {
        const today = new Date('2024-01-15');
        const result = formatDueDate(today, 'pending');
        expect(result).toBe('Due today');
      });
    });
    
    describe('Due tomorrow', () => {
      it('should show "Due tomorrow" for tomorrow', () => {
        const tomorrow = new Date('2024-01-16');
        const result = formatDueDate(tomorrow, 'pending');
        expect(result).toBe('Due tomorrow');
      });
    });
    
    describe('Due within a week', () => {
      it('should show "Due in X days" for dates within a week', () => {
        const inThreeDays = new Date('2024-01-18');
        const result = formatDueDate(inThreeDays, 'pending');
        expect(result).toBe('Due in 3 days');
      });
      
      it('should show "Due in 7 days"', () => {
        const inSevenDays = new Date('2024-01-22');
        const result = formatDueDate(inSevenDays, 'pending');
        expect(result).toBe('Due in 7 days');
      });
    });
    
    describe('Due later', () => {
      it('should show formatted date for dates beyond a week', () => {
        const laterDate = new Date('2024-02-15');
        const result = formatDueDate(laterDate, 'pending');
        expect(result).toContain('Due');
        expect(result).toContain('Feb');
      });
    });
    
    describe('Invalid dates', () => {
      it('should handle invalid date objects', () => {
        const invalidDate = new Date('invalid');
        const result = formatDueDate(invalidDate, 'pending');
        expect(result).toBe('Invalid date');
      });
      
      it('should handle string dates', () => {
        const dateString = '2024-01-20';
        const result = formatDueDate(dateString, 'pending');
        expect(result).toContain('Due');
      });
    });
  });
  
  describe('getPriorityIndicator', () => {
    it('should return error border for urgent priority', () => {
      const result = getPriorityIndicator('urgent');
      expect(result).toContain('border-l-error-500');
    });
    
    it('should return warning border for high priority', () => {
      const result = getPriorityIndicator('high');
      expect(result).toContain('border-l-warning-500');
    });
    
    it('should return transparent border for normal priority', () => {
      const result = getPriorityIndicator('normal');
      expect(result).toContain('border-l-transparent');
    });
    
    it('should return transparent border for low priority', () => {
      const result = getPriorityIndicator('low');
      expect(result).toContain('border-l-transparent');
    });
  });
  
  describe('getPriorityText', () => {
    it('should return "Urgent priority" for urgent', () => {
      expect(getPriorityText('urgent')).toBe('Urgent priority');
    });
    
    it('should return "High priority" for high', () => {
      expect(getPriorityText('high')).toBe('High priority');
    });
    
    it('should return "Normal priority" for normal', () => {
      expect(getPriorityText('normal')).toBe('Normal priority');
    });
    
    it('should return "Low priority" for low', () => {
      expect(getPriorityText('low')).toBe('Low priority');
    });
  });
  
  describe('getStatusText', () => {
    it('should return "Pending" for pending status', () => {
      expect(getStatusText('pending')).toBe('Pending');
    });
    
    it('should return "In progress" for in_progress status', () => {
      expect(getStatusText('in_progress')).toBe('In progress');
    });
    
    it('should return "Overdue" for overdue status', () => {
      expect(getStatusText('overdue')).toBe('Overdue');
    });
    
    it('should return "Completed" for completed status', () => {
      expect(getStatusText('completed')).toBe('Completed');
    });
  });
  
  describe('getTaskAccessibilityLabel', () => {
    const createMockTask = (overrides?: Partial<Task>): Task => ({
      id: 'task-1',
      title: 'Test Task',
      type: 'document_request',
      status: 'pending',
      priority: 'normal',
      createdAt: new Date(),
      ...overrides,
    });
    
    it('should include task title', () => {
      const task = createMockTask({ title: 'Sign Agreement' });
      const result = getTaskAccessibilityLabel(task);
      expect(result).toContain('Sign Agreement');
    });
    
    it('should include description when present', () => {
      const task = createMockTask({ description: 'Please sign the document' });
      const result = getTaskAccessibilityLabel(task);
      expect(result).toContain('Please sign the document');
    });
    
    it('should include urgent priority text', () => {
      const task = createMockTask({ priority: 'urgent' });
      const result = getTaskAccessibilityLabel(task);
      expect(result).toContain('Urgent priority');
    });
    
    it('should include high priority text', () => {
      const task = createMockTask({ priority: 'high' });
      const result = getTaskAccessibilityLabel(task);
      expect(result).toContain('High priority');
    });
    
    it('should not include normal priority text', () => {
      const task = createMockTask({ priority: 'normal' });
      const result = getTaskAccessibilityLabel(task);
      expect(result).not.toContain('Normal priority');
    });
    
    it('should include status', () => {
      const task = createMockTask({ status: 'overdue' });
      const result = getTaskAccessibilityLabel(task);
      expect(result).toContain('Overdue');
    });
    
    it('should include due date', () => {
      const task = createMockTask({ 
        dueDate: new Date('2024-01-20'),
        status: 'pending' 
      });
      const result = getTaskAccessibilityLabel(task);
      expect(result).toContain('Due');
    });
    
    it('should include manager name', () => {
      const task = createMockTask({ 
        manager: { id: 'm1', name: 'Alexander Petrov' } 
      });
      const result = getTaskAccessibilityLabel(task);
      expect(result).toContain('From Alexander Petrov');
    });
    
    it('should combine all parts correctly', () => {
      const task = createMockTask({
        title: 'Sign Agreement',
        description: 'Review and sign',
        priority: 'urgent',
        status: 'pending',
        manager: { id: 'm1', name: 'John Doe' },
      });
      const result = getTaskAccessibilityLabel(task);
      expect(result).toContain('Sign Agreement');
      expect(result).toContain('Review and sign');
      expect(result).toContain('Urgent priority');
      expect(result).toContain('Pending');
      expect(result).toContain('From John Doe');
    });
  });
  
  describe('getTaskAccessibilityHint', () => {
    const createMockTask = (overrides?: Partial<Task>): Task => ({
      id: 'task-1',
      title: 'Test Task',
      type: 'document_request',
      status: 'pending',
      priority: 'normal',
      createdAt: new Date(),
      ...overrides,
    });
    
    it('should return completed hint for completed tasks', () => {
      const task = createMockTask({ status: 'completed' });
      const result = getTaskAccessibilityHint(task);
      expect(result).toBe('Completed. Tap to view details.');
    });
    
    it('should use actionLabel when present', () => {
      const task = createMockTask({ 
        status: 'pending',
        actionLabel: 'Sign Now' 
      });
      const result = getTaskAccessibilityHint(task);
      expect(result).toBe('Tap to sign now');
    });
    
    it('should return default hint when no actionLabel', () => {
      const task = createMockTask({ status: 'pending' });
      const result = getTaskAccessibilityHint(task);
      expect(result).toBe('Tap to view task details');
    });
  });
  
  describe('formatTaskCount', () => {
    it('should return "0" for zero', () => {
      expect(formatTaskCount(0)).toBe('0');
    });
    
    it('should return string representation for counts under 100', () => {
      expect(formatTaskCount(5)).toBe('5');
      expect(formatTaskCount(50)).toBe('50');
      expect(formatTaskCount(99)).toBe('99');
    });
    
    it('should return "99+" for counts over 99', () => {
      expect(formatTaskCount(100)).toBe('99+');
      expect(formatTaskCount(150)).toBe('99+');
      expect(formatTaskCount(1000)).toBe('99+');
    });
  });
  
  describe('isTaskOverdue', () => {
    beforeEach(() => {
      jest.useFakeTimers();
      jest.setSystemTime(new Date('2024-01-15T12:00:00Z'));
    });
    
    afterEach(() => {
      jest.useRealTimers();
    });
    
    it('should return false for completed tasks', () => {
      const pastDate = new Date('2024-01-10');
      const result = isTaskOverdue(pastDate, 'completed');
      expect(result).toBe(false);
    });
    
    it('should return false when no due date', () => {
      const result = isTaskOverdue(undefined, 'pending');
      expect(result).toBe(false);
    });
    
    it('should return true for past due dates', () => {
      const pastDate = new Date('2024-01-10');
      const result = isTaskOverdue(pastDate, 'pending');
      expect(result).toBe(true);
    });
    
    it('should return false for future due dates', () => {
      const futureDate = new Date('2024-01-20');
      const result = isTaskOverdue(futureDate, 'pending');
      expect(result).toBe(false);
    });
    
    it('should return false for today', () => {
      const today = new Date('2024-01-15');
      const result = isTaskOverdue(today, 'pending');
      expect(result).toBe(false);
    });
    
    it('should handle string dates', () => {
      const pastDate = '2024-01-10';
      const result = isTaskOverdue(pastDate, 'pending');
      expect(result).toBe(true);
    });
    
    it('should return false for invalid dates', () => {
      const invalidDate = new Date('invalid');
      const result = isTaskOverdue(invalidDate, 'pending');
      expect(result).toBe(false);
    });
  });
  
  describe('truncateText', () => {
    it('should return original text if under max length', () => {
      const text = 'Short text';
      const result = truncateText(text, 100);
      expect(result).toBe('Short text');
    });
    
    it('should return original text if exactly max length', () => {
      const text = 'Exactly twenty chars';
      const result = truncateText(text, 20);
      expect(result).toBe('Exactly twenty chars');
    });
    
    it('should truncate and add ellipsis if over max length', () => {
      const text = 'This is a longer text that needs to be truncated';
      const result = truncateText(text, 20);
      expect(result).toBe('This is a longer te...');
    });
    
    it('should handle empty string', () => {
      const result = truncateText('', 100);
      expect(result).toBe('');
    });
    
    it('should use default max length of 100', () => {
      const longText = 'a'.repeat(150);
      const result = truncateText(longText);
      expect(result.length).toBe(103); // 100 chars + '...'
    });
    
    it('should trim whitespace before adding ellipsis', () => {
      const text = 'This text has trailing spaces     ';
      const result = truncateText(text, 20);
      expect(result).toBe('This text has trail...');
    });
  });
});

export {};
