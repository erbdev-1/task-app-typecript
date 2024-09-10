# React + TypeScript + Vite

This template provides a minimal setup to get React working in Vite with HMR and some ESLint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/README.md) uses [Babel](https://babeljs.io/) for Fast Refresh
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react-swc) uses [SWC](https://swc.rs/) for Fast Refresh

## Expanding the ESLint configuration

If you are developing a production application, we recommend updating the configuration to enable type aware lint rules:

- Configure the top-level `parserOptions` property like this:

```js
export default tseslint.config({
  languageOptions: {
    // other options...
    parserOptions: {
      project: ["./tsconfig.node.json", "./tsconfig.app.json"],
      tsconfigRootDir: import.meta.dirname,
    },
  },
});
```

- Replace `tseslint.configs.recommended` to `tseslint.configs.recommendedTypeChecked` or `tseslint.configs.strictTypeChecked`
- Optionally add `...tseslint.configs.stylisticTypeChecked`
- Install [eslint-plugin-react](https://github.com/jsx-eslint/eslint-plugin-react) and update the config:

````js
// eslint.config.js
import react from 'eslint-plugin-react'

export default tseslint.config({
  // Set the react version
  settings: { react: { version: '18.3' } },
  plugins: {
    // Add the react plugin
    react,
  },
  rules: {
    // other rules...
    // Enable its recommended rules
    ...react.configs.recommended.rules,
    ...react.configs['jsx-runtime'].rules,
  },
})
## Challenge - Task Application

### Setup

- Create the following in './starter/10-tasks':
  - `Form.tsx` (with a basic return)
  - `List.tsx` (with a basic return)
  - `types.ts`
    - Export a type named 'Task' with the following properties:
      - `id: string`
      - `description: string`
      - `isCompleted: boolean`
- In `index.tsx`, import 'Task' type and set up a state value of type 'Task[]'.
- Also, import and render 'Form' and 'List' in `index.tsx`.

### Form

- Create a form with a single input.
- Set up a controlled input.
- Set up a form submit handler and ensure it checks for empty values.

### Add Task

- In `index.tsx`, create an 'addTask' function that adds a new task to the list.
- Pass 'addTask' as a prop to 'Form'.
- In 'Form', set up the correct type and invoke 'addTask' if the input has a value.

### Toggle Task

- In `index.tsx`, create a 'toggleTask' function that toggles 'isCompleted'.
- Pass the function and list as props to 'List'.
- In `List.tsx`:
  - Set up the correct type for props.
  - Render the list.
  - Set up a checkbox in each item and add an 'onChange' handler.
  - Invoke the 'toggleTask' functionality.

### Local Storage

- Incorporate LocalStorage into the application.

## 10 - Tasks

- create Form, List components

types.ts

```ts
export type Task = {
  id: string;
  description: string;
  isCompleted: boolean;
};
````

index.tsx

```tsx
import { useEffect, useState } from "react";
import Form from "./Form";
import List from "./List";
import { type Task } from "./types";

function Component() {
  const [tasks, setTasks] = useState<Task[]>([]);

  return (
    <section>
      <Form />
      <List />
    </section>
  );
}
export default Component;
```

Form.tsx

```tsx
import { useState } from "react";
import { type Task } from "./types";

function Form() {
  const [text, setText] = useState("");

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    if (!text) {
      alert("please enter a task");
      return;
    }
    //  add task
    setText("");
  };
  return (
    <form className="form task-form" onSubmit={handleSubmit}>
      <input
        type="text"
        className="form-input"
        value={text}
        onChange={(e) => {
          setText(e.target.value);
        }}
      />
      <button type="submit" className="btn">
        add task
      </button>
    </form>
  );
}
export default Form;
```

index.tsx

```tsx
import { useEffect, useState } from "react";
import Form from "./Form";
import List from "./List";
import { type Task } from "./types";

function Component() {
  const [tasks, setTasks] = useState<Task[]>([]);

  const addTask = (task: Task) => {
    setTasks([...tasks, task]);
  };

  return (
    <div>
      <Form addTask={addTask} />
      <List />
    </div>
  );
}
export default Component;
```

Form.tsx

```tsx
import { useState } from "react";
import { type Task } from "./types";

type FormProps = {
  addTask: (task: Task) => void;
};

function Form({ addTask }: FormProps) {
  const [text, setText] = useState("");

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    if (!text) {
      alert("please enter a task");
      return;
    }
    addTask({
      id: new Date().getTime().toString(),
      description: text,
      isCompleted: false,
    });
    setText("");
  };
  return (
    <form className="form task-form" onSubmit={handleSubmit}>
      <input
        type="text"
        className="form-input"
        value={text}
        onChange={(e) => {
          setText(e.target.value);
        }}
      />
      <button type="submit" className="btn">
        add task
      </button>
    </form>
  );
}
export default Form;
```

index.tsx

```tsx
const toggleTask = ({ id }: { id: string }) => {
  setTasks(
    tasks.map((task) => {
      if (task.id === id) {
        return { ...task, isCompleted: !task.isCompleted };
      }
      return task;
    })
  );
};
return (
  <div>
    <Form addTask={addTask} />
    <List tasks={tasks} toggleTask={toggleTask} />
  </div>
);
```

List.tsx

```tsx
import { type Task } from "./types";

type ListProps = {
  tasks: Task[];
  toggleTask: ({ id }: { id: string }) => void;
};

const List = ({ tasks, toggleTask }: ListProps) => {
  return (
    <ul className="list">
      {tasks.map((task) => {
        return (
          <li key={task.id}>
            <p className="task-text">{task.description}</p>
            <input
              type="checkbox"
              checked={task.isCompleted}
              onChange={() => {
                toggleTask({ id: task.id });
              }}
            />
          </li>
        );
      })}
    </ul>
  );
};
export default List;
```

index.tsx

```tsx
import { useEffect, useState } from "react";
import Form from "./Form";
import List from "./List";
import { type Task } from "./types";

// Load tasks from localStorage
function loadTasks(): Task[] {
  const storedTasks = localStorage.getItem("tasks");
  return storedTasks ? JSON.parse(storedTasks) : [];
}

function updateStorage(tasks: Task[]): void {
  localStorage.setItem("tasks", JSON.stringify(tasks));
}

function Component() {
  const [tasks, setTasks] = useState<Task[]>(() => loadTasks());

  const addTask = (task: Task) => {
    setTasks([...tasks, task]);
  };

  const toggleTask = ({ id }: { id: string }) => {
    setTasks(
      tasks.map((task) => {
        if (task.id === id) {
          return { ...task, isCompleted: !task.isCompleted };
        }
        return task;
      })
    );
  };
  useEffect(() => {
    updateStorage(tasks);
  }, [tasks]);
  return (
    <div>
      <Form addTask={addTask} />
      <List tasks={tasks} toggleTask={toggleTask} />
    </div>
  );
}
export default Component;
```
