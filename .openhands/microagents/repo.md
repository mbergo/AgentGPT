# AgentGPT - How It Works

AgentGPT is a web-based platform that allows users to configure and deploy autonomous AI agents directly in their browser. The system enables users to create AI agents with specific goals and watch them autonomously break down tasks, execute them, and learn from results to achieve their objectives.

## Architecture Overview

AgentGPT is built as a modern full-stack web application using the T3 stack:

- **Frontend**: Next.js 13 with TypeScript and React
- **Styling**: TailwindCSS with HeadlessUI components
- **Backend**: Next.js API routes with tRPC for type-safe APIs
- **Database**: Prisma ORM with MySQL/Supabase
- **Authentication**: NextAuth.js
- **AI Integration**: LangChain with OpenAI GPT models
- **State Management**: Zustand for client-side state

## Core Components

### 1. AutonomousAgent Class (`src/components/AutonomousAgent.ts`)

The heart of the system is the `AutonomousAgent` class, which orchestrates the entire agent lifecycle:

- **Initialization**: Takes a name, goal, and configuration settings
- **Task Generation**: Uses AI to break down the main goal into actionable tasks
- **Task Execution**: Executes tasks one by one using AI reasoning
- **Dynamic Planning**: Generates new tasks based on execution results
- **Loop Management**: Continues until goal completion or max iterations reached

### 2. Agent Service (`src/services/agent-service.ts`)

Handles AI model interactions through three main functions:

- `startGoalAgent()`: Generates initial tasks from the user's goal
- `executeTaskAgent()`: Executes individual tasks and returns results
- `createTasksAgent()`: Creates new tasks based on previous task results

### 3. AI Prompts (`src/utils/prompts.ts`)

Three specialized prompts guide the AI behavior:

- **Goal Prompt**: Converts user goals into initial task lists
- **Execute Prompt**: Guides task execution with context awareness
- **Create Tasks Prompt**: Generates follow-up tasks based on results

## How It Works - Step by Step

### 1. User Input
- User provides an agent name and goal description
- System validates inputs and checks authentication/API limits

### 2. Agent Initialization
```typescript
const agent = new AutonomousAgent(
  name, goal, messageHandler, shutdownHandler, settings, session
);
```

### 3. Initial Task Generation
- Agent sends the goal to OpenAI with the `startGoalPrompt`
- AI returns 0-3 initial tasks as a JSON array
- Tasks are parsed and added to the task queue

### 4. Main Execution Loop
For each task in the queue:
1. **Task Execution**: Send task to AI with context (goal + task)
2. **Result Processing**: Receive and display execution results
3. **New Task Generation**: AI analyzes results and creates follow-up tasks
4. **Queue Management**: Add new tasks to queue, mark current as complete

### 5. Termination Conditions
The agent stops when:
- All tasks are completed (success)
- Maximum loop limit reached (safety)
- User manually stops the agent
- API errors or rate limits encountered

## Key Features

### Autonomous Operation
- Agents operate independently after initial goal setting
- No human intervention required during execution
- Self-directed task creation and prioritization

### Real-time Updates
- Live task status updates in the UI
- Streaming results as tasks execute
- Visual progress indicators and animations

### Flexible Configuration
- Custom API keys for extended usage
- Adjustable model parameters (temperature, max tokens)
- Configurable loop limits for cost control

### User Management
- Authentication via NextAuth.js
- Usage limits for free vs. paid users
- Agent run history and saving capabilities

## Data Flow

1. **Client → Server**: User submits goal via React form
2. **Server → OpenAI**: Goal converted to initial tasks via API
3. **OpenAI → Server**: Returns task array
4. **Server → Client**: Tasks streamed to UI
5. **Client → Server**: Execute first task
6. **Server → OpenAI**: Task execution request
7. **OpenAI → Server**: Execution results
8. **Server → Client**: Results displayed
9. **Repeat**: Generate new tasks based on results

## API Architecture

### Client-Side vs Server-Side Execution
- **Custom API Key**: Runs client-side with LangChain
- **No API Key**: Uses server-side API routes with shared keys
- Rate limiting and usage tracking on server endpoints

### Database Schema
- **Users**: Authentication and subscription management
- **Agents**: Saved agent configurations and goals
- **AgentTasks**: Individual task records with status tracking

## Security & Limitations

### Rate Limiting
- Built-in rate limiting via Upstash Redis
- Different limits for free vs. paid users
- Graceful degradation when limits exceeded

### Loop Protection
- Maximum iteration limits prevent infinite loops
- Different limits based on user tier and API key usage
- Automatic shutdown with explanatory messages

### Content Safety
- OpenAI's built-in content policies apply
- Error handling for policy violations
- Graceful fallback when tasks are rejected

## Development Setup

The application supports multiple deployment modes:
- **Local Development**: `npm run dev` with local database
- **Docker**: Containerized deployment with `docker-compose`
- **Production**: Vercel deployment with Supabase database

This architecture enables AgentGPT to provide a seamless, autonomous AI agent experience while maintaining safety, scalability, and user control.