/**
 * SummaryCard Component Tests
 * 
 * Unit tests for the SummaryCard component.
 * 
 * @module features/tasks/components/__tests__/SummaryCard.test
 */

import React from 'react';
import { render, fireEvent } from '@testing-library/react-native';
import { SummaryCard } from '../SummaryCard';

// Mock icons
jest.mock('lucide-react-native', () => ({
  Clock: () => null,
  AlertCircle: () => null,
  CheckCircle: () => null,
}));

describe('SummaryCard', () => {
  const mockOnPress = jest.fn();
  
  beforeEach(() => {
    jest.clearAllMocks();
  });
  
  describe('Rendering', () => {
    it('should render pending card correctly', () => {
      const { getByText } = render(
        <SummaryCard type="pending" count={5} onPress={mockOnPress} />
      );
      
      expect(getByText('5')).toBeTruthy();
      expect(getByText('Pending')).toBeTruthy();
    });
    
    it('should render overdue card correctly', () => {
      const { getByText } = render(
        <SummaryCard type="overdue" count={2} onPress={mockOnPress} />
      );
      
      expect(getByText('2')).toBeTruthy();
      expect(getByText('Overdue')).toBeTruthy();
    });
    
    it('should render completed card correctly', () => {
      const { getByText } = render(
        <SummaryCard type="completed" count={12} onPress={mockOnPress} />
      );
      
      expect(getByText('12')).toBeTruthy();
      expect(getByText('Completed')).toBeTruthy();
    });
    
    it('should display zero count', () => {
      const { getByText } = render(
        <SummaryCard type="pending" count={0} onPress={mockOnPress} />
      );
      
      expect(getByText('0')).toBeTruthy();
    });
    
    it('should display large numbers', () => {
      const { getByText } = render(
        <SummaryCard type="completed" count={999} onPress={mockOnPress} />
      );
      
      expect(getByText('999')).toBeTruthy();
    });
  });
  
  describe('Interactions', () => {
    it('should call onPress when pressed', () => {
      const { getByRole } = render(
        <SummaryCard type="pending" count={5} onPress={mockOnPress} />
      );
      
      const button = getByRole('button');
      fireEvent.press(button);
      
      expect(mockOnPress).toHaveBeenCalledTimes(1);
    });
    
    it('should work without onPress prop', () => {
      const { getByRole } = render(
        <SummaryCard type="pending" count={5} />
      );
      
      const button = getByRole('button');
      // Should not throw
      fireEvent.press(button);
    });
  });
  
  describe('Accessibility', () => {
    it('should have button role', () => {
      const { getByRole } = render(
        <SummaryCard type="pending" count={5} onPress={mockOnPress} />
      );
      
      expect(getByRole('button')).toBeTruthy();
    });
    
    it('should have accessible label with count and type', () => {
      const { getByLabelText } = render(
        <SummaryCard type="pending" count={5} onPress={mockOnPress} />
      );
      
      expect(getByLabelText('5 pending tasks')).toBeTruthy();
    });
    
    it('should have accessibility hint', () => {
      const { getByHintText } = render(
        <SummaryCard type="overdue" count={2} onPress={mockOnPress} />
      );
      
      expect(getByHintText('Tap to view all overdue tasks')).toBeTruthy();
    });
    
    it('should have correct label for overdue type', () => {
      const { getByLabelText } = render(
        <SummaryCard type="overdue" count={3} onPress={mockOnPress} />
      );
      
      expect(getByLabelText('3 overdue tasks')).toBeTruthy();
    });
    
    it('should have correct label for completed type', () => {
      const { getByLabelText } = render(
        <SummaryCard type="completed" count={10} onPress={mockOnPress} />
      );
      
      expect(getByLabelText('10 completed tasks')).toBeTruthy();
    });
  });
  
  describe('Styling', () => {
    it('should apply pending styles', () => {
      const { getByRole } = render(
        <SummaryCard type="pending" count={5} onPress={mockOnPress} />
      );
      
      const button = getByRole('button');
      expect(button.props.className).toContain('bg-amber-50');
    });
    
    it('should apply overdue styles', () => {
      const { getByRole } = render(
        <SummaryCard type="overdue" count={2} onPress={mockOnPress} />
      );
      
      const button = getByRole('button');
      expect(button.props.className).toContain('bg-red-50');
    });
    
    it('should apply completed styles', () => {
      const { getByRole } = render(
        <SummaryCard type="completed" count={10} onPress={mockOnPress} />
      );
      
      const button = getByRole('button');
      expect(button.props.className).toContain('bg-green-50');
    });
  });
  
  describe('Test ID', () => {
    it('should apply testID prop', () => {
      const { getByTestId } = render(
        <SummaryCard 
          type="pending" 
          count={5} 
          onPress={mockOnPress} 
          testID="summary-pending" 
        />
      );
      
      expect(getByTestId('summary-pending')).toBeTruthy();
    });
  });
});

export {};
