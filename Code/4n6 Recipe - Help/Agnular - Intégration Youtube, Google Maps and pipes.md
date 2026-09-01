
`Partager` -> `Intégrer` -> Copier-coller `<iframe>`

```html
<main>
    <!-- BAD src="https://www.youtube.com/embed/wsF3t33jN8Y"  BAD -->
    <iframe width="560" height="315" [src]="videoURL" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</main>
```
`videoURL : string = "https://www.youtube.com/embed/wsF3t33jN8Y" `

`constructor(public sanitizer: DomSanitizer)`

``` typescript
getSafeUrl(): SafeResourceUrl {
    return this.sanitizer.bypassSecurityTrustResourceUrl(this.videoURL);
}
```
----------------------------------------------------------------------------------------------

`[src]=""getSafeUrl() `

`const Youtube_URL = "https://www.youtube.com/embed" `

`videoURL_1 :string = "wsF3t33jN8Y" `

``` typescript
getSafeUrl(): SafeResourceUrl {
    return this.sanitizer.bypassSecurityTrustResourceUrl(Youtube_URL + this.videoURL_1);
}
```
app.Module.ts
`YoutubePlayerModule`