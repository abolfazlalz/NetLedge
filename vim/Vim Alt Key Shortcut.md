In my case, I am used to ALT+1 as shortcut for open folder explorer in VS-Code and Jetbrians IDE.
So I wanted to use this in vim as well.

In my search I came up with the following answer:
[stackexchange](https://vi.stackexchange.com/questions/2350/how-to-map-alt-key)

Write it to my `.vimrc`:
```.vimrc
execute "set <M-1>=\ej"
nnoremap <M-1> 1
```