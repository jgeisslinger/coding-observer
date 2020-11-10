# Coding Observer

### Coding and Technology Blog

Coding Observer is my personal blog for coding observations, tutorials and all other tech stuff. I am presenting observations I made during my daily programming as well as tutorials for javascript, PowerBI, node.js and other software snippets.

### Tech Stack
Coding Observer is made with

+ **hugo** static site generator
+ **Github** repo manager
+ **Vercel** automatic site builder and hosting

### Template

My blog is based on a fork of the hugo template **noteworthy** from **@kimcc** found [here](https://github.com/kimcc/hugo-theme-noteworthy)

I adjusted the layout of the template a bit and included **Fontawesome** and **Talkyard** for comments.

#### Fontawesome

Just include in the config.toml the following lines:

```toml
enableFontAwesome = true
fontawesome = "KITID"
```
You get the Kit ID from your Fontawesome Dashboard. If you create a new kit you should get a code with the kit ID included like

```html
<script src="https://kit.fontawesome.com/KITID.js" crossorigin="anonymous"></script>
```
Next include in your head file within the noteworthy template the following lines:

```go
{{ if .Site.Params.enableFontAwesome }}

<script src="https://kit.fontawesome.com/{{ .Site.Params.FontAwesome }}.js"></script>

{{ end }}
```

Afterwards you can use Fontawesome Icons as in any other HTML page.

