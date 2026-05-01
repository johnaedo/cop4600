---
theme: css/dracula.css
width: "1920"
height: "1080"
share_cop4600: "true"
site-folder: docs/Lecture Slides
---
### Policies
- Best Fit
- Worst Fit
- First Fit
- Next Fit
- Other Approaches
---
### Best Fit
- Scan through the free list to find a free block that is the closest in size to the request.  First find all blocks that are equal or greater than the request, then pick the smallest.
### Advantages
- Simple
- Reduces wasted space
### Disadvantages
- Must walk the entire free list.
---
### Worst Fit
- Opposite of Best Fit.  Finds the largest available free space block.
### Advantages
- Tries to keep open blocks large to accommodate more requests
### Disadvantages
- Must walk the entire free list
- In practice, results in fragmentation in addition to the search overhead.
---
### First Fit
- Finds the first block that can satisfy the request.
### Advantages
- SPEEED!  Allocations are fast since the full list isn't traversed.
### Disadvantage
- Pollutes the beginning of the list with small chunks of free space.
- Solving the bias requires an additional step of sorting the free list
---
### Next Fit
- Similar to First Fit, but keeps an additional pointer marking the block last searched.
### Advantages
- Same speed as First Fit
- More evenly distributes free space by avoiding bias
### Disadvantages
- More complex than first fit
---
### Other Approaches
- *Segregation* - Keep separate free lists of similar-sized chunks.  Reduces fragmentation while also improving performance by potentially reducing the search time due to smaller lists.  Bonwick found that kernel objects are ideally suited for this approach as objects like file-system inodes and locks are of a fixed sized and can easily be managed separately.  We can consider this to be like an object cache.
	- Slab allocation - When an object cache needs memory, it requests it from the general allocator as "slabs"
- *Binary Buddy* - Subdivide a large chunk of free space by two until a space is found to fit the request.  Coalescing is easier as free operations check if the "buddy" is also free to then perform an immediate coalesce.  Checking buddies is as easy as checking a single bit since buddies are, by definition, adjacent.  Recursively check and free buddies during free until you find a buddy that is allocated.
---
### More Allocators
- Doug Lea's dlmalloc()
	- Boundary tags allow traversing the free list forwards and backwards
	- Binning (like Binary Buddy, but with flexible sizing) 
- Garbage Collection
	- Periodic scanning of allocations and freeing based on in/out of scope
	- Used by OOP languages like Java, C#, Python, etc.

---
### In Practice...
- Zone-based allocation (Windows)
	- Keeps a separate pool based on usage type and other characteristics.  Notable pools:
		- Paged pools - any memory that can be swapped to disk
		- Non-paged pools - memory that needs to remain resident at all times
		- Session pools - memory associated with a specific user
---
### In Practice...
- jemalloc 
	- Designed to address performance limitations of dlmalloc with respect to multithreading.
	- Firefox, Android, and can be compiled into others like mySQL, redis.
- tcmalloc - Google Chrome, mySQL, Redis, nginx
- 