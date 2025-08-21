
```
\\ Syntax for links:
[`=[[Notes]].protagonist`](Notes#%60=this.protagonist%60)

\\ Syntax for booleans
`=choice([[Protagonist]].gender = "male","He","She")` 
```

https://blacksmithgu.github.io/obsidian-dataview/

https://forum.obsidian.md/t/obsidian-css-inspector-workflow/58178

https://developer.mozilla.org/en-US/docs/Learn/CSS

https://forum.obsidian.md/t/theme-header-css-overwritten-by-the-app/33702

https://forum.obsidian.md/t/styling-reading-view/62716

Edited Screenplay plugin with:
```
/* removes the underline in preview and source view that comes with the ITS Theme*/

.markdown-preview-view.md-screenplay h2, .markdown-source-view.md-screenplay .HyperMD-header-2::after {

    background: none;

}

/* removes the underline in reading view that comes with the ITS Theme*/

.md-screenplay h2::after {

    background: none;

}
```