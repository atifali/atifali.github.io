---
layout: post
title: Chicken or egg problem?
---

So last night I was actually thinking about how we humans "instruct" machines to perform specific tasks for us. In more general terms, we "program" the computer with our instructions in form of syntactical statements that are formally defined in a "language" specification<!-- more -->, and in turn the resulting combinations of symbols ("tokens") are then used to sort of make sense of what we want that specific program to do. Now, once we make sense of what our syntactical essay is trying to do, we basically convert it into the language that the computer speaks, which on the lowest level is literally a series of 1's and 0's (binary code). I must admit, this is clearly an oversimplification of the process, but in essence all programs are fundamentally a list of ON/OFF instructions for physical transistors.

There are a couple ways of handling this translation from human-readable statements to machine code. In most cases either the source code is "compiled" or "interpreted", which is dependent on what language one uses. There is sort of a blurry line between these two techniques, but in simple terms a compiled language as it suggests is translated down to the machine code by a "compiler", while an interpreted language first compiles to an intermediary code which is then passed onto an "interpreter" to run it, no new machine code is generated in the latter technique. 

Now, I must admit that I have known all that for a long time, and still I never really gave any thought to the real cogs in the wheel (more like the coachman) here...compilers! To me they were just some magical component of a language that did most of my heavy-lifting for me, and I just had to sit back and type-away. This led me down an interesting whirlwind of curious questioning and subsequent learning, which honestly fascinated me.

So what really baffled me was, this idea of compilers written in the same language that they are trying to compile. Let that soak in for a second. This is an analog of the classic chicken or egg causality dilemma. More precisely, if a compiler needs to be compiled for a language X, written in language X, how does the first compiler come into existence? 

But as it turns out, it is not really a dilemma (just like the innocent chicken or egg question - spoiler: its the egg!). This idea is referred to as bootstrapping, and such a compiler is called self-hosted or self-compiling. The solution here really is to make the minimal required subset of the compiler in some another language, and then use that to compile the version written in the new language. The process simply looks as follows:

1. Write a minimal required subset of the compiler (ver 0) for language X (the new language!) in language Y (compiler for which already exists)
2. Then write the same compiler using language X (ver 1)
3. Compile this version using the old compiler written in language Y
4. Make changes to the compiler source code in language X now
5. Use the previous compiler written in language X to compile this new compiler (ver 2)
6. Repeat above two steps for newer subsequent versions!

Some examples of languages that you might be using that use self-hosted compilers are Python, Java, Go and Rust. Interpreters have a similar sort of back-story, but for now lets leave that for the next time! 