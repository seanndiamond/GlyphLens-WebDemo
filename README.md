# React + TypeScript + Vite

This template provides a minimal setup to get React working in Vite with HMR and some ESLint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react) uses [Babel](https://babeljs.io/) for Fast Refresh
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react-swc) uses [SWC](https://swc.rs/) for Fast Refresh

## Expanding the ESLint configuration

If you are developing a production application, we recommend updating the configuration to enable type-aware lint rules:

```js
export default tseslint.config([
  globalIgnores(['dist']),
  {
    files: ['**/*.{ts,tsx}'],
    extends: [
      // Other configs...

      // Remove tseslint.configs.recommended and replace with this
      ...tseslint.configs.recommendedTypeChecked,
      // Alternatively, use this for stricter rules
      ...tseslint.configs.strictTypeChecked,
      // Optionally, add this for stylistic rules
      ...tseslint.configs.stylisticTypeChecked,

      // Other configs...
    ],
    languageOptions: {
      parserOptions: {
        project: ['./tsconfig.node.json', './tsconfig.app.json'],
        tsconfigRootDir: import.meta.dirname,
      },
      // other options...
    },
  },
])
```

You can also install [eslint-plugin-react-x](https://github.com/Rel1cx/eslint-react/tree/main/packages/plugins/eslint-plugin-react-x) and [eslint-plugin-react-dom](https://github.com/Rel1cx/eslint-react/tree/main/packages/plugins/eslint-plugin-react-dom) for React-specific lint rules:

```js
// eslint.config.js
import reactX from 'eslint-plugin-react-x'
import reactDom from 'eslint-plugin-react-dom'

export default tseslint.config([
  globalIgnores(['dist']),
  {
    files: ['**/*.{ts,tsx}'],
    extends: [
      // Other configs...
      // Enable lint rules for React
      reactX.configs['recommended-typescript'],
      // Enable lint rules for React DOM
      reactDom.configs.recommended,
    ],
    languageOptions: {
      parserOptions: {
        project: ['./tsconfig.node.json', './tsconfig.app.json'],
        tsconfigRootDir: import.meta.dirname,
      },
      // other options...
    },
  },
])
```
📁 `glyphlens-fullstack`

```
.
├── client/                    # Frontend (Vite + React)
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   │   └── ui/
│   │   │       ├── button.tsx
│   │   │       ├── card.tsx
│   │   │       ├── dialog.tsx
│   │   │       └── input.tsx
│   │   ├── App.tsx            # Main UI component
│   │   ├── main.tsx           # Entry point
│   │   └── vite-env.d.ts
│   ├── index.html
│   ├── package.json           # Frontend dependencies
│   └── vite.config.ts
│
├── server/                    # Backend (Express.js)
│   ├── index.ts               # Main server logic
│   ├── routes/
│   │   └── analyze.ts         # POST /analyze route
│   ├── utils/
│   │   └── analyzer.ts        # Image analysis stub logic
│   ├── package.json           # Backend dependencies
│   └── tsconfig.json
│
├── .gitignore
└── README.md
```

---

### 🔧 Sample backend code (server/index.ts)
```ts
import express from 'express';
import fileUpload from 'express-fileupload';
import analyzeRoute from './routes/analyze';

const app = express();
const PORT = process.env.PORT || 3001;

app.use(fileUpload());
app.use(express.json());
app.use('/analyze', analyzeRoute);

app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

### 📥 Sample analyze route (server/routes/analyze.ts)
```ts
import express from 'express';
import { analyzeImage } from '../utils/analyzer';

const router = express.Router();

router.post('/', async (req, res) => {
  const file = req.files?.image;
  if (!file || Array.isArray(file)) {
    return res.status(400).json({ error: 'No image uploaded' });
  }

  const result = await analyzeImage(file.data);
  res.json({ result });
});

export default router;
```

### 🧠 Sample analyzer stub (server/utils/analyzer.ts)
```ts
export async function analyzeImage(buffer: Buffer): Promise<string> {
  // Placeholder logic
  return "[AI] Spiral Operator detected: Subsurface Beacon Interface.";
}
```

### 🎛️ Updated client `src/App.tsx`
```tsx
import { useState } from 'react';

export default function App() {
  const [image, setImage] = useState<File | null>(null);
  const [result, setResult] = useState<string>('');

  const handleUpload = async () => {
    if (!image) return;
    const formData = new FormData();
    formData.append('image', image);

    const res = await fetch('http://localhost:3001/analyze', {
      method: 'POST',
      body: formData,
    });

    const data = await res.json();
    setResult(data.result);
  };

  return (
    <div className="p-6">
      <h1 className="text-2xl font-bold mb-4">GlyphLens Uploader</h1>
      <input type="file" onChange={(e) => setImage(e.target.files?.[0] || null)} />
      <button onClick={handleUpload} className="ml-2 px-4 py-2 bg-blue-500 text-white rounded">
        Analyze
      </button>
      {result && (
        <div className="mt-4 text-green-600 font-mono">
          <strong>Result:</strong> {result}
        </div>
      )}
    </div>
  );
}
```
