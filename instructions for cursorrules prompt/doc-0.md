# Agentic Multi-step Document Processor Guide

## Overview
The Agentic Multi-step Document Processor is an automated system that processes multiple documents through a defined series of steps, gradually extracting and refining information to create comprehensive markdown outputs. The processor follows a systematic approach where each document goes through all steps before moving to the next document, ensuring thorough and consistent processing.

## Core Architecture

### Basic Structure
```
project/
├── docs/                 # Input documents
├── outputs/             # Generated markdown files
└── .cursorrules      # Processing rules and steps
```

### Process Flow
1. Read documents from the docs folder sequentially
2. Apply each processing step to the current document
3. Generate markdown output for each document
4. Move to next document only after completing all steps
5. Continue until all documents are processed

## Processing Steps

### Step 1: Initial Outline
- Creates a skeleton outline identifying essential elements
- Focuses on structure and main components
- No detailed content at this stage

### Step 2: Section Summaries
- Adds high-level summaries for each section
- Includes key points and relevant details
- Maintains hierarchical structure

### Step 3: Detailed Content
- Incorporates comprehensive details
- Adds tables, charts, and supporting elements
- Finalizes the markdown document

## Use Case: UX Research Analysis

### Scenario
Processing 10 user interview transcripts to generate UX deliverables

### Folder Structure
```
ux-research/
├── docs/
│   ├── raw-interviews/
│   │   ├── interview1.txt
│   │   ├── interview2.txt
│   │   └── ...
│   └── .cursorrules
├── outputs/
│   ├── step1-analysis/
│   │   ├── interview1-analysis.md
│   │   ├── interview2-analysis.md
│   │   └── ...
│   ├── step2-insights/
│   │   ├── interview1-insights.md
│   │   ├── interview2-insights.md
│   │   └── consolidated-insights.md
│   └── step3-deliverables/
       ├── personas/
       │   ├── persona1.md
       │   └── persona2.md
       ├── journey-maps/
       │   └── journey-map1.md
       └── requirements/
           └── feature-requirements.md
```

### Processing Steps and Output Generation

#### Step 1: Initial Analysis
```markdown
# .cursorrules - Step 1

prompt: "Analyze the interview transcript and create a structured analysis markdown file with the following sections:

1. Participant Overview
   - Age, occupation, location
   - Technical proficiency
   - Relevant background

2. Key Findings
   - Main pain points
   - Feature requests
   - Current workflow
   - Tools used

3. Notable Quotes
   - Direct quotes organized by theme
   - Context for each quote

4. Usage Patterns
   - Frequency of use
   - Common scenarios
   - Workflow descriptions

Save the output as 'interview{N}-analysis.md' in the step1-analysis folder."

Example Output (interview1-analysis.md):
---
# Interview 1 - Analysis

## Participant Overview
- Age: 34
- Occupation: Product Manager
- Location: San Francisco
- Technical proficiency: High
- Background: 5 years in product management

## Key Findings
### Pain Points
1. Difficulty collaborating on user research
2. No centralized storage for insights
...

### Feature Requests
1. Automated transcription
2. Tags for organizing insights
...

## Notable Quotes
### On Collaboration
"The biggest challenge is sharing findings with the team..."

### On Documentation
"I spend too much time formatting reports..."
...
```

#### Step 2: Insights Generation
```markdown
# .cursorrules - Step 2

prompt: "Using the analysis from step 1, create two files:

1. Individual insight file (interview{N}-insights.md):
   - Synthesized behavioral patterns
   - Primary user needs
   - Feature priorities
   - Suggested solutions

2. Update consolidated-insights.md:
   - Merge new insights with existing patterns
   - Update user segments
   - Revise feature priority matrix
   - Add new quotes to evidence bank

Include cross-references to source interviews."

Example Output (interview1-insights.md):
---
# Interview 1 - Insights

## Behavioral Patterns
1. Relies heavily on async communication
2. Prefers visual documentation
...

## User Needs
1. Streamlined research sharing
2. Automated documentation
...

Example Output (consolidated-insights.md):
---
# Consolidated Research Insights

## User Segments
1. Tech-savvy Product Managers
   - Characteristics: [...]
   - Evidence: [Interview 1, 4, 7]
...

## Feature Priority Matrix
| Feature | Frequency | Impact | Evidence |
|---------|-----------|---------|----------|
| Auto-transcription | High | High | Int 1, 3 |
...
```

#### Step 3: UX Deliverables
```markdown
# .cursorrules - Step 3

prompt: "Based on the consolidated insights, generate:

1. Persona Creation (save as personas/persona{N}.md):
   Template:
   # [Persona Name]
   ## Background
   - Composite of interviews: [list]
   - Demographics
   - Context
   
   ## Behaviors & Motivations
   - Key patterns from insights
   - Goals and motivations
   - Frustrations
   
   ## Scenarios
   - Common use cases
   - Example quotes
   
   ## Requirements
   - Must-have features
   - Nice-to-have features

2. Journey Map Creation (save as journey-maps/journey-map{N}.md):
   Template:
   # [Journey Name]
   ## Overview
   - User segment
   - Scenario
   
   ## Journey Stages
   [Table with stages, actions, thoughts, emotions]
   
   ## Opportunities
   - Pain points
   - Potential solutions

3. Requirements Doc (save as requirements/feature-requirements.md):
   Template:
   # Feature Requirements
   ## Priority Matrix
   [Table with features, impact, effort]
   
   ## User Stories
   [Organized by feature]
   
   ## Acceptance Criteria
   [Detailed criteria per feature]"

Example Output (personas/persona1.md):
---
# Sarah Chen - Product Research Lead

## Background
- Composite of interviews: 1, 4, 7
- Demographics: 30-35, urban tech hub
- Context: Mid-size SaaS company

## Behaviors & Motivations
### Goals
1. Streamline research documentation
2. Improve insight sharing
...

[Additional example outputs for journey maps and requirements]
```
```

## Additional Use Cases

### Technical Documentation Processing
- Processing multiple API documentation files
- Creating consistent documentation structure
- Generating technical specifications

### Content Analysis
- Analyzing research papers
- Processing academic articles
- Creating literature reviews

### Market Research
- Processing competitor analysis documents
- Creating market insight reports
- Generating trend analysis

## Best Practices

### Document Preparation
1. Ensure consistent document formatting
2. Use clear file naming conventions
3. Organize documents in appropriate subfolders

### Prompt Design
1. Make prompts specific and focused
2. Include clear output requirements
3. Define expected format and structure
4. Use consistent terminology

### Output Management
1. Create separate output folders for different deliverable types
2. Use version control for outputs
3. Maintain clear naming conventions

## Advanced Features

### Custom Processing Rules
You can modify the .cursorrules file to:
- Add more processing steps
- Define custom output formats
- Include specific analysis requirements
- Add validation rules

### Integration Possibilities
- Connect with version control systems
- Integrate with documentation platforms
- Link with project management tools
- Export to various formats

## Troubleshooting

### Common Issues
1. Inconsistent document formats
2. Missing information in outputs
3. Processing errors

### Solutions
1. Standardize input documents
2. Verify processing steps
3. Check output completeness

## Conclusion
The Agentic Multi-step Document Processor provides a powerful framework for automated document analysis and content generation. Its flexible architecture and systematic approach make it particularly valuable for UX research and other analytical tasks requiring consistent processing of multiple documents.