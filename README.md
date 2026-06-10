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

## Some Custom Cursore 
Instead of 1 dot → use 2 layers:

🔵 Inner dot (follows fast)
⚪ Outer ring (lags behind)
```jsx
"use client";

import { motion } from "framer-motion";
import { useEffect, useState } from "react";

type CursorType = "default" | "link" | "image";

export default function CustomCursor() {
  const [pos, setPos] = useState({ x: 0, y: 0 });
  const [type, setType] = useState<CursorType>("default");

  useEffect(() => {
    const move = (e: MouseEvent) => {
      setPos({ x: e.clientX, y: e.clientY });
    };

    const detect = (e: MouseEvent) => {
      const el = e.target as HTMLElement;

      if (el.closest("a")) setType("link");
      else if (el.closest("img")) setType("image");
      else setType("default");
    };

    window.addEventListener("mousemove", move);
    window.addEventListener("mouseover", detect);

    return () => {
      window.removeEventListener("mousemove", move);
      window.removeEventListener("mouseover", detect);
    };
  }, []);

  return (
    <>
      {/* OUTER RING (smooth lag) */}
      <motion.div
        className="fixed top-0 left-0 w-10 h-10 rounded-full border border-white/40 pointer-events-none z-[9999]"
        animate={{
          x: pos.x - 20,
          y: pos.y - 20,
          scale: type === "link" ? 1.5 : type === "image" ? 2 : 1,
        }}
        transition={{ type: "spring", stiffness: 120, damping: 20 }}
      />

      {/* INNER DOT */}
      <motion.div
        className="fixed top-0 left-0 w-2.5 h-2.5 rounded-full bg-white pointer-events-none z-[9999]"
        animate={{
          x: pos.x - 5,
          y: pos.y - 5,
          scale: type === "link" ? 0 : 1, // hide dot on link hover
        }}
        transition={{ type: "spring", stiffness: 600, damping: 30 }}
      />
    </>
  );
}
```

## Design 2
```jsx
"use client";

import { motion } from "framer-motion";
import { useEffect, useState } from "react";

type CursorType = "default" | "link" | "image";

export default function CustomCursor() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const [type, setType] = useState<CursorType>("default");

  useEffect(() => {
    const moveCursor = (e: MouseEvent) => {
      setPosition({ x: e.clientX, y: e.clientY });
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
  }, []);

  const variants = {
    default: {
      scale: 1,
      backgroundColor: "#ffffff",
      mixBlendMode: "difference",
    },
    link: {
      scale: 2,
      backgroundColor: "#00ffcc",
      mixBlendMode: "difference",
    },
    image: {
      scale: 2.5,
      backgroundColor: "#ffcc00",
      mixBlendMode: "difference",
    },
  };

  return (
    <motion.div
      className="fixed top-0 left-0 w-4 h-4 rounded-full pointer-events-none z-[9999]"
      animate={{
        x: position.x - 8,
        y: position.y - 8,
        ...variants[type],
      }}
      transition={{
        type: "spring",
        stiffness: 700,
        damping: 35,
      }}
    />
  );
}   

```

## ✨ DESIGN: “AURA GRADIENT CURSOR” (Modern Premium Style)

👉 Looks like a soft glowing energy field
👉 Gradient + blur + smooth trail
👉 Very Awwwards-style

```jsx
"use client";

import { motion } from "framer-motion";
import { useEffect, useState } from "react";

export default function CustomCursor() {
  const [pos, setPos] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const move = (e: MouseEvent) => {
      setPos({ x: e.clientX, y: e.clientY });
    };

    window.addEventListener("mousemove", move);

    return () => window.removeEventListener("mousemove", move);
  }, []);

  return (
    <>
      {/* BIG AURA GLOW (background layer) */}
      <motion.div
        className="fixed top-0 left-0 w-32 h-32 rounded-full pointer-events-none z-[9998]"
        animate={{
          x: pos.x - 64,
          y: pos.y - 64,
        }}
        transition={{
          type: "spring",
          stiffness: 80,
          damping: 20,
        }}
        style={{
          background:
            "radial-gradient(circle, rgba(0,255,204,0.25), transparent 60%)",
          filter: "blur(20px)",
        }}
      />

      {/* INNER CORE CURSOR */}
      <motion.div
        className="fixed top-0 left-0 w-4 h-4 rounded-full pointer-events-none z-[9999]"
        animate={{
          x: pos.x - 8,
          y: pos.y - 8,
        }}
        transition={{
          type: "spring",
          stiffness: 600,
          damping: 30,
        }}
        style={{
          background:
            "linear-gradient(135deg, #00ffcc, #3b82f6, #a855f7)",
          boxShadow: "0 0 20px rgba(0,255,204,0.6)",
        }}
      />
    </>
  );
}
```

## 🎯 DESIGN: “PIXEL TRAIL CURSOR” (Modern + Tech Feel)

👉 Cursor leaves a fading trail of small pixels
👉 Feels like motion graphics / cyber UI
👉 Very unique for portfolios
👉 Great for developer / creative sites
```jsx
"use client";

import { motion } from "framer-motion";
import { useEffect, useState } from "react";

type Point = { x: number; y: number };

export default function CustomCursor() {
  const [points, setPoints] = useState<Point[]>([]);

  useEffect(() => {
    const move = (e: MouseEvent) => {
      const newPoint = { x: e.clientX, y: e.clientY };

      setPoints((prev) => {
        const updated = [...prev, newPoint];
        return updated.slice(-12); // trail length
      });
    };

    window.addEventListener("mousemove", move);
    return () => window.removeEventListener("mousemove", move);
  }, []);

  return (
    <>
      {/* TRAIL PIXELS */}
      {points.map((point, i) => (
        <motion.div
          key={i}
          className="fixed top-0 left-0 w-2 h-2 bg-white rounded-sm pointer-events-none z-[9999]"
          animate={{
            x: point.x,
            y: point.y,
            opacity: i / points.length,
            scale: i / points.length,
          }}
          transition={{
            type: "spring",
            stiffness: 300,
            damping: 25,
          }}
        />
      ))}
    </>
  );
}
```
## 🌊 DESIGN: RADIAL RIPPLE CURSOR (Modern Interaction Style)

👉 Cursor creates soft ripple waves as it moves
👉 Feels like water / touch interaction
👉 Very modern SaaS + portfolio UI style
👉 Great for “interactive feel” websites
```jsx
"use client";

import { motion } from "framer-motion";
import { useEffect, useState } from "react";

type Ripple = {
  id: number;
  x: number;
  y: number;
};

export default function CustomCursor() {
  const [pos, setPos] = useState({ x: 0, y: 0 });
  const [ripples, setRipples] = useState<Ripple[]>([]);

  useEffect(() => {
    const move = (e: MouseEvent) => {
      setPos({ x: e.clientX, y: e.clientY });

      // create ripple on movement (light + smooth)
      const newRipple = {
        id: Date.now(),
        x: e.clientX,
        y: e.clientY,
      };

      setRipples((prev) => [...prev, newRipple].slice(-6));
    };

    window.addEventListener("mousemove", move);

    return () => window.removeEventListener("mousemove", move);
  }, []);

  return (
    <>
      {/* CORE DOT */}
      <motion.div
        className="fixed top-0 left-0 w-3 h-3 rounded-full bg-white pointer-events-none z-[9999]"
        animate={{
          x: pos.x - 6,
          y: pos.y - 6,
        }}
        transition={{
          type: "spring",
          stiffness: 700,
          damping: 30,
        }}
      />

      {/* RIPPLE WAVES */}
      {ripples.map((ripple) => (
        <motion.div
          key={ripple.id}
          className="fixed top-0 left-0 w-10 h-10 rounded-full border border-white/30 pointer-events-none z-[9998]"
          initial={{ scale: 0.5, opacity: 0.5 }}
          animate={{ scale: 3, opacity: 0 }}
          transition={{ duration: 0.8 }}
          style={{
            x: ripple.x - 20,
            y: ripple.y - 20,
          }}
        />
      ))}
    </>
  );
}
```
##

