---
published: true
title: Verified patchsets in Gerrit
layout: post
---
Follow these steps on to permit merge of each individual patchset only it has been marked *Verified*. These settings are per project and not for the entire Gerrit server. 

First through Gerrit web interface create group *Verifiers* and to it users who will be able to mark patchsets Verified. Save UUID of this group in the variable *GUUID*.

Clone your repository, *cd* into it and then do:

{% highlight bash %}
$ git fetch origin refs/meta/config:config
$ git checkout config
$ echo "${GUUID}\tVerifiers" >> groups
$ cat << EOF >> project.config
[label "Verified"]
        function = MaxWithBlock
        value = -1 Fails
        value =  0 No score
        value = +1 Verified
        defaultValue = 0
[access "refs/heads/*"]
        label-Verified = -1..+1 group Verifiers
EOF
{% endhighlight %}

Then push it back into Gerrit

{% highlight bash %}
$ git add project.config groups
$ git commit -m 'Setup of Verify label'
$ git push origin HEAD:refs/meta/config
{% endhighlight %}
