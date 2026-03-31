/**
 * useTasks Hook Tests
 * 
 * Unit tests for the useTasks custom hooks.
 * 
 * @module features/tasks/hooks/__tests__/useTasks.test
 */

import { renderHook, waitFor, act } from '@testing-library/react-native';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import React from 'react';
import { useTasks, useTasksInfinite, useTask, useCompleteTask } from '../useTasks';
import { tasksApi } from '../../api/tasksApi';
import type { Task, TasksResponse } from '../../types/taskTypes';

// Mock the API
jest.mock('../../api/tasksApi');

// Create wrapper with QueryClient
const createWrapper = () => {
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: {
        retry: false,
        gcTime: 0,
      },
    },
  });
  
  return ({ children }: { children: React.ReactNode }) => (
    <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>
  );
};

describe('useTasks', () => {
  const mockTasksResponse: TasksResponse = {
    tasks: [
      {
        id: 'task-1',
        title: 'Sign Agreement',
        type: 'signature_request',
        status: 'pending',
        priority: 'urgent',
        createdAt: new Date('2024-01-01'),
      },
      {
        id: 'task-2',
        title: 'Review Portfolio',
        type: 'review_request',
        status: 'completed',
        priority: 'normal',
        createdAt: new Date('2024-01-02'),
      },
    ],
    pagination: {
      page: 1,
      pageSize: 20,
      total: 2,
      hasMore: false,
    },
    summary: {
      pending: 1,
      overdue: 0,
      completed: 1,
    },
  };
  
  beforeEach(() => {
    jest.clearAllMocks();
    (tasksApi.getAll as jest.Mock).mockResolvedValue(mockTasksResponse);
  });
  
  describe('useTasks', () => {
    it('should fetch tasks successfully', async () => {
      const wrapper = createWrapper();
      const { result } = renderHook(() => useTasks(), { wrapper });
      
      await waitFor(() => expect(result.current.isSuccess).toBe(true));
      
      expect(result.current.data).toEqual(mockTasksResponse);
      expect(tasksApi.getAll).toHaveBeenCalledWith(undefined);
    });
    
    it('should fetch tasks with filters', async () => {
      const wrapper = createWrapper();
      const filters = { status: 'pending' as const };
      
      const { result } = renderHook(() => useTasks(filters), { wrapper });
      
      await waitFor(() => expect(result.current.isSuccess).toBe(true));
      
      expect(tasksApi.getAll).toHaveBeenCalledWith(filters);
    });
    
    it('should handle errors', async () => {
      const error = new Error('Network error');
      (tasksApi.getAll as jest.Mock).mockRejectedValue(error);
      
      const wrapper = createWrapper();
      const { result } = renderHook(() => useTasks(), { wrapper });
      
      await waitFor(() => expect(result.current.isError).toBe(true));
      
      expect(result.current.error).toBe(error);
    });
    
    it('should cache results', async () => {
      const wrapper = createWrapper();
      
      const { result } = renderHook(() => useTasks(), { wrapper });
      
      await waitFor(() => expect(result.current.isSuccess).toBe(true));
      
      // Second call should use cache
      const { result: result2 } = renderHook(() => useTasks(), { wrapper });
      
      await waitFor(() => expect(result2.current.isSuccess).toBe(true));
      
      // API should only be called once due to caching
      expect(tasksApi.getAll).toHaveBeenCalledTimes(1);
    });
  });
  
  describe('useTasksInfinite', () => {
    it('should fetch first page', async () => {
      const wrapper = createWrapper();
      const { result } = renderHook(() => useTasksInfinite(), { wrapper });
      
      await waitFor(() => expect(result.current.isSuccess).toBe(true));
      
      expect(result.current.data?.pages).toHaveLength(1);
      expect(result.current.data?.pages[0]).toEqual(mockTasksResponse);
    });
    
    it('should flatten tasks from all pages', async () => {
      const wrapper = createWrapper();
      const { result } = renderHook(() => useTasksInfinite(), { wrapper });
      
      await waitFor(() => expect(result.current.isSuccess).toBe(true));
      
      const tasks = result.current.data?.pages.flatMap(page => page.tasks) ?? [];
      expect(tasks).toHaveLength(2);
    });
    
    it('should indicate when more pages available', async () => {
      const responseWithMore: TasksResponse = {
        ...mockTasksResponse,
        pagination: { ...mockTasksResponse.pagination, hasMore: true },
      };
      (tasksApi.getAll as jest.Mock).mockResolvedValue(responseWithMore);
      
      const wrapper = createWrapper();
      const { result } = renderHook(() => useTasksInfinite(), { wrapper });
      
      await waitFor(() => expect(result.current.isSuccess).toBe(true));
      
      expect(result.current.hasNextPage).toBe(true);
    });
    
    it('should fetch next page when fetchNextPage is called', async () => {
      const responseWithMore: TasksResponse = {
        ...mockTasksResponse,
        pagination: { ...mockTasksResponse.pagination, hasMore: true, page: 1 },
      };
      const secondPageResponse: TasksResponse = {
        ...mockTasksResponse,
        tasks: [{ ...mockTasksResponse.tasks[0], id: 'task-3' }],
        pagination: { ...mockTasksResponse.pagination, page: 2, hasMore: false },
      };
      
      (tasksApi.getAll as jest.Mock)
        .mockResolvedValueOnce(responseWithMore)
        .mockResolvedValueOnce(secondPageResponse);
      
      const wrapper = createWrapper();
      const { result } = renderHook(() => useTasksInfinite(), { wrapper });
      
      await waitFor(() => expect(result.current.isSuccess).toBe(true));
      
      act(() => {
        result.current.fetchNextPage();
      });
      
      await waitFor(() => expect(result.current.data?.pages).toHaveLength(2));
      
      expect(tasksApi.getAll).toHaveBeenCalledTimes(2);
      expect(tasksApi.getAll).toHaveBeenNthCalledWith(2, expect.objectContaining({ page: 2 }));
    });
  });
  
  describe('useTask', () => {
    const mockTask: Task = {
      id: 'task-1',
      title: 'Sign Agreement',
      type: 'signature_request',
      status: 'pending',
      priority: 'urgent',
      createdAt: new Date('2024-01-01'),
    };
    
    beforeEach(() => {
      (tasksApi.getById as jest.Mock).mockResolvedValue(mockTask);
    });
    
    it('should fetch a single task by ID', async () => {
      const wrapper = createWrapper();
      const { result } = renderHook(() => useTask('task-1'), { wrapper });
      
      await waitFor(() => expect(result.current.isSuccess).toBe(true));
      
      expect(result.current.data).toEqual(mockTask);
      expect(tasksApi.getById).toHaveBeenCalledWith('task-1');
    });
    
    it('should not fetch when ID is empty', async () => {
      const wrapper = createWrapper();
      const { result } = renderHook(() => useTask(''), { wrapper });
      
      expect(result.current.isFetching).toBe(false);
      expect(tasksApi.getById).not.toHaveBeenCalled();
    });
  });
  
  describe('useCompleteTask', () => {
    const mockCompletedTask: Task = {
      id: 'task-1',
      title: 'Sign Agreement',
      type: 'signature_request',
      status: 'completed',
      priority: 'urgent',
      createdAt: new Date('2024-01-01'),
      completedAt: new Date('2024-01-15'),
    };
    
    beforeEach(() => {
      (tasksApi.complete as jest.Mock).mockResolvedValue(mockCompletedTask);
    });
    
    it('should complete a task', async () => {
      const wrapper = createWrapper();
      const { result } = renderHook(() => useCompleteTask(), { wrapper });
      
      await act(async () => {
        await result.current.mutateAsync('task-1');
      });
      
      expect(tasksApi.complete).toHaveBeenCalledWith('task-1');
    });
    
    it('should invalidate queries on success', async () => {
      const queryClient = new QueryClient({
        defaultOptions: { queries: { retry: false } },
      });
      
      const invalidateSpy = jest.spyOn(queryClient, 'invalidateQueries');
      
      const wrapper = ({ children }: { children: React.ReactNode }) => (
        <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>
      );
      
      const { result } = renderHook(() => useCompleteTask(), { wrapper });
      
      await act(async () => {
        await result.current.mutateAsync('task-1');
      });
      
      expect(invalidateSpy).toHaveBeenCalled();
    });
  });
});

export {};
