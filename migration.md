---
layout: default
---

# Migration

The next major release of Notion will be Notion 4. This release will include a
number of changes that are either not backwards compatible, or change the
(default) behavior.

## Dependencies

Notion is no longer compatible with Lua 5.0. This change was required in order to
support Lua 5.3, which removed "old-style" Lua 5.0 variadic function arguments,
the "new-style" variadic arguments were introduced with Lua 5.1.

If your custom scripts use the old `function(arg)` method of putting variadic
arguments into an array named `arg`, you will need to use the new `function(...)`
syntax instead.

```lua
function foo(a, b, arg)
    print(unpack(arg))
end
```
becomes
```lua
function foo(a, b, ...)
    print(...)
end
```
and capturing `...` into a table can be achieved with `local arg={...}`.

Notion 4 also requires a C compiler capable of compiling C99.

## Custom styles

Custom styles used to automatically inherit defaults from the "*" style. This
is no longer the case. To get the old behavior back, adding `based_on = "*"`
at the top of any custom styles is likely sufficient:

```
de.defstyle("tab", {
    based_on = "*",
    font = "xft:Source Code Sans:size=16",
})
```

## Keybindings

When defining a submap in your keybindings, it used to be possible to introduce
a `submap_wait` inside the submap that got triggered when the submap was
entered. This has been refactored, and now you can use `kpress` 'next to' the
submap.

Before:

```
defbindings("WFrame.toplevel", {
    submap(META.."K", {
        submap_wait("ioncore.tabnum.show(_)"),
```

After:

```
defbindings("WFrame.toplevel", {
    kpress(META.."K", "WFrame.set_numbers(_, 'during_grab')"),
    submap(META.."K", {
        submap_wait("ioncore.tabnum.show(_)"),
```

## Minimum window sizes

Notion used to respect the 'minimum size' as specified by some windows. This
is often inconvenient, since it can prevent resizing tiles. However, this
feature has been broken for some time, so it did not interfere.

In Notion 4, the feature works again, but is disabled by default. If you want
to respect the minimum size for some windows, you can enable it using the
winprops mechanism:

```
defwinprop{
    -- maybe more selectors
    ignore_min_size = false,
}
```