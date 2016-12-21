This repository contains a minimum example for what looks like a bug on
cfn-init.

# The problem itself

## Context

Simply using `cfn-init` to create a symlink described in the `Metadata`
property given to an `AWS::EC2::Instance` object.

Relevant parts of the documentation, taken from
<http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-init.html> :

> content: [...] When you create a symlink, specify the symlink target as the content.

> mode: [...] Use the first three digits for symlinks and the last three digits
> for setting permissions. To create a symlink, specify 120000. To specify
> permissions for a file, use the last three digits, such as 000644.

> The following example snippet creates a symlink /tmp/myfile2.txt that points
> at an existing file /tmp/myfile1.txt.

```
files: 
  /tmp/myfile2.txt: 
    content: "/tmp/myfile1.txt"
    mode: "120000"
```

From my understanding, creating a symlink to a file should not modify the
linked file.

## Stack "without mode"

Take the stack described in the `no-mode` tag of this repository, with the
following Metadata:

```
Metadata:
  AWS::CloudFormation::Init:
    config:
      files:
        "/home/ubuntu/demo_link":
          content: "/etc/issue"
          owner:  "ubuntu"
          group:  "ubuntu"
```

What is **expected** is a simple file `/home/ubuntu/deno_link` with
`/etc/issue` as **content** (not a symlink in any way). Let's see how it goes.

```
$ ls -la /home/ubuntu/demo_link 
-rw-r--r-- 1 ubuntu ubuntu 10 Dec 21 10:45 /home/ubuntu/demo_link
```

This is fine. Plain file with `0644` default mode, as expected.

```
$ cat /home/ubuntu/demo_link 
/etc/issue
```

This is fine too. We have the file's **content** as expected.

```
$ ls -la /etc/issue
-rw-r--r-- 1 root root 26 Aug 12 17:48 /etc/issue
```

This is also fine: it's the `/etc/issue` by default of the AMI, untouched in
any way.

## Stack "with symlink mode"

Now, let's take the stack described in the `symlink-mode` tag of this
repository, with the following Metadata:

```
Metadata:
  AWS::CloudFormation::Init:
    config:
      files:
        "/home/ubuntu/demo_link":
          content: "/etc/issue"
          mode:   "120000"
          owner:  "ubuntu"
          group:  "ubuntu"
```

Note that the difference is only this :

```
$ git diff no-mode symlink-mode
diff --git a/cf-stack.yml b/cf-stack.yml
index d962676..367bd18 100644
--- a/cf-stack.yml
+++ b/cf-stack.yml
@@ -72,5 +72,6 @@ Resources:
           files:
             "/home/ubuntu/demo_link":
               content: "/etc/issue"
+              mode:   " 120000"
               owner:  "ubuntu"
               group:  "ubuntu"
```

Let's take a look at what it produces.

```
$ ls -la /home/ubuntu/demo_link 
lrwxrwxrwx 1 ubuntu ubuntu 10 Dec 21 10:55 /home/ubuntu/demo_link -> /etc/issue
```

Ok, we're fine here, it created a symlink to the good file, as expected.

```
$ ls -la /etc/issue
---------- 1 root root 26 Aug 12 17:48 /etc/issue
```

And here we go. It just set the **mode of the linked file**, `/etc/issue`, to
`0000`. If we set the mode to "120654" it will set the `/etc/issue` mode to
`0654` and so on.

# What I expected

For me, the "To create a symlink, specify 120000" should be strictly respected.

I don't expect something that create a link to a file to touch this file by any
mean : when you do a simple `ln -s TARGET LINK_NAME`, `ln` will not touch
anything on the `LINK_NAME` file since it's taken strictly as a link **name** :
it's just a property of you link, that's why you can even create symlink to
things that don't exist.

# How to reproduce it

Just run the stack defined in `cf-stack.yml` with the following parameters :

- VpcId
- KeyPair
- SubnetId
- AZ

There are 2 tags on this repository :

- `no-mode` on commit `ce10023e98d490de53ab4d8a27b7ae947b8ceb9b`
- `symlink-mode` on commit `c373c95593d7cacd9ed0568dbd4324c7a89ad1c6`

They both provide the CloudFormation stack to test yourself what I just
explained (the problem is in the `symlink-mode` one).

You'll also find what is needed to spawn this stack from Ansible if you prefer,
but this problem is absolutely not related to Ansible whatsoever and can be
reproduced by spawning this stack "by hand" on the CloudFormation web-UI.
