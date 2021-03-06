---
published: false
title: 2016 state of passwords uses in web application
layout: post
date: "2016-02-18 13:00:00 +0200"
---



## PHP
>  The First Rule of Cryptography: Don't Implement it Yourself

>  Developing cryptography features is best left to the experts. By all means, do feel free to tinker, but don't deploy your experiments in production or share them with other developers who might deploy them in production.

>  Instead, use a high-level cryptography library that experts have already vetted. Follow the link to read our PHP cryptography library recommendations.


> First, make sure you're using a supported version of PHP. If you are, then the PHP password API will be available for use. If you aren't, consider upgrading. If you can't, check out password_compat.


2. Use a supported version of PHP: versions 5.3 and 5.4 have reach the end-of-life state and are no longer supported. version 5.5 will reach the end-of-life state by mid 2016. If you are using any of these version, you should upgrade as soon as possible, as they may be exposed to unpatched security vulnerabilities. As it is the final PHP 5 release, support for PHP 5.6 has been extended up January 1st 2019.



{% highlight php startinline %}
<?php

$hash = password_hash($userPassword, PASSWORD_DEFAULT);
?>
{% endhighlight %}

{% highlight php startinline %}
<?php

string password_hash ( string $password , integer $algo [, array $options ] )

?>
{% endhighlight %}

```password_hash()``` takes care of salting the hash, transparently.  This method is a simple ```crypt()``` wrapper and uses a strong hash, generates a strong salt, and applies proper rounds automatically.

As of PHP 7, PASSWORD_DEFAULT still uses bcrypt. It is worth noting that over time this constant can (and likely will) change. Therefore you should be aware that the length of the resulting hash can change. Therefore, if you use PASSWORD_DEFAULT you should store the resulting hash in a way that can store more than 60 characters (255 is the recomended width). 


>  Supported Options:
>  - salt - to manually provide a salt to use when hashing the password. Note that this will override and prevent a salt from being automatically generated.
>  If omitted, a random salt will be generated by password_hash() for each password hashed. This is the intended mode of operation.
>  - cost - which denotes the algorithmic cost that should be used. Examples of these values can be found on the crypt() page. 
>   As of PHP 7, the salt option has been deprecated. As such, no matter your version of PHP, it is recommended to simply use the salt that is generated. 
>   
Regarding the cost option, you can specify your own. The absolute minimum value you should consider using is 10. 12 is good, provided your hardware supports it. The default cost parameter is 10.

{% highlight php startinline %}
<?php

string password_hash ( string $password , integer $algo [, array $options ] )

?>
{% endhighlight %}

{% highlight php startinline %}
<?php
$hash = password_hash($userPassword, PASSWORD_DEFAULT, ['cost' => 12]);
?>
{% endhighlight %}

As of PHP 7, PASSWORD_DEFAULT still uses bcrypt. In a future version, it may migrate to Argon2.


Note: When defining the column to store the password in your database, make sure 

> Verifying a password against a stored hash is incredibly simple:

{% highlight php startinline %}
<?php
if (password_verify($userPassword, $hash)) {
    // Login successful.
    if (password_needs_rehash($hash, PASSWORD_DEFAULT, ['cost' => 12])) {
        // Recalculate a new password_hash() and overwrite the one we stored previously
    }
}
?>
{% endhighlight %}



Work in progress

{% highlight php startinline %}

<?php
function test() {
	phpinfo();
}
?>
{% endhighlight %}


Further readings and references for this post:

- [How to safely store your users' Password in 2016](https://paragonie.com/blog/2016/02/how-safely-store-password-in-2016)
- [Hacker news discussion on the above topic](https://news.ycombinator.com/item?id=11118720)
- [Why password sucks](https://medium.com/inside/why-passwords-suck-d1d1f38c1bb4)
- [Password are obsolete](https://medium.com/@ninjudd/passwords-are-obsolete-9ed56d483eb)
- [Security VS convenience](https://medium.com/@majelbstoat/security-vs-convenience-f68366e4242a)
- [Signing in to Medium by email](https://medium.com/the-story/signing-in-to-medium-by-email-aacc21134fcd)
- [Would you implement  Passwordless login?](http://www.sitepoint.com/implement-passwordless-login/)
- [Is it time for password less login?](http://notes.xoxco.com/post/27999787765/is-it-time-for-password-less-login)
- [Importance of DMARK configuration in email delivery of authentication links](https://medium.com/@onehangryman/since-you-are-relying-on-email-as-the-mode-of-delivery-for-this-form-of-authentication-are-there-1997b17aa41e#.hzm9y4rm7)
- [Why Passwordless authentication works](http://www.sitepoint.com/passwordless-authentication-works/)
- [Write crypto code, don't publish it!](http://www.cryptofails.com/post/75204435608/write-crypto-code-dont-publish-it)