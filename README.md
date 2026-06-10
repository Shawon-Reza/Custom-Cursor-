# 🖱️ Custom Cursor with Next.js + Framer Motion

## 📌 Introduction

Modern websites often use custom cursors to enhance user experience and create a more interactive feel.

Instead of relying on the default system cursor, this setup replaces it with a **smooth, animated, and interactive cursor**.

This implementation uses:

- Next.js (App Router)
- Framer Motion
- React Hooks
- Motion Values + Spring Physics

✔ Lightweight  
✔ Smooth  
✔ Performance optimized  
✔ Production ready  

---

## 🎯 Features

This custom cursor supports:

- Default cursor state  
- Link hover state  
- Image hover state  
- Smooth trailing animation  
- Spring-based motion system  
- High performance (no React re-render on mouse move)  

---

## 🧠 How It Works

The system separates logic into:

- Mouse tracking
- Motion rendering
- Hover detection
- Animation system

### Internal Workflow

```txt
Mouse Move Event
        ↓
Motion Values update (no re-render)
        ↓
Spring animation adds smooth trailing effect
        ↓
Framer Motion renders cursor
        ↓
Hover detection updates cursor style
```
# Implementation 
## 1 Installation
npm install framer-motion

## 2 Custom Cursor Component(CustomCursor.tsx)
```jsx
"use client";

import { motion, useMotionValue, useSpring } from "framer-motion";
import { useEffect, useState } from "react";

type CursorType = "default" | "link" | "image";

export default function CustomCursor() {
  const [type, setType] = useState<CursorType>("default");

  const x = useMotionValue(0);
  const y = useMotionValue(0);

  const smoothX = useSpring(x, { stiffness: 500, damping: 40 });
  const smoothY = useSpring(y, { stiffness: 500, damping: 40 });

  useEffect(() => {
    const moveCursor = (e: MouseEvent) => {
      x.set(e.clientX);
      y.set(e.clientY);
    };

    const detectHover = (e: MouseEvent) => {
      const target = e.target as HTMLElement;

      if (target.closest("a")) setType("link");
      else if (target.closest("img")) setType("image");
      else setType("default");
    };

    window.addEventListener("mousemove", moveCursor);
    window.addEventListener("mouseover", detectHover);

    return () => {
      window.removeEventListener("mousemove", moveCursor);
      window.removeEventListener("mouseover", detectHover);
    };
  }, [x, y]);

  const variants = {
    default: {
      scale: 1,
      backgroundColor: "#ffffff",
    },
    link: {
      scale: 2,
      backgroundColor: "#00ffcc",
    },
    image: {
      scale: 2.5,
      backgroundColor: "#ffcc00",
    },
  };

  return (
    <motion.div
      className="fixed top-0 left-0 w-4 h-4 rounded-full pointer-events-none z-[9999] mix-blend-difference"
      style={{
        x: smoothX,
        y: smoothY,
      }}
      animate={variants[type]}
      transition={{
        type: "spring",
        stiffness: 500,
        damping: 30,
      }}
    />
  );
}


```
## 3 Add to Root Layout
```jsx
<html lang="en">
      <body className="cursor-none">
        <CustomCursor />
        {children}
      </body>
    </html>
```

## 4 Global CSS
```jsx
body {
  cursor: none;
}
```


