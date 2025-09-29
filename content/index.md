![page banner](/images/banner.png)

# BATCHTRIS

## WHATISAMANAMI<br>SERABLELITTLE<br>PILEOFSECRETS 

A take on tetris based on the idea of placing a sequence of pieces in "batches". Name needs work.

[ -> 2025-09-27 >> Linked-Lists, Technical Debt](/blog/2025-09-27)

You're given a queue, say a 7-bag, then you need to somehow input the total sequence for the entire 7-bag, without seeing the result of each move. Thinking the input notation would also assume perfect finesse. While writing a simple typing test program in Python using Curses, I learned that dealing with user input, specifically "key-release" events is not trivial in the slightest. If I were to write a tetris clone with modern features (custom DAS and ARR, support for various spins), the standard ways of dealing with input wouldn't cut it. I'll get into the reasons for this in future logs.

###### Overview:

1. write it in C
2. have it be a TUI app
3. modern tetris mechanics<ul><li>hard drops</li><li>spins</li><li>SRS</li></ul>

###### Considerations:

- Verticle aspect ratio of terminal characters. I need squares
- Accessibility for those unfamiliar with perfect finesse inputs
- Logic of how spins and slides are handled feels like it will be a doozey
- Spend no more than a couple of weeks to finish the project. (foreshadowing)


#### TOGETHAAAWEWI

#### LLDEVOWAAAAAA

#### THEVERYGODSXD

[About](/about)

<sub>`the pages on this website were generated from markdown files, written by following the "static site generator" guided project over at ` [boot->dev](https://www.boot.dev)</sub>
