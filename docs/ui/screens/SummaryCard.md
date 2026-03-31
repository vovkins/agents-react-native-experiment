/**
 * SummaryCard Component
 * 
 * Displays a summary card with count and icon for task status.
 * Used in the TaskScreen header to show pending, overdue, and completed counts.
 * 
 * @module features/tasks/components/SummaryCard
 */

import React from 'react';
import { TouchableOpacity, Text, View, StyleSheet } from 'react-native';
import { Clock, AlertCircle, CheckCircle } from 'lucide-react-native';

/**
 * Props for the SummaryCard component
 */
interface SummaryCardProps {
  /** Type of summary (pending, overdue, or completed) */
  type: 'pending' | 'overdue' | 'completed';
  /** Number of tasks in this category */
  count: number;
  /** Callback when card is pressed */
  onPress?: () => void;
  /** Test ID for testing */
  testID?: string;
}

/**
 * Configuration for each card variant
 */
const variants = {
  pending: {
    containerStyle: 'bg-amber-50 border border-amber-200',
    countStyle: 'text-amber-700',
    labelStyle: 'text-amber-700',
    iconColor: '#B45309', // warning-700
    Icon: Clock,
  },
  overdue: {
    containerStyle: 'bg-red-50 border border-red-200',
    countStyle: 'text-red-700',
    labelStyle: 'text-red-700',
    iconColor: '#B91C1C', // error-700
    Icon: AlertCircle,
  },
  completed: {
    containerStyle: 'bg-green-50 border border-green-200',
    countStyle: 'text-green-700',
    labelStyle: 'text-green-700',
    iconColor: '#15803D', // success-700
    Icon: CheckCircle,
  },
} as const;

/**
 * Labels for each card type
 */
const labels: Record<'pending' | 'overdue' | 'completed', string> = {
  pending: 'Pending',
  overdue: 'Overdue',
  completed: 'Completed',
};

/**
 * SummaryCard displays a compact card showing task count by status.
 * 
 * @example
 * ```tsx
 * <SummaryCard
 *   type="pending"
 *   count={5}
 *   onPress={() => filterByStatus('pending')}
 * />
 * ```
 */
export const SummaryCard: React.FC<SummaryCardProps> = ({
  type,
  count,
  onPress,
  testID,
}) => {
  const variant = variants[type];
  const Icon = variant.Icon;
  
  return (
    <TouchableOpacity
      onPress={onPress}
      className={`w-[110px] rounded-xl p-3 ${variant.containerStyle}`}
      activeOpacity={0.7}
      accessible={true}
      accessibilityLabel={`${count} ${type} tasks`}
      accessibilityHint={`Tap to view all ${type} tasks`}
      accessibilityRole="button"
      testID={testID}
      style={styles.container}
    >
      {/* Icon */}
      <Icon 
        size={20} 
        color={variant.iconColor}
        style={styles.icon}
      />
      
      {/* Count */}
      <Text 
        className={`text-2xl font-bold ${variant.countStyle}`}
        style={styles.count}
      >
        {count}
      </Text>
      
      {/* Label */}
      <Text 
        className={`text-xs font-medium mt-1 ${variant.labelStyle}`}
      >
        {labels[type]}
      </Text>
    </TouchableOpacity>
  );
};

/**
 * Styles for the SummaryCard component
 */
const styles = StyleSheet.create({
  container: {
    minHeight: 88,
    position: 'relative',
  },
  icon: {
    position: 'absolute',
    top: 12,
    right: 12,
  },
  count: {
    fontFamily: 'JetBrainsMono-Regular',
  },
});

export default SummaryCard;
