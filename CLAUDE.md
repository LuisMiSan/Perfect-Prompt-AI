# CLAUDE.md - AI Assistant Guide for Perfect Prompt AI

## Project Overview

**Perfect Prompt AI** is a React-based web application that helps users create high-quality, structured prompts for various AI models. The application leverages Google Gemini to transform user inputs into optimized prompts for text generation, image creation, video production, audio synthesis, and code generation.

**Primary Language:** Spanish (UI and prompts are in Spanish)

## Technology Stack

- **Framework:** React 19 with TypeScript
- **Build Tool:** Vite 6
- **AI Integration:** Google Gemini API (`@google/genai` SDK)
- **Styling:** Tailwind CSS (inline classes with dark theme, neon/cyberpunk aesthetic)
- **State Management:** React useState/useCallback hooks (no external state library)
- **Storage:** localStorage for prompt history persistence

## Project Structure

```
/
├── App.tsx                    # Main application component, state management hub
├── index.tsx                  # React DOM entry point
├── types.ts                   # TypeScript type definitions and interfaces
├── metadata.json              # App metadata (name, description, permissions)
├── components/
│   ├── Header.tsx             # App header with voice command button
│   ├── Footer.tsx             # Simple footer component
│   ├── PromptInputForm.tsx    # Main form with tabs for different prompt types
│   ├── PromptOutput.tsx       # Displays generated prompt with actions
│   ├── EnhancedPrompt.tsx     # Displays AI-enhanced version of prompt
│   ├── HistoryPanel.tsx       # Table-based prompt library/history
│   └── Loader.tsx             # Loading spinner component
├── services/
│   └── geminiService.ts       # Gemini API integration for prompt generation
├── hooks/
│   └── useSpeechRecognition.ts # Web Speech API hook for voice commands
├── utils/
│   ├── optionsHelper.ts       # Initial options factory for each prompt type
│   └── urlHelper.ts           # URL sharing (encode/decode state to URL params)
└── Configuration files
    ├── package.json
    ├── tsconfig.json
    ├── vite.config.ts
    └── .gitignore
```

## Key Concepts

### Prompt Types (TabType)

The app supports 5 prompt categories, each with specific options:

1. **Text** - General text generation prompts
   - Fields: objetivo, rol, contexto, formato, tono, herramienta

2. **Image** - Image generation/editing prompts
   - Fields: modo, descripcion, estilo, composicion, iluminacion, extras

3. **Video** - Video generation prompts (Veo)
   - Fields: modo, escena, accion, estiloVisual, camara, extras

4. **Audio** - Audio/music generation prompts
   - Fields: modo, tipoSonido, genero, atmosfera, instrumentos, uso

5. **Code** - Code generation prompts
   - Fields: lenguaje, tarea, requisitos, ejemplo, nivel

### Application State (AppState)

```typescript
enum AppState {
  IDLE,      // No prompt generated yet
  LOADING,   // API call in progress
  SUCCESS,   // Prompt successfully generated
  ERROR      // API call failed
}
```

### Data Flow

1. User fills form fields in `PromptInputForm`
2. Clicks "Generar Prompt" -> `App.handleGeneratePrompt()`
3. `geminiService.constructPrompt()` calls Gemini API
4. Result displayed in `PromptOutput`
5. Optional: "Mejorar" -> `geminiService.enhancePrompt()` -> `EnhancedPrompt`
6. Auto-saved to history (localStorage)

## Development Commands

