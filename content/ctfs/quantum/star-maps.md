# Star Maps

**Skills:** Dangerously set innerHTML, source maps

In this challenge, looking at the files already given to you is key. When your browser accesses a webpage, all the necessary `.js` files are downloaded.
(Found in Inspect → Sources, with a tree of files thereafter.)

The files downloaded (especially the JS ones) are minified — variable names are shortened to 1 character and newlines are removed, making them practically unreadable. The files are also bundled so that multiple source files are brought together into one.

Looking at one of the Routes files, the comment `//# sourceMappingURL=1.C2QAL_4o.js.map` can be seen. This indicates that source mappings are in place, and a minimised function can be mapped back. These mappings are found in the `/src` files.

Within the `src/` folder, I found a `.svelte` file that appeared to be displayed to the browser:

![svelte file screenshot](/content/ctfs/image-1.png)

Variables were being imported from `encryption.ts`. This file was the gold mine:

![encryption.ts screenshot](/content/ctfs/image-2.png)

It showed how it concatenated a "secret code", "magic word", and "flag seed" into a base64 string. Finding the missing two values was fairly easy — they were in the HTML of the webpage:

![HTML source screenshot](/content/ctfs/image-3.png)

`"hubble"` and `"sunflower"`.

Combine them separated by a `":"`, then base64 encode, slice 2 chars (paste the code in `encryption.js` into the browser console), and reverse it. When sent via the input field, the output is the flag:

```
NexusCTF{dangerouslySetInnerHTML_sourcemaps_are_amazing}
```

> Note: the source maps allow the minimised JS code to be mapped back to its original source. However, I didn't end up needing to use them directly.

---

[Previous: Interstellar Ingress](/content/ctfs/quantum/interstellar-ingress.md) | [Back to Quantum](/content/ctfs/quantum.md)
