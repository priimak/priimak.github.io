---
published: true
title: Verified patchsets in Gerrit
layout: post
---
Follow these steps to set up Gerrit in such a way so that merge of each individual patchset will only be permitted if it has been marked *Verified*. These settings are per project and not for the entire Gerrit server. 

First through Gerrit web interface create group *Verifiers* and add to it users who will be able to mark patchsets Verified. For example example shown below that group UUID is assumed to be held in the environment variable *GUUID*.

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

From now on merge operation will be permitted only if value of *Verified* label is set to *+1*. You can manually set value of this label on the command line for a given patch set like so

{% highlight bash %}
 ssh -p 29418 ${user_in_verified_group}@${you.gerrit.server} gerrit review --label Verified=1 ${patchset_hash}
{% endhighlight %}