```bash
# Install dependencies
npm install

# Start development server (port 3000)
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

## Environment Configuration

The app requires a Gemini API key:

1. Create `.env.local` file in root
2. Add: `GEMINI_API_KEY=your_api_key_here`

The key is exposed via Vite's `define` as `process.env.API_KEY` and `process.env.GEMINI_API_KEY`.

## Code Conventions

### TypeScript

- Use strict typing; all component props are typed via interfaces
- Union types for `PromptOptions` discriminated by `type` field
- Enum for `AppState`, string literal union for `TabType`

### React Patterns

- Functional components with TypeScript `React.FC<Props>`
- `useCallback` for memoized handlers passed to children
- `React.memo` for performance optimization on form fields
- Custom hooks in `/hooks/` directory

### Styling Conventions

- Tailwind CSS utility classes inline
- Dark theme with slate backgrounds (`bg-slate-900`, `bg-slate-950`)
- Neon accent colors: cyan (`cyan-300`, `cyan-500`), fuchsia, lime
- Drop shadows with color glow effects: `drop-shadow-[0_0_Xpx_rgba(...)]`
- `shadow-[0_0_Xpx_rgba(...)]` for box shadows with glow

### Component Structure

- Icons defined as inline SVG components within component files
- Section wrapper component for consistent form field grouping
- CustomDropdown component for styled select inputs

### Naming Conventions

- Components: PascalCase (`PromptInputForm.tsx`)
- Hooks: camelCase with `use` prefix (`useSpeechRecognition.ts`)
- Utilities: camelCase (`optionsHelper.ts`)
- Constants: SCREAMING_SNAKE_CASE (`SAVED_PROMPTS_KEY`)
- Spanish field names in types (objetivo, descripcion, etc.)

## Key Files Reference

### `services/geminiService.ts`

Two main exported functions:
- `constructPrompt(options: PromptOptions, model: string)` - Generates initial prompt
- `enhancePrompt(existingPrompt: string, model: string)` - Refines existing prompt

Uses system instructions to guide Gemini for prompt engineering best practices.

### `hooks/useSpeechRecognition.ts`

Custom hook for Web Speech API integration:
- Returns: `isListening`, `transcript`, `startListening`, `stopListening`, `isSupported`, `error`
- Language set to `es-ES` (Spanish)

### `utils/urlHelper.ts`

URL-based state sharing:
- `generateShareUrl(state)` - Encodes state to URL with `?s=` parameter
- `parseShareUrl()` - Decodes state from URL on page load

### `types.ts`

Core type definitions:
- `AppState` enum
- `TabType` union type
- `*PromptOptions` interfaces for each tab type
- `PromptOptions` discriminated union
- `HistoryItem` interface for saved prompts

## Common Tasks for AI Assistants

### Adding a New Prompt Type

1. Add new type to `TabType` union in `types.ts`
2. Create new `*PromptOptions` interface in `types.ts`
3. Add to `PromptOptions` union type
4. Add case in `utils/optionsHelper.ts` for initial values
5. Create form fields component in `PromptInputForm.tsx`
6. Add tab icon and rendering in `PromptInputForm`
7. Update `geminiService.ts` `formatOptionsToString()` for new type
8. Update `HistoryPanel.tsx` `getColumnData()` for history display
9. Update voice commands in `App.tsx` if needed

### Adding a New Form Field

1. Add field to appropriate `*PromptOptions` interface
2. Add initial value in `optionsHelper.ts`
3. Add form input in appropriate `*FormFields` component
4. Add to `formatOptionsToString()` in `geminiService.ts`

### Modifying Gemini Prompts

Edit system instructions in `geminiService.ts`:
- `constructPrompt()` for initial generation
- `enhancePrompt()` for enhancement/refinement

### Adding Voice Commands

1. Add command detection in `App.tsx` voice command useEffect
2. Commands support both Spanish and English keywords
3. Update `CommandHelp` component in `Header.tsx`

## Testing Considerations

- No test framework currently configured
- Manual testing via `npm run dev`
- Test with different prompt types
- Test voice commands in supported browsers (Chrome recommended)
- Test URL sharing functionality
- Test localStorage persistence

## Browser Compatibility

- Voice commands require Web Speech API (Chrome, Edge, Safari)
- localStorage required for history persistence
- Modern ES2022 features used (check `tsconfig.json` target)

## Performance Notes

- History limited to 50 items (see `App.tsx` `addPromptToHistory`)
- Uses `React.memo` for form field components
- Dropdown components manage their own open/close state
- Speech recognition is non-continuous (single utterance mode)

## API Error Handling

The `handleGeminiError()` function in `geminiService.ts` provides user-friendly Spanish error messages for common API issues:
- Invalid API key
- Safety policy violations
- Quota exceeded
- Billing issues
- Resource exhaustion

## Accessibility

- Form labels connected to inputs via `htmlFor`/`id`
- `aria-label` on icon buttons
- `role="status"` and `aria-live="polite"` on loaders and alerts
- `sr-only` class for screen reader text
