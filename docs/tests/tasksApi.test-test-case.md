/**
 * tasksApi Tests
 * 
 * Unit tests for the tasks API functions.
 * 
 * @module features/tasks/api/__tests__/tasksApi.test
 */

import { tasksApi } from '../tasksApi';
import { apiClient } from '@shared/api';
import type { TasksResponse, Task, TasksFilters } from '../../types/taskTypes';

// Mock the API client
jest.mock('@shared/api', () => ({
  apiClient: {
    get: jest.fn(),
    post: jest.fn(),
  },
}));

describe('tasksApi', () => {
  const mockApiClient = apiClient as jest.Mocked<typeof apiClient>;
  
  beforeEach(() => {
    jest.clearAllMocks();
  });
  
  describe('getAll', () => {
    const mockResponse: TasksResponse = {
      tasks: [
        {
          id: 'task-1',
          title: 'Test Task',
          type: 'document_request',
          status: 'pending',
          priority: 'normal',
          createdAt: new Date('2024-01-01'),
        },
      ],
      pagination: {
        page: 1,
        pageSize: 20,
        total: 1,
        hasMore: false,
      },
      summary: {
        pending: 1,
        overdue: 0,
        completed: 0,
      },
    };
    
    it('should fetch tasks without filters', async () => {
      mockApiClient.get.mockResolvedValueOnce({ data: mockResponse });
      
      const result = await tasksApi.getAll();
      
      expect(mockApiClient.get).toHaveBeenCalledWith('/tasks');
      expect(result).toEqual(mockResponse);
    });
    
    it('should fetch tasks with status filter', async () => {
      mockApiClient.get.mockResolvedValueOnce({ data: mockResponse });
      
      const filters: TasksFilters = { status: 'pending' };
      await tasksApi.getAll(filters);
      
      expect(mockApiClient.get).toHaveBeenCalledWith(
        expect.stringContaining('status=pending')
      );
    });
    
    it('should not add status to params when status is "all"', async () => {
      mockApiClient.get.mockResolvedValueOnce({ data: mockResponse });
      
      const filters: TasksFilters = { status: 'all' };
      await tasksApi.getAll(filters);
      
      expect(mockApiClient.get).toHaveBeenCalledWith('/tasks');
    });
    
    it('should fetch tasks with type filter', async () => {
      mockApiClient.get.mockResolvedValueOnce({ data: mockResponse });
      
      const filters: TasksFilters = { type: 'signature_request' };
      await tasksApi.getAll(filters);
      
      expect(mockApiClient.get).toHaveBeenCalledWith(
        expect.stringContaining('type=signature_request')
      );
    });
    
    it('should fetch tasks with priority filter', async () => {
      mockApiClient.get.mockResolvedValueOnce({ data: mockResponse });
      
      const filters: TasksFilters = { priority: 'urgent' };
      await tasksApi.getAll(filters);
      
      expect(mockApiClient.get).toHaveBeenCalledWith(
        expect.stringContaining('priority=urgent')
      );
    });
    
    it('should fetch tasks with search query', async () => {
      mockApiClient.get.mockResolvedValueOnce({ data: mockResponse });
      
      const filters: TasksFilters = { search: 'agreement' };
      await tasksApi.getAll(filters);
      
      expect(mockApiClient.get).toHaveBeenCalledWith(
        expect.stringContaining('search=agreement')
      );
    });
    
    it('should fetch tasks with pagination', async () => {
      mockApiClient.get.mockResolvedValueOnce({ data: mockResponse });
      
      const filters: TasksFilters = { page: 2, pageSize: 10 };
      await tasksApi.getAll(filters);
      
      expect(mockApiClient.get).toHaveBeenCalledWith(
        expect.stringContaining('page=2')
      );
      expect(mockApiClient.get).toHaveBeenCalledWith(
        expect.stringContaining('pageSize=10')
      );
    });
    
    it('should combine multiple filters', async () => {
      mockApiClient.get.mockResolvedValueOnce({ data: mockResponse });
      
      const filters: TasksFilters = {
        status: 'pending',
        priority: 'urgent',
        page: 1,
        pageSize: 20,
      };
      await tasksApi.getAll(filters);
      
      const callUrl = mockApiClient.get.mock.calls[0][0];
      expect(callUrl).toContain('status=pending');
      expect(callUrl).toContain('priority=urgent');
      expect(callUrl).toContain('page=1');
      expect(callUrl).toContain('pageSize=20');
    });
    
    it('should handle API errors', async () => {
      const error = new Error('Network error');
      mockApiClient.get.mockRejectedValueOnce(error);
      
      await expect(tasksApi.getAll()).rejects.toThrow('Network error');
    });
  });
  
  describe('getById', () => {
    const mockTask: Task = {
      id: 'task-1',
      title: 'Test Task',
      type: 'document_request',
      status: 'pending',
      priority: 'normal',
      createdAt: new Date('2024-01-01'),
    };
    
    it('should fetch single task by ID', async () => {
      mockApiClient.get.mockResolvedValueOnce({ data: mockTask });
      
      const result = await tasksApi.getById('task-1');
      
      expect(mockApiClient.get).toHaveBeenCalledWith('/tasks/task-1');
      expect(result).toEqual(mockTask);
    });
    
    it('should handle not found error', async () => {
      const error = new Error('Not found');
      mockApiClient.get.mockRejectedValueOnce(error);
      
      await expect(tasksApi.getById('non-existent')).rejects.toThrow('Not found');
    });
  });
  
  describe('complete', () => {
    const mockCompletedTask: Task = {
      id: 'task-1',
      title: 'Test Task',
      type: 'document_request',
      status: 'completed',
      priority: 'normal',
      createdAt: new Date('2024-01-01'),
      completedAt: new Date('2024-01-15'),
    };
    
    it('should complete a task', async () => {
      mockApiClient.post.mockResolvedValueOnce({ data: mockCompletedTask });
      
      const result = await tasksApi.complete('task-1');
      
      expect(mockApiClient.post).toHaveBeenCalledWith('/tasks/task-1/complete');
      expect(result.status).toBe('completed');
      expect(result.completedAt).toBeTruthy();
    });
    
    it('should handle completion error', async () => {
      const error = new Error('Cannot complete task');
      mockApiClient.post.mockRejectedValueOnce(error);
      
      await expect(tasksApi.complete('task-1')).rejects.toThrow('Cannot complete task');
    });
  });
  
  describe('startProgress', () => {
    const mockInProgressTask: Task = {
      id: 'task-1',
      title: 'Test Task',
      type: 'document_request',
      status: 'in_progress',
      priority: 'normal',
      createdAt: new Date('2024-01-01'),
    };
    
    it('should start task progress', async () => {
      mockApiClient.post.mockResolvedValueOnce({ data: mockInProgressTask });
      
      const result = await tasksApi.startProgress('task-1');
      
      expect(mockApiClient.post).toHaveBeenCalledWith('/tasks/task-1/start');
      expect(result.status).toBe('in_progress');
    });
  });
  
  describe('dismiss', () => {
    it('should dismiss a task', async () => {
      mockApiClient.post.mockResolvedValueOnce({});
      
      await tasksApi.dismiss('task-1');
      
      expect(mockApiClient.post).toHaveBeenCalledWith('/tasks/task-1/dismiss');
    });
    
    it('should handle dismiss error', async () => {
      const error = new Error('Cannot dismiss task');
      mockApiClient.post.mockRejectedValueOnce(error);
      
      await expect(tasksApi.dismiss('task-1')).rejects.toThrow('Cannot dismiss task');
    });
  });
  
  describe('URL Encoding', () => {
    it('should encode special characters in search query', async () => {
      mockApiClient.get.mockResolvedValueOnce({
        data: { tasks: [], pagination: { page: 1, pageSize: 20, total: 0, hasMore: false }, summary: { pending: 0, overdue: 0, completed: 0 } }
      });
      
      await tasksApi.getAll({ search: 'test & more' });
      
      const callUrl = mockApiClient.get.mock.calls[0][0];
      expect(callUrl).toContain('test%20%26%20more');
    });
  });
});

export {};
