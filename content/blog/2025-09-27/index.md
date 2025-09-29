# Let's begin

[< Back Home](/)

Right. We're ~700 lines deep into this thing already (I suspect half of those lines are comments). I was almost done with the groundwork - barebones tetris gameplay:

- Gravity ✓
- Movement and Rotation ✓
- Collision ✓
- Populate board ✓

The missing _piece_ left to write: line clearing. Oh boy.

I try to be conscious of the following situation (up until now I wasn't sure it had a name but found the perfect, concise term for it which I'll get to):

> I want to get started on some project.
> This project has problems that I do not
> know how to solve yet, but I'm excited
> to get in there and figure it out.
> To even start solving that hard problem,
> I need to write some foundational code.
> And of course, that foundation ends up
> being poorly thought out, because we
> just want to get to the juicy bits, right?
> Only to realize a few days later, that we
> built our amazing solution on a jenga
> tower made of matchsticks.

I've heard the term _"technical debt"_ before in passing, never taking the time to find out exactly what it means, but I think I might have encountered it here, very organically. Might not be that exact thought, but we'll go with it. Technical debt.

Up until this point, my board logic was built on a typical 20 x 10 array. 20 rows, 10 columns. Nice and simple to reason about. Need a value at some (y , x)? Just subscript and get it - `grid[6][9]`.

_(side note: I'll often describe coordinates using y first - it feels wrong but, because I'm using the ncurses library, which takes_ **all** _of it's coordinate arguments in that order, e.g._ `mvaddch(y, x, ch).` _I've just accepted that convention for this project. Maybe there's an excellent reason they wrote it this way, perhaps that's just how terminal rows and columns are navigated. Let's not go down that rabbit-hole immediately)_

Thinking about how to handle line-clearing, I came to the conclusion that some linked list might be best. Reason being, if we fill and then clear e.g. a _single row_ in the grid, we want to effectively "zero-out" that row, and move all of the lines above it down to fill in the gap.

1. some row is full.
2. for each non-empty row above it, move each down a row in the grid.

_(another side note: literally as I'm writing this, I'm going back and forth thinking:_

> "a 2D array is probably more than fine, maybe even faster that this linked list garbage, stop overcomplicating things"

_and:_

> "no wait, a linked-list will make future optimisation easier"

_and then:_

> "it's just a tetris clone, we have all the memory and CPU speed in the world, just do the cleanest, easy-to-reason-about solution."

_and then:_

> "but trying to implement this linked list solution is fun in-and-of-itself, that's reason enough to try it"

_this thought pattern can be debilitating, but hey, don't worry - it only get's worse from here)_

Now this sounds like a perfect use-case for a linked list to me. One static-length linked list, each node representing a row, that just get's shuffled around as lines get cleared. Let's take the single clear example again:

1. some row is full.
2. set the row before it to point to the row after the full row.
3. point the full row to the head of the list.
4. now make the full row the head of the list.
5. zero-out the full row.

Sure, it sounds convoluted. But I believe this will make multiple line-clears, and **bottom-line-clears** while the stack is high (very common situation in regular play - think classic 9-0 stacking, aka, right-side-well) more efficient.

1. 4 rows have just been filled at once.
2. set the row before the highest of the lot to point to the row after the lowest of the lot (aka, just start at the highest, and traverse 4 rows down the list)
3. point the lowest full row to the head.
4. make the highest full row the head.
5. zero-out the 4 rows.

    Compare this with a standard 2D array approach:

1. 4 rows have been cleared at once.
2. for each non-empty row above the highest full row, copy it down starting at the lowest full row.

Way more copying involved here, we need to rewrite each individual "unaffected" row above the clear.

Anyways. In conclusion, here's how my row clear function looks currently. It handles contiguous 1-to-4-line-clears:

```
void clear_rows(board_t *board, int fullrow, int count) {
	    /*  ---- Set up row pointers ----    */
	   /*   row_at_index(head, i-th node)   */
	  /*    returns a pointer to the i-th  */
	 /*     node down from the head node. */
	row_t *pre_gap = row_at_index(board, fullrow - 1);
	row_t *full_row = pre_gap->next;
	row_t *post_gap;
	post_gap = full_row; 
             // go down 'count' times
            // to find the end of gap
	while (count > 1) {             
		post_gap = post_gap->next;  
		count--;                   
	}
	pre_gap->next = post_gap->next;
	post_gap->next = board->head;
	board->head = full_row;
}
```
