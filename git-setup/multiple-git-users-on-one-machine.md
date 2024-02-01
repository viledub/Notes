# Multiple Git users on one machine

## Generate Keys

Every user should have a separate key associated with their individual email. Generate separate keys for each address:&#x20;

`ssh-keygen -t rsa -b 4096 -C "email@address"`

and save with an identifying name.

## Configure Subfolder

Open your gitconfig under \~/.gitconfig:

<pre><code>[user]
<strong>    name = John Doe
</strong>    email = john@doe.tld

[includeIf "gitdir:~/per/"]
    path = ~/per/.gitconfig
</code></pre>

In the above the / at the end of per means that any repos below \~/per/ will use the \~/per/.gitconfig config which looks like:

```
 [user]
 email = john.doe@company.tld
```

## Test

Verify by calling the following in affected folders:&#x20;

`git config --list`

Calling this command from under the \~/per/ directory should output the name from the \~/per/.gitconfig
