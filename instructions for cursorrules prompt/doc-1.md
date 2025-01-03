# Feature Feedback Analysis System
## A Step-by-Step Implementation Guide

## Scenario
Analyzing feature feedback from multiple sources (app store reviews, support tickets, and user feedback emails) to identify patterns and prioritize feature improvements.

## Project Structure
```
feature-analysis/
├── feedback/
│   ├── app_store/
│   │   ├── reviews_jan.md
│   │   ├── reviews_feb.md
│   │   └── reviews_mar.md
│   ├── support/
│   │   ├── tickets_q1.md
│   │   └── tickets_q2.md
│   └── emails/
│       ├── feedback_batch1.md
│       └── feedback_batch2.md
├── cursorrules.md
└── outputs/
    ├── analysis/
    ├── patterns/
    └── insights/
```

## Implementation Guide

### Step 1: Set Up cursorrules.md

```markdown
# Feature Feedback Analysis System

## Document Processing Rules

### Step 1: Initial Analysis
prompt: |
  Analyze the current feedback document and extract:
  1. Feature mentions
  2. Sentiment
  3. User pain points
  4. Specific requests
  
  Format your response as follows:
  
  # Document Analysis: [document_name]
  
  ## Feature Mentions
  - [Feature]: [Context] | [Sentiment]
  
  ## Pain Points
  - [Issue]: [Description] | [Frequency]
  
  ## Requests
  - [Request]: [Priority] | [User Impact]
  
  ## Key Metrics
  - Sentiment Overview: [positive/negative ratio]
  - Most Mentioned Features: [list]
  - Critical Issues: [list]

### Step 2: Pattern Recognition
prompt: |
  Using the current analysis and any previous patterns:
  
  1. Compare with existing patterns:
  {previous_patterns}
  
  2. Identify:
     - Recurring issues
     - Feature request trends
     - Sentiment patterns
     
  Format your response as:
  
  # Pattern Analysis
  
  ## Updated Patterns
  - [Pattern ID]: [Description] | [Strength] | [Change]
  
  ## New Patterns
  - [Pattern ID]: [Description] | [Initial Strength]
  
  ## Pattern Evidence
  - [Pattern ID]: [Supporting Data Points]

### Step 3: Insight Generation
prompt: |
  Cross-reference all patterns and previous insights:
  
  Previous insights:
  {previous_insights}
  
  Generate:
  1. Updated insights
  2. Priority recommendations
  3. Action items
  
  Format your response as:
  
  # Insight Summary
  
  ## Key Insights
  - [Insight ID]: [Description] | [Confidence] | [Impact]
  
  ## Recommendations
  - [Rec ID]: [Action] | [Priority] | [Effort]
  
  ## Next Steps
  1. [Action Item]
  2. [Action Item]
```

## Example Processing

### Document 1: App Store Reviews (reviews_jan.md)
```markdown
Reviews January 2024:

1. "Love the new dashboard, but export feature is clunky" ⭐⭐⭐⭐
2. "Can't figure out how to bulk edit items" ⭐⭐
3. "Great app overall, wish it had dark mode" ⭐⭐⭐⭐⭐
4. "Export feature crashed twice today" ⭐⭐
5. "Need better keyboard shortcuts" ⭐⭐⭐
```

#### AI Analysis Output:
```markdown
# Document Analysis: reviews_jan.md

## Feature Mentions
- Dashboard: New feature | Positive
- Export: Functionality | Negative
- Bulk Edit: Missing feature | Negative
- Dark Mode: Requested feature | Neutral
- Keyboard Shortcuts: Enhancement | Neutral

## Pain Points
- Export reliability: Crashes reported | High
- Bulk operations: Missing functionality | Medium
- Navigation efficiency: Keyboard control | Medium

## Requests
- Dark Mode: Medium | Usability
- Keyboard Shortcuts: Medium | Productivity
- Bulk Edit: High | Workflow

## Key Metrics
- Sentiment Overview: 60% positive
- Most Mentioned: Export, Dashboard
- Critical Issues: Export reliability

# Pattern Analysis

## New Patterns
- P1: Export functionality issues | Strength: High
- P2: Productivity feature requests | Strength: Medium
- P3: UI customization desires | Strength: Low

## Pattern Evidence
- P1: 2 negative reports, crash mentions
- P2: Keyboard shortcuts, bulk edit requests
- P3: Dark mode request, UI feedback
```

### Document 2: Support Tickets (tickets_q1.md)
```markdown
Q1 Support Tickets:

1. Ticket #123
   Issue: Bulk export timeout
   Impact: High
   Users affected: 15

2. Ticket #124
   Issue: Keyboard shortcuts not working
   Impact: Medium
   Users affected: 8

3. Ticket #125
   Issue: Dark mode contrast issues
   Impact: Medium
   Users affected: 12
```

#### AI Follow-up Analysis:
```markdown
# Document Analysis: tickets_q1.md

## Feature Mentions
- Export: Bulk functionality | Negative
- Keyboard Shortcuts: Functionality | Negative
- Dark Mode: Implementation | Negative

## Pain Points
- Export performance: Timeout issues | High
- Shortcut reliability: Not working | Medium
- Dark mode usability: Contrast | Medium

# Pattern Analysis

## Updated Patterns
- P1: Export functionality issues | Strength: High | Increasing
- P2: Productivity feature issues | Strength: High | Increasing
- P3: UI customization issues | Strength: Medium | Increasing

## Pattern Evidence
- P1: Added performance issues data
- P2: Confirmed keyboard shortcut problems
- P3: Specific dark mode implementation issues

# Insight Summary

## Key Insights
- I1: Export feature needs urgent overhaul | Confidence: 90% | Impact: High
- I2: Productivity features unreliable | Confidence: 85% | Impact: Medium
- I3: UI customization has implementation gaps | Confidence: 75% | Impact: Medium

## Recommendations
- R1: Redesign export feature | Priority: High | Effort: High
- R2: Fix keyboard shortcuts | Priority: Medium | Effort: Medium
- R3: Audit dark mode implementation | Priority: Medium | Effort: Medium

## Next Steps
1. Technical audit of export feature
2. User research on productivity workflows
3. Dark mode usability testing
```

## Using the System

1. **Start Processing:**
   ```markdown
   Human: "Please analyze reviews_jan.md using the feature feedback analysis system."
   
   AI: "I'll begin analyzing reviews_jan.md following the defined steps..."
   [Provides initial analysis]
   
   Human: "Now analyze tickets_q1.md"
   
   AI: "I'll analyze tickets_q1.md and compare with previous patterns..."
   [Provides analysis and updated insights]
   ```

2. **Request Summaries:**
   ```markdown
   Human: "Summarize the key findings so far."
   
   AI: "Based on the analysis of reviews and tickets, here are the key findings..."
   [Provides comprehensive summary]
   ```

3. **Generate Reports:**
   ```markdown
   Human: "Create a priority report for the development team."
   
   AI: "I'll create a prioritized list of issues and recommendations..."
   [Generates development-focused report]
   ```

## Best Practices

1. **Document Preparation**
   - Use consistent formatting
   - Include relevant metadata
   - Organize by time period

2. **Analysis Process**
   - Process documents chronologically
   - Maintain pattern terminology
   - Track confidence levels

3. **Output Usage**
   - Create targeted summaries
   - Link insights to evidence
   - Update priorities based on new data

## Tips for Success

1. **Pattern Recognition**
   - Start broad, then refine
   - Look for correlations
   - Track pattern strength changes

2. **Insight Development**
   - Build on previous insights
   - Validate with new data
   - Adjust confidence levels

3. **Action Planning**
   - Prioritize based on impact
   - Consider implementation effort
   - Track resolution progress